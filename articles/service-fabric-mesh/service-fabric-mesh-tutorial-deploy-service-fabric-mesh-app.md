---
title: チュートリアル - Service Fabric mesh アプリケーションを Service Fabric mesh にデプロイする | Microsoft Docs
description: バックエンド Web サービスと通信する ASP.NET Core Web サイトで構成される Azure Service Fabric mesh アプリケーションを発行する方法を説明します。
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: f9dea759f6556bc521dda4efbd27176f1e06452b
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126577"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-web-application"></a>チュートリアル: Service Fabric mesh Web アプリケーションをデプロイする

このチュートリアルはシリーズの第 3 部です。ここでは、Azure Service Fabric mesh Web アプリケーションを Visual Studio から直接 発行する方法について説明します。

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * アプリを Azure に発行する
> * アプリケーションのデプロイの状態を確認する
> * サブスクリプションに現在デプロイされているすべてのアプリケーションを表示する
> * アプリケーション ログを表示する
> * アプリで使用されているリソースをクリーンアップする

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [Service Fabric mesh Web アプリケーションを作成する](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [アプリをローカルでデバッグする](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * アプリを Azure に発行する

ASP.NET Web フロントエンドと ASP.NET Core Web API バックエンド サービスを含む Azure Service Fabric mesh アプリを作成する方法を学びます。 次に、ローカル開発クラスターでアプリをデバッグし、アプリを Azure に発行します。 作業が完了すると、Service Fabric mesh Web アプリケーションでサービス間の呼び出しを行う方法を示す簡単な To Do アプリが作成されます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)できます。

* Service Fabric ランタイム、SDK、Docker、Visual Studio 2017 がインストールされた[開発環境が設定](service-fabric-mesh-howto-setup-developer-environment-sdk.md)されていることを確認します。

## <a name="download-the-to-do-sample-application"></a>To Do サンプル アプリケーションをダウンロードする

[このチュートリアル シリーズの第 2 部](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)で To Do サンプル アプリケーションを作成しなかった場合は、ダウンロードできます。 コマンド ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

アプリケーションは `src\todolistapp` ディレクトリにあります。

## <a name="publish-to-azure"></a>Azure に発行する

Service Fabric mesh プロジェクトを Azure に発行するには、Visual Studio で **[ServiceFabricMeshApp]** を右クリックし、**[発行...]** を選択します。

**[Service Fabric アプリケーションの発行]** ダイアログが表示されます。

![Visual Studio - Service Fabric mesh の発行ダイアログ](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Azure アカウントとサブスクリプションを選択します。 **[場所]** を選択します。 この記事では、**[米国東部]** を使用します。

**[リソース グループ]** で、**[\<新しいリソース グループの作成...>]** を選択します。 新しいリソース グループを作成するダイアログが表示されます。 この記事では、場所として **[米国東部]** を使用し、グループに **sfmeshTutorial1RG** という名前を付けます (組織に同じサブスクリプションを使用する複数のユーザーがいる場合は、一意のグループ名を選択します)。  **[作成]** をクリックしてリソース グループを作成し、発行ダイアログに戻ります。

![Visual Studio - Service Fabric mesh の新しいリソース グループ ダイアログ](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

**[Service Fabric アプリケーションの発行]** ダイアログに戻り、**[Azure Container Registry]** の **[\<Create New Container Registry...>]\(<新しいコンテナー レジストリの作成...>\)** を選択します。 **[コンテナー レジストリの作成]** ダイアログで、**[コンテナー レジストリ名]** に一意の名前を使用します。 **場所**を指定します (このチュートリアルでは、**[米国東部]** を使用します)。 前の手順で作成した**リソース グループ**をドロップダウンで選択します (例: **sfmeshTutorial1RG**)。 **[SKU]** を **[Basic]** に設定し、**[作成]** をクリックして発行ダイアログに戻ります。

![Visual Studio - Service Fabric mesh の新しいリソース グループ ダイアログ](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

リソース プロバイダーがサブスクリプションに登録されていないことを示すエラーが表示された場合は、登録できます。 まず、サブスクリプションでリソース プロバイダーが使用可能かどうかを確認します。

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

コンテナー レジストリ プロバイダー (`Microsoft.ContainerRegistry`) が使用可能な場合は、PowerShell から登録します。

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

発行ダイアログで **[発行]** をクリックして、Service Fabric アプリケーションを Azure にデプロイします。

Azure に初めて発行したときは、Docker イメージが Azure Container Registry (ACR) にプッシュされます。イメージのサイズによっては、この処理に時間がかかります。 同じプロジェクトの以降の発行は高速になります。 Visual Studio の**出力**ウィンドウで **[Service Fabric Tools]** ウィンドウを選択することで、デプロイの進行状況を監視できます。 デプロイが完了すると、**Service Fabric Tools** の出力に、アプリケーションの IP アドレスとポートが URL の形式で表示されます。

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Web ブラウザーを開き、その URL に移動して、Azure で実行されている Web サイトを表示します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

残りの手順は、Azure Cloud Shell または Azure CLI のローカル インストールを使用して実行できます。

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.35 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 最新バージョンの CLI をインストールするか、最新バージョンにアップグレードする場合は、「[Azure CLI 2.0 のインストール][azure-cli-install]」をご覧ください。

## <a name="install-the-az-mesh-cli"></a>az mesh cli をインストールする
CLI プロンプト

1) Azure Service Fabric mesh CLI モジュールの以前のインストールを削除します。

```cli
az extension remove --name mesh
```

2)  Azure Service Fabric mesh CLI 拡張モジュールをインストールします。 プレビューでは、Azure Service Fabric mesh CLI は Azure CLI の拡張機能として記述されていますが、パブリック プレビューでは Azure CLI の一部として出荷されます。

```cli
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

## <a name="check-application-deployment-status"></a>アプリケーションのデプロイの状態を確認する

この時点で、アプリケーションはデプロイされています。 `app show` コマンドを使用して、アプリケーションの状態を確認できます。 

このチュートリアルのアプリのアプリケーション名は `ServiceMeshApp` です。 次のコマンドを使用して、アプリケーションの詳細を収集します。

```azurecli-interactive
az mesh app show --resource-group $rg --name ServiceMeshApp
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>サブスクリプションに現在デプロイされているすべてのアプリケーションを表示する

"app list" コマンドを使用して、サブスクリプションにデプロイされているアプリケーションのリストを取得できます。

```cli
az mesh app list --output table
```

## <a name="see-the-application-logs"></a>アプリケーション ログを表示する

デプロイ済みのアプリケーションのログを確認します。

```azurecli-interactive
az mesh code-package-log get --resource-group $rg --application-name ServiceMeshApp --service-name todoservice --replica-name 0 --code-package-name ServiceMeshApp
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、作成したすべてのリソースを削除します。 ACR サービス リソースと Service Fabric mesh サービス リソースの両方をホストする新しいリソース グループを作成したので、このリソース グループを安全に削除できます。リソース グループを削除すると、関連するすべてのリソースが削除されます。

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

リソース グループは、[ポータルから](../azure-resource-manager/resource-group-portal.md#delete-resource-group-or-resources)削除することもできます。 

## <a name="next-steps"></a>次の手順

チュートリアルのこの部分で学習した内容は次のとおりです。
> [!div class="checklist"]
> * アプリを Azure に発行する
> * アプリケーションのデプロイの状態を確認する
> * サブスクリプションに現在デプロイされているすべてのアプリケーションを表示する
> * アプリケーション ログを表示する
> * アプリで使用されているリソースをクリーンアップする

Service Fabric mesh アプリケーションを Azure に発行したら、次のことを行ってみてください。

* [投票アプリ サンプル](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp)を調べて、サービス間通信の別の例を確認する
* [Service Fabric のリソースを確認する](service-fabric-mesh-service-fabric-resources.md)
* [Cloud Shell の概要を読む](https://docs.microsoft.com/azure/cloud-shell/overview)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest