---
title: 'Python チュートリアル: Search 統合のハイライト'
titleSuffix: Azure Cognitive Search
description: このチート シートでは、Search 対応の Web サイトで使用される Python SDK Search 統合クエリについて説明します。
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 05/25/2021
ms.custom: devx-track-python
ms.devlang: python
ms.openlocfilehash: a8f1078e714bd88d2b2e9911a9e1708c21aa91b2
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110580601"
---
# <a name="4---python-search-integration-cheat-sheet"></a>4 - Python Search 統合のチート シート

前のレッスンでは、静的 Web アプリに検索を追加しました。 このレッスンでは、統合を確立するための基本的な手順について説明します。 検索を Python アプリに統合する方法に関するチート シートをお探しの場合に、この記事では知っておく必要があることについて説明します。

アプリケーションは利用可能です。 
* [サンプル](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/search-website)
* [デモ Web サイト - aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="azure-sdk-azure-search-documents"></a>Azure SDK azure-search-documents

関数アプリでは、Azure SDK for Cognitive Search を使用します。

* [PYPI パッケージ azure-search-documents](https://pypi.org/project/azure-search-documents/)
* [リファレンス ドキュメント](/python/api/azure-search-documents)

関数アプリにより、リソース名、API キー、およびインデックス名が使用され、クラウドベースの Cognitive Search API に対する認証がSDK を通じて行われます。 シークレットは静的 Web アプリの設定に格納されており、環境変数として関数に取り込まれます。 

## <a name="configure-secrets-in-a-configuration-file"></a>構成ファイルでシークレットを構成する

Azure 関数アプリの設定環境変数は、3 つの API 関数間で共有される `__init__.py` というファイルから取り込まれます。 

:::code language="python" source="~/azure-search-python-samples/search-website/api/shared_code/__init__.py" highlight="6-9" :::

## <a name="azure-function-search-the-catalog"></a>Azure 関数: カタログを検索する

Search [API](https://github.com/Azure-Samples/azure-search-python-samples/blob/master/search-website/api/Search/__init__.py) は検索語句を受け取り、検索インデックス内のドキュメントを検索して、一致項目の一覧を返します。 

Search API のルーティングは、[function.json](https://github.com/Azure-Samples/azure-search-python-samples/blob/master/search-website/api/Search/function.json) のバインドに含まれています。

Azure 関数は、検索の構成情報を取り込んで、クエリを実行します。

:::code language="python" source="~/azure-search-python-samples/search-website/api/Search/__init__.py" highlight="8-18, 122" :::

## <a name="client-search-from-the-catalog"></a>クライアント: カタログから検索する

次のコードを使用して、React クライアントで Azure 関数を呼び出します。 

:::code language="javascript" source="~/azure-search-python-samples/search-website/src/pages/Search/Search.js" highlight="42-55" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Azure 関数: カタログからの検索候補

`Suggest` [API](https://github.com/Azure-Samples/azure-search-python-samples/blob/master/search-website/api/Suggest/__init__.py) は、ユーザーが入力している間は検索語句を受け取り、検索インデックス内のドキュメントに含まれる書籍のタイトルや著者名などの検索候補を提示して、一致する小さな一覧を返します。 

検索 suggester、`sg` は、一括アップロードの際に使用される[スキーマ ファイル](https://github.com/Azure-Samples/azure-search-python-samples/blob/master/search-website/bulk-upload/good-books-index.json)に定義されています。

Suggest API のルーティングは、[function.json](https://github.com/Azure-Samples/azure-search-python-samples/blob/master/search-website/api/Suggest/function.json) のバインドに含まれています。

:::code language="python" source="~/azure-search-python-samples/search-website/api/Suggest/__init__.py" highlight="8-23, 35" :::

## <a name="client-suggestions-from-the-catalog"></a>クライアント: カタログからの検索候補

Suggest 関数 API は、コンポーネント初期化の一部として、React アプリの中で `\src\components\SearchBar\SearchBar.js` で呼び出されます。

:::code language="javascript" source="~/azure-search-python-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Azure 関数: 特定のドキュメントを取得する 

`Lookup` [API](https://github.com/Azure-Samples/azure-search-python-samples/blob/master/search-website/api/Lookup/__init__.py) は ID を受け取り、検索インデックスからドキュメント オブジェクトを返します。 

Lookup API のルーティングは、[function.json](https://github.com/Azure-Samples/azure-search-python-samples/blob/master/search-website/api/Lookup/function.json) のバインドに含まれています。

:::code language="python" source="~/azure-search-python-samples/search-website/api/Lookup/__init__.py" highlight="8-18, 27" :::

## <a name="client-get-specific-document"></a>クライアント: 特定のドキュメントを取得する 

この関数 API は、コンポーネント初期化の一部として、React アプリの中で `\src\pages\Details\Detail.js` で呼び出されます。

:::code language="javascript" source="~/azure-search-python-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="next-steps"></a>次のステップ

* [Azure SQL データのインデックスを作成する](search-indexer-tutorial.md)
