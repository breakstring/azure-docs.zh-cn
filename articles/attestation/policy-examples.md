---
title: Azure 证明策略的示例
description: Azure 证明策略的示例。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 51e8f01726c732604199ff08323f073d508da66e
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602302"
---
# <a name="examples-of-an-attestation-policy"></a>证明策略的示例

证明策略用于处理证明证据，并确定 Azure 证明是否将颁发证明令牌。 可以通过自定义策略控制证明令牌的生成。 下面是证明策略的一些示例。

## <a name="default-policy-for-an-sgx-enclave"></a>SGX enclave 的默认策略 

```
version= 1.0;
authorizationrules
{
    c:[type=="$is-debuggable"] => permit();
};

issuancerules
{
    c:[type=="$is-debuggable"] => issue(type="is-debuggable", value=c.value);
    c:[type=="$sgx-mrsigner"] => issue(type="sgx-mrsigner", value=c.value);
    c:[type=="$sgx-mrenclave"] => issue(type="sgx-mrenclave", value=c.value);
    c:[type=="$product-id"] => issue(type="product-id", value=c.value);
    c:[type=="$svn"] => issue(type="svn", value=c.value);
    c:[type=="$tee"] => issue(type="tee", value=c.value);
};
```

## <a name="sample-custom-policy-for-an-sgx-enclave"></a>SGX enclave 的示例自定义策略 

```
version= 1.0;
authorizationrules
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==<product-id> ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="<mrsigner>"]
    => permit();
};
```

## <a name="unsigned-policy-for-an-sgx-enclave-with-policyformatjwt"></a>SGX enclave 的未签名策略，PolicyFormat=JWT

```
eyJhbGciOiJub25lIn0.eyJBdHRlc3RhdGlvblBvbGljeSI6ICJkbVZ5YzJsdmJqMGdNUzR3TzJGMWRHaHZjbWw2WVhScGIyNXlkV3hsYzN0ak9sdDBlWEJsUFQwaUpHbHpMV1JsWW5WbloyRmliR1VpWFNBOVBpQndaWEp0YVhRb0tUdDlPMmx6YzNWaGJtTmxjblZzWlhON1l6cGJkSGx3WlQwOUlpUnBjeTFrWldKMVoyZGhZbXhsSWwwZ1BUNGdhWE56ZFdVb2RIbHdaVDBpYVhNdFpHVmlkV2RuWVdKc1pTSXNJSFpoYkhWbFBXTXVkbUZzZFdVcE8yTTZXM1I1Y0dVOVBTSWtjMmQ0TFcxeWMybG5ibVZ5SWwwZ1BUNGdhWE56ZFdVb2RIbHdaVDBpYzJkNExXMXljMmxuYm1WeUlpd2dkbUZzZFdVOVl5NTJZV3gxWlNrN1l6cGJkSGx3WlQwOUlpUnpaM2d0YlhKbGJtTnNZWFpsSWwwZ1BUNGdhWE56ZFdVb2RIbHdaVDBpYzJkNExXMXlaVzVqYkdGMlpTSXNJSFpoYkhWbFBXTXVkbUZzZFdVcE8yTTZXM1I1Y0dVOVBTSWtjSEp2WkhWamRDMXBaQ0pkSUQwLUlHbHpjM1ZsS0hSNWNHVTlJbkJ5YjJSMVkzUXRhV1FpTENCMllXeDFaVDFqTG5aaGJIVmxLVHRqT2x0MGVYQmxQVDBpSkhOMmJpSmRJRDAtSUdsemMzVmxLSFI1Y0dVOUluTjJiaUlzSUhaaGJIVmxQV011ZG1Gc2RXVXBPMk02VzNSNWNHVTlQU0lrZEdWbElsMGdQVDRnYVhOemRXVW9kSGx3WlQwaWRHVmxJaXdnZG1Gc2RXVTlZeTUyWVd4MVpTazdmVHMifQ.
```

## <a name="signed-policy-for-an-sgx-enclave-with-policyformatjwt"></a>SGX enclave 的签名策略，PolicyFormat=JWT

