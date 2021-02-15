---
title: 使用 Azure 密钥管理库配置 Always Encrypted
description: 本教程说明如何使用 SQL Server Management Studio 中的 Always Encrypted 向导，通过数据加密来保护 Azure SQL 数据库中数据库中的敏感数据。
keywords: 数据加密, 加密密钥, 云加密
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 11/02/2020
ms.openlocfilehash: 257abf03994c7006b1c3789174f550515dcd309a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841455"
---
# <a name="configure-always-encrypted-by-using-azure-key-vault"></a>使用 Azure 密钥管理库配置 Always Encrypted 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]

本文介绍如何使用 [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms) 中的 [Always Encrypted 向导](/sql/relational-databases/security/encryption/always-encrypted-wizard)，通过数据加密来保护 Azure SQL 数据库中数据库中的敏感数据。 它还包括如何将每个加密密钥存储在 Azure 密钥保管库的说明。

Always Encrypted 是一种数据加密技术，用于保护服务器上的静态的、在客户端和服务器之间进行移动的、以及数据使用期间的敏感数据。 始终加密可以确保敏感数据永远不会在数据库系统中以明文形式显示。 配置数据加密之后，仅客户端应用程序或应用服务器（具有密钥访问权限）能够访问明文数据。 有关详细信息，请参阅[始终加密（数据库引擎）](/sql/relational-databases/security/encryption/always-encrypted-database-engine)。

将数据库配置为使用始终加密后，会通过 Visual Studio 在 C# 中创建一个客户端应用程序，以便处理加密的数据。

请执行本文中的步骤，了解如何在 Azure SQL 数据库或 SQL 托管实例中为数据库设置 Always Encrypted。 在本文中，可以学习如何执行以下任务：

- 在 SSMS 中使用始终加密向导创建[始终加密密钥](/sql/relational-databases/security/encryption/always-encrypted-database-engine#Anchor_3)。
  - 创建[列主密钥 (CMK)](/sql/t-sql/statements/create-column-master-key-transact-sql)。
  - 创建[列加密密钥 (CEK)](/sql/t-sql/statements/create-column-encryption-key-transact-sql)。
- 创建一个数据库表并加密列。
- 创建一个可以从已加密列插入、选择和显示数据的应用程序。

## <a name="prerequisites"></a>先决条件


- Azure 帐户和订阅。 如果没有，请注册 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
- [Azure SQL 数据库](single-database-create-quickstart.md)或 [Azure SQL 托管实例](../managed-instance/instance-create-quickstart.md)中的数据库。
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 版本 13.0.700.242 或更高版本。
- [.NET Framework 4.6](/dotnet/framework/) 或更高版本（在客户端计算机上）。
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)。
- [Azure PowerShell](/powershell/azure/) 或 [Azure CLI](/cli/azure/install-azure-cli)

## <a name="enable-client-application-access"></a>启用客户端应用程序访问

首先必须通过设置 Azure Active Directory (Azure AD) 应用程序并复制对应用程序进行身份验证所需的应用程序 ID 和密钥，使客户端应用程序可以访问 SQL 数据库服务 。

若要获取应用程序 ID 和 *密钥*，请按照 [创建可访问资源的 Azure Active Directory 应用程序和服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)中的步骤进行操作。

## <a name="create-a-key-vault-to-store-your-keys"></a>创建密钥保管库以存储密钥

至此，已配置客户端应用并且已拥有应用程序 ID，接下来，可以创建密钥保管库并配置其访问策略，以便你和你的应用程序可以访问保管库的机密（Always Encrypted 密钥）。 *create*、*get*、*list*、*sign*、*verify*、*wrapKey* 和 *unwrapKey* 权限是用于创建新的列主密钥以及通过 SQL Server Management Studio 设置加密所必需的。

通过运行以下脚本，可以快速创建密钥保管库。 有关这些命令的详细说明以及有关创建和配置密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../../key-vault/general/overview.md)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> PowerShell Azure 资源管理器 (RM) 模块仍受 Azure SQL 数据库支持，但所有未来的开发都是针对 Az.Sql 模块的。 AzureRM 模块至少在 2020 年 12 月之前将继续接收 bug 修补程序。  Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 若要详细了解其兼容性，请参阅[新 Azure PowerShell Az 模块简介](/powershell/azure/new-azureps-module-az)。

```powershell
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

Connect-AzAccount
$subscriptionId = (Get-AzSubscription -SubscriptionName $subscriptionName).Id
Set-AzContext -SubscriptionId $subscriptionId

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
Set-AzKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your database in Azure SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

az login
az account set --subscription $subscriptionName

az group create --location $location --name $resourceGroupName

az keyvault create --name $vaultName --resource-group $resourceGroupName --location $location

az keyvault set-policy --name $vaultName --key-permissions create get list sign unwrapKey verify wrapKey --resource-group $resourceGroupName --upn $userPrincipalName
az keyvault set-policy --name $vaultName --key-permissions get list sign unwrapKey verify wrapKey --resource-group $resourceGroupName --spn $applicationId
```

