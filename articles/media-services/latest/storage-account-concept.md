---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>必填字段。 有关详细信息，请参阅 aka.ms/skyeye/meta。
标题： Azure 存储帐户： Azure 媒体服务说明：了解如何创建用于 Azure 媒体服务的 Azure 存储帐户。
服务：媒体服务 documentationcenter： ' ' author： IngridAtMicrosoft manager： femila editor： ' ' ms. service： media-服务毫秒：：01/29/2021：： inhenkel
---

# <a name="azure-storage-accounts"></a>Azure 存储帐户

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

若要开始管理、加密、编码、分析和流式处理 Azure 中的媒体内容，需要创建媒体服务帐户。 创建媒体服务帐户时，需要提供 Azure 存储帐户资源的名称。 指定存储帐户会附加到媒体服务帐户。

媒体服务帐户和所有关联的存储帐户必须位于同一 Azure 订阅中。 强烈建议在媒体服务帐户所在的位置使用存储帐户，避免额外的延迟和数据出口成本。

必须具有一个主存储帐户，并且可以拥有任意数量的与媒体服务帐户关联的辅助存储帐户 。 媒体服务支持常规用途 v2 (GPv2) 或常规用途 v1 (GPv1) 帐户 。 不允许将仅限 Blob 的帐户作为主帐户。

我们建议你使用 GPv2，以便可以利用最新的功能和性能。 若要了解存储帐户的详细信息，请参阅 [Azure 存储帐户概述](../../storage/common/storage-account-overview.md)。

> [!NOTE]
> 仅热访问层支持与 Azure 媒体服务配合使用，尽管其他访问层可用于降低未活跃使用的内容的存储成本。

可以为存储帐户选择不同的 SKU。 若要通过存储帐户进行试验，请使用 `--sku Standard_LRS`。 但是，在选取用于生产的 SKU 时，应考虑 `--sku Standard_RAGRS`，以便通过异地复制确保业务连续性。

## <a name="assets-in-a-storage-account"></a>存储帐户中的资产

在媒体服务 v3 中，存储 API 用于将文件上传到资产中。 有关详细信息，请参阅 [Azure 媒体服务 v3 中的资产](assets-concept.md)。

> [!Note]
> 在不使用媒体服务 API 的情况下，请勿尝试更改媒体服务 SDK 生成的 BLOB 容器内容。

## <a name="storage-side-encryption"></a>存储端加密

若要保护静态资产，应通过存储端加密对资产进行加密。 下表显示了存储端加密在媒体服务 v3 中的工作方式：

|加密选项|说明|媒体服务 v3|
|---|---|---|
|媒体服务存储加密| AES-256 加密，媒体服务管理的密钥。 |不支持。<sup>1</sup>|
|[静态数据的存储服务加密](../../storage/common/storage-service-encryption.md)|由 Azure 存储提供的服务器端加密，由 Azure 或客户托管的密钥。|。|
|[存储客户端加密](../../storage/common/storage-client-side-encryption.md)|由 Azure 存储提供的客户端加密，由 Key Vault 中的客户托管密钥。|不支持。|

<sup>1</sup> 在媒体服务 v3 中，仅当资产是使用媒体服务 v2 创建的时才支持存储加密（AES-256 加密）以实现向后兼容性，这意味着 v3 适用于现有存储加密的资产，但不允许创建新资产。

## <a name="storage-account-double-encryption"></a>存储帐户双加密

存储帐户支持双加密，但必须显式启用第二层。 请参阅[静态数据的 Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#doubly-encrypt-data-with-infrastructure-encryption)。  

## <a name="storage-account-errors"></a>存储帐户错误

如果某个媒体服务帐户处于“已断开连接”状态，则表明该帐户不再能够访问一个或多个附加的存储帐户，因为存储访问密钥已更改。 媒体服务需要最新的存储访问密钥才能执行帐户中的许多任务。

下面这些主要场景会导致媒体服务帐户无法访问附加的存储帐户。

|问题|解决方案|
|---|---|
|媒体服务帐户或附加的存储帐户已迁移到单独的订阅。 |迁移存储帐户或媒体服务帐户，使之全都位于同一订阅中。 |
|媒体服务帐户在使用另一订阅中的附加存储帐户，因为它是支持此功能的早期媒体服务帐户。 所有早期的媒体服务帐户都已转换成新式的基于 Azure 资源管理器的帐户，其状态将为“已断开连接”。 |迁移存储帐户或媒体服务帐户，使之全都位于同一订阅中。|

## <a name="next-steps"></a>后续步骤

若要了解如何将存储帐户附加到媒体服务帐户，请参阅[创建帐户](./create-account-howto.md)。
