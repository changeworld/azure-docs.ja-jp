---
title: スキルのリセット (api-version=2019-050-06-Preview)
titleSuffix: Azure Cognitive Search
description: スキルセットの全体または部分的な再処理が必要な場合にインクリメンタル エンリッチメントに使用されるプレビュー REST API。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4557e966c431eca31aa7577bebc75caae9365dc9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832151"
---
# <a name="reset-skills-api-version2019-05-06-preview"></a>スキルのリセット (api-version=2019-05-06-Preview)

> [!IMPORTANT] 
> インクリメンタル エンリッチメントは現在、パブリック プレビューの段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 [REST API バージョン 2019-05-06-Preview](search-api-preview.md) でこの機能を提供します。 現時点で、ポータルまたは .NET SDK はサポートされていません。

**スキルのリセット**要求は、次のインデクサー実行時に処理するスキルを指定します。 キャッシュが有効になっているインデクサーでは、インデクサーが検出できないスキル更新の処理を明示的に要求できます。 たとえば、カスタム スキルのリビジョンなどの外部の変更を行う場合は、この API を使用してスキルを再実行できます。 ナレッジ ストアや検索インデックスなどの出力は、キャッシュにある再利用可能なデータと更新されたスキルに基づく新しいコンテンツを使用して更新されます。

HTTP PUT を使用して、既存の[スキルセット](https://docs.microsoft.com/rest/api/searchservice/create-skillset)をリセットすることができます。これには、要求 URI で更新するスキルセットの名前を指定します。 要求で **api-version=2019-05-06-Preview** を使用する必要があります。

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```  

または、POST を使用し、要求の本文にインデクサー名を入力します。

```http
POST https://[service name].search.windows.net/indexers/resetskills?api-version=[api-version]  
api-key: [admin key]  
```  

## <a name="request-headers"></a>要求ヘッダー  

 次の表では、必須と省略可能の要求ヘッダーについて説明します。  

|要求ヘッダー|[説明]|  
|--------------------|-----------------|  
|*Content-Type:*|必須。 これを `application/json`|  
|*api-key:*|必須。 `api-key` は Search サービスに対する要求の認証に使用されます。 これはサービスに固有の文字列値です。 **スキルセットのリセット**要求の `api-key` ヘッダーには (クエリ キーではなく) 管理者キーを設定する必要があります。|  

要求 URL を作成するために、サービス名も必要です。 サービス名と `api-key` はどちらも、Azure portal のサービスの [概要] ページから取得できます。 ページのナビゲーション ヘルプについては、「[Azure Cognitive Search サービスを作成する](https://docs.microsoft.com/azure/search/search-create-service-portal)」を参照してください。  

## <a name="request-body-syntax"></a>要求本文の構文  

要求の本文はスキル名の配列です。

```json
{   
    "skillNames" : ["<SKILL-1>", "SKILL-n"]
}  
```

## <a name="response"></a>Response  

状態コード: 成功応答の場合は、204 No Content。 

## <a name="see-also"></a>参照

+ [REST API の検索](https://docs.microsoft.com/rest/api/searchservice)
+ [HTTP 状態コード](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)  
+ [AI エンリッチメントの概要](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)
+ [キャッシュとインクリメンタル エンリッチメントの構成](search-howto-incremental-index.md)
+ [インクリメンタル エンリッチメント](cognitive-search-incremental-indexing-conceptual.md)