---
title: 适用于 Azure 证明的基于虚拟化的安全 (VBS) 协议
description: VBS 证明协议
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 38012c5b4bb9338c1200d9583256193ee8402c98
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507873"
---
# <a name="trusted-platform-module-tpm-and-virtualization-based-securityvbs-enclave-attestation-protocol"></a>受信任的平台模块 (基于 TPM) 和基于虚拟化的安全 (VBS) enclave 证明协议 

Microsoft Azure 证明，提供强大的安全保障依赖于验证信任链是否从信任的根 (TPM) 启动虚拟机监控程序和安全内核。 若要实现此 Azure 认证，必须先证明计算机的启动状态，然后才能在 secure enclave 中建立信任。 操作系统、虚拟机监控程序和安全内核二进制文件必须由正确的官方 Microsoft 机构签署，并以安全的方式进行配置。 在受信任的平台模块之间建立信任后 (TPM) 和虚拟机监控程序的运行状况，我们可以信任已测量的启动日志中提供的基于虚拟化的安全 (VBS) enclave IDKs，这样我们就可以验证 enclave 和 mint 生成的密钥对，并将其绑定到绑定此密钥中的信任，并包含其他声明，如安全级别和启动证明属性 

VBS enclaves 要求使用 TPM 来提供用于验证安全基础的度量。 除了协议中的 request 对象外，还可以通过 TPM 终结点证明 VBS enclaves。 

## <a name="protocol-messages"></a>协议消息

### <a name="init-message"></a>初始化消息

#### <a name="direction"></a>方向

客户端 > Azure 证明

#### <a name="payload"></a>Payload

```
{ 
  "type": "aikcert" 
} 
```

"type" (ASCII string) ：表示请求的证明类型。 目前仅支持 "aikcert"。

### <a name="challenge-message"></a>质询消息

#### <a name="direction"></a>方向

Azure 证明-> 客户端

#### <a name="payload"></a>Payload

```
{ 
  "challenge": "<BASE64URL(CHALLENGE)>", 
  "service_context": "<BASE64URL(SERVICECONTEXT)>" 
} 
```