```
eyJhbGciOiJSU0EyNTYiLCJ4NWMiOlsiTUlJQzFqQ0NBYjZnQXdJQkFnSUlTUUdEOUVGakJcdTAwMkJZd0RRWUpLb1pJaHZjTkFRRUxCUUF3SWpFZ01CNEdBMVVFQXhNWFFYUjBaWE4wWVhScGIyNURaWEowYVdacFkyRjBaVEF3SGhjTk1qQXhNVEl6TVRneU1EVXpXaGNOTWpFeE1USXpNVGd5TURVeldqQWlNU0F3SGdZRFZRUURFeGRCZEhSbGMzUmhkR2x2YmtObGNuUnBabWxqWVhSbE1EQ0NBU0l3RFFZSktvWklodmNOQVFFQkJRQURnZ0VQQURDQ0FRb0NnZ0VCQUpyRWVNSlo3UE01VUJFbThoaUNLRGh6YVA2Y2xYdkhmd0RIUXJ5L3V0L3lHMUFuMGJ3MVU2blNvUEVtY2FyMEc1WmYxTUR4alZOdEF5QjZJWThKLzhaQUd4eFFnVVZsd1dHVmtFelpGWEJVQTdpN1B0NURWQTRWNlx1MDAyQkJnanhTZTBCWVpGYmhOcU5zdHhraUNybjYwVTYwQUU1WFx1MDAyQkE1M1JvZjFUUkNyTXNLbDRQVDRQeXAzUUtNVVlDaW9GU3d6TkFQaU8vTy9cdTAwMkJIcWJIMXprU0taUXh6bm5WUGVyYUFyMXNNWkptRHlyUU8vUFlMTHByMXFxSUY2SmJsbjZEenIzcG5uMXk0Wi9OTzJpdFBxMk5Nalx1MDAyQnE2N1FDblNXOC9xYlpuV3ZTNXh2S1F6QVR5VXFaOG1PSnNtSThUU05rLzBMMlBpeS9NQnlpeDdmMTYxQ2tjRm1LU3kwQ0F3RUFBYU1RTUE0d0RBWURWUjBUQkFVd0F3RUIvekFOQmdrcWhraUc5dzBCQVFzRkFBT0NBUUVBZ1ZKVWRCaXRud3ZNdDdvci9UMlo4dEtCUUZsejFVcVVSRlRUTTBBcjY2YWx2Y2l4VWJZR3gxVHlTSk5pbm9XSUJROU9QamdMa1dQMkVRRUtvUnhxN1NidGxqNWE1RUQ2VjRyOHRsejRISjY0N3MyM2V0blJFa2o5dE9Gb3ZNZjhOdFNVeDNGTnBhRUdabDJMUlZHd3dcdTAwMkJsVThQd0gzL2IzUmVCZHRhQTdrZmFWNVx1MDAyQml4ZWRjZFN5S1F1VkFUbXZNSTcxM1A4VlBsNk1XbXNNSnRrVjNYVi9ZTUVzUVx1MDAyQkdZcU1yN2tLWGwxM3lldUVmVTJWVkVRc1ovMXRnb29iZVZLaVFcdTAwMkJUcWIwdTJOZHNcdTAwMkJLamRIdmFNYngyUjh6TDNZdTdpR0pRZnd1aU1tdUxSQlJwSUFxTWxRRktLNmRYOXF6Nk9iT01zUjlpczZ6UDZDdmxGcEV6bzVGUT09Il19.eyJBdHRlc3RhdGlvblBvbGljeSI6ImRtVnljMmx2YmoweExqQTdZWFYwYUc5eWFYcGhkR2x2Ym5KMWJHVnpJSHRqT2x0MGVYQmxQVDBpSkdsekxXUmxZblZuWjJGaWJHVWlYU0FtSmlCYmRtRnNkV1U5UFhSeWRXVmRJRDAtSUdSbGJua29LVHM5UGlCd1pYSnRhWFFvS1R0OU8ybHpjM1ZoYm1ObGNuVnNaWE1nZXlBZ0lDQmpPbHQwZVhCbFBUMGlKR2x6TFdSbFluVm5aMkZpYkdVaVhTQTlQaUJwYzNOMVpTaDBlWEJsUFNKT2IzUkVaV0oxWjJkaFlteGxJaXdnZG1Gc2RXVTlZeTUyWVd4MVpTazdJQ0FnSUdNNlczUjVjR1U5UFNJa2FYTXRaR1ZpZFdkbllXSnNaU0pkSUQwLUlHbHpjM1ZsS0hSNWNHVTlJbWx6TFdSbFluVm5aMkZpYkdVaUxDQjJZV3gxWlQxakxuWmhiSFZsS1RzZ0lDQWdZenBiZEhsd1pUMDlJaVJ6WjNndGJYSnphV2R1WlhJaVhTQTlQaUJwYzNOMVpTaDBlWEJsUFNKelozZ3RiWEp6YVdkdVpYSWlMQ0IyWVd4MVpUMWpMblpoYkhWbEtUc2dJQ0FnWXpwYmRIbHdaVDA5SWlSelozZ3RiWEpsYm1Oc1lYWmxJbDBnUFQ0Z2FYTnpkV1VvZEhsd1pUMGljMmQ0TFcxeVpXNWpiR0YyWlNJc0lIWmhiSFZsUFdNdWRtRnNkV1VwT3lBZ0lDQmpPbHQwZVhCbFBUMGlKSEJ5YjJSMVkzUXRhV1FpWFNBOVBpQnBjM04xWlNoMGVYQmxQU0p3Y205a2RXTjBMV2xrSWl3Z2RtRnNkV1U5WXk1MllXeDFaU2s3SUNBZ0lHTTZXM1I1Y0dVOVBTSWtjM1p1SWwwZ1BUNGdhWE56ZFdVb2RIbHdaVDBpYzNadUlpd2dkbUZzZFdVOVl5NTJZV3gxWlNrN0lDQWdJR002VzNSNWNHVTlQU0lrZEdWbElsMGdQVDRnYVhOemRXVW9kSGx3WlQwaWRHVmxJaXdnZG1Gc2RXVTlZeTUyWVd4MVpTazdmVHMifQ.c0l-xqGDFQ8_kCiQ0_vvmDQYG_u544CYmoiucPNxd9MU8ZXT69UD59UgSuya2yl241NoVXA_0LaMEB2re0JnTbPD_dliJn96HnIOqnxXxRh7rKbu65ECUOMWPXbyKQMZ0I3Wjhgt_XyyhfEiQGfJfGzA95-wm6yWqrmW7dMI7JkczG9ideztnr0bsw5NRsIWBXOjVy7Bg66qooTnODS_OqeQ4iaNsN-xjMElHABUxXhpBt2htbhemDU1X41o8clQgG84aEHCgkE07pR-7IL_Fn2gWuPVC66yxAp00W1ib2L-96q78D9J52HPdeDCSFio2RL7r5lOtz8YkQnjacb6xA       
```

</br>

## <a name="next-steps"></a>后续步骤

- [如何创作证明策略并对其签名](author-sign-policy.md)
- [使用 PowerShell 设置 Azure 证明](quickstart-powershell.md)
