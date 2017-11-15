---
title: "Azure Search Service REST API バージョン 2016-09-01-Preview | Microsoft Docs"
description: "Azure Search Service REST API バージョン 2016-09-01-Preview には、シノニムや moreLikeThis 検索などの試験的機能が含まれています。"
services: search
documentationcenter: na
author: mhko
manager: jlembicz
editor: 
ms.assetid: 3dba3bf8-9c83-42f6-82bc-04727bd11037
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 10/25/2017
ms.author: nateko
ms.openlocfilehash: 082c207f892fcc277d30d66c6165dd9920d3ab27
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2017
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Azure Search Service REST API: バージョン 2016-09-01-Preview
この記事は、 `api-version=2016-09-01-Preview`のリファレンス ドキュメントです。 このプレビューは、以下の試験的機能を提供することによって、現在一般公開されているバージョン [api-version=2016-09-01](https://msdn.microsoft.com/library/dn798935.aspx) を拡張するものです。

* シノニム マップをアップロードし、検索範囲を拡張するための [Synonyms API](search-synonyms.md)。
* 特定のドキュメントに関連する別のドキュメントを検索するための [`moreLikeThis` クエリ パラメーター](search-more-like-this.md)。

これらの実験的な機能を試すには、プレビュー API バージョン `api-version=2016-09-01-Preview` がターゲットになっていることを確認してください。 次の例は、プレビュー API バージョンで more-like-this クエリを実行するように指定しています。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> プレビュー機能は、フィードバックの収集を目的とするテストと実験のために提供されており、変更される可能性があります。 **実稼働アプリケーションではプレビュー API を使用しないことを強くお勧めします。**

Azure Search サービスは複数のバージョンで使用できます。 詳細については、「 [Azure Search サービスのバージョン](http://msdn.microsoft.com/library/azure/dn864560.aspx) 」を参照してください。
