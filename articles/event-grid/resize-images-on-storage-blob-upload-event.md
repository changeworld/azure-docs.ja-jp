---
title: Azure Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する | Microsoft Docs
description: Azure Event Grid は、Azure Storage での BLOB アップロードをトリガーできます。 これを使って、Azure Storage にアップロードされたイメージ ファイルを、サイズ変更や他の改善のために Azure Functions などの他のサービスに送信することができます。
services: event-grid, functions
author: ggailey777
manager: cfowler
editor: ''
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 922c87f2d577aff86d51a1fde53f221ebd2fa82c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446692"
---
# <a name="automate-resizing-uploaded-images-using-event-grid"></a>Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する

[Azure Event Grid](overview.md) は、クラウドのイベント処理サービスです。 Event Grid を使うと、Azure サービスまたはサード パーティのリソースによって生成されるイベントのサブスクリプションを作成できます。  

このチュートリアルは、ストレージ チュートリアル シリーズの第 2 部です。 [前のストレージ チュートリアル][previous-tutorial]に、Azure Event Grid と Azure Functions を使うサーバーレスの自動サムネイル生成機能を追加します。 Event Grid により、[Azure Functions](..\azure-functions\functions-overview.md) は [Azure Blob Storage](..\storage\blobs\storage-blobs-introduction.md) のイベントに応答して、アップロードされたイメージのサムネイルを生成できます。 Blob Storage の作成イベントに対して、イベント サブスクリプションが作成されます。 特定の Blob Storage コンテナーに BLOB が追加されると、関数エンドポイントが呼び出されます。 Event Grid から関数バインドに渡されたデータが、BLOB へのアクセスとサムネイル イメージの生成に使われます。

既存のイメージ アップロード アプリにサイズ変更機能を追加するには、Azure CLI と Azure Portal を使います。