---

## <a name="connect-with-ssms"></a>使用 SSMS 进行连接

打开 SQL Server Management Studio (SSMS) 并连接到通过数据库管理的服务器。

1. 打开 SSMS。 （转到“连接” > “数据库引擎”以打开“连接到服务器”窗口）（如果它未打开。）

2. 输入你的服务器名称或实例名称和凭据。 

    ![复制连接字符串](./media/always-encrypted-azure-key-vault-configure/ssms-connect.png)

如果“新建防火墙规则”窗口打开，请登录到 Azure，让 SSMS 创建新的防火墙规则。

## <a name="create-a-table"></a>创建表

在本部分中，需要创建一个表以保存患者数据。 它最初尚未加密 - 可在下一部分配置加密。

1. 展开“数据库”。
2. 右键单击数据库，然后单击“新建查询”。
3. 将以下 Transact-SQL (T-SQL) 粘贴到新查询窗口中，并“执行”它。

```sql
CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
GO
```

## <a name="encrypt-columns-configure-always-encrypted"></a>加密列（配置始终加密）

SSMS 提供了一个向导，通过设置列主密钥、列加密密钥和已加密列即可轻松地配置始终加密。

1. 展开“数据库” > “Clinic” > “表”。
2. 右键单击“患者”表，并选择“加密列”以打开始终加密向导：

    ![突出显示“加密列…”菜单选项的屏幕截图。](./media/always-encrypted-azure-key-vault-configure/encrypt-columns.png)

Always Encrypted 向导包括以下几部分：**列选择**、**主密钥配置**、**验证** 和 **摘要**。

### <a name="column-selection"></a>列选择

