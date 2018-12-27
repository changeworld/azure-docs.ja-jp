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
ms.openlocfilehash: 243ee16f8de8add8283581c8c03a37594797864b
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430036"
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
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
このエラーコードは 3 桁の HTTP ステータス コードの後に､エラーをさらに分類するための 3 桁の数字を続けた 6 桁の数字です｡ 一般的なエラー コード:

| コード | 説明 |
|:----|:-----|
| 400000| 無効な要求入力があります｡|
| 400001| "scope" パラメーターが無効です｡|
| 400002| "category" パラメーターが無効です｡|
| 400003| 言語指定子が見つからないか無効です。|
| 400004| ターゲット スクリプト指定子 ("To script") が見つからないか無効です｡|
| 400005| 入力テキストが見つからないか無効です｡|
| 400006| 言語とスクリプトの組み合わせが無効です｡|
| 400018| ソース スクリプト指定子 ("From script") が見つからないか無効です｡|
| 400019| サポートされていない言語が指定されています｡|
| 400020| 一連の入力テキストに無効な要素があります｡|
| 400021| API バージョン パラメーターが見つからないか無効です｡|
| 400023| 無効な言語ペアが指定されています｡|
| 400035| ソース言語 ("From" フィールド) が無効です｡|
| 400036| ターゲット言語 ("To" フィールド) が見つからないか無効です｡|
| 400042| 無効なオプション ("Options" フィールド) が指定されています｡|
| 400043| クライアント トレース ID (ClientTraceId フィールドか X-ClientTranceId ヘッダー) が見つからないか無効です｡|
| 400050| 入力テキストが長すぎます。|
| 400064| "translation" パラメーターが見つからないか無効です｡|
| 400070| ターゲット スクリプトの個数 (ToScript パラメーター) がターゲット言語 (To パラメーター) の個数と一致しません｡|
| 400071| TextType に対する値が無効です。|
| 400072| 一連の入力テストの要素数が多すぎます｡|
| 400073| スクリプト パラメーターが無効です｡|
| 400074| 要求の本体が有効な JSON ではありません｡|
| 400075| 言語ペアとカテゴリの組み合わせが無効です｡|
| 400077| 要求の最大サイズを超えています。|
| 400079| from 言語と to 言語間の翻訳に要求されたカスタム システムは存在しません｡|
| 401000| 資格情報が見つからないか無効なため、要求は許可されません｡|
| 401015| ｢指定された資格情報は Speech API に対するものです｡ この要求には､Text API に対する資格情報が必要です｡ Translator Text API のサブスクリプションを使用してください｡｣|
| 403000| この操作は許可されていません｡|
| 403001| サブスクリプションが無料クォータを超えているため､この操作は許可されていません｡|
| 405000| 要求リソースに対してこの要求メソッドを使用することはできません｡|
| 415000| Content-Type Content-type ヘッダーが見つからないか無効です。|
| 429000､429001､429002| クライアントから送信されている要求が多すぎるため､サーバーによって要求が拒否されました｡ スロットリングを回避するため､要求の頻度を減らしてください｡|
| 500000| 予期しないエラーが発生しました。 エラーが解決しない場合は、エラーの発生日時と応答ヘッダーの要求識別子 X-RequestID、要求ヘッダーのクライアント識別子 X-ClientTraceID を添えてその旨をご報告ください。|
| 503000| サービスが一時的に利用できません。 もう一度試してください。 エラーが解決しない場合は、エラーの発生日時と応答ヘッダーの要求識別子 X-RequestID、要求ヘッダーのクライアント識別子 X-ClientTraceID を添えてその旨をご報告ください。|

