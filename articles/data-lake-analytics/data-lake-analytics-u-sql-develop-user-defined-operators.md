---
title: 开发 U SQL 用户定义的运算符-Azure Data Lake Analytics
description: 了解如何开发可在 Azure Data Lake Analytics 作业中使用和重复使用的用户定义的运算符。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: 11efdb727bacadb674fb49374ef1c70fcc788ecc
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92219983"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>开发 U-SQL 用户定义的运算符 (UDO)
本文介绍如何开发用户定义的运算符以处理 U-SQL 作业中的数据。

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>在 U-SQL 中定义和使用用户定义的运算符

### <a name="to-create-and-submit-a-u-sql-job"></a>创建和提交 U-SQL 作业

1. 在 Visual Studio 中，选择“文件”>“新建”>“项目”>“U-SQL 项目”****。
2. 单击“确定”。 Visual Studio 将创建包含 Script.usql 文件的解决方案。
3. 在“解决方案资源管理器”**** 中，展开 Script.usql，并单击 “Script.usql.cs”****。
4. 将以下代码粘贴到文件中：

   ```usql
   using Microsoft.Analytics.Interfaces;
   using System.Collections.Generic;
   namespace USQL_UDO
   {
       public class CountryName : IProcessor
       {
           private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
           {
               {
                   "Deutschland", "Germany"
               },
               {
                   "Suisse", "Switzerland"
               },
               {
                   "UK", "United Kingdom"
               },
               {
                   "USA", "United States of America"
               },
               {
                   "中国", "PR China"
               }
           };
           public override IRow Process(IRow input, IUpdatableRow output)
           {
               string UserID = input.Get<string>("UserID");
               string Name = input.Get<string>("Name");
               string Address = input.Get<string>("Address");
               string City = input.Get<string>("City");
               string State = input.Get<string>("State");
               string PostalCode = input.Get<string>("PostalCode");
               string Country = input.Get<string>("Country");
               string Phone = input.Get<string>("Phone");
               if (CountryTranslation.Keys.Contains(Country))
               {
                   Country = CountryTranslation[Country];
               }
               output.Set<string>(0, UserID);
               output.Set<string>(1, Name);
               output.Set<string>(2, Address);
               output.Set<string>(3, City);
               output.Set<string>(4, State);
               output.Set<string>(5, PostalCode);
               output.Set<string>(6, Country);
               output.Set<string>(7, Phone);
               return output.AsReadOnly();
           }
       }
   }
   ```

5. 打开 **Script.usql**，并粘贴以下 U-SQL 脚本：

   ```usql
   @drivers =
       EXTRACT UserID      string,
               Name        string,
               Address     string,
               City        string,
               State       string,
               PostalCode  string,
               Country     string,
               Phone       string
       FROM "/Samples/Data/AmbulanceData/Drivers.txt"
       USING Extractors.Tsv(Encoding.Unicode);

   @drivers_CountryName =
       PROCESS @drivers
       PRODUCE UserID string,
               Name string,
               Address string,
               City string,
               State string,
               PostalCode string,
               Country string,
               Phone string
       USING new USQL_UDO.CountryName();

   OUTPUT @drivers_CountryName
       TO "/Samples/Outputs/Drivers.csv"
       USING Outputters.Csv(Encoding.Unicode);
   ```

6. 指定 Data Lake Analytics 帐户、数据库和架构。
7. 在“解决方案资源管理器”中，右键单击“Script.usql”，并单击“生成脚本”。************
8. 在“解决方案资源管理器”中，右键单击“Script.usql”，并单击“提交脚本”。************
9. 如果尚未连接到 Azure 订阅，系统将提示输入 Azure 帐户凭据。
10. 单击“提交” 。 完成提交后，“结果”窗口中会出现提交结果和作业链接。
11. 单击“刷新”**** 按钮以查看最新的作业状态和刷新屏幕。

### <a name="to-see-the-output"></a>查看输出

1. 在“服务器资源管理器”**** 中依次展开 “Azure”****、“Data Lake Analytics”****、Data Lake Analytics 帐户、“存储帐户”****，右键单击“默认存储”，并单击“资源管理器”****。

2. 展开示例、展开输出，并双击 “Drivers.csv”****。

## <a name="next-steps"></a>后续步骤

* [Extending U-SQL Expressions with User-Code](/u-sql/concepts/extending-u-sql-expressions-with-user-code)（使用用户代码扩展 U-SQL 表达式）
* [使用针对 Visual Studio 的 Data Lake 工具开发 U-SQL 应用程序](data-lake-analytics-data-lake-tools-get-started.md)
