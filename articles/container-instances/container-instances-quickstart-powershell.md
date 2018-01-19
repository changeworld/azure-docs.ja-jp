---
title: "クイック スタート - PowerShell を使用した最初の Azure Container Instances コンテナーの作成"
description: "PowerShell を使用して Windows コンテナー インスタンスを作成することによって、Azure Container Instances の使用を開始します。"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 01/02/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 18a342fed7e99e82082764d6f5a3429a3ce794b7
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2018
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Azure Container Instances での最初のコンテナーの作成

Azure Container Instances を使用すると、仮想マシンをプロビジョニングしたり、より高度なレベルのサービスを採用したりしなくても、Azure の Docker コンテナーを簡単に作成、管理できます。

このクイック スタートでは、Azure で Windows コンテナーを作成し、パブリック IP アドレスを使用してインターネットに公開します。 この操作は、1 つのコマンドで完結します。 少し時間が経てば、以下のように、実行中のアプリケーションをブラウザーで確認できるようになります。

![Azure Container Instances を使用してデプロイされたアプリのブラウザーでの表示][qs-powershell-01]

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 3.6 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループの作成

[New-AzureRmResourceGroup][New-AzureRmResourceGroup] を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>コンテナーを作成する

コンテナーを作成するには、名前、Docker イメージ、および Azure リソース グループを [New-AzureRmContainerGroup][New-AzureRmContainerGroup] コマンドレットに指定します。 コンテナーは、必要に応じて、パブリック IP アドレスを使用してインターネットに公開できます。 この場合は、インターネット インフォメーション サービス (IIS) を実行している Nano サーバー コンテナーを使用します。

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

数秒以内に、あなたのリクエストに対する応答を得るでしょう。 最初に、コンテナーは**作成中**の状態ですが、1 ～ 2 分以内に起動されます。 [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] コマンドレットを使用して状態を確認することができます。

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

コンテナーのプロビジョニング状態と IP アドレスがコマンドレットの出力に表示されます。

```
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/12345678-1234-1234-1234-12345678abcd/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            :
IpAddress                : 40.71.248.73
Ports                    : {80}
OsType                   : Windows
Volumes                  :
```

コンテナーの **ProvisioningState** が `Succeeded` に移行すると、指定した IP アドレスを使用して、お使いのブラウザーでこのコンテナーにアクセスできます。

![ブラウザーに表示された、Azure Container Instances を使用してデプロイされた IIS][qs-powershell-01]

## <a name="delete-the-container"></a>コンテナーを削除する

コンテナーを使い終えたら、[Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup] コマンドレットを使用してそのコンテナーを削除することができます。

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、事前に構築された Windows コンテナーを Azure Container Instances で起動しました。 Azure Container Registry を使用してコンテナーのビルドと Azure Container Instances へのデプロイを自分で試す場合は、Azure Container Instances のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
