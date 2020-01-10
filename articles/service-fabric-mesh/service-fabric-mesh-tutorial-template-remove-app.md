---
title: チュートリアル - Azure Service Fabric Mesh で実行されているアプリを削除する
description: このチュートリアルでは、Service Fabric Mesh で実行されているアプリケーションを削除する方法について説明し、リソースを削除します。
author: dkkapur
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 4780f81d23f0183837d2aafb9a8e5e2c41faa1cf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351794"
---
# <a name="tutorial-remove-an-application-and-resources"></a>チュートリアル:アプリケーションとリソースを削除する

このチュートリアルは、シリーズの第 4 部です。 [Service Fabric Mesh に対し事前に展開されていた](service-fabric-mesh-tutorial-template-deploy-app.md)実行中のアプリケーションを削除する方法について説明します。 

シリーズの第 4 部では、次の方法を学習します。

> [!div class="checklist"]
> * Service Fabric Mesh で実行されているアプリを削除します
> * アプリケーション リソースを削除します

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [テンプレートを使用して Azure Service Fabric Mesh にアプリケーションをデプロイする](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Service Fabric Mesh で実行しているアプリケーションをスケーリングする](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Service Fabric Mesh で実行されているアプリケーションをアップグレードする](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * アプリケーションの削除

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)できます。

* [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) を開くか、または[Azure CLI と Service Fabric Mesh CLI をローカルにインストール](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli)します。

## <a name="delete-the-resource-group-and-all-the-resources"></a>リソース グループとすべてのリソースを削除します

不要になったら、作成したすべてのリソースを削除します。 以前は、Azure Container Registry インスタンスと Service Fabric Mesh アプリケーションのリソースをホストするために[、新しいリソース グループを作成しました](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry)。  関連付けられているリソースはすべて削除されますが、このリソース グループを削除することができます。

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>リソースを個別に削除する
ACR インスタンス、Service Fabric Mesh アプリケーション、およびネットワーク リソースを個別に削除することもできます。

ACR インスタンスを削除します：

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

Service Fabric Mesh アプリケーションを削除します：

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

ネットワークを削除します：
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>次のステップ

チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Service Fabric Mesh で実行されているアプリを削除します
> * アプリケーション リソースを削除します