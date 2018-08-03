---
title: Bing Visual Search API の JavaScript クイック スタート | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Bing Visual Search API にイメージをアップロードし、そのイメージに関する分析情報を取得する方法を示します。
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 60b1dc9b8ea9eda258e9776b8967df38c97d964e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071705"
---
# <a name="your-first-bing-visual-search-query-in-javascript"></a>JavaScript での最初の Bing Visual Search クエリ

Bing Visual Search API は、提供されたイメージに関する情報を返します。 イメージを提供するには、イメージの URL または分析情報トークンを使用するか、イメージをアップロードします。 これらのオプションの詳細については、「[Bing Visual Search API とは](../overview.md)」を参照してください。 この記事では、イメージのアップロードについて説明します。 よく知られているランドマークの写真を撮影し、それに関する情報を取得するモバイル シナリオで、イメージのアップロードは便利であると考えられます。 たとえば、分析情報にはランドマークに関する雑学が含まれることがあります。 

ローカル イメージをアップロードする場合は、次に示すフォーム データを POST の本文に含める必要があります。 フォーム データには Content-Disposition ヘッダーが含まれる必要があります。 その `name` パラメーターには "image" を設定する必要があり、`filename` パラメーターには任意の文字列を設定できます。 フォームの内容は、イメージのバイナリです。 アップロードできるイメージの最大サイズは、1 MB です。 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

この記事では、Bing Visual Search API 要求を送信し、JSON 検索結果を表示するシンプルなコンソール アプリケーションを紹介します。 このアプリケーションは JavaScript で記述されていますが、API は、HTTP 要求の発行と JSON の解析が可能な任意のプログラミング言語と互換性がある RESTful Web サービスです。 

## <a name="prerequisites"></a>前提条件

このコードを実行するには [Node.js 6](https://nodejs.org/en/download/) が必要です。

このクイック スタートでは、[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)サブスクリプション キーまたは有料サブスクリプション キーを使用できます。

## <a name="running-the-application"></a>アプリケーションの実行

次のコードは、Node.js 内の FormData を使用してメッセージを送信する方法を示しています。

このアプリケーションを実行するには、次の手順に従います。

1. プロジェクト用のフォルダーを作成します (または、お気に入りの IDE やエディターを使用)。
2. コマンド プロンプトまたはターミナルから、先ほど作成したフォルダーに移動します。
3. request モジュールをインストールします。  
  ```  
  npm install request  
  ```  
3. form-data モジュールをインストールします。  
  ```  
  npm install form-data  
  ```  
4. GetVisualInsights.js という名前のファイルを作成し、次のコードを追加します。
5. `subscriptionKey` 値を、サブスクリプション キーに置き換えます。
6. `imagePath` 値を、アップロードするイメージのパスに置き換えます。
7. プログラムを実行します。  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');
var fs = require('fs');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';
var imagePath = "<pathtoyourimagegoeshere>";

var form = new FormData();
form.append("image", fs.createReadStream(imagePath));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


## <a name="next-steps"></a>次の手順

[分析情報トークンを使用してイメージに関する分析情報を取得する](../use-insights-token.md)  
[Bing Visual Search 画像アップロードのチュートリアル](../tutorial-visual-search-image-upload.md)
[Bing Visual Search シングルページ アプリのチュートリアル](../tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual Search の概要](../overview.md)  
[試してみる](https://aka.ms/bingvisualsearchtryforfree)  
[無料試用版のアクセス キーを入手する](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Search API のリファレンス](https://aka.ms/bingvisualsearchreferencedoc)
