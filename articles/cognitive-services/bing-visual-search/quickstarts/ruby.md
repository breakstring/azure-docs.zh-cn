---
title: 快速入门：使用 REST API 和 Ruby 获取图像见解 - 必应视觉搜索
titleSuffix: Azure Cognitive Services
description: 了解如何使用必应视觉搜索 API 和 Ruby 上传图像，并获取有关该图像的见解。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.openlocfilehash: d907e10484b1873974ec9707d0aec1a89ab9eb50
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499020"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-ruby"></a>快速入门：使用必应视觉搜索 REST API 和 Ruby 获取图像见解

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

使用本快速入门来使用 Ruby 编程语言首次调用必应视觉搜索 API。 POST 请求可将图像上传到 API 终结点。 结果包含 URL 以及与已上传图像类似的图像的相关描述信息。

## <a name="prerequisites"></a>先决条件

* 请安装 [Ruby 2.4 或更高版本](https://www.ruby-lang.org/en/downloads/)。
* 获取订阅密钥。

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="project-and-required-modules"></a>项目和所需的模块

在 IDE 或编辑器中新建一个 Ruby 项目。 导入 `net/http`、`uri` 和 `json` 以处理结果中的 JSON 文本。 导入用于对文件名字符串进行编码的 `base64` 库。 

```ruby
require 'net/https'
require 'uri'
require 'json'
require 'base64'
```

## <a name="define-variables"></a>定义变量

以下代码声明主函数并指定必需的变量： 

1. 确认终结点正确并将 `accessKey` 值替换为来自你的 Azure 帐户的有效订阅密钥。 
2. 对于 `batchNumber`，请分配 POST 数据的前导和尾随边界所需的 GUID。 
3. 对于 `fileName`，请分配要用于 POST 的映像文件。 
4. 使用 `if` 块测试订阅密钥是否有效。

```ruby
accessKey = "ACCESS-KEY"
uri  = "https://api.cognitive.microsoft.com"
path = "/bing/v7.0/images/visualsearch"
batchNumber = "dc05c75a-6b5e-4059-b556-0b7c079819a5"
fileName = "red-dress.jpg"

if accessKey.length != 32 then
    puts "Invalid Bing Search API subscription key!"
    puts "Please paste yours into the source code."
    abort
end

```

## <a name="form-data-for-post-request"></a>构成 POST 请求的数据

1. 通过前导和尾随边界，将 POST 的图像数据封装。 以下函数设置边界：

   ```ruby
   def BuildFormDataStart(batNum, fileName)
       startBoundary = "--batch_" + batNum
       return startBoundary + "\r\n" + "Content-Disposition: form-data; name=\"image\"; filename=" + "\"" + fileName + "\"" + "\r\n\r\n"    
   end

   def BuildFormDataEnd(batNum)
       return "\r\n\r\n" + "--batch_" + batNum + "--" + "\r\n"
   end
   ```

2. 构造终结点 URI，并构造一个数组以包含 POST 正文。 使用上面的函数将起始边界载入该数组。 将图像文件读入数组，然后将结束边界读入数组。

   ```ruby
   uri = URI(uri + path)
   print uri
   print "\r\n\r\n"

   post_body = []

   post_body << BuildFormDataStart(batchNumber, fileName)

   post_body << File.read(fileName) #Base64.encode64(File.read(fileName))

   post_body << BuildFormDataEnd(batchNumber)
   ```

## <a name="create-the-http-request"></a>创建 HTTP 请求

设置 `Ocp-Apim-Subscription-Key` 标头。 创建请求，然后分配标头和内容类型。 将前面创建的 POST 正文加入到请求。

```ruby
header = {'Ocp-Apim-Subscription-Key': accessKey}
request = Net::HTTP::Post.new(uri)  # , 'ImageKnowledge' => 'ImageKnowledge'

request['Ocp-Apim-Subscription-Key'] = accessKey
request.content_type = "multipart/form-data; boundary=batch_" + batchNumber  
request.body = post_body.join

```

## <a name="request-and-response"></a>请求和响应

Ruby 使用以下代码发送请求并获取响应：

```ruby
response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
   http.request(request)
end

```

## <a name="print-the-results"></a>传输结果

输出响应的标头，并使用 JSON 库设置输出格式：

```ruby
puts "\nRelevant Headers:\n\n"
response.each_header do |key, value|
    if key.start_with?("bingapis-") or key.start_with?("x-msedge-") then
        puts key + ": " + value
    end
end

puts "\nJSON Response:\n\n"
puts JSON::pretty_generate(JSON(response.body))

```

## <a name="json-response"></a>JSON 响应

以下 JSON 是输出的片段：

```JSON
Relevant Headers:

bingapis-traceid: 6E19E78D4FEC4A61AB4F85977EEDB8E6
x-msedge-clientid: 3928F9869390668A304CF49792DC6746
x-msedge-ref: Ref A: 6E19E78D4FEC4A61AB4F85977EEDB8E6 Ref B: BY3EDGE0310 Ref C: 2019-02-28T19:25:12Z

JSON Response:

{
  "_type": "ImageKnowledge",
  "instrumentation": {
    "_type": "ResponseInstrumentation",
    "pingUrlBase": "https://www.bingapis.com/api/ping?IG=3F6A656574B24F81A553485B4B3244EF&CID=3928F9869390668A304CF49792DC6746&ID=",
    "pageLoadPingUrl": "https://www.bingapis.com/api/ping/pageload?IG=3F6A656574B24F81A553485B4B3244EF&CID=3928F9869390668A304CF49792DC6746&Type=Event.CPT&DATA=0"
  },
  "tags": [
    {
      "displayName": "",
      "actions": [
        {
          "_type": "ImageModuleAction",
          "actionType": "PagesIncluding",
          "data": {
            "value": [
              {
                "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=6988549F8C0C0688972CCC9F6A5E5F70F17D6F1E&simid=608025573664556965",
                "webSearchUrlPingSuffix": "DevEx,5044.1",
                "name": "Peanut Butter & Banana Training Treats. | Loved By A Collie",
                "thumbnailUrl": "https://tse2.mm.bing.net/th?id=OIP.FyoKOORtLFGzzanAVfPt4QAAAA&pid=Api",
                "datePublished": "2014-02-04T12:00:00.0000000Z",
                "isFamilyFriendly": true,
                "contentUrl": "https://lovedbyacollie.lifeseven.com/wp-content/uploads/2014/02/PBBcookies.png",
                "contentUrlPingSuffix": "DevEx,5046.1",
                "hostPageUrl": "https://lovedbyacollie.lifeseven.com/2014/02/04/peanut-butter-banana-training-treats/",
                "hostPageUrlPingSuffix": "DevEx,5045.1",
                "contentSize": "197552 B",
                "encodingFormat": "png",
                "hostPageDisplayUrl": "lovedbyacollie.lifeseven.com/2014/02/04/peanut-butter-banana...",
                "width": 300,
                "height": 409,
                "hostPageFavIconUrl": "https://www.bing.com/th?id=AR_2ceefde49d9f981b6cab9e9bd0e6693b&pid=Api",
                "thumbnail": {
                  "width": 300,
                  "height": 409
                },
                "imageInsightsToken": "ccid_FyoKOORt*mid_6988549F8C0C0688972CCC9F6A5E5F70F17D6F1E*simid_608025573664556965*thid_OIP.FyoKOORtLFGzzanAVfPt4QAAAA",
                "insightsMetadata": {
                  "pagesIncludingCount": 2,
                  "availableSizesCount": 2
                },
                "imageId": "6988549F8C0C0688972CCC9F6A5E5F70F17D6F1E",
                "accentColor": "8A6E41"
              },
              {
                "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=B5B5C37666FB3DB326209F358A1AC9E7B0F484EB&simid=607993125239130030",
                "webSearchUrlPingSuffix": "DevEx,5050.1",
                "name": "RPG Maker Icons favourites by Leon-Murayami on DeviantArt",
                "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OIP.vzeNdrscXihmLeTdDe6G6AHaKL&pid=Api",
                "datePublished": "2019-01-22T06:59:00.0000000Z",
                "isFamilyFriendly": true,
                "contentUrl": "https://orig00.deviantart.net/753e/f/2018/147/5/0/signs_metal_rusted_by_sarahyt-dccnku0.png",
                "contentUrlPingSuffix": "DevEx,5052.1",
                "hostPageUrl": "https://www.deviantart.com/leon-murayami/favourites/71077691/RPG-Maker-Icons",
                "hostPageUrlPingSuffix": "DevEx,5051.1",
                "contentSize": "569772 B",
                "encodingFormat": "png",
                "hostPageDisplayUrl": "https://www.deviantart.com/leon-murayami/favourites/71077691/RPG...",
                "width": 768,
                "height": 1056,
                "hostPageFavIconUrl": "https://www.bing.com/th?id=AR_b246060a4abab0d7111f5aa733205f06&pid=Api",
                "thumbnail": {
                  "width": 474,
                  "height": 651
                },
                "imageInsightsToken": "ccid_vzeNdrsc*mid_B5B5C37666FB3DB326209F358A1AC9E7B0F484EB*simid_607993125239130030*thid_OIP.vzeNdrscXihmLeTdDe6G6AHaKL",
                "insightsMetadata": {
                  "pagesIncludingCount": 3,
                  "availableSizesCount": 3
                },
                "imageId": "B5B5C37666FB3DB326209F358A1AC9E7B0F484EB",
                "accentColor": "936C38"
              }
            ]
          }
        },
        {
          "image": {
            "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO",
            "webSearchUrlPingSuffix": "DevEx,5057.1",
            "name": "",
            "isFamilyFriendly": true,
            "contentSize": "572 B",
            "encodingFormat": "jpeg",
            "hostPageDisplayUrl": "",
            "width": 583,
            "height": 583,
            "thumbnail": {
              "width": 0,
              "height": 0
            },
            "visualWords": "0bfcc afa3e ac572 0aa3e b5daf 12d1a aabab 620ad 0c081 0a5a6 0c1d6 5ebfe ac579 ab7a3 0bff6 ab1eb b1fa0 61970 ac57d 0bfcf 5a8f00bf60e71d1c15a4c1586622845a8f22db5d2db98f058c6d7a112f5c17363ab964ac3f4f058b622795a8335a8f30bfcb"
          },
          "actionType": "MoreSizes"
        },
        {
          "_type": "ImageModuleAction",
          "actionType": "VisualSearch",
          "data": {
            "value": [
              {
                "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=21423B0D286BB2FCFB3B7090D59EA6F8A54AB7CB&simid=608005928522679185",
                "webSearchUrlPingSuffix": "DevEx,5064.1",
                "name": "Bemz review of IKEA Söderhamn sofa - Bemz",
                "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OIP.v0zvOeP8dnNFPWQNSu80hwAAAA&pid=Api",
                "datePublished": "2017-09-08T02:27:00.0000000Z",
                "isFamilyFriendly": true,
                "contentUrl": "https://bemz.scene7.com/is/image/Bemz?obj=masks&src=ir%7bBemzRender%2fsq50v2%3f%26src%3dDG2130%26rs%3dQ5%26sharpen%3d1%26res%3d20%26wid%3d2400%26hei%3d2400%7d&resmode=sharp2&op_usm=1,1,8&wid=280&hei=280&scl=5",
                "contentUrlPingSuffix": "DevEx,5066.1",
                "hostPageUrl": "https://bemz.com/inspiration/popular-products/i-love-my-soderhamn/",
                "hostPageUrlPingSuffix": "DevEx,5065.1",
                "contentSize": "3000 B",
                "encodingFormat": "jpeg",
                "hostPageDisplayUrl": "bemz.com/inspiration/popular-products/i-love-my-soderhamn",
                "width": 280,
                "height": 280,
                "thumbnail": {
                  "width": 280,
                  "height": 280
                },
                "imageInsightsToken": "ccid_v0zvOeP8*mid_21423B0D286BB2FCFB3B7090D59EA6F8A54AB7CB*simid_608005928522679185*thid_OIP.v0zvOeP8dnNFPWQNSu80hwAAAA",
                "insightsMetadata": {
                  "pagesIncludingCount": 1,
                  "availableSizesCount": 1
                },
                "imageId": "21423B0D286BB2FCFB3B7090D59EA6F8A54AB7CB",
                "accentColor": "963555"
              },

              . . .

```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [什么是必应视觉搜索 API？](../overview.md)
> [构建视觉搜索单页 Web 应用](../tutorial-bing-visual-search-single-page-app.md)