单击“简介”页上的“下一步”，可以打开“列选择”页。 在此页上，选择想要加密的列，[加密类型和要使用的列加密密钥 (CEK)](/sql/relational-databases/security/encryption/always-encrypted-wizard#Anchor_2)。

加密每位患者的“SSN”和“出生日期”信息。 SSN 列将使用确定性加密，该加密支持相等性查找、联接和分组方式。 BirthDate 列会使用随机加密，该加密不支持操作。

将 SSN 列的“加密类型”设置为“确定”，并将 BirthDate 列设置为“随机”。 单击“下一步”。

![加密列](./media/always-encrypted-azure-key-vault-configure/column-selection.png)

### <a name="master-key-configuration"></a>主密钥配置

**主密钥配置** 页是设置 CMK 和选择密钥存储提供程序（在其中存储 CMK）的地方。 目前，可以将 CMK 存储在 Windows 证书存储、Azure 密钥保管库或硬件安全模块 (HSM) 中。

本教程演示如何将密钥存储在 Azure 密钥保管库中。

1. 选择“Azure 密钥保管库”。
2. 从下拉列表中选择所需密钥保管库。
3. 单击“下一步”。

![主密钥配置](./media/always-encrypted-azure-key-vault-configure/master-key-configuration.png)

### <a name="validation"></a>验证

可以现在就加密这些列，也可以保存 PowerShell 脚本供以后运行。 对于本教程，请选择“现在完成”，并单击“下一步”。

### <a name="summary"></a>摘要

验证设置是否全都正确，并单击“完成”以完成“始终加密”的设置。

![显示任务标记为“已通过”的“结果”页面的屏幕截图。](./media/always-encrypted-azure-key-vault-configure/summary.png)

### <a name="verify-the-wizards-actions"></a>验证向导的操作

向导完成后，数据库就会设置为始终加密。 该向导执行以下操作：

- 创建列主密钥并将其存储在 Azure 密钥保管库中。
- 创建列加密密钥并将其存储在 Azure 密钥保管库中。
- 配置了所选列的加密。 “患者”表目前尚无数据，但所选列中的任何现有数据都会进行加密。

可以验证 SSMS 中密钥的创建，只需展开“Clinic” > “安全” > “始终加密密钥”即可。

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>创建处理已加密数据的客户端应用程序

现在已设置始终加密，可以生成一个应用程序，用其在已加密列上执行某些 *inserts* 操作和 *selects* 操作。  

> [!IMPORTANT]
> 通过始终加密列将明文数据传递到服务器时，应用程序必须使用 [SqlParameter](/dotnet/api/system.data.sqlclient.sqlparameter) 对象。 在不使用 SqlParameter 对象的情况下传递文本值会导致异常。

1. 打开 Visual Studio 并创建新的 C# **控制台应用程序**（Visual Studio 2015 和更低版本）或 **控制台应用 (.NET Framework)** （Visual Studio 2017 和更高版本）。 确保将项目设置为 **.NET Framework 4.6** 或更高版本。
2. 将项目命名为 **AlwaysEncryptedConsoleAKVApp**，并单击“确定”。
3. 通过转到“工具” > “NuGet 包管理器” > “包管理器控制台”来安装以下 NuGet 包。

在包管理器控制台中运行以下 2 行代码：

   ```powershell
   Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>修改连接字符串以启用始终加密

本节介绍如何在数据库连接字符串中启用始终加密。

要启用“始终加密”，你需要将“列加密设置”关键字添加到连接字符串中，并将其设置为“启用”。

可以在连接字符串中直接进行该设置，也可以使用 [SqlConnectionStringBuilder](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder) 进行设置。 下一节中的示例应用程序演示如何使用 **SqlConnectionStringBuilder**。

### <a name="enable-always-encrypted-in-the-connection-string"></a>在连接字符串中启用始终加密

将以下关键字添加到连接字符串中。

   `Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>通过 SqlConnectionStringBuilder 启用始终加密

以下代码显示了如何通过将 [SqlConnectionStringBuilder.ColumnEncryptionSetting](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting) 设置为[启用](/dotnet/api/system.data.sqlclient.sqlconnectioncolumnencryptionsetting)来启用“始终加密”。

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder = new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting = SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>注册 Azure 密钥保管库提供程序
下面的代码演示如何使用 ADO.NET 驱动程序注册 Azure 密钥保管库提供程序。

```csharp
private static ClientCredential _clientCredential;

static void InitializeAzureKeyVaultProvider() {
    _clientCredential = new ClientCredential(applicationId, clientKey);

    SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider = new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

    Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers = new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

    providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
    SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
}
```

## <a name="always-encrypted-sample-console-application"></a>始终加密示例控制台应用程序

此示例演示了如何执行以下操作：

- 修改连接字符串以启用始终加密。
- 将 Azure 密钥保管库注册为应用程序的密钥存储提供程序。  
- 将数据插入已加密列。
- 通过在已加密列中筛选出特定的值来选择记录。

将 *Program.cs* 的内容替换为以下代码。 将 Main 方法前一行中全局 connectionString 变量的连接字符串替换为 Azure 门户中的有效连接字符串。 这是需要对此代码进行的唯一更改。

运行该应用以在操作中查看始终加密。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
using System.Data.SqlClient;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

namespace AlwaysEncryptedConsoleAKVApp {
    class Program {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";

        static void Main(string[] args) {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();

            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;

            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient() {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });

            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients()) {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null) {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }

        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider() {
            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope) {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient) {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);

            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex) {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients() {
            List<Patient> patients = new List<Patient>();

            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));

            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patients.Add(new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }

            return patients;
        }

        static Patient SelectPatientBySSN(string ssn) {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patient = new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else {
                        patient = null;
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }
            return patient;
        }

        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable() {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex) {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>确保数据已加密

通过 SSMS 对患者数据进行查询（使用当前的连接，其中，**列加密设置** 尚未启用），可以快速查看服务器上的实际数据是否已加密。

针对 Clinic 数据库运行以下查询。

```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

可以看到，加密的列不包含任何明文数据。

   ![显示已加密列不包含任何明文数据的屏幕截图。](./media/always-encrypted-azure-key-vault-configure/ssms-encrypted.png)

若要使用 SSMS 访问纯文本数据，首先需要确保用户具有 Azure Key Vault 的适当权限：get、unwrapKey 和 verify。 有关详细信息，请参阅[创建和存储列主密钥 (Always Encrypted)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted)。

然后在连接期间添加 *Column Encryption Setting=enabled* 参数。

1. 在 SSMS 中，右键单击“对象资源管理器”中的服务器，并选择“断开连接”。
2. 单击“连接” > “数据库引擎”打开“连接到服务器”窗口，并单击“选项”。
3. 单击“其他连接参数”，并键入 **Column Encryption Setting=enabled**。

    ![显示“其他更正参数”选项卡的屏幕截图。](./media/always-encrypted-azure-key-vault-configure/ssms-connection-parameter.png)

4. 针对 Clinic 数据库运行以下查询。

   ```sql
   SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

   现在，可以看到已加密列中的明文数据。
   
   ![新建控制台应用程序](./media/always-encrypted-azure-key-vault-configure/ssms-plaintext.png)

## <a name="next-steps"></a>后续步骤

将数据库配置为使用 Always Encrypted 后，可能需要执行以下操作：

- [轮换使用和清除密钥](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio)。
- [迁移已使用始终加密加密的数据](/sql/relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted)。

## <a name="related-information"></a>相关信息

- [始终加密（客户端开发）](/sql/relational-databases/security/encryption/always-encrypted-client-development)
- [透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption)
- [SQL Server 加密](/sql/relational-databases/security/encryption/sql-server-encryption)
- [始终加密向导](/sql/relational-databases/security/encryption/always-encrypted-wizard)
- [始终加密博客](/archive/blogs/sqlsecurity/always-encrypted-key-metadata)
