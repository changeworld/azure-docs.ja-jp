---
title: C# を使用してプレビュー環境からデータを照会する - Azure Time Series Insights | Microsoft Docs
description: C# で記述されたアプリを使用して Azure Time Series Insights 環境からデータを照会する方法を学びます。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 76e3ac85a6725976ebd14dac1805079613c94ec6
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76980989"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>C# を使用して Azure Time Series Insights プレビュー環境からデータを照会する

この C# サンプルでは、Azure Time Series Insights プレビュー環境にある[プレビューのデータ アクセス API](https://docs.microsoft.com/rest/api/time-series-insights/preview) からデータを照会する方法を示しています。

> [!TIP]
> [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample) で、プレビューの C# コード サンプルを閲覧してください。

## <a name="summary"></a>まとめ

次のサンプル コードでは、以下の機能が示されます。

* [Azure AutoRest ](https://github.com/Azure/AutoRest)からの SDK 自動生成のサポート。
* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) を使用して、Azure Active Directory を通じてアクセス トークンを取得する方法。
* 取得したアクセス トークンを後続のデータ アクセス API 要求の `Authorization` ヘッダー内で渡す方法。 
* サンプルでは、以下に対して HTTP 要求が行われる方法を示すコンソール インターフェイスを提供しています。

    * [プレビューの Environments API](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [Get Environments Availability API ](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)および [Get Event Schema API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [プレビューの Query API](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [Get Events API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)、[Get Series API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)、および[ Get Aggregate Series API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [タイム シリーズ モデル API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [Get Hierarchies API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) と [Hierarchies Batch API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [Get Types API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) と [Types Batch API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [Get Instances API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) と [Instances Batch API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* 高度な[検索](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features)および [TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) の機能。

## <a name="prerequisites-and-setup"></a>前提条件と設定

サンプル コードをコンパイルして実行する前に、次の手順を実行します。

1. [プレビューの Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) の環境をプロビジョニングします。
1. [認証と承認](time-series-insights-authentication-and-authorization.md)に関するページにある説明に基づき、Azure Active Directory で Azure Time Series Insights 環境を設定します。 
1. [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) に示されているように [GenerateCode.bat ](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat)を実行して、Time Series Insights プレビュー クライアントの依存関係を生成します。
1. Visual Studio 上で `TSIPreviewDataPlaneclient.sln` ソリューションを開き、`DataPlaneClientSampleApp` を既定のプロジェクトとして設定します。
1. [以下](#project-dependencies)に示された手順を使用して必要なプロジェクトの依存関係をインストールし、サンプルを実行可能な `.exe` ファイルにコンパイルします。
1. `.exe` ファイルをダブルクリックして、実行します。

## <a name="project-dependencies"></a>プロジェクト依存関係

最新版の Visual Studio を使用することをお勧めします。

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - バージョン 16.4.2+

サンプル コードには、[packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config) ファイル内で確認できる必須の依存関係がいくつかあります。

**[ビルド]**  >  **[ソリューションのビルド]** オプションを順に選択して、Visual Studio 2019 にパッケージをダウンロードします。 

または、[NuGet 2.12+ ](https://www.nuget.org/)を使用して、各パッケージを追加します。 次に例を示します。

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# サンプル コード

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * コード サンプルは、既定の環境変数を変更せずに実行できます。
> * コード サンプルは、.NET 実行可能ファイルのコンソール アプリにコンパイルされます。

## <a name="next-steps"></a>次のステップ

- クエリの詳細については、[クエリ API リファレンス](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)を参照してください。

- Time Series Insights に [クライアント SDK を使用して JavaScript アプリを接続する](https://github.com/microsoft/tsiclient)方法について確認します。
