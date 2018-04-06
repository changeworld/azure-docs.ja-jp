---
title: クイック スタート - PowerShell を使用した Azure でのプライベート Docker レジストリの作成
description: PowerShell を使用してプライベート Docker コンテナー レジストリを作成する方法を簡単に説明します。
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 9603ac779c7dbc640a7c24856c32f04edbac849d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>クイック スタート: PowerShell を使用して Azure Container Registry を作成する

Azure Container Registry は、プライベート Docker コンテナー イメージを保管するための管理された Docker コンテナー レジストリ サービスです。 このガイドでは、PowerShell を使って Azure Container Registry インスタンスを作成し、そのレジストリにコンテナー イメージをプッシュして、最後に Azure Container Instances (ACI) に対してレジストリからコンテナーをデプロイする方法について詳しく説明します。

このクイック スタートには、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

Docker もローカルにインストールする必要があります。 Docker では、[Mac][docker-mac]、[Windows][docker-windows]、または [Linux][docker-linux] システムで Docker を簡単に構成できるパッケージが提供されています。

## <a name="log-in-to-azure"></a>Azure にログインする

`Login-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Create resource group

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

[New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry) コマンドを使用して ACR インスタンスを作成します。

レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 次の例では、*myContainerRegistry007* を使用します。 これを一意の値に更新します。

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>ACR にログインする

コンテナー イメージをプッシュしたりプルしたりするには、あらかじめ ACR インスタンスにログインしておく必要があります。 まず、[Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) コマンドを使用して、ACR インスタンスの管理者の資格情報を取得します。

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

次に、[docker login][docker-login] コマンドを使用して ACR インスタンスにログインします。

```powershell
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

このコマンドは、完了すると `Login Succeeded` を返します。 `--password-stdin` パラメーターの使用を推奨するセキュリティ警告が表示されることもあります。 このパラメーターの使用について、ここでは説明していませんが、このベスト プラクティスに従うことをお勧めします。 詳細については、[docker login][docker-login] コマンドのリファレンスを参照してください。

## <a name="push-image-to-acr"></a>ACR にイメージをプッシュする

Azure Container Registry にイメージをプッシュするには、まずイメージを用意する必要があります。 必要な場合は、次のコマンドを実行して、事前に作成したイメージを Docker Hub からプルします。

```powershell
docker pull microsoft/aci-helloworld
```

イメージは、ACR ログイン サーバー名でタグ付けする必要があります。 そのためには、[docker tag][docker-tag] コマンドを使用します。

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
docker tag microsoft/aci-helloworld $image
```

最後に、[docker push][docker-push] を使用して、ACR にイメージをプッシュします。

```powershell
docker push $image
```

## <a name="deploy-image-to-aci"></a>ACI へのイメージのデプロイ
Azure Container Instances (ACI) にコンテナー インスタンスとしてイメージをデプロイするにはまず、そのレジストリの資格情報を PSCredential に変換します。

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

1 CPU コアおよび 1 GB メモリで、コンテナー レジストリからコンテナー イメージをデプロイするには、次のコマンドを実行します。

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name mycontainer -Image $image -Cpu 1 -MemoryInGB 1 -IpAddressType public -Port 80 -RegistryCredential $pscred
```

Azure Resource Manager から最初の応答が、対象コンテナーについての詳しい情報と共に返されます。 コンテナーのステータスを監視して、実行されるタイミングを調べるために、[Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] を繰り返します。 通常は 1 分かかりません。

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

出力例: `Succeeded`

## <a name="view-the-application"></a>アプリケーションを表示する
ACI へのデプロイが正常に完了したら、[Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] コマンドを使用して、コンテナーのパブリック IP アドレスを取得します。

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).IpAddress
```

出力例: `"13.72.74.222"`

実行中のアプリケーションを表示するには、お使いのブラウザーでパブリック IP アドレスにアクセスします。 これは、次のようになります。

![ブラウザーでの Hello World アプリ][qs-portal-15]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] コマンドを使用して、リソース グループ、Azure Container Registry、およびすべての Azure Container Instances を削除できます。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure CLI を使って Azure Container Registry を作成し、そのインスタンスを Azure Container Instances で起動しました。 Azure Container Instances のチュートリアルに進んで、ACI についての理解を深めましょう。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png
