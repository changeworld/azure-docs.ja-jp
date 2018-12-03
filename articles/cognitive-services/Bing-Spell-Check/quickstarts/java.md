---
title: 'クイック スタート: Bing Spell Check API (Java)'
titlesuffix: Azure Cognitive Services
description: Bing Spell Check API をすぐに使い始めるのに役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: aahi
ms.openlocfilehash: 90963f90d4526a6495ec9de73e10f2cd792841e0
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2018
ms.locfileid: "52306932"
---
# <a name="quickstart-for-bing-spell-check-api-with-java"></a>Bing Spell Check API のクイック スタート (Java) 

この記事では、Java で [Bing Spell Check API](https://azure.microsoft.com/services/cognitive-services/spell-check/)  を使用する方法について説明します。 Spell Check API は、認識できない単語のリストを置換候補と共に返します。 一般に、この API にテキストを送信し、テキスト内の単語を置換候補に置き換えるか、その判断をユーザー自身が行えるように置換候補をアプリケーションのユーザーに表示することになるでしょう。 この記事では、"Hollo, wrld!" というテキストを含んだ要求を送信する方法について説明します。 置換候補は "Hello" と "world" になります。

## <a name="prerequisites"></a>前提条件

このコードをコンパイルして実行するには、[JDK 7 または 8](https://aka.ms/azure-jdks) が必要です。 好みの Java IDE がある場合はそれを使用してもかまいませんが、テキスト エディターで十分です。

[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Bing Spell Check API v7** を取得している必要があります。 このクイック スタートには[無料試用版](https://azure.microsoft.com/try/cognitive-services/#lang)で十分です。 無料試用版を起動するとき、アクセス キーを入力する必要があります。または、Azure ダッシュボードの有料サブスクリプション キーを使用できます。  「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)」も参照してください。

## <a name="get-spell-check-results"></a>スペル チェックの結果を取得する

1. 適切な IDE で新しい Java プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `subscriptionKey` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```java
import java.io.*;
import java.net.*;
import javax.net.ssl.HttpsURLConnection;

public class HelloWorld {

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    // NOTE: Replace this example key with a valid subscription key.
    static String key = "ENTER KEY HERE";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";

    public static void check () throws Exception {
        String params = "?mkt=" + mkt + "&mode=" + mode;
        URL url = new URL(host + path + params);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
        connection.setRequestProperty("Content-Length", "" + text.length() + 5);
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();

        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            System.out.println(line);
        }
        in.close();
    }

    public static void main(String[] args) {
        try {
            check ();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

**応答**

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
> [Bing Spell Check のチュートリアル](../tutorials/spellcheck.md)

## <a name="see-also"></a>関連項目

- [Bing Spell Check の概要](../proof-text.md)
- [Bing Spell Check API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
