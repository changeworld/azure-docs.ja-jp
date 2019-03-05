---
title: クイック スタート:Bing Spell Check REST API と Java を使用してスペルをチェックする
titlesuffix: Azure Cognitive Services
description: Bing Spell Check REST API を使用してスペルと文法をチェックしてみましょう。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: d2905d05dce48b705de44780425ed2b55b02555c
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888986"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>クイック スタート:Bing Spell Check REST API と Java を使用してスペルをチェックする

このクイック スタートを使用して、Bing Spell Check の REST API を呼び出してみましょう。 このシンプルな Java アプリケーションは、API に要求を送信して、一連の修正候補を返します。 このアプリケーションは Java で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このアプリケーションのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java) で入手できます。

## <a name="prerequisites"></a>前提条件

Java Development Kit(JDK) 7 以降

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-an-application"></a>アプリケーションを作成して初期化する

1. 普段使用している IDE またはエディターで新しい Java プロジェクトを作成し、以下のパッケージをインポートします。

    ```java
    import java.io.*;
    import java.net.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. API エンドポイントのホスト、パス、サブスクリプション キーの変数を作成します。 市場、スペル チェック対象のテキスト、スペル チェック モードの文字列に使用する変数を作成します。

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "ENTER YOUR KEY HERE";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>API 要求を作成して送信する

1. API 要求を作成して送信する `check()` という関数を作成します。 その中で、次の手順に従います。 要求のパラメーターの文字列を作成します。 該当する市場文字列に `?mkt=` パラメーターを、スペル チェック モードに `&mode=` パラメーターを追加します。  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
   //...
   }
   ```

2. エンドポイントのホスト、パス、パラメーターの文字列を結合して URL を作成します。 新しい `HttpsURLConnection` オブジェクトを作成します。

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) 
    ```

3. その URL への接続を開きます。 要求メソッドを `POST` に設定します。 要求のパラメーターを追加します。 `Ocp-Apim-Subscription-Key` ヘッダーには必ずサブスクリプション キーを追加してください。 

    ```java
    url.openConnection();
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. 新しい `DataOutputStream` オブジェクトを作成して API に要求を送信します。

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="read-the-response"></a>応答を読み取る

1. `BufferedReader` を作成して、API から応答を読み取ります。 それをコンソールに出力します。
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(line);
    }
    in.close();
    ```

2. アプリケーションの main 関数から、上で作成した関数を呼び出します。 

    ```java
    public static void main(String[] args) {
        try {
            check();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
    ```
    
## <a name="example-json-response"></a>JSON の応答例

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](../tutorials/spellcheck.md)

- [Bing Spell Check API とは](../overview.md)
- [Bing Spell Check API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
