---
title: Azure Search Service REST API バージョン 2017-11-11-Preview | Microsoft Docs
description: Azure Search Service REST API バージョン 2017-11-11-Preview には、シノニムや moreLikeThis 検索などの試験的機能が含まれています。
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/17/2018
ms.author: HeidiSteen
ms.openlocfilehash: ce5771777762414a0229cf83425c2f3601cb979a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660226"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Azure Search Service REST api-version 2017-11-11-Preview
この記事では、まだ一般公開されていない実験的機能を提供する Azure Search Service REST API の `api-version=2017-11-11-Preview` バージョンについて説明します。

> [!NOTE]
> プレビュー機能は、フィードバックの収集を目的とするテストと実験のために提供されており、変更される可能性があります。 運用アプリケーションではプレビュー API を使用しないことを強くお勧めします。


## <a name="new-in-2017-11-11-preview"></a>2017-11-11-Preview の新機能

[コグニティブ検索](cognitive-search-concept-intro.md)は、Azure Search における新しい強化機能で、テキスト以外のソースや区別されていないテキスト内の潜在的な情報を検索し、それを Azure Search でフルテキスト検索可能なコンテンツに変換します。

プレビュー REST API では、次のリソースが導入または変更されています。 その他のすべての REST API は、一般公開バージョンまたはプレビュー バージョンのどちらを呼び出しても同じです。

+ [スキルセットの作成 (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)

+ [インデクサーの作成 (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

+ [定義済みのスキル](cognitive-search-predefined-skills.md)

その他の REST API は、api-version の設定に関係なく、すべて同じです。 たとえば、`GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` と `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (`Preview` なし) は機能的に同じです。

## <a name="other-preview-features"></a>他のプレビュー機能

以前のプレビューで発表された機能は、パブリック プレビューにもあります。 以前のプレビュー api-version で API を呼び出している場合は、引き続きそのバージョンを使用することも、想定される動作を変更することなく、`2017-11-11-Preview` に切り替えることもできます。

+ `api-version=2015-02-28-Preview` で導入された [Azure BLOB インデックス作成での CSV ファイル](search-howto-index-csv-blobs.md)は、プレビュー機能のままです。 この機能は Azure BLOB インデックス作成の一部であり、パラメーターの設定を通じて呼び出されます。 CSV ファイルの各行は、個別のドキュメントとしてインデックスが作成されます。

+ `api-version=2015-02-28-Preview` で導入された [Azure BLOB インデックス作成での JSON 配列](search-howto-index-json-blobs.md)は、プレビュー機能のままです。 この機能は Azure BLOB インデックス作成の一部であり、パラメーターの設定を通じて呼び出されます。 配列内の各要素は、個別のドキュメントとしてインデックスが作成されます。

+ [moreLikeThis クエリ パラメーター](search-more-like-this.md)は、特定のドキュメントに関連するドキュメントを検索します。 この機能は、以前のプレビューからありました。 


## <a name="how-to-call-a-preview-api"></a>プレビュー API を呼び出す方法

古いプレビューはまだ動作しますが、時間がたてば古くなります。 コードで `api-version=2016-09-01-Preview` または `api-version=2015-02-28-Preview` を呼び出している場合、それらの呼び出しはまだ有効です。 ただし、機能強化の更新が行われるのは、最新のプレビュー バージョンのみです。 

次のサンプル構文は、API のプレビュー バージョンの呼び出しを示しています。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Azure Search サービスは複数のバージョンで使用できます。 詳しくは、[API バージョン](search-api-versions.md)に関するページをご覧ください。
