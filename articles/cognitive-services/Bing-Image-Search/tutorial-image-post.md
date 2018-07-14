---
title: Bing 画像をアップロードして分析情報を取得する | Microsoft Docs
description: Bing Image Search API を使用して画像をアップロードし、画像の分析情報を取得するコンソール アプリケーションです。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 12/07/2017
ms.author: v-gedod
ms.openlocfilehash: f0bf32a9951527a072fffe464f6b5f50d0f237a2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372840"
---
# <a name="tutorial-bing-image-upload-for-insights"></a>チュートリアル: Bing 画像をアップロードして分析情報を取得する

Bing Image Search API には、画像をアップロードし、画像に関連する情報を検索する `POST` オプションが用意されています。 この C# コンソール アプリケーションは、Image Search エンドポイントを使用して画像をアップロードし、画像の詳細を取得します。
その結果を簡潔に示すと、次のような JSON オブジェクトになります。

![[JSON の結果]](media/cognitive-services-bing-images-api/jsonResult.jpg)

このチュートリアルでは、次の方法について説明します。

> [!div class="checklist"]
> * `POST` 要求で Image Search `/details` エンドポイントを使用する
> * 要求のヘッダーを指定する
> * URL パラメーターを使用して結果を指定する
> * 画像データをアップロードし、`POST` 要求を送信する
> * コンソールに JSON の結果を出力する

## <a name="app-components"></a>アプリのコンポーネント

チュートリアル アプリケーションには、次の 3 つの部分が含まれます。

> [!div class="checklist"]
> * Bing Image Search エンドポイントと必要なヘッダーを指定するエンドポイント構成
> * エンドポイントに対する `POST` 要求のための画像ファイルのアップロード
> * `POST` 要求から返される詳細である JSON の結果の解析

## <a name="scenario-overview"></a>シナリオの概要
[3 つの Image Search エンドポイント](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint)があります。 `/details` エンドポイントは、要求の本文に画像データを含む `POST` 要求を使用できます。
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
`/details?` に続く `modules` URL パラメーターは、結果に含まれる詳細の種類を指定します。
* `modules=All`
* `modules=RecognizedEntities` (画像に表示されている人または場所)

`POST` 要求で `modules=All` を指定して、次の一覧を含む JSON テキストを取得します。
* `bestRepresentativeQuery` - アップロードされた画像に似ている画像を返す Bing クエリ
* `detectedObjects` - 画像内の境界ボックスやホット スポットなど
* `image` メタデータ
* `imageInsightsToken` - `RecognizedEntities` (画像に表示されている人または場所) を取得する後続の `GET` 要求のトークン 
* `imageTags`
* `pagesIncluding` - 画像を含む Web ページ
* `relatedSearches`
* `visuallySimilarImages`

`POST` 要求で `modules=RecognizedEntities` を指定して、後続の `GET` 要求で使用される `imageInsightsToken` のみを取得します。 画像に表示されている人または場所を識別します。

## <a name="webclient-and-headers-for-the-post-request"></a>POST 要求の WebClient およびヘッダー
`WebClient` オブジェクトを作成し、ヘッダーを設定します。 Bing Search API に対するすべての要求に `Ocp-Apim-Subscription-Key` が必要です。 画像をアップロードする `POST` 要求では、`ContentType: multipart/form-data` を指定する必要もあります。

```
            WebClient client = new WebClient();
            client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            client.Headers["ContentType"] = "multipart/form-data"; 
```

## <a name="upload-the-image-and-get-results"></a>画像をアップロードし、結果を取得する

`WebClient` クラスには、`POST` 要求のデータを書式設定する `UpLoadFile` メソッドが含まれています。 `RequestStream` を書式設定し、`HttpWebRequest` を呼び出して、複雑さを軽減します。
`/details` エンドポイントおよびアップロードする画像ファイルを指定して `WebClient.UpLoadFile` を呼び出します。 応答は、JSON に簡単に変換できるバイナリ データです。 

JSON テキストを使用して `SearchResult` 構造のインスタンスを初期化します (コンテキストについては、[アプリケーションのソース コード](tutorial-image-post-source.md)をご覧ください)。
```        
         const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";

        // The image to upload. Replace with your file and path.
        const string imageFile = "ansel-adams-tetons-snake-river.jpg";
            
        byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
        var json = System.Text.Encoding.Default.GetString(resp);

        // Create result object for return
        var searchResult = new SearchResult()
        {
            jsonResult = json,
            relevantHeaders = new Dictionary<String, String>()
        };
```

## <a name="print-the-results"></a>結果を出力する
コードの残りの部分では、JSON の結果を解析し、コンソールに出力します。

```
        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
```
## <a name="get-request-using-the-imageinsightstoken"></a>ImageInsightsToken を使用した GET 要求
`POST` の結果と共に返された `ImageInsightsToken` を使用するには、次のような `GET` 要求を作成します。
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```
画像内に識別可能な人物または場所が含まれる場合、この要求はそれらに関する情報を返します。
[クイック スタート](https://docs.microsoft.com/azure/cognitive-services/bing-image-search)には、多数のコード例が含まれています。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Image Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

