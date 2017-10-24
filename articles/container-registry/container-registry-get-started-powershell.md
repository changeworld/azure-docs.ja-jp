---
title: "クイック スタート - PowerShell を使用した Azure でのプライベート Docker レジストリの作成"
description: "PowerShell を使用してプライベート Docker コンテナー レジストリを作成する方法を簡単に説明します。"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: nepeters
ms.openlocfilehash: 15046d1d2aabafd72df590233f416dd266c661de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-container-registry-using-powershell"></a>PowerShell を使用して Azure Container Registry を作成する

Azure Container Registry は、プライベート Docker コンテナー イメージを保管するための管理された Docker コンテナー レジストリ サービスです。 このガイドでは、Azure Container Registry インスタンスを PowerShell で作成する方法について詳しく説明します。

このクイック スタートには、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

Docker もローカルにインストールする必要があります。 Docker では、[Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、または [Linux](https://docs.docker.com/engine/installation/#supported-platforms) システムで Docker を簡単に構成できるパッケージが提供されています。

## <a name="log-in-to-azure"></a>Azure へのログイン

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

レジストリの名前は**一意である必要があります**。 次の例では、*myContainerRegistry007* を使用します。 これを一意の値に更新します。

```PowerShell
$Registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>ACR へのログイン

コンテナー イメージをプッシュしたりプルしたりするには、あらかじめ ACR インスタンスにログインしておく必要があります。 まず、[Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) コマンドを使用して、ACR インスタンスの管理者の資格情報を取得します。

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $Registry
```

次に、[docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドを使用して ACR インスタンスにログインします。

```bash
docker login $Registry.LoginServer -u $creds.Username -p $creds.Password
```

このコマンドは、完了すると "Login Succeeded (ログインに成功しました)" というメッセージを返します。

## <a name="push-image-to-acr"></a>ACR へのイメージのプッシュ

Azure Container Registry にイメージをプッシュするには、まずイメージを用意する必要があります。 必要な場合は、次のコマンドを実行して、事前に作成したイメージを Docker Hub からプルします。

```bash
docker pull microsoft/aci-helloworld
```

イメージは、ACR ログイン サーバー名でタグ付けする必要があります。 ACR インスタンスのログイン サーバー名を返す [Get-AzureRmContainerRegistry](/powershell/module/containerregistry/Get-AzureRmContainerRegistry) コマンドを実行します。

```powershell` Get-AzureRmContainerRegistry | Select Loginserver
```

Tag the image using the [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) command. Replace *acrLoginServer* with the login server name of your ACR instance.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

最後に、[docker push](https://docs.docker.com/engine/reference/commandline/push/) を使用して、ACR インスタンスにイメージをプッシュします。 *acrLoginServer* を ACR インスタンスのログイン サーバー名で置き換えます。

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドを使用して、リソース グループ、ACR インスタンス、およびすべてのコンテナー イメージを削除できます。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure CLI を使用して Azure Container Registry を作成しました。 Azure Container Instances と一緒に Azure Container Registry を使用する場合は、Azure Container Instances のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル](../container-instances/container-instances-tutorial-prepare-app.md)