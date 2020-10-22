---
title: プレーンテキスト BLOB を検索する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search でのフルテキスト検索のために、Azure の BLOB からプレーンテキストを抽出する検索インデクサーを構成します。
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 417bdacc3ce8b619d5ec9618e6060ac071882471
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533927"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Azure Cognitive Search でプレーンテキスト BLOB のインデックスを作成する方法

フルテキスト検索のために [BLOB インデクサー](search-howto-indexing-azure-blob-storage.md)を使用して検索可能なテキストを抽出するときには、インデックス作成でより良い結果を得るため、さまざまな解析モードを呼び出ことができます。 インデクサーでは、既定で、区切りテキストの BLOB を 1 つのテキスト チャンクとして解析します。 ただし、すべての BLOB に同じエンコードのプレーンテキストが含まれている場合、**テキスト解析モード**を使用してインデックス作成のパフォーマンスを大幅に改善できます。

## <a name="set-up-plain-text-indexing"></a>プレーンテキストのインデックス作成を設定する

プレーンテキスト BLOB のインデックスを作成するには、[インデクサーの作成](/rest/api/searchservice/create-indexer)要求で `parsingMode` 構成プロパティを `text` に設定して、インデクサーの定義を作成または更新します。

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

既定では、`UTF-8` エンコードが想定されます。 別のエンコードを指定するには、`encoding` 構成プロパティを使用します。 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

## <a name="request-example"></a>要求の例

解析モードは、インデクサーの定義で指定されています。

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-plaintext-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>Azure Cognitive Search の品質向上にご協力ください

ご希望の機能や品質向上のアイデアがありましたら、[UserVoice](https://feedback.azure.com/forums/263029-azure-search/) までお寄せください。 既存の機能の使用について助けが必要な場合は、[Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870) で質問を投稿してください。

## <a name="next-steps"></a>次のステップ

* [Azure Cognitive Search のインデクサー](search-indexer-overview.md)
* [BLOB インデクサーを構成する方法](search-howto-indexing-azure-blob-storage.md)
* [BLOB のインデックス作成の概要](search-blob-storage-integration.md)