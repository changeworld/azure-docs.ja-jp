---
title: Azure CLI での初めての関数の作成
description: Azure CLI と Azure Functions Core Tools を使用して、サーバーレス実行のための最初の Azure 関数を作成する方法について説明します。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 09/10/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: f65ff3e38e3768dccf04e9eaa4345ffcea4daa9e
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451122"
---
# <a name="create-your-first-function-from-the-command-line"></a>コマンド ラインから最初の関数を作成する

このクイックスタート トピックでは、コマンド ラインまたはターミナルから最初の関数を作成する方法について説明します。 Azure CLI を使用して、関数をホストする[サーバーレス](https://azure.microsoft.com/overview/serverless-computing/) インフラストラクチャである Function App を作成します。 関数コード プロジェクトは、[Azure Functions Core Tools](functions-run-local.md) を使用してテンプレートから生成されます。このツールは、関数アプリ プロジェクトを Azure にデプロイするためにも使用されます。

以下の手順は、Mac、Windows、または Linux コンピューターを使用して実行することができます。

## <a name="prerequisites"></a>前提条件

このサンプルを実行する前に、以下が必要です。

+ [Azure Core Tools バージョン 2.x](functions-run-local.md#v2) をインストールします。

+ [Azure CLI]( /cli/azure/install-azure-cli) をインストールします。 この記事では、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 [Azure Cloud Shell](https://shell.azure.com/bash) を使用することもできます。

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

コマンドを実行すると、次のような出力が表示されます。

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

## <a name="create-a-function"></a>関数を作成する

次のコマンドは、新しいプロジェクトに移動し、HTTP でトリガーされる `MyHtpTrigger` という名前の関数を作成します。

```bash
cd MyFunctionProj
func new --name MyHttpTrigger --template "HttpTrigger"
```

コマンドを実行すると、次のような出力が表示されます。これは JavaScript 関数です。

```output
Writing C:\functions\MyFunctionProj\MyHttpTrigger\index.js
Writing C:\functions\MyFunctionProj\MyHttpTrigger\sample.dat
Writing C:\functions\MyFunctionProj\MyHttpTrigger\function.json
```

## <a name="edit-the-function"></a>関数を編集する

既定では、テンプレートによって、要求時に関数キーを要求する関数が作成されます。 Azure で関数をテストしやすくするために、匿名アクセスを許可するように関数を更新する必要があります。 この変更を行う方法は、関数プロジェクトの言語によって異なります。

### <a name="c"></a>C\#

新しい関数である MyHttpTrigger.cs コード ファイルを開き、関数定義の中の **AuthorizationLevel** 属性の値を `anonymous` に更新し、変更を保存します。

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

新しい関数である function.json ファイルをテキスト エディターで開き、**bindings.httpTrigger** 内の **authLevel** プロパティ を `anonymous` に更新し、変更を保存します。

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

これで、関数キーを指定することなく Azure で関数を呼び出すことができます。 ローカルでの実行時に関数キーが要求されることはありません。

## <a name="run-the-function-locally"></a>関数をローカルで実行する

次のコマンドで関数アプリを起動します。 アプリは、Azure 内にある同じ Azure Functions ランタイムを使用して実行されます。

```bash
func host start --build
```

C# プロジェクトをコンパイルするには、`--build` オプションが必須です。 JavaScript プロジェクトの場合、このオプションは必要はありません。

Functions ホストが起動すると、次のような出力が表示されます。この出力は、読みやすいように切り詰められています。

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

ランタイム出力から `HTTPTrigger` 関数の URL をコピーして、それをブラウザーのアドレス バーに貼り付けます。 この URL にクエリ文字列 `?name=<yourname>` を追加して、要求を実行します。 ローカルの関数によって返された GET 要求に対するブラウザーでの応答を次に示します。

![ブラウザーでローカルにテストする](./media/functions-create-first-azure-function-azure-cli/functions-test-local-browser.png)

ローカルで関数を実行したので、これで、Azure で関数アプリとその他の必要なリソースを作成できます。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Function App を作成する

関数の実行をホストするための Function App が存在する必要があります。 Function App は、関数コードのサーバーレス実行の環境を提供します。 Function App を使用すると、リソースの管理、デプロイ、共有を容易にするためのロジック ユニットとして関数をグループ化できます。 Function App の作成には、[az functionapp create](/cli/azure/functionapp#az-functionapp-create) コマンドを使用します。 

次のコマンドでは、`<app_name>` プレースホルダーを一意の Function App 名で、`<storage_name>` をストレージ アカウント名で置き換えます。 `<app_name>` は、Function App の既定の DNS ドメインとして使用されます。そのため、名前は Azure のすべてのアプリ間で一意である必要があります。 _deployment-source-url_ パラメーターは、HTTP によってトリガーされる "Hello World" 関数を含む GitHub のサンプル リポジトリです。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```

_consumption-plan-location_ パラメーターを設定すると、従量課金ホスティング プランで Function App がホストされます。 このサーバーレス プランでは、関数からの要求に応じてリソースが動的に追加され、関数が実行中のときだけ課金されます。 詳細については、「[Azure Functions の適切なサービス プランを選択する](functions-scale.md)」を参照してください。

Function App が作成されると、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

## <a name="configure-the-function-app"></a>Function App を構成する

Core Tools バージョン 2.x では、Azure Functions 2.x ランタイム用のテンプレートを使用してプロジェクトが作成されます。 このため、Azure でバージョン 2.x ランタイムが使用されていることを確認する必要があります。 `FUNCTIONS_WORKER_RUNTIME` アプリケーション設定を `~2` に設定すると、関数アプリが最新の 2.x バージョンに固定されます。 [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) コマンドを使用して、アプリケーション設定を行います。

次の Azure CLI コマンドの `<app_name> は、お使いの関数アプリの名前です。

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~2
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
