---
title: Azure Fun ctions を Azure SignalR Service と統合するためのチュートリアル | Microsoft Docs
description: このチュートリアルでは、Azure SignalR Service で Azure Functions を使用する方法について説明します
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2018
ms.author: wesmc
ms.openlocfilehash: b1bb6b3fe545d5a42fa985ab85bd7a914128e56b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-integrate-azure-functions-with-azure-signalr-service"></a>チュートリアル: Azure Functions を Azure SignalR Service と統合する

このチュートリアルは、前のチュートリアルで作成したチャット ルーム アプリケーションをベースにしています。 まだ「[Create a chat room with SignalR Service (SignalR Service を使用してチャット ルームを作成する)](signalr-quickstart-dotnet-core.md)」と「[Azure SignalR Service authentication (Azure SignalR Service 認証)](./signalr-authenticate-oauth.md)」を完了していない場合は、先にこれらの演習を完了してください。 

リアルタイム アプリケーションの一般的なシナリオでは、コンテンツの更新がサーバーで行われてから Web クライアントに発行されます。 [Azure Functions](../azure-functions/functions-overview.md) は、これらのコンテンツの更新の生成に適しています。 Azure Functions を使用することの主な利点は、アプリケーション全体のアーキテクチャや、それを実行するインフラストラクチャを気にすることなく、オンデマンドでコードを実行できることです。 また、実際にコードが実行された時間だけが支払い対象になります。  

通常、このシナリオでは、SignalR を使用すると問題が発生します。これは、SignalR がコンテンツ更新をプッシュするために、クライアントとサーバー間の接続を維持しようとするためです。 コードはオンデマンドで実行されるだけであるため、接続を維持することはできません。 ただし、Azure SignalR Service は実行時の接続を管理するため、このシナリオをサポートすることができます。

このチュートリアルでは、毎分の始めに Azure Functions のタイマー関数を使用してメッセージを生成します。 関数は、前のチュートリアルで作成したチャット ルームのすべてのクライアントにメッセージを発行します。 タイマー関数の詳細については、[タイマー関数](../azure-functions/functions-create-scheduled-function.md)に関するページを参照してください。

