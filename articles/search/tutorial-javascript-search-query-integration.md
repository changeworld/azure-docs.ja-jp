---
title: 'JavaScript チュートリアル: 検索統合の概要'
titleSuffix: Azure Cognitive Search
description: 検索が有効な Web サイトで使用される JavaScript SDK 検索クエリを理解する
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: cf4e1b1ecf209b587a45ca4c43607bfa95155aee
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104723500"
---
# <a name="4---search-integration-highlights"></a>4 - 検索統合の概要

前のレッスンでは、静的 Web アプリに検索を追加しました。 このレッスンでは、統合を確立するための基本的な手順について説明します。 検索を JavaScript アプリに統合する方法に関するチート シートをお探しの場合に、この記事では知っておく必要があることについて説明します。

## <a name="azure-sdk-azuresearch-documents"></a>Azure SDK @azure/search-documents 

関数アプリでは、Azure SDK for Cognitive Search を使用します。

* NPM: [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* リファレンス ドキュメント: [クライアント ライブラリ](/javascript/api/overview/azure/search-documents-readme)

関数アプリは、リソース名、リソース キー、およびインデックス名を使用して、SDK を通じてクラウドベースの Cognitive Search API に対して認証を行います。 シークレットは静的 Web アプリの設定に格納されており、環境変数として関数に取り込まれます。 

## <a name="configure-secrets-in-a-configuration-file"></a>構成ファイルでシークレットを構成する

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/config.js" highlight="3,4" :::

## <a name="azure-function-search-the-catalog"></a>Azure 関数: カタログを検索する

`Search` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/index.js) は検索語句を受け取り、検索インデックス内のドキュメントを検索して、一致項目の一覧を返します。 

Search API のルーティングは、[function.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/function.json) のバインドに含まれています。

Azure 関数は、検索の構成情報を取り込んで、クエリを実行します。

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Search/index.js" highlight="4-9, 75" :::

## <a name="client-search-from-the-catalog"></a>クライアント: カタログから検索する

次のコードを使用して、React クライアントで Azure 関数を呼び出します。 

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Azure 関数: カタログからの検索候補

`Suggest` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/index.js) は、ユーザーが入力している間は検索語句を受け取り、検索インデックス内のドキュメントに含まれる書籍のタイトルや著者名などの検索候補を提示して、一致する小さな一覧を返します。 

検索 suggester、`sg` は、一括アップロードの際に使用される[スキーマ ファイル](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/bulk-insert/good-books-index.json)に定義されています。

Suggest API のルーティングは、[function.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/function.json) のバインドに含まれています。

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Suggest/index.js" highlight="4-9, 21" :::

## <a name="client-suggestions-from-the-catalog"></a>クライアント: カタログからの検索候補

Suggest 関数 API は、コンポーネント初期化の一部として、React アプリの中で `\src\components\SearchBar\SearchBar.js` で呼び出されます。

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Azure 関数: 特定のドキュメントを取得する 

`Lookup` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/index.js) は ID を受け取り、検索インデックスからドキュメント オブジェクトを返します。 

Lookup API のルーティングは、[function.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/function.json) のバインドに含まれています。

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Lookup/index.js" highlight="4-9, 17" :::

## <a name="client-get-specific-document"></a>クライアント: 特定のドキュメントを取得する 

この関数 API は、コンポーネント初期化の一部として、React アプリの中で `\src\pages\Details\Detail.js` で呼び出されます。

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="next-steps"></a>次のステップ

* [Azure SQL データのインデックスを作成する](search-indexer-tutorial.md)