**质询** (BASE64URL (个八进制) # A3：服务发出的随机值。

**service_context** (BASE64URL (八进制数) # A3：服务创建的不透明上下文。


### <a name="request-message"></a>请求消息

#### <a name="direction"></a>方向

客户端 > Azure 证明 

#### <a name="payload"></a>Payload

```
{
  "request": "<JWS>"
}
```

**请求** (JWS) ：请求包含一个 JSON Web 签名 (JWS) 结构。 JWS 保护的标头和 JWS 负载如下所示。 与任何 JWS 结构一样，最终值由以下内容组成：

BASE64URL (UTF8 (JWS 受保护的标头) # A3 | |'.' ||

BASE64URL (JWS 负载) | |'.' ||

BASE64URL (JWS 签名) 

##### <a name="jws-protected-header"></a>JWS 保护的标头

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>JWS 有效负载

JWS 负载可以是 basic 或 VBS 类型。 当证明证据不包含 VBS 数据时，使用 Basic。 

仅 TPM 示例： 

``` 
{ 
  "att_type": "basic", 
  "att_data": { 
    "rp_id": "<URL>", 
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
    "challenge": "<BASE64URL(CHALLENGE)>", 

    "tpm_att_data": { 
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "aik_pub": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
      "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
    }, 

    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

    "attest_key": { 
      "kty": "RSA", 
      "n": "<Base64urlUInt(MODULUS)>", 
      "e": "<Base64urlUInt(EXPONENT)>" 
    }, 
    "custom_claims": [ 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      }, 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      } 
    ], 
    "service_context": "<BASE64URL(SERVICECONTEXT)>" 
  } 
} 
```

TPM + VBS enclave 示例： 

``` 
{ 
  "att_type": "vbs", 
  "att_data": { 
    "report_signed": { 
      "rp_id": "<URL>", 
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
      "challenge": "<BASE64URL(CHALLENGE)>", 
      "tpm_att_data": { 
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

        "aik_pub": { 
          "kty": "RSA", 
          "n": "<Base64urlUInt(MODULUS)>", 
          "e": "<Base64urlUInt(EXPONENT)>" 
        }, 
        "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
        "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
      }, 

      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "attest_key": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "custom_claims": [ 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        }, 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        } 
      ], 
      "service_context": "<BASE64URL(SERVICECONTEXT)>" 
    }, 
    "vsm_report": "<BASE64URL(REPORT)>" 
  } 
} 
``` 

**rp_id** (StringOrURI) ：信赖方标识符。 由服务在计算机 ID 声明的计算中使用

**rp_data** (BASE64URL (个八进制数) # A3：依赖方传递的不透明数据。 这通常由信赖方用作 nonce 来保证报表的新鲜度

**质询** (BASE64URL (个八进制) # A3：服务发出的随机值

**tpm_att_data**：与 tpm 相关的证明数据

- **srtm_boot_log (BASE64URL (个八位字节) # B3：** 由函数 Tbsi_Get_TCG_Log_Ex 检索的包含日志类型 = TBS_TCGLOG_SRTM_BOOT 的启动日志

- **srtm_resume_log (BASE64URL (个八进制数) # B3**： srtm 恢复由函数 Tbsi_Get_TCG_Log_Ex 检索的日志，日志类型 = TBS_TCGLOG_SRTM_RESUME

- **drtm_boot_log (BASE64URL (个八位字节) # B3：** 由函数 Tbsi_Get_TCG_Log_Ex 检索的包含日志类型 = TBS_TCGLOG_DRTM_BOOT 的启动日志

- **drtm_resume_log (BASE64URL (个八进制数) # B3**： drtm 恢复由函数 Tbsi_Get_TCG_Log_Ex 检索的日志，日志类型 = TBS_TCGLOG_DRTM_RESUME

- **aik_cert (BASE64URL (八进制数) # B3**：由函数 NCryptGetProperty 和 property = 返回的 aik 的 x.509 证书 NCRYPT_CERTIFICATE_PROPERTY

- **aik_pub**： aik 的公共部分，表示为 JSON Web 密钥 (JWK) 对象 (RFC 7517) 

- **current_claim (BASE64URL (个八位字节) # B3**：当前 PCR 状态的证明声明，由函数 NCryptCreateClaim 和 dwClaimType = NCRYPT_CLAIM_PLATFORM 返回，参数 NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK 设置为包含所有 PCRs。 服务发送的质询还应用于计算此声明

- **boot_claim (BASE64URL (八进制数) # B3**：启动时的 PCR 状态的证明声明，NCryptCreateClaim 具有 dwClaimType = NCRYPT_CLAIM_PLATFORM，参数 NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK 设置为 Include all PCRs

**vsm_report**   (BASE64URL (八进制) # A3：由函数 EnclaveGetAttestationReport 返回的 VBS enclave 证明报告。 EnclaveData 参数必须是 report_signed (的值的 512 SHA-1 哈希，包括左大括号和右大括号) 。 哈希函数输入 (report_signed UTF8) 

**attest_key**：表示为 JSON Web 密钥 (JWK)  (RFC 7517 对象的 enclave 密钥的公共部分) 

**custom_claims**：发送到服务的自定义 enclave 声明数组，该服务可通过策略进行评估。 声明

- **name (String)**：声明的名称。 此名称将追加到由证明服务 (确定的 url，以避免冲突) ，串联的字符串将成为可在策略中使用的声明的类型

- **值 (String)**：声明的值

- **value_type (String)**：声明值的数据类型

**service_context** (BASE64URL (八进制数) # A3：服务创建的不透明上下文。

### <a name="report-message"></a>报告消息

#### <a name="direction"></a>方向

Azure 证明-> 客户端

#### <a name="payload"></a>Payload

```
{
  "report": "<JWT>"
}
```

**报表** (jwt) ： JSON Web 令牌中的证明报告 (JWT) 格式 (RFC 7519) 。

## <a name="next-steps"></a>后续步骤

- [Azure 证明工作流](workflow.md)
