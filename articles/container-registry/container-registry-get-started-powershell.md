---
title: クイック スタート - Azure でのプライベート Docker レジストリの作成 - PowerShell
description: Azure で PowerShell を使用してプライベート Docker コンテナー レジストリを作成する方法を簡単に説明します。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 05/08/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 7dcdca594949a3b20000b31db681370ee4f9eac3
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255287"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>クイック スタート:Azure PowerShell を使用してプライベート コンテナー レジストリを作成する

Azure Container Registry は、Docker コンテナー イメージのビルド、保管、サポートをするための、管理されたプライベート Docker コンテナー レジストリ サービスです。 このクイックスタートでは、PowerShell を使用して Azure Container Registry を作成する方法を簡単に説明します。 レジストリの作成後、コンテナー イメージをレジストリにプッシュして、その後コンテナーをレジストリから Azure Container Instances (ACI) にデプロイします。

## <a name="prerequisites"></a>前提条件

このクイックスタートには、Azure PowerShell モジュール バージョン 5.7.0 以降が必要です。 `Get-Module -ListAvailable AzureRM` を実行して、インストールされたバージョンを判断します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

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

## <a name="log-in-to-registry"></a>レジストリへのログイン

コンテナー イメージをプッシュしたりプルしたりするには、あらかじめレジストリにログインしておく必要があります。 [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential] コマンドを使用して、まずはレジストリの管理者の資格情報を取得します。

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

次に、[docker login][docker-login] を実行してログインします。

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

ログインに成功すると、`Login Succeeded` が返されます。

```console
PS Azure:\> $creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
Login Succeeded
```

## <a name="push-image-to-registry"></a>レジストリにイメージをプッシュする

レジストリにログインしたので、コンテナー イメージをプッシュすることができます。 レジストリにプッシュできるイメージを取得するには、Docker Hub からパブリック [aci-helloworld][aci-helloworld-image] イメージをプルします。 [aci-helloworld][aci-helloworld-github] イメージは、Azure Container Instances のロゴを表示する静的な HTML ページを提供する、小規模な Node.js アプリケーションです。

```powershell
docker pull microsoft/aci-helloworld
```

イメージをプルすると、次のように出力されます。

```console
PS Azure:\> docker pull microsoft/aci-helloworld
Using default tag: latest
latest: Pulling from microsoft/aci-helloworld
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
3be0618266da: Pull complete
9e232827e52f: Pull complete
b53c152f538f: Pull complete
Digest: sha256:a3b2eb140e6881ca2c4df4d9c97bedda7468a5c17240d7c5d30a32850a2bc573
Status: Downloaded newer image for microsoft/aci-helloworld:latest
```

イメージを Azure コンテナー レジストリにプッシュするには、レジストリの完全修飾ドメイン名 (FQDN) を使用して、そのイメージにタグを付けておく必要があります。 Azure コンテナー レジストリの FQDN は、*\<registry-name\>.azurecr.io* の形式になっています。

完全なイメージ タグを変数に設定します。 ログイン サーバー、リポジトリ名 ("aci-helloworld")、イメージのバージョン ("v1") を含めます。

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
```

[docker tag][docker-tag] を使用してイメージにタグを付けます。

```powershell
docker tag microsoft/aci-helloworld $image
```

最後に、レジストリに [docker push][docker-push] します。

```powershell
docker push $image
```

Docker クライアントがイメージをプッシュすると、出力は次のようになります。

```console
PS Azure:\> docker push $image
The push refers to repository [myContainerRegistry007.azurecr.io/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

お疲れさまでした。 これで、最初のコンテナー イメージのレジストリへのプッシュが終了しました。

## <a name="deploy-image-to-aci"></a>ACI へのイメージのデプロイ

レジストリ内にイメージがあるので、Azure Container Instances に直接コンテナーをデプロイして、Azure で実行することができます。

まずは、レジストリの資格情報を *PSCredential* に変換します。 コンテナー インスタンスの作成に使用する `New-AzureRmContainerGroup` コマンドは、この形式である必要があります。

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

さらに、コンテナーの DNS 名ラベルは、作成する Azure のリージョン内で一意である必要があります。 生成された名前を変数に設定するには、次のコマンドを実行します。

```powershell
$dnsname = "aci-demo-" + (Get-Random -Maximum 9999)
```

最後に、[New-AzureRmContainerGroup][New-AzureRmContainerGroup] を実行して、1 つの CPU コアと 1 GB のメモリで、レジストリのイメージからコンテナーをデプロイします。

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
```

Azure から最初の応答が、対象コンテナーについての詳しい情報と共に返されます。最初はその状態は「Pending」です。

```console
PS Azure:\> New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials : {myContainerRegistry007}
RestartPolicy            : Always
IpAddress                : 40.117.255.198
DnsNameLabel             : aci-demo-8751
Fqdn                     : aci-demo-8751.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Linux
Volumes                  :
State                    : Pending
Events                   : {}
```

その状態を監視して、いつ実行されているかを判断するには、[Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] コマンドを数回実行します。 コンテナーは 1 分以内に開始します。

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

ここでは、コンテナーの ProvisioningState が最初は *Creating* で、その後実行されると *Succeeded* 状態に移行します。

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Creating
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Succeeded
```

## <a name="view-running-application"></a>実行中のアプリケーションを表示する

ACI へのデプロイが正常に完了して、コンテナーが実行されると、ブラウザーで完全修飾ドメイン名 (FQDN) に移動することで、Azure でアプリが実行しているのを確認できます。

[Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] でコンテナーの FQDN を取得します。


```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
```

コマンドの出力は、コンテナー インスタンスの FQDN です。

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
aci-demo-8571.eastus.azurecontainer.io
```

FQDN を手にしたら、ブラウザーで FDQN に移動します。

![ブラウザーでの Hello World アプリ][qs-psh-01-running-app]

お疲れさまでした。 Azure では、プライベート Azure コンテナー レジストリのコンテナー イメージから直接デプロイされたコンテナーがアプリケーションを実行しています。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイックスタートで作成したリソースでの作業が完了したら、[Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] コマンドを使用してリソース グループ、コンテナー レジストリ、コンテナー インスタンスを削除します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure CLI を使って Azure Container Registry を作成し、そのインスタンスを Azure Container Instances で起動しました。 Azure Container Instances のチュートリアルに進んで、ACI についての理解を深めましょう。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[aci-helloworld-github]: https://github.com/Azure-Samples/aci-helloworld
[aci-helloworld-image]: https://hub.docker.com/r/microsoft/aci-helloworld/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[New-AzureRMContainerRegistry]: /powershell/module/azurerm.containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-psh-01-running-app]: ./media/container-registry-get-started-powershell/qs-psh-01-running-app.png
