---
title: Azure の Linux で初めての関数を作成する
description: Azure Functions Core Tools と Azure CLI を使用して、Azure の Linux でホストされる初めての関数を作成する方法について説明します。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 09/12/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: multiple
manager: jeconnoc
ms.openlocfilehash: 1045e0cc0d114bb8b35e6136a2054b3642eac7e8
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249874"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>Core Tools と Azure CLI を使用して Linux でホストされる初めての関数を作成する (プレビュー)

Azure Functions を使用すると、最初に VM を作成したり Web アプリケーションを発行したりしなくても、[サーバーレス](https://azure.microsoft.com/overview/serverless-computing/)の Linux 環境でコードを実行できます。 Linux でのホストは現在プレビュー段階であり、[Functions 2.0 ランタイム](functions-versions.md)が必要です。

このクイック スタート記事では、Linux 上で動作する初めての関数アプリを Azure CLI を使用して作成する方法について説明します。 [Azure Functions Core Tools](functions-run-local.md) を使用して、関数のコードをローカルで作成し、その後 Azure にデプロイします。

次の手順は、Mac、Windows、または Linux コンピューターでサポートされます。 この記事では、JavaScript または C# で関数を作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

このサンプルを実行する前に、以下が必要です。

+ [Azure Core Tools バージョン 2.x](functions-run-local.md#v2) をインストールします。

+ [Azure CLI]( /cli/azure/install-azure-cli) をインストールします。 この記事では、Azure CLI バージョン 2.0.46 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 [Azure Cloud Shell](https://shell.azure.com/bash) を使用することもできます。

+ 有効な Azure サブスクリプション

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>ローカルの関数アプリ プロジェクトを作成する

コマンド ラインから次のコマンドを実行し、現在のローカル ディレクトリの `MyFunctionProj` フォルダー内に関数アプリ プロジェクトを作成します。 GitHub リポジトリも `MyFunctionProj` 内に作成されます。

```bash
func init MyFunctionProj
```

指示されたら、方向キーを使用して、次の言語の選択肢から worker ランタイムを選択します。

+ `dotnet`: .NET クラス ライブラリ プロジェクト (.csproj) を作成します。
+ `node`: JavaScript プロジェクトを作成します。

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Azure に Linux の関数アプリを作成する

Linux での関数の実行をホストするための関数アプリが必要です。 関数アプリは、関数コードを実行するためのサーバーレス環境を提供します。 Function App を使用すると、リソースの管理、デプロイ、共有を容易にするためのロジック ユニットとして関数をグループ化できます。 Linux 上で動作する関数アプリの作成には、[az functionapp create](/cli/azure/functionapp#az_functionapp_create) コマンドを使用します。

次のコマンドの `<app_name>` プレースホルダーには一意の Function App 名を、`<storage_name>` にはストレージ アカウント名を使用します。 `<app_name>` は、関数アプリの既定の DNS ドメインでもあります。 この名前は、Azure のすべてのアプリで一意である必要があります。

```azurecli
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--location "westus" --is-linux
```

> [!NOTE]
> Linux 以外の App Service アプリを含んだ `myResourceGroup` という名前のリソース グループが既にある場合は、別のリソース グループを使用する必要があります。 Windows アプリと Linux アプリの両方を同じリソース グループでホストすることはできません。  

関数アプリの作成後、次のメッセージが表示されます。

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Azure の新しい関数アプリにプロジェクトを発行する準備が整いました。

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>次の手順

この記事では、既定の Azure App Service コンテナーで関数アプリをホストする方法について説明しました。 独自のカスタム コンテナー内の Linux 上で関数をホストすることもできます。

> [!div class="nextstepaction"] 
> [カスタム イメージを使用して Linux で関数を作成する](functions-create-function-linux-custom-image.md)
