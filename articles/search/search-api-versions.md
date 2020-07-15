---
title: API のバージョン
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search の REST API のバージョン ポリシーと .NET SDK のクライアント ライブラリ。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a7179f88f507f0deedc79e7ae49988c8b5a32f86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830096"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure Cognitive Search の API のバージョン

Azure Cognitive Search では、定期的に機能更新がロールアウトされています。 このような更新が発生すると、下位互換性を維持するために、API の新しいバージョンが必要になる場合があります (毎回とは限りません)。 新しいバージョンが発行されると、お客様は検索サービスの更新内容をコードに統合するタイミングと方法を管理することができます。

Azure Cognitive Search のチームでは、原則として必要な場合にのみ新しい API バージョンを公開しています。新しい API バージョンの使用には、お客様側でコードをアップグレードする作業が必要になる可能性があるためです。 下位互換性が保たれなくなる形で API の一部の要素が変更された場合に限り、新しいバージョンが必要です。 このような変更は、既存の機能に修正を加えたことや、新機能において既存の API のセキュリティ構成を変更したことが原因で発生することがあります。

SDK 更新プログラムにも同じルールが適用されます。 Azure Cognitive Search SDK は[セマンティック バージョニング](https://semver.org/) ルールに従っています。つまり、バージョンにはメジャー、マイナー、およびビルド番号という 3 つの部分があります (たとえば、1.1.0)。 SDK の新しいメジャー バージョンは、下位互換性が保たれなくなる変更が生じた場合にのみリリースされます。 下位互換性が保たれる機能更新の場合はマイナー バージョンがインクリメントされ、バグ修正の場合はビルド バージョンのみがインクリメントされます。

> [!NOTE]
> Azure Cognitive Search サービスのインスタンスでは、最新のバージョンを含む、REST API の複数のバージョンをサポートしています。 バージョンが最新ではなくなった場合でも、そのバージョンを引き続き使用できますが、最新バージョンを使用するようにコードを移行することをお勧めします。 REST API を使用している場合は、api-version パラメーターを使用して、すべての要求に API バージョンを指定する必要があります。 .NET SDK を使用している場合は、使用している SDK のバージョンによって REST API の対応するバージョンが決まります。 サービスが新しいバージョンの API をサポートするようにアップグレードされた場合でも、使用中の古い SDK のコードを変更なしで引き続き実行できます。

## <a name="rest-apis"></a>REST API

次の表には、現在および以前にリリースされた Search Service REST API のバージョンのバージョン履歴を示します。 現在の安定バージョンとプレビュー バージョンのドキュメントが公開されています。

| バージョン&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | 下位互換性に関する問題 |
|-------------|--------|------------------------------|
| [Management 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) | 一般公開 | エンドポイントの保護が強化された、Management REST API の最新の安定リリースです。 新しいサービスとして、プライベート エンドポイント、プライベート リンクのサポート、およびネットワーク規則が追加されています。 |
| [Management 2019-10-01-Preview](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) | プレビュー  | バージョン番号にかかわらず、これは引き続き Management REST API の現在のプレビュー バージョンです。 現時点ではプレビュー機能はありません。 最近、すべてのプレビュー機能が最近一般公開されました。 |
| Management 2015-08-19  | Stable| Management REST API の最初の一般公開バージョン。 サービスのプロビジョニング、スケールアップ、および API キーの管理が可能です。 |
| Management 2015-08-19-Preview | プレビュー| Management REST API の最初のプレビュー バージョン。 |
| [Search 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/index)| Stable | 関連性のスコアリングが強化された、Search REST API の最新の安定リリースです。 |
| [Search 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview)| プレビュー | 安定バージョンに関連付けられているプレビュー バージョン。 |
| 検索 2019-05-06 | Stable | 複合型が追加されています。 |
| 検索 2019-05-06-Preview | プレビュー | 安定バージョンに関連付けられているプレビュー バージョン。 |
| Search 2017-11-11 | Stable  | スキルセットと AI エンリッチメントが追加されています。 |
| Search 2017-11-11-Preview | プレビュー | 安定バージョンに関連付けられているプレビュー バージョン。 |
| Search 2016-09-01 |Stable | インデクサーが追加されています。|
| Search 2016-09-01-Preview | プレビュー | 安定バージョンに関連付けられているプレビュー バージョン。|
| Search 2015-02-28 | Stable  | 最初に一般公開されたリリースです。  |
| Search 2015-02-28-Preview | プレビュー | 安定バージョンに関連付けられているプレビュー バージョン。 |
| Search 2014-10-20-Preview | プレビュー | 2 番目のパブリック プレビューです。 |
| Search 2014-07-31-Preview | プレビュー | 1 番目のパブリック プレビューです。 |

## <a name="azure-sdk-for-net"></a>Azure SDK for .NET

パッケージのバージョン履歴は、NuGet.org でご確認いただけます。この表では、各パッケージのページへのリンクを示しています。

| SDK バージョン | Status | 説明 |
|-------------|--------|------------------------------|
| [**Azure.Search.Documents 1.0.0-preview.4**](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | プレビュー | 2020 年 5 月にリリースされた、Azure .NET SDK の新しいクライアント ライブラリです。 REST 2020-06-30 API バージョンを対象としています。|
| [**Microsoft.Azure.Search 10.0**](https://www.nuget.org/packages/Microsoft.Azure.Search/) | 一般公開、2019 年 5 月にリリース済み。 REST 2019-05-06 API バージョンを対象としています。|
| [**Microsoft.Azure.Search 8.0-preview**](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | プレビュー、2019 年 4 月にリリース済み。 REST 2019-05-06-Preview API バージョンを対象としています。|
| [**Microsoft.Azure.Management.Search 3.0.0**](https://docs.microsoft.com/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Stable | Management REST api-version=2015-08-19 を対象としています。 |

## <a name="azure-sdk-for-java"></a>Azure SDK for Java

| SDK バージョン | Status | 説明  |
|-------------|--------|------------------------------|
| [**Java SearchManagementClient 1.35.0**](https://docs.microsoft.com/java/api/overview/azure/search/management?view=azure-java-stable) | Stable | Management REST api-version=2015-08-19 を対象としています。|

## <a name="azure-sdk-for-python"></a>Azure SDK for Python

| SDK バージョン | Status | 説明  |
|-------------|--------|------------------------------|
| [**Python azure-mgmt-search 1.0**](https://docs.microsoft.com/python/api/overview/azure/search?view=azure-python) | Stable | Management REST api-version=2015-08-19 を対象としています。 |