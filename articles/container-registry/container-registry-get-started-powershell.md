---
title: クイック スタート - レジストリを作成する - PowerShell
description: Azure Container Registry で PowerShell を使用してプライベート Docker レジストリを作成する方法を簡単に説明します
ms.date: 06/03/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, mvc, mode-api
ms.openlocfilehash: 448b692adb603c884f1034bf5d7234e459ce5f66
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043492"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>クイック スタート:Azure PowerShell を使用してプライベート コンテナー レジストリを作成する

Azure Container Registry は、コンテナー イメージおよび関連アーティクルのビルド、保管、管理をするための、プライベート レジストリ サービスです。 このクイックスタートでは、Azure PowerShell を使用して Azure コンテナー レジストリ インスタンスを作成します。 次に、Docker コマンドを使用してコンテナー イメージをレジストリにプッシュし、最後にレジストリからイメージをプルして実行します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このクイック スタートには、Azure PowerShell モジュールが必要です。 `Get-Module -ListAvailable Az` を実行して、インストールされたバージョンを判断します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

Docker もローカルにインストールする必要があります。 Docker には [macOS][docker-mac]、[Windows][docker-windows]、[Linux][docker-linux] システム用のパッケージがあります。

Azure Cloud Shell には、必要な Docker コンポーネント (`dockerd` デーモン) すべてが含まれていないため、このクイックスタートで Cloud Shell を使用することはできません。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Connect-AzAccount][Connect-AzAccount] コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>リソース グループの作成

Azure での認証が済んだら、[New-AzResourceGroup][New-AzResourceGroup] を使用してリソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>コンテナー レジストリを作成する

次に、[New-AzContainerRegistry][New-AzContainerRegistry] コマンドを使用して新しいリソース グループにコンテナー レジストリを作成します。

レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 次の例では、「myContainerRegistry007」という名前のレジストリを作成しています。 以下のコマンドで *myContainerRegistry007* を置換後実行して、レジストリを作成します。

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

[!INCLUDE [container-registry-quickstart-sku](../../includes/container-registry-quickstart-sku.md)]

## <a name="log-in-to-registry"></a>レジストリへのログイン

コンテナー イメージをプッシュしたりプルしたりするには、[Connect-AzContainerRegistry][connect-azcontainerregistry] コマンドレットを使用してあらかじめレジストリにログインしておく必要があります。 次の例では、`Connect-AzAccount` コマンドレットを使用して Azure に認証する際、ログインで使用したのと同じ資格情報を使用しています。

> [!NOTE]
> 次の例で、`$registry.Name` の値はリソース名です。完全修飾レジストリ名ではありません。

```powershell
Connect-AzContainerRegistry -Name $registry.Name
```

このコマンドは、完了すると `Login Succeeded` を返します。

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイック スタートで作成したリソースでの作業が完了したら、[Remove-AzResourceGroup][Remove-AzResourceGroup] コマンドを使用して、リソース グループ、コンテナー レジストリ、そこに格納されているコンテナー イメージを削除します。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure PowerShell を使って Azure Container Registry を作成し、コンテナー イメージをプッシュしてから、レジストリからイメージをプルして実行しました。 Azure Container Registry のチュートリアルに進んで、ACR についての理解を深めましょう。

> [!div class="nextstepaction"]
> [Azure Container Registry のチュートリアル][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry タスクのチュートリアル][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[connect-azcontainerregistry]: /powershell/module/az.containerregistry/connect-azcontainerregistry
