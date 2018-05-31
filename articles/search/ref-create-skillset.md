---
title: スキルセットを作成する (REST api-version=2017-11-11-Preview) - Azure Search | Microsoft Docs
description: スキルセットは、エンリッチメント パイプラインを構成するコグニティブ スキルのコレクションです。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a5277d4137ede5fe6dacf993413eefd7c9e46ad4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786911"
---
# <a name="create-skillset-api-version2017-11-11-preview"></a>スキルセットを作成する (api-version=2017-11-11-Preview)

**適用対象:** api-version-2017-11-11-Preview

スキルセットは、自然言語処理とその他の変換に使用される、コグニティブ スキルのコレクションです。 スキルに含まれる代表的なものは、名前付きエンティティの抽出、キー フレーズ抽出、論理ページへのテキストのチャンク処理です。

スキルセットを使用するには、Azure Search インデクサーでそれを参照してから、インデクサーを実行してデータのインポート、変換やエンリッチメントの呼び出し、出力フィールドのインデックスへのマッピングを行います。 スキルセットは高レベルのリソースですが、インデクサーの処理内でのみ使用できます。 スキルセットは高レベルのリソースであるため、一度設計すればその後は、複数のインデクサーで参照できます。 

スキルセットは Azure Search において、HTTP PUT または POST 要求を通して表されます。 PUT の場合、要求の本文は、どのスキルが呼び出されるかを指定する JSON スキーマです。 スキルは入力と出力の関連付けを通して連結され、ある変換の出力が別の変換への入力になります。

1 つのスキルセットには、スキルが少なくとも 1 つ必要です。 スキルの最大数に理論上の制限はありませんが、3 つから 5 つがよくある構成です。  


> [!NOTE]
> コグニティブ検索はパブリック プレビューの段階にあり、スキルセットの実行は現在無料で提供されています。 この機能の価格は後日発表される予定です。

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```  

## <a name="request"></a>要求  
 HTTPS はすべてのサービス要求に必要です。 **Create Skillset** 要求は、URL の一部としてスキルセットの名前を指定した、PUT メソッドを使用して作成できます。 スキルセットは、存在しない場合に作成されます。 既に存在する場合は、新しい定義に更新されます。 PUT で指定できるのは一度に 1 つのスキルセットのみであることに注意してください。  

 スキルセット名は以下の要件を満たす必要があります。

- 小文字になっている
- 名前の先頭と末尾は英文字または数字である
- スラッシュやドットが含まれない
- 128 文字より少ない 

スキルセット名は英文字または数字で始め、残りの部分には英文字、数字、ダッシュを使用できます。ダッシュは連続させてはいけません。  

**api-version** パラメーターは必須です。 使用できるバージョンは `2017-11-11-Preview` のみです。 すべてのバージョンの一覧については、「[Azure Search の API バージョン](https://go.microsoft.com/fwlink/?linkid=834796)」をご覧ください。 


### <a name="request-headers"></a>要求ヘッダー  

 次の表では、必須と省略可能の要求ヘッダーについて説明します。  

|要求ヘッダー|[説明]|  
|--------------------|-----------------|  
|*Content-Type:*|必須。 これを `application/json`|  
|*api-key:*|必須。 `api-key` は Search サービスに対する要求の認証に使用されます。 これはサービスに固有の文字列値です。 **Create Skillset** 要求の `api-key` ヘッダーには (クエリ キーではなく) 管理者キーを設定する必要があります。|  

要求 URL を作成するために、サービス名も必要です。 サービス名と `api-key` はどちらも、Azure Portal のサービス ダッシュボードから取得できます。 ページのナビゲーション ヘルプについては、「 [ポータルで Azure Search サービスを作成する](search-create-service-portal.md) 」を参照してください。  

### <a name="request-body-syntax"></a>要求本文の構文  

要求の本文にはスキルセットの定義が含まれていて、1 つ以上の完全に指定されたスキルに加え、省略可能な name および description パラメーターで構成されています。  

要求ペイロードを構築するための構文は次のとおりです。 要求の例については、この記事で後述します。また、[スキルセットを定義する方法](cognitive-search-defining-skillset.md)に関するページにも示されています。  

```
{   
    "name" : "Required for POST, optional for PUT. Friendly name of the skillset",  
    "description" : "Optional. Anything you want, or null",  
    "Skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",  
}  
```

### <a name="request-example"></a>要求の例
 次の例では、財務ドキュメントのコレクションを強化するために使用されるスキルを作成しています。

```http
PUT https://[servicename].search.windows.net/skillsets/financedocenricher?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

要求の本文は JSON ドキュメントです。 この特定のスキルセットでは、2 つのスキルを非同期的に使用し、`contents` の中身を 2 つの異なる変換として個別に処理します。 あるいは、1 つの変換の出力を、別の変換の入力に向けることができます。 詳細については、[スキルセットを定義する方法](cognitive-search-defining-skillset.md)に関するページをご覧ください。

```json
{
  "name": "financedocenricher",
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
  ]
}
```

## <a name="response"></a>Response  

 要求が成功した場合は、状態コード "201 Created" が表示されます。  

 既定では、応答本文には作成されたスキルセット定義の JSON が含まれます。 ただし、Prefer 要求ヘッダーが return=minimal に設定された場合、応答本文は空になり、成功の状態コードは "201 Created" ではなく "204 No Content" になります。 これは、スキルセットを作成するのに PUT または POST のどちらが使用されかに関わらず同じです。   

## <a name="see-also"></a>関連項目

+ [コグニティブ検索の概要](cognitive-search-concept-intro.md)
+ [クイック スタート: コグニティブ検索を使ってみる](cognitive-search-quickstart-blob.md)
+ [チュートリアル: Azure BLOB のエンリッチメントされるインデックス作成](cognitive-search-tutorial-blob.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [フィールドをマップする方法](cognitive-search-output-field-mapping.md)
+ [カスタム インターフェイスの定義方法](cognitive-search-custom-skill-interface.md)
+ [例: カスタム スキルの作成](cognitive-search-create-custom-skill-example.md)
+ [定義済みのスキル](cognitive-search-predefined-skills.md)