このクイック スタートの手順の実行には、任意のコード エディターを使用することができます。 ただし、[Visual Studio Code](https://code.visualstudio.com/) は、Windows、macOS、および Linux プラットフォームで利用可能な優れた選択肢です。

このチュートリアルのコードは、[AzureSignalR-samples の GitHub リポジトリ](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer)でダウンロードすることができます。

![サーバー メッセージを使用するチャット アプリ](./media/signalr-integrate-functions/signalr-functions-complete.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure CLI を使用して、Azure Functions で新しい Timer 関数を作成します。
> * ローカル Git リポジトリのデプロイ用にタイマー関数を構成します。
> * タイマーを SignalR Service に接続して、毎分更新をプッシュします

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしている必要があります。

* [Git](https://git-scm.com/)
* [.NET コア SDK](https://www.microsoft.com/net/download/windows) 
* [構成済みの Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) github リポジトリのダウンロードまたは複製。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-function-app"></a>Function App を作成する

関数の実行環境を定義する関数アプリを作成する必要があります。 関数アプリを使用すると、管理、デプロイ、およびリソースの共有を容易にするための論理ユニットとして、複数の関数をグループ化することもできます。 詳細については、「[Azure CLI での初めての関数の作成](../azure-functions/functions-create-first-azure-function-azure-cli.md)」を参照してください。

このセクションでは、Azure Cloud Shell を使用して、ローカル Git リポジトリからのデプロイ用に構成された、新しい Azure 関数アプリを作成します。 

関数アプリのリソースを作成する場合は、前のチュートリアルで使用したのと同じリソース グループ内に作成します。 そのようにすると、すべてのチュートリアル リソースを簡単に管理することができます。

次のスクリプトをテキスト エディターにコピーし、変数パラメーターの値を実際のリソースの値に置き換えます。 更新したスクリプトをコピーして Azure Cloud Shell に貼り付け、**Enter** キーを押してストレージ アカウントと関数アプリを作成します。

```azurecli-interactive
#====================================================================
#=== Update these variables to match your values from previous    === 
#=== tutorials.                                                   ===
#====================================================================
ResourceGroupName=SignalRTestResources
location=eastus

#====================================================================
#=== Update these variables for the new function app and storage  ===
#=== account.                                                     ===
#====================================================================
functionappName=mysignalfunctionapp
storageAccountName=mystorageaccount

# Create a storage account to hold function app code and settings
az storage account create --resource-group $ResourceGroupName \
--name $storageAccountName \
--location $location --sku Standard_LRS

# Create the function app
az functionapp create --resource-group $ResourceGroupName \
--name $functionappName \
--consumption-plan-location $location \
--storage-account $storageAccountName

```

| パラメーター | [説明] |
| -------------------- | --------------- |
| ResourceGroupName | このリソース グループ名は、前のチュートリアルで指定されたものです。 すべてのチュートリアル リソースをまとめてグループ化しておくことをお勧めします。 前のチュートリアルで使用したのと同じリソース グループを使用します。 | 
| location | この変数に、前のチュートリアルでリソース グループを作成するために使用したのと同じ場所を設定します。 | 
| functionappName | この変数に、新しい関数アプリの一意の名前を設定します。 たとえば、signalrfunctionapp22665120 です。 | 
| storageAccountName | 関数アプリのコードと設定を保持する新しいストレージ アカウントの名前を入力します。 | 



## <a name="configure-the-function-app"></a>Function App を構成する

このセクションでは、Azure SignalR Service リソースの接続文字列などのアプリ設定で関数アプリを構成します。 関数コードは、この設定を使用してチャット ルームに接続し、メッセージを発行します。 また、ローカル Git リポジトリからデプロイするための関数アプリも構成します。

次のスクリプトをコピーし、パラメーターの値を置き換えます。 更新したスクリプトを Azure Cloud Shell に貼り付け、**Enter** キーを押します。

```azurecli-interactive
#====================================================================
#=== Update these variables to match your resources.              === 
#====================================================================
ResourceGroupName=SignalRTestResources
functionappName=mysignalfunctionapp

#========================================================================
#=== Replace this value with the connection string for your           ===
#=== SignalR Service resource.                                        ===
#========================================================================
connstring="Endpoint=<service_endpoint>;AccessKey=<access_key>;"

# Add the SignalR Service connection string app setting
az functionapp config appsettings set --resource-group $ResourceGroupName \
    --name $functionappName \
    --setting "Azure:SignalR:ConnectionString=$connstring"

# configure for deployment from a local Git repository
az functionapp deployment source config-local-git --name $functionappName \
    --resource-group $ResourceGroupName

```

| パラメーター | [説明] |
| -------------------- | --------------- |
| ResourceGroupName | このリソース グループ名は、前のチュートリアルで指定されたものです。 すべてのチュートリアル リソースをまとめてグループ化しておくことをお勧めします。 前のチュートリアルで使用したのと同じリソース グループを使用します。 | 
| functionappName | この変数に、新しい関数アプリの一意の名前を設定します。 たとえば、signalrfunctionapp22665120 です。 | 
| connstring | SignalR Service リソースの接続文字列を入力します。 この接続文字列は、Azure Portal の SignalR Service リソース ページで、**[設定]** の **[キー]** をクリックして取得できます。 | 



最後のコマンドから返される Git デプロイ URL を書き留めておきます。 関数コードをデプロイするために、この URL を使用します。


## <a name="the-timer-function"></a>タイマー関数

タイマー関数のサンプルは、ダウンロードの */samples/Timer* ディレクトリ、または [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer) github リポジトリの複製にあります。 タイマー関数のコードは、*TimerFunction.cs* にあります。 このコードでは、既定の構成キー (*Azure:SignalR:ConnectionString*) に保存された接続文字列を使用して、ハブへのプロキシを作成します。 関数コードはサーバー側で実行されるため、通常のクライアントとして認証されることを必要とする理由はありません。 コードは接続文字列を使用できるように信頼されています。 このハブ プロキシを使用すると、関数コードは、ハブで定義されている任意のメソッドを呼び出すことができます。 コードは `BroadcastMessage` メソッドを呼び出して、トリガーが起動された時刻が含まれているメッセージを発行します。

関数コードのトリガーは、*timerTrigger* です。このトリガーは、*TimerFunction/function.json* 内のバインディングに定義されています。 これには、毎分の始めにタイマー トリガーが起動されるように設定する [CRON 式](https://wikipedia.org/wiki/Cron#CRON_expression)が含まれています。

```json
{
  "bindings": [
    {
      "type": "timerTrigger",
      "schedule": "0 * * * * *",
      "useMonitor": true,
      "runOnStartup": false,
      "name": "myTimer"
    }
  ],
  "disabled": false,
  "scriptFile": "../Timer.dll",
  "entryPoint": "Timer.TimerFunction.Run"
}
```


## <a name="building-the-timer-function"></a>タイマー関数のビルド

次の手順では、[.NET Core コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/dotnet/core/tools/) を使用して、関数をビルドし、デプロイ用に準備します。

1. ダウンロードの */samples/Timer* ディレクトリ、または [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) github リポジトリの複製に移動します。

2. 次のコマンドを使用して、NuGet パッケージを復元します。

        dotnet restore

3. 次のコマンドを使用して、*Timer* 関数アプリをビルドします。

        msbuild /p:Configuration=Release


## <a name="create-and-deploy-the-local-git-repo"></a>ローカル Git リポジトリを作成してデプロイする

1. Git shell で、ビルド サブディレクトリ */samples/Timer/bin/Release/net461* に移動します。

        cd ./AzureSignalR-samples/samples/Timer/bin/Release/net461

2. 次のコマンドを使用して、ディレクトリを新しい Git リポジトリとして初期化します。

        git init

3. ビルド ディレクトリのすべてのファイルに対する新しいコミットを追加します。

        git add -A
        git commit -v -a -m "Initial Timer function commit"        

4. 関数アプリの構成時に書き留めた Git デプロイ URL のリモート エンドポイントを追加します。

        git remote add Azure <enter your Git deployment URL>

5. 関数アプリをデプロイする

        git push Azure master

   コードがデプロイされると、タイマーはコードを実行するための毎分の起動を直ちに開始します。

## <a name="test-the-chat-app"></a>チャット アプリをテストする

チャット アプリケーションに移動すると、作成した Timer 関数が毎分の始めに時刻を報告するようになっています。

![サーバー メッセージを使用するチャット アプリ](./media/signalr-integrate-functions/signalr-functions-complete.png)



## <a name="clean-up-resources"></a>リソースのクリーンアップ

アプリケーションのテストを続ける場合は、作成したリソースを保持できます。

サンプル アプリケーションの使用を終える場合は、課金を避けるために Azure リソースを削除することができます。 

> [!IMPORTANT]
> いったん削除したリソース グループを元に戻すことはできません。リソース グループとそこに存在するすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 このサンプルのホストとなるリソースを、保持するリソースが含まれている既存のリソース グループ内に作成した場合は、リソース グループを削除するのではなく、個々のブレードから各リソースを個別に削除することができます。
> 
> 

[Azure ポータル](https://portal.azure.com) にサインインし、 **[リソース グループ]** をクリックします。

**[名前でフィルター]** ボックスにリソース グループの名前を入力します。 この記事の手順では、*SignalRTestResources* という名前のリソース グループを使用しました。 結果一覧でリソース グループの **[...]** をクリックし、**[リソース グループの削除]** をクリックします。

   
![削除](./media/signalr-integrate-functions/signalr-delete-resource-group.png)


リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにリソース グループの名前を入力し、**[削除]** をクリックします。
   
しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure 関数トリガーに基づいてクライアントに更新をプッシュする Azure 関数を統合する方法について学習しました。 Azure SignalR Server の使用方法の詳細については、SignalR Service の Azure CLI サンプルを参照してください。

> [!div class="nextstepaction"]
> [Azure SignalR の CLI サンプル](./signalr-cli-samples.md)



