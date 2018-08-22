---
title: Azure Video Indexer API v1 から v2 に移行する | Microsoft Docs
description: このトピックでは、Azure Video Indexer API v1 から v2 への移行方法について説明します。
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: b1737960a4142f5c0d949ce8c2524c34fe9cd79e
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "40187378"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>Azure Video Indexer API v1 から v2 に移行する

> [!Note]
> Video Indexer V1 API は 2018 年 8 月 1 日に非推奨になりました。 今後は Video Indexer v2 API を使用してください。 <br/>Video Indexer v2 API を使用して開発する場合は、[こちら](https://api-portal.videoindexer.ai/)の手順を参照してください。 

この記事では、v2 で導入された変更について説明します。  

## <a name="api-changes"></a>API の変更

### <a name="authorization-and-operations"></a>Authorization と Operations

v2 バージョンの Video Indexer では、API の認証と承認のモデルが変更されています。 2 つの API のセットがあります。 

* Authorization 
* [操作]

**Authorization** API は、**Operations** API を呼び出すためのアクセス トークンの取得に使用されます。 **Operations** API には、Upload video、Get insights などの操作の Video Indexer API がすべて含まれます。

**Authorization** API に[サブスクライブ](video-indexer-get-started.md)すると、(v1 で行っていたのと同様に) サブスクリプション キーを渡すことでアクセス トークンを取得できるようになります。

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>Operations API 用のアクセス トークンの取得と使用

今後は、**Operations** API を呼び出すときにサブスクリプション キーが使用されなくなります。 その代わりに、**Authorization** API によって取得されたアクセス トークンを渡します。 

各要求に、呼び出す API のアクセス レベルと一致した有効なトークンが必要です。 たとえば、ユーザーに対する操作 (アカウントの取得など) には、ユーザー アクセス トークンが必要です。 アカウント レベルに対する操作 (すべてのビデオの一覧表示など) には、アカウント アクセス トークンが必要です。 ビデオに対する操作 (ビデオのインデックス再作成など) には、アカウント アクセス トークンまたはビデオ アクセス トークンが必要です。

わかりやすくするために、最初にユーザー トークン取得せずに、**Authorization** API の **GetAccounts** を使用してアカウントを取得することができます。 有効なトークンを持つアカウントの取得を要求することもできます。これで、アカウント トークンを取得する追加の呼び出しをスキップすることができます。

さまざまなアクセス トークンの詳細については、「[Azure Video Indexer API を使用する](video-indexer-use-apis.md)」を参照してください。

### <a name="locations"></a>場所

各 API 呼び出しには、Video Indexer アカウントの場所が含まれている必要があります。 場所のない API 呼び出しや、誤った場所を含んだ API 呼び出しは失敗します。

次の表で説明する値が適用されます。 **パラメーター値**は、API を使用するときに渡す値です。

|**名前**|**パラメーター値**|**説明**|
|---|---|---|
|試用版|試用|試用アカウントに使用されます。 たとえば、「 https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English」のように入力します。|
|米国西部|westus2|Azure 米国西部 2 リージョンに使用されます。  たとえば、「 https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English」のように入力します。|
|北ヨーロッパ |northeurope|Azure 北ヨーロッパ リージョンに使用されます。 たとえば、「https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English」のように入力します。 |
|東アジア|eastasia|Azure 東アジア リージョンに使用されます。 たとえば、「https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English」のように入力します。|

### <a name="data-model"></a>データ モデル

Video Indexer のデータ モデルが簡素化され、よりわかりやすい分析情報が提供されるようになりました。 v2 API によって生成される出力の詳細については、「[v2 API によって生成される Video Indexer の出力を調べる](video-indexer-output-json-v2.md)」を参照してください。

### <a name="swagger"></a>Swagger

それに応じて、Video Indexer API の定義も更新されました。これは [API ポータル](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token)からダウンロードできます。


### <a name="v1-vs-v2-examples"></a>V1 と V2 の例

新しい V2 API には、3 つの主要なパラメーターが関係しています。

1. [LOCATION] - 前述のとおりです。 値は trial、westus2、northeurope、eastasia のいずれかです。
2. [YOUR_ACCOUNT_ID] - アカウントの GUID ID。 すべてのアカウントを取得するときに取得されます (以下で説明します)。
3. [YOUR_VIDEO_ID] - ビデオの ID ("d4fa369abc" など)。 ビデオのアップロード時またはビデオの検索時に返されます。

#### <a name="uploading-a-video-in-v1"></a>V1 でのビデオのアップロード:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>V2 でのビデオのアップロード:

1. アップロード要求のためのアクセス トークンを取得します。

  すべてのアカウントとそのアクセス トークンを取得します。

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  または、特定のアカウント アクセス トークンを取得します。
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. ビデオのアップロード:

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>V1 での分析情報の取得:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>V2 での分析情報の取得:

1. アカウント アクセス トークンを使用するか、ビデオ レベルのアクセス トークンを取得します。

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. 分析情報の取得:

  ```
  https://api.videoindexer.ai/[LOCATION]/Accounts[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>V1 でのビデオ処理状態の取得:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>V2 でのビデオ処理状態の取得:

API v2 では、処理状態は Get Video Index API の一部として返されます。

1. アカウント アクセス トークンを使用するか、ビデオ レベルのアクセス トークンを取得します。

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. 分析情報の取得:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>次の手順

[Azure Video Indexer API を使用する](video-indexer-use-apis.md)