![Microsoft Edge ブラウザーでの発行された Web アプリ](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 一般 Azure ストレージ アカウントを作成します
> * Azure Functions を使ってサーバーレス コードをデプロイします
> * Event Grid で Blob Storage のイベント サブスクリプションを作成します

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

前の Blob Storage チュートリアル「[Upload image data in the cloud with Azure Storage][previous-tutorial]」(Azure Storage でクラウドにイメージ データをアップロードする) を完了している必要があります。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.14 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

Cloud Shell を使用していない場合は、先に `az login` でサインインする必要があります。

## <a name="create-an-azure-storage-account"></a>Azure Storage アカウントの作成

Azure Functions には、一般的なストレージ アカウントが必要です。 [az storage account create](/cli/azure/storage/account#az-storage-account-create) コマンドを使って、リソース グループ内に一般的なストレージ アカウントを別に作成します。

ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 

次のコマンドで、`<general_storage_account>` プレースホルダーを一般的なストレージ アカウントのグローバルで一意な名前に置き換えます。 

```azurecli-interactive
az storage account create --name <general_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind storage
```

## <a name="create-a-function-app"></a>Function App を作成する  

関数の実行をホストするには Function App が必要です。 Function App は、関数コードのサーバーレス実行の環境を提供します。 Function App の作成には、[az functionapp create](/cli/azure/functionapp#az-functionapp-create) コマンドを使用します。 

次のコマンドで、`<function_app>` プレースホルダーを独自の一意の Function App 名に置き換えます。 この関数アプリ名は、関数アプリの既定の DNS ドメインとして使用されます。そのため、名前は Azure のすべてのアプリ間で一意である必要があります。 `<general_storage_account>` には、作成した一般的なストレージ アカウントの名前を使用します。

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <general_storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westcentralus
```

ここで、[前のチュートリアル][previous-tutorial]で作成した BLOB ストレージ アカウントに接続するように関数アプリを構成する必要があります。

## <a name="configure-the-function-app"></a>Function App を構成する

この関数では、BLOB ストレージ アカウントに接続するために接続文字列が必要です。 次の手順で Azure にデプロイする関数コードは、アプリ設定 myblobstorage_STORAGE で接続文字列を探し、アプリ設定 myContainerName でサムネイル イメージ コンテナー名を探します。 [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string) コマンドで接続文字列を取得します。 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) コマンドを使用して、アプリケーション設定を行います。

次の CLI コマンドの `<blob_storage_account>` は、前のチュートリアルで作成した BLOB ストレージ アカウントの名前です。

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <blob_storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings myblobstorage_STORAGE=$storageConnectionString \
myContainerName=thumbnails
```

この Function App に関数コード プロジェクトをデプロイできるようになります。

## <a name="deploy-the-function-code"></a>関数コードをデプロイする 

# <a name="nettabnet"></a>[\.NET](#tab/net)

C# スクリプト (.csx) のサイズ変更のサンプルは、[GitHub](https://github.com/Azure-Samples/function-image-upload-resize) で入手できます。 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config) コマンドを使って、この Functions コード プロジェクトを関数アプリにデプロイします。 

次のコマンドの `<function_app>` は、先ほど作成した関数アプリの名前です。

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
Node.js のサイズ変更関数のサンプルは、[GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node) で入手できます。 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config) コマンドを使って、この Functions コード プロジェクトを関数アプリにデプロイします。 


次のコマンドの `<function_app>` は、先ほど作成した関数アプリの名前です。

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node
```
---

イメージのサイズ変更関数は、Event Grid サービスからその関数に送信される HTTP 要求によってトリガーされます。 Event Grid には、イベントのサブスクリプションを作成して関数の URL でこれらの通知を取得することを指示します。 このチュートリアルでは、BLOB によって作成されたイベントをサブスクライブします。

Event Grid の通知から関数に渡されるデータには、BLOB の URL が含まれます。 その URL は、Blob Storage からアップロードされたイメージを取得するために入力バインドに渡されます。 関数はサムネイル イメージを生成し、結果のストリームを Blob Storage 内の別のコンテナーに書き込みます。 

このプロジェクトでは、トリガーの種類として `EventGridTrigger` が使用されています。 汎用の HTTP トリガーよりも Event Grid トリガーを使用することをお勧めします。 Event Grid では、Event Grid 関数トリガーが自動的に検証されます。 汎用 HTTP トリガーの場合は、[検証応答](security-authentication.md#webhook-event-delivery)を実装する必要があります。

この関数の詳細については、[function.json ファイルと run.csx ファイル](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/imageresizerfunc)を参照してください。
 
関数プロジェクトのコードは、パブリック サンプル リポジトリから直接デプロイされます。 Azure Functions のデプロイ オプションについて詳しくは、「[Azure Functions の継続的なデプロイ](../azure-functions/functions-continuous-deployment.md)」をご覧ください。

## <a name="create-an-event-subscription"></a>イベント サブスクリプションの作成

イベント サブスクリプションは、どのプロバイダー生成イベントを特定のエンドポイントに送信するかを示します。 この場合、エンドポイントは関数によって公開されます。 Azure Portal で関数に通知を送信するイベント サブスクリプションを作成するには、次の手順に従います。 

1. [Azure Portal](https://portal.azure.com) で、左下にある矢印をクリックしてすべてのサービスを展開し、**[フィルター]** フィールドに「*functions*」と入力して、**[Function App]** を選択します。 

    ![Azure Portal で Function App を参照する](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. 目的の関数アプリを展開して、**imageresizerfunc** 関数を選び、**[Event Grid サブスクリプションの追加]** を選びます。

    ![Azure Portal で Function App を参照する](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. 次の表で指定されているようにイベント サブスクリプションを設定します。
    
    ![Azure Portal で関数からイベント サブスクリプションを作成する](./media/resize-images-on-storage-blob-upload-event/event-subscription-create-flow.png)

    | Setting      | 推奨値  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名前** | imageresizersub | 新しいイベント サブスクリプションを示す名前。 | 
    | **トピックの種類** |  ストレージ アカウント | ストレージ アカウント イベント プロバイダーを選びます。 | 
    | **サブスクリプション** | お使いの Azure サブスクリプション | 既定では、現在の Azure サブスクリプションが選択されています。   |
    | **[リソース グループ]** | myResourceGroup | **[既存のものを使用]** を選び、このチュートリアルで使っているリソース グループを選びます。  |
    | **インスタンス** |  お使いの BLOB ストレージ アカウント |  作成した Blob Storage アカウントを選びます。 |
    | **イベントの種類** | Blob created (作成された BLOB) | **[Blob created]\(作成された BLOB\)** 以外のすべての種類をオフにします。 `Microsoft.Storage.BlobCreated` のイベントの種類のみが関数に渡されます。| 
    | **サブスクライバーの種類** |  webhook |  選択肢は、webhook または Event Hubs です。 |
    | **サブスクライバー エンドポイント** | 自動生成 | 自動的に生成されるエンドポイントの URL を使います。 | 
    | **プレフィックス フィルター** | /blobServices/default/containers/images/blobs/ | **images** コンテナーのストレージ イベントだけになるようにフィルター処理します。| 

4. **[作成]** をクリックしてイベント サブスクリプションを追加します。 これにより、BLOB が *images* コンテナーに追加されたときに `imageresizerfunc` をトリガーするイベント サブスクリプションが作成されます。 この関数によって、画像は、サイズが変更され、*thumbnails* コンテナーに追加されます。

バックエンド サービスの構成が済んだので、サンプル Web アプリでイメージ サイズ変更の機能をテストします。 

## <a name="test-the-sample-app"></a>サンプル アプリをテストする

Web アプリでイメージのサイズ変更をテストするには、公開したアプリの URL を参照します。 Web アプリの既定の URL は、`https://<web_app>.azurewebsites.net` です。

**[Upload photos]** 領域をクリックし、ファイルを選んでアップロードします。 この領域に写真をドラッグしてもかまいません。 

アップロードされたイメージが消えた後、アップロードされたイメージのコピーが **[Generated thumbnails]** 領域に表示されることを確認します。 この画像は、関数によってサイズが変更され、*thumbnails* コンテナーに追加された後、Web クライアントによってダウンロードされたものです。

![Microsoft Edge ブラウザーでの発行された Web アプリ](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 一般 Azure ストレージ アカウントを作成します
> * Azure Functions を使ってサーバーレス コードをデプロイします
> * Event Grid で Blob Storage のイベント サブスクリプションを作成します

ストレージ チュートリアル シリーズの第 3 部に進み、ストレージ アカウントへのアクセスをセキュリティで保護する方法について学習します。

> [!div class="nextstepaction"]
> [クラウド内のアプリケーションのデータへのアクセスをセキュリティで保護する](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Event Grid について詳しくは、「[Azure Event Grid の概要](overview.md)」をご覧ください。 
+ Azure Functions を利用するもう 1 つのチュートリアルを試すには、「[Azure Logic Apps と統合される関数を作成する](..\azure-functions\functions-twitter-email.md)」をご覧ください。 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
