---
title: API のバージョン
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search の REST API のバージョン ポリシーと .NET SDK のクライアント ライブラリ。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 9a53eba2b46333f3be08432ce69b22cfb3d9ef9c
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073527"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure Cognitive Search の API のバージョン

Azure Cognitive Search では、定期的に機能更新がロールアウトされています。 このような更新が発生すると、下位互換性を維持するために、API の新しいバージョンが必要になる場合があります (毎回とは限りません)。 新しいバージョンが発行されると、お客様は検索サービスの更新内容をコードに統合するタイミングと方法を管理することができます。

Azure Cognitive Search のチームでは、原則として必要な場合にのみ新しい API バージョンを公開しています。新しい API バージョンの使用には、お客様側でコードをアップグレードする作業が必要になる可能性があるためです。 下位互換性が保たれなくなる形で API の一部の要素が変更された場合に限り、新しいバージョンが必要です。 このような変更は、既存の機能に修正を加えたことや、新機能において既存の API のセキュリティ構成を変更したことが原因で発生することがあります。

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>サポートされていないバージョン

以下に示す一部の API バージョンはサポートされなくなったため、検索サービスによって拒否されます。

+ **2015-02-28**
+ **2015-02-28-Preview**
+ **2014-07-31-Preview**
+ **2014-10-20-Preview**

さらに、[**3.0.0-rc**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc) より前のバージョンの Azure Cognitive Search .NET SDK も、これらの REST API バージョンの 1 つを対象としているため、廃止される予定です。

上記のバージョンのサポートは、2020 年 10 月 15 日に停止されました。 停止されたバージョンを使用しているコードがある場合は、新しい [REST API バージョン](/rest/api/searchservice/)または新しい Azure SDK に[既存のコードを移行](search-api-migration.md)できます。

## <a name="rest-apis"></a>REST API

| REST API | リンク |
|----------|------|
| 検索サービス (データ プレーン) | [API のバージョン](/rest/api/searchservice/search-service-api-versions) |
| 管理 (コントロール プレーン) | [API のバージョン](/rest/api/searchmanagement/management-api-versions) |

## <a name="azure-sdk-for-net"></a>Azure SDK for .NET

次の表では、新しいバージョンの SDK へのリンクを示します。 

| SDK バージョン | Status | 説明 |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11](/dotnet/api/overview/azure/search.documents-readme) | Stable | 2020 年 7 月に最初にリリースされた、Azure .NET SDK の新しいクライアント ライブラリです。 |
| [Microsoft.Azure.Search 10](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | リリース日: 2019 年 5 月。 これは Microsoft.Azure.Search パッケージの最後のバージョンです。 これは、Azure.Search.Documents によって引き継がれました。 |
| [Microsoft.Azure.Management.Search 4.0.0](/dotnet/api/overview/azure/search/management) | Stable | Management REST api-version=2020-08-01 を対象としています。  |
| Microsoft.Azure.Management.Search 3.0.0 | Stable | Management REST api-version=2015-08-19 を対象としています。  |

## <a name="azure-sdk-for-java"></a>Azure SDK for Java

| SDK バージョン | Status | 説明  |
|-------------|--------|------------------------------|
| [Java azure-search-documents 11](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | Stable | 2020 年 7 月にリリースされた、Azure Java SDK の新しいクライアント ライブラリです。 Search REST api-version=2019-05-06 を対象としています。 |
| [Java Management Client 1.35.0](/java/api/overview/azure/search/management) | Stable | Management REST api-version=2015-08-19 を対象としています。 |

## <a name="azure-sdk-for-javascript"></a>Azure SDK for JavaScript

| SDK バージョン | Status | 説明  |
|-------------|--------|------------------------------|
| [JavaScript @azure/search-documents 11.0](https://www.npmjs.com/package/@azure/search-documents) | Stable | 2020 年 7 月にリリースされた、Azure JavaScript と TypesScript SDK の新しいクライアント ライブラリです。 Search REST api-version=2016-09-01 を対象としています。 |
| [JavaScript @azure/arm-search](https://www.npmjs.com/package/@azure/arm-search) | Stable | Management REST api-version=2015-08-19 を対象としています。 |

## <a name="azure-sdk-for-python"></a>Azure SDK for Python

| SDK バージョン | Status | 説明  |
|-------------|--------|------------------------------|
| [Python azure-search-documents 11.0](https://pypi.org/project/azure-search-documents/) | Stable | 2020 年 7 月にリリースされた、Azure Python SDK の新しいクライアント ライブラリです。 Search REST api-version=2019-05-06 を対象としています。 |
| [Python azure-mgmt-search 8.0](https://pypi.org/project/azure-mgmt-search/) | Stable | Management REST api-version=2015-08-19 を対象としています。 |

## <a name="all-azure-sdks"></a>すべての Azure SDK

ベータ版のクライアント ライブラリとドキュメントをお探しの場合、[このページ](https://azure.github.io/azure-sdk/releases/latest/index.html)に、すべての Azure SDK ライブラリ パッケージ、コード、ドキュメントへのリンクが含まれています。 
