---
title: 快速入门：使用 REST API 和 Go 获取图像见解 - 必应视觉搜索
titleSuffix: Azure Cognitive Services
description: 了解如何使用必应视觉搜索 API 和 Go 上传图像，并获取有关该图像的见解。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.openlocfilehash: baa0c66508e63fc4363fbf72f01ed7016f2a30eb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499095"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-go"></a>快速入门：使用必应视觉搜索 REST API 和 Go 获取图像见解

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

使用本快速入门来使用 Go 编程语言首次调用必应视觉搜索 API。 POST 请求可将图像上传到 API 终结点。 结果包含 URL 以及与已上传图像类似的图像的相关描述信息。

## <a name="prerequisites"></a>先决条件

* 安装 [Go 二进制文件](https://golang.org/dl/)。
* 安装用于显示结果的 go-spew 代码深度美化器。 若要安装 go-spew，请使用 `$ go get -u https://github.com/davecgh/go-spew` 命令。

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="project-and-libraries"></a>项目和库

在 IDE 或编辑器中创建一个 Go 项目。 然后，导入用于请求的 `net/http`，导入 `ioutil` 来读取响应，导入 `encoding/json` 来处理结果的 JSON 文本。 使用 `go-spew` 库来分析 JSON 结果。

```go
package main

import (
    "bytes"
    "io"
    "fmt"
    "io/ioutil"
    "mime/multipart"
    "net/http"
    "os"
    "path/filepath"
    "encoding/json"
    "github.com/davecgh/go-spew/spew"
)

```

## <a name="struct-to-format-results"></a>用于设置结果格式的结构

`BingAnswer` 结构用于设置 JSON 响应中返回的数据的格式。此类数据分为多个层次并很复杂。 以下实现涵盖了部分基础知识：

```go
type BingAnswer struct {
    Type         string `json:"_type"`
    Instrumentation struct {
        Type string   `json:"_type"`
        PingUrlBase   string  `json:"_pingUrlBase"`
        PageLoadPingUrl  string `json:"_pageLoadPingUrl"`
    } `json:"instrumentation"`
    Tags []struct  {
        DisplayName       string    `json:"displayName"`
        Actions                 []struct {
            Type      string `json:"_type"`
            ActionType    string    `json:"actionType"`
            Data             struct {
                Value     []struct {
                    WebSearchUrl          string `json:"webSearchUrl"`
                    Name        string  `json:"name"`
                }`json:"value"`
                ImageInsightsToken string `json:"imageInsightsToken"`
                InsightsMetadata struct {
                    PagesIncludingCount int `json:"pagesIncludingCount"`
                    AvailableSizesCount  int  `json:"availableSizesCount"`
                }
                ImageId string  `json:"imageId"`
                AccentColor  string `json:"accentColor"`
            } `json:"data"`
        } `json:"actions"`
    } `json:"tags"`
    Image struct {
        WebSearchUrl   string  `json:"webSearchUrl"`
        WebSearchUrlPingSuffix  string `json:"webSearchUrlPingSuffix"`
        Name   string   `json:"name"`
        IsFamilyFriendly   bool  `json:"isFamilyFriendly"`
        ContentSize   string   `json:"contentSize"`
        EncodingFormat    string   `json:"encodingFormat"`
        HostPageDisplayUrl   string    `json:"hostPageDisplayUrl"`
        Width     int   `json:"width"`
        Height    int    `json:"height"`
        Thumbnail    struct  {
            Width   int   `json:"width"`
            Height  int   `json:"height"`
        }
        InsightsMetadata  struct {
            PagesIncludingCount   int   `json:"pagesIncludingCount"`
            AvailableSizesCount    int    `json:"availableSizesCount"`
        }
        AccentColor   string     `json:"accentColor"`
        VisualWords   string   `json:"visualWords"`
    } `json:"image"`
}

```

## <a name="main-function-and-variables"></a>Main 函数和变量  

以下代码声明主函数并指定必需的变量： 

1. 确认终结点正确并将 `token` 值替换为来自你的 Azure 帐户的有效订阅密钥。 
2. 对于 `batchNumber`，请分配 POST 数据的前导和尾随边界所需的 GUID。 
3. 对于 `fileName`，请分配要用于 POST 的映像文件。 
4. 对于 `endpoint`，你可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

```go
func main() {
    // Verify the endpoint URI and replace the token string with a valid subscription key.se
    endpoint := "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch"
    token := "YOUR-ACCESS-KEY"
    client := &http.Client{}
    batchNumber := "d7ecc447-912f-413e-961d-a83021f1775f"
    fileName := "ElectricBike.JPG"

    body, contentType := createRequestBody(fileName, batchNumber)
    
    req, err := http.NewRequest("POST", endpoint, body)
    req.Header.Add("Content-Type", contentType)
    req.Header.Set("Ocp-Apim-Subscription-Key", token)
    
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }
    
       defer resp.Body.Close()
    
    resbody, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    //fmt.Print(string(resbody))
    
    // Create a new answer.  
    ans := new(BingAnswer)
    err = json.Unmarshal(resbody, &ans)
    if err != nil {
        fmt.Println(err)
    }
    
    fmt.Print("Output of BingAnswer: \r\n\r\n")
    
    // Iterate over search results and print the result name and URL.
    for _, result := range ans.Tags {
        spew.Dump(result)
    }
}

```

## <a name="boundaries-of-post-body"></a>POST 正文的边界

对视觉搜索终结点的 POST 请求要求使用前导和尾随边界来封闭 POST 数据。 这些函数不包括在 `main()` 块中。

前导边界包括批号、内容类型标识符 `Content-Disposition: form-data; name="image"; filename=`，以及要 POST 的图像的文件名。 

尾随边界仅包括批号。 


```go
func BuildFormDataStart(batNum string, fileName string) string{

    startBoundary := "--batch_" + batNum + "\r\n"
    requestBoundary := startBoundary + "\r\n" + "Content-Disposition: form-data; name=\"image\"; filename=" + fileName + "\r\n\r\n"
    return requestBoundary
}

func BuildFormDataEnd(batNum string) string{

    return "\r\n" + "\r\n" + "--batch_" + batNum + "\r\n"

}

```
## <a name="add-image-bytes-to-post-body"></a>向 POST 正文添加图像字节

下面的代码创建包含图像数据的 POST 请求：

```go
func createRequestBody(fileName string, batchNumber string) (*bytes.Buffer, string) {
    file, err := os.Open(fileName)
    if err != nil {
        panic(err)
    }
    defer file.Close()

    body := &bytes.Buffer{}
    writer := multipart.NewWriter(body)
    
    writer.SetBoundary(BuildFormDataStart(batchNumber, fileName))
    
    part, err := writer.CreateFormFile("image", filepath.Base(file.Name()))
    if err != nil {
        panic(err)
    }
    io.Copy(part, file)
    writer.WriteField("lastpart", BuildFormDataEnd(batchNumber))    
    writer.Close()
    return body, writer.FormDataContentType()
}

```

## <a name="send-the-request"></a>发送请求

以下代码发送请求并读取结果：

```go
resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }
    
       defer resp.Body.Close()
    
    resbody, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

```

## <a name="handle-the-response"></a>处理响应

`Unmarshall` 函数从视觉搜索 API 返回的 JSON 文本提取信息。 `go-spew` 代码美化器显示结果。

```go
    // Create a new answer.  
    ans := new(BingAnswer)
    err = json.Unmarshal(resbody, &ans)
    if err != nil {
        fmt.Println(err)
    }
    
    fmt.Print("Output of BingAnswer: \r\n\r\n")
    
    // Iterate over search results and print the result name and URL.
    for _, result := range ans.Tags {
        spew.Dump(result)
    }

```
> [!NOTE]
> 此示例的代码由 Francesco Giordano 提供。

## <a name="results"></a>结果

结果标识与 POST 正文中包含的图像类似的图像。 有用的字段为 `WebSearchUrl` 和 `Name`。

```go
    Value: ([]struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) (len=66 cap=94) {
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=B9E6621161769D578A9E4DD9FD742128DE65225A&simid=608046863828453626",
      Name: (string) (len=87) "26\"Foldable Electric Mountain Bike Bicycle Ebike W/Lithium Battery 250W 36V MY8L | eBay"
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=5554757348A9E88E9EEAB74217E228689E716B61&simid=607988237543409883",
      Name: (string) (len=96) "Best 25+ Electric mountain bike ideas on Pinterest | Mountain bicycle, Mtb mountain bike and ..."
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=B56F626A4803D829FE326842E2B97CE5B87F17F2&simid=607991699288949513",
      Name: (string) (len=100) "Electric Fat Bike Mountain Bicycle Snow Bike Cruiser Ebike Motor Lithium Battery Dual Oil Brakes ..."
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=CCAE400EE510DCA6F5740ABAF08A5310B4EF0698&simid=608003854048890458",
      Name: (string) (len=81) "Best Mountain Bikes Under 1500 (Outstanding Performance) | Mountain Bicycle World"
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=1244730E2E3F2D5DFBED9A9CFE1DBE27B09F08BC&simid=608005181199745622",
      Name: (string) (len=98) "ANCHEER Folding Electric Mountain Bike with 26\" Super Lightweight Magnesium (36V 250W) - GearScoot"
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=38AAD876E57BB0D502FBDD5B1CB29EB7EB8DD9E2&simid=608041654062220328",
      Name: (string) (len=97) "29 best CB Bikes Gadgets & Accessories images on Pinterest | Bicycles, Bicycling and Bike gadgets"
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=6892C8FD22D0E42911C99AFD8FE1FD37BD82E02C&simid=608052803759703185",
      Name: (string) (len=98) "ANCHEER Folding Electric Mountain Bike with 26\" Super Lightweight Magnesium (36V 250W) - GearScoot"
     },

```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [什么是必应视觉搜索 API？](../overview.md)
> [以 Go 编写的必应 Web 搜索快速入门](../../Bing-Web-Search/quickstarts/go.md)