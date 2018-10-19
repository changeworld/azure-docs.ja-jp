---
title: Translator Text API V3.0 リファレンス
titlesuffix: Azure Cognitive Services
description: Translator Text API V3.0 のリファレンス ドキュメント。
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 9282d8af30cbfb3346394bcd71510faf8d8c8a21
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129388"
---
# <a name="translator-text-api-v30"></a>Translator Text API v3.0

## <a name="whats-new"></a>新機能

Translator Text API のバージョン 3 には、最新の JSON ベースの Web API が用意されています。 既存の機能をより少ない操作に統合することによって、使いやすさとパフォーマンスが向上しています。また、新機能が用意されています。

 * ある言語のテキストを、ある書記体系から別の書記体系に変換する音訳。
 * 1 つの要求での複数言語への翻訳。
 * 1 つの要求での言語の検出、翻訳、および音訳。
 * 用語の翻訳候補を検索し、逆翻訳やコンテキスト内で使用されている用語を示す例を見つけるための辞書。
 * 多くの情報が得られる言語検出結果。

## <a name="base-urls"></a>ベース URL

Text API v3.0 は、次のクラウドで利用できます。

| 説明 | リージョン | ベース URL                                        |
|-------------|--------|-------------------------------------------------|
| Azure       | グローバル | api.cognitive.microsofttranslator.com           |


## <a name="authentication"></a>Authentication

Microsoft Cognitive Services 内の Translator Text API にサブスクライブし、ユーザーの (Azure Portal で使用できる) サブスクリプション キーを使用します。 

最も簡単な方法は、要求ヘッダー `Ocp-Apim-Subscription-Key` を使用して Translator サービスに Azure の秘密鍵を渡すことです。

別の方法として、秘密鍵を使用してトークン サービスから承認トークンを取得します。 次にこの承認トークンを、`Authorization` 要求ヘッダーを使用する Translator サービスに渡します。 承認トークンを取得するには、次の URL に `POST` 要求を送信します。

| 環境     | 承認サービスの URL                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

指定の秘密鍵でトークンを取得する要求の例を次に示します。

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'
// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

要求が成功すると、応答本文内にプレーン テキストとしてエンコードされたアクセス トークンが返されます。 有効なトークンは、Authorization 内のベアラー トークンとして Translator サービスに渡されます。

```
Authorization: Bearer <Base64-access_token>
```

認証トークンは 10 分間有効です。 Translator API に対して複数の呼び出しを行う場合は、このトークンを再利用する必要があります。 ただし、プログラムで、長時間にわたって Translator API に要求を行う場合は、一定間隔 (例: 8 分ごと) でプログラムから新しいアクセス トークンを要求する必要があります。

要約すると、Translator API に対するクライアント要求には、次の表から取った 1 つの承認ヘッダーが含められます。

<table width="100%">
  <th width="30%">headers</th>
  <th>説明</th>
  <tr>
    <td>Ocp-Apim-Subscription-Key</td>
    <td>*秘密鍵を渡そうとしている場合は、Cognitive Services サブスクリプションで使用します*。<br/>値は、Translator Text API に対するユーザーのサブスクリプションの Azure 秘密鍵です。</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>*認証トークンを渡そうとしている場合は、Cognitive Services サブスクリプションで使用します。*<br/>値はベアラー トークンで、`Bearer <token>` となります。</td>
  </tr>
</table> 

## <a name="errors"></a>Errors

標準的なエラー応答は、`error` という名前の名前/値ペアを含む JSON オブジェクトです。 値は、以下のプロパティを含む JSON オブジェクトでもあります。

  * `code`: サーバー定義のエラー コード。

  * `message`: エラーを人間が読み取ることができる表現にした文字列。

たとえば、無料試用版のサブスクリプションを使用したユーザーは、無料クォータがなくなると、次のエラーを受け取ります。

```
{
  "error": {
    "code":403000,
    "message":"The subscription has exceeded its free quota."
    }
}
```
