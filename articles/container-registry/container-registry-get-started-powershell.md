---
title: クイック スタート - Azure でのプライベート Docker レジストリの作成 - PowerShell
description: Azure で PowerShell を使用してプライベート Docker コンテナー レジストリを作成する方法を簡単に説明します。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: b8ff8e671d51a148177e66b30225dd7536a48028
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299745"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>クイック スタート:Azure PowerShell を使用してプライベート コンテナー レジストリを作成する

Azure Container Registry は、Docker コンテナー イメージのビルド、保管、サポートをするための、管理されたプライベート Docker コンテナー レジストリ サービスです。 このクイックスタートでは、PowerShell を使用して Azure Container Registry を作成する方法を簡単に説明します。 次に、Docker コマンドを使用してコンテナー イメージをレジストリにプッシュし、最後にレジストリからイメージをプルして実行します。

## <a name="prerequisites"></a>前提条件

このクイックスタートには、Azure PowerShell モジュール バージョン 5.7.0 以降が必要です。 `Get-Module -ListAvailable AzureRM` を実行して、インストールされたバージョンを判断します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/azurerm/install-azurerm-ps)に関するページを参照してください。

Docker もローカルにインストールする必要があります。 Docker には [macOS][docker-mac]、[Windows][docker-windows]、[Linux] [docker-linux]システム用のパッケージがあります。

Azure Cloud Shell には、必要な Docker コンポーネント (`dockerd` デーモン) すべてが含まれていないため、このクイックスタートで Cloud Shell を使用することはできません。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Connect-AzureRmAccount][Connect-AzureRmAccount] コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>リソース グループの作成

一度 Azure で認証したら、[New-AzureRmResourceGroup][New-AzureRmResourceGroup] を使用してリソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>コンテナー レジストリを作成する

次に、新しいリソース グループに [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry] コマンドでコンテナー レジストリを作成します。

レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 次の例では、「myContainerRegistry007」という名前のレジストリを作成しています。 以下のコマンドで *myContainerRegistry007* を置換後実行して、レジストリを作成します。

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

このクイック スタートでは、*Basic* レジストリを作成します。これは、Azure Container Registry について学習している開発者にとって、コストが最適なオプションです。 利用可能なサービス レベルの詳細については、「[Container Registry の SKU][container-registry-skus]」を参照してください。

## <a name="log-in-to-registry"></a>レジストリへのログイン

コンテナー イメージをプッシュしたりプルしたりするには、あらかじめレジストリにログインしておく必要があります。 運用環境のシナリオでは、コンテナー レジストリにアクセスするために個人 ID またはサービス プリンシパルを使用する必要がありますが、このクイック スタートでは、作業を簡略化するために、[Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential] コマンドを使用して、レジストリ上の管理者ユーザーを有効にします。

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

次に、[docker login][docker-login] を実行してログインします。

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

このコマンドは、完了すると `Login Succeeded` を返します。

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートで作成したリソースでの作業が完了したら、[Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] コマンドを使用して、リソース グループ、コンテナー レジストリ、およびそこに格納されているコンテナー イメージを削除します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure PowerShell を使って Azure Container Registry を作成し、コンテナー イメージをプッシュしてから、レジストリからイメージをプルして実行しました。 Azure Container Registry のチュートリアルに進んで、ACR についての理解を深めましょう。

> [!div class="nextstepaction"]
> [Azure Container Registry のチュートリアル][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRMContainerRegistry]: /powershell/module/azurerm.containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md