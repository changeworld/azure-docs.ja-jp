---
title: "Durable Functions 拡張機能とサンプルをインストールする - Azure"
description: "Azure Functions、ポータル開発、または Visual Studio 開発用に Durable Functions 拡張機能をインストールする方法を説明します。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: debadde78d937bcd4ec1df665aacfd1887fbcd02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Durable Functions 拡張機能とサンプルをインストールする (Azure Functions)

Azure Functions の [Durable Functions](durable-functions-overview.md) 拡張機能は、NuGet パッケージ [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) に提供されます。 この記事では、次の開発環境用のパッケージとサンプル セットをインストールする方法を示します。

* Visual Studio 2017 (推奨) 
* Azure ポータル

## <a name="visual-studio-2017"></a>Visual Studio 2017

現時点では、Durable Functions を使用するアプリを開発するための 最適なエクスペリエンスは Visual Studio が提供します。  開発した関数はローカルに実行でき、Azure に発行することもできます。 空のプロジェクトまたはサンプル関数セットから始めることができます。

### <a name="prerequisites"></a>前提条件

* [Visual Studio の最新バージョン](https://www.visualstudio.com/downloads/)のインストール (バージョン 15.3 以降)。 セットアップ オプションに Azure ツールを含めてください。

### <a name="start-with-sample-functions"></a>サンプル関数から始める

1. [Visual Studio 用のサンプル アプリの .zip ファイル](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip)をダウンロードします。 NuGet 参照はサンプル プロジェクトに既に含まれているため、追加する必要はありません。
2. [Azure Storage Emulator](https://docs.microsoft.com/azure/storage/storage-use-emulator) バージョン 5.2 以降をインストールして実行します。 または、*local.appsettings.json* ファイルを実際の Azure Storage の接続文字列で更新できます。
3. Visual Studio 2017 でプロジェクトを開きます。 
4. このサンプルの実行方法については、[関数チェーン - Hello シーケンス サンプル](durable-functions-sequence.md)に記載されている手順から始めてください。 サンプルは、ローカルで実行することも Azure に発行することもできます。

### <a name="start-with-an-empty-project"></a>空のプロジェクトから始める
 
サンプルから始めるのと同じ手順に従いますが、*.zip* ファイルをダウンロードする代わりに次の手順に従います。

1. Function App プロジェクトを作成します。
2. 次の NuGet パッケージ参照を *.csproj* ファイルに追加します。

   ```xml
   <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.DurableTask" Version="1.0.0-beta" />
   ```

## <a name="azure-portal"></a>Azure ポータル

必要に応じて、Durable Functions 開発で Azure ポータルを使用できます。

### <a name="create-an-orchestrator-function"></a>オーケストレーター関数を作成する

1. [functions.azure.com](https://functions.azure.com/signin) で関数アプリを新規作成します。
2. 関数アプリを [2.0 ランタイム バージョン](functions-versions.md)を使用するように構成します。
3. 新しい関数を作成し、**Durable Functions オーケストレーター - C#** テンプレートを選択します。
4. **[拡張機能がインストールされていません]** の下の **[インストール]** をクリックして、NuGet.org から拡張機能をダウンロードします。

### <a name="copy-sample-code-to-the-function-app"></a>サンプル コードを関数アプリにコピーする

1. [DFSampleApp.zip](https://github.com/Azure/azure-functions-durable-extension/raw/master/docfx/files/DFSampleApp.zip) ファイルをダウンロードします。
2. Kudu または FTP を使用して、サンプル ファイルを関数アプリの `D:\home\site\wwwroot` に解凍します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [関数チェーンのサンプルを実行する](durable-functions-sequence.md)
