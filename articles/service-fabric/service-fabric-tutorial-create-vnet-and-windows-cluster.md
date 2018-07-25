---
title: Azure に Windows Service Fabric クラスターを作成する | Microsoft Docs
description: このチュートリアルでは、PowerShell を使用して Windows Service Fabric クラスターを Azure 仮想ネットワークやネットワーク セキュリティ グループにデプロイする方法を学習します。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/22/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: f795333e8af2f09800dedc0b65030c42165d6bbb
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068905"
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>チュートリアル: Azure 仮想ネットワークに Service Fabric Windows クラスターをデプロイする

このチュートリアルは、シリーズの第 1 部です。 PowerShell とテンプレートを使用して、Windows を実行している Service Fabric クラスターを [Azure 仮想ネットワーク (VNET)](../virtual-network/virtual-networks-overview.md) および[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)にデプロイする方法を説明します。 完了すると、クラウドで実行されているクラスターにアプリケーションをデプロイできるようになります。  Azure CLI を使用して Linux クラスターを作成する場合は、[Azure でのセキュリティで保護された Linux クラスターの作成](service-fabric-tutorial-create-vnet-and-linux-cluster.md)に関するページを参照してください。

このチュートリアルでは、本番環境シナリオについて説明します。  テスト目的で小規模のクラスターをすばやく作成する場合は、[3 ノードのテスト クラスターの作成](./scripts/service-fabric-powershell-create-test-cluster.md)に関するページを参照してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * PowerShell を使用して Azure に VNET を作成する
> * Key Vault を作成して証明書をアップロードする
> * セキュリティで保護された Service Fabric クラスターを Azure PowerShell に作成する
> * X.509 証明書を持つクラスターを保護する
> * PowerShell を使用してクラスターに接続する
> * クラスターの削除

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Azure にセキュリティで保護されたクラスターを作成する
> * [クラスターをスケールインまたはスケールアウトする](service-fabric-tutorial-scale-cluster.md)
> * [クラスターのランタイムをアップグレードする](service-fabric-tutorial-upgrade-cluster.md)
> * [Service Fabric を使用して API Management をデプロイする](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する
* [Service Fabric SDK と PowerShell モジュール](service-fabric-get-started.md)をインストールする
* [Azure PowerShell モジュール Version 4.1 以上](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)をインストールする

次の手順で、5 ノードの Service Fabric クラスターを作成します。 Azure で Service Fabric クラスターを実行することによって発生するコストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用します。

## <a name="key-concepts"></a>主要な概念


  [Service Fabric クラスター](service-fabric-deploy-anywhere.md)は、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 クラスターは多数のマシンにスケールできます。 クラスターに属しているコンピューターまたは VM をノードといいます。 それぞれのノードには、ノード名 (文字列) が割り当てられます。 ノードには、配置プロパティなどの特性があります。

ノード タイプは、クラスターの一連の仮想マシンのサイズ、数、およびプロパティを定義します。 すべての定義されたノード タイプは、セットとして仮想マシンのコレクションをデプロイおよび管理するために使用できる Azure 計算リソースである、[仮想マシン スケール セット](/azure/virtual-machine-scale-sets/)として設定されます。 各ノードの種類は、個別にスケール アップまたはスケール ダウンすることができ、さまざまなセットのポートを開き、異なる容量のメトリックスを持つことができます。 ノード タイプを使用して、一連の "フロントエンド" または "バックエンド" などのクラスター ノードの役割を定義します。  クラスターには複数のノード タイプを指定できますが、運用環境クラスターの場合、プライマリ ノード タイプには少なくとも 5 つの VM が必要です (テスト環境のクラスターの場合は 3 つ以上)。  [Service Fabric のシステム サービス](service-fabric-technical-overview.md#system-services)は、プライマリ ノード タイプのノードに配置されます。

クラスターはクラスター証明書で保護されています。 クラスター証明書は、ノード間通信のセキュリティ保護と、クラスター管理エンドポイントの管理クライアントへの認証に使用される X.509 証明書です。  このクラスター証明書は、HTTPS 管理 API および HTTPS 経由の Service Fabric Explorer に対しても SSL を提供します。 自己署名証明書は、テスト クラスターに役立ちます。  運用環境クラスターの場合は、証明機関 (CA) から取得した証明書をクラスター証明書として使用します。

クラスター証明書には、次の要件があります。

* 秘密キーが含まれている。
* キー交換のために作成されており、Personal Information Exchange (.pfx) ファイルにエクスポートできる。
* 保持しているサブジェクト名が、Service Fabric クラスターへのアクセスに使用されるドメインと一致している。 この整合性は、HTTPS 管理エンドポイントと Service Fabric Explorer 用の SSL を提供するために必要です。 証明機関 (CA) から .cloudapp.azure.com ドメインの SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書のサブジェクト名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

Azure Key Vault を使用して、Azure で Service Fabric クラスター用の証明書を管理します。  Azure にクラスターがデプロイされると、Service Fabric クラスターの作成担当 Azure リソース プロバイダーにより Key Vault から証明書が取得されてクラスター VM にインストールされます。

このチュートリアルでは、1 つのノード タイプで 5 つのノードを備えるクラスターを示します。 ただし、運用環境クラスター デプロイの場合、[キャパシティ プランニング](service-fabric-cluster-capacity.md)は重要なステップです。 ここでは、そのプロセスの一環として考慮すべき事柄の一部を取り上げます。

* クラスターで必要となるノードの数とノード タイプ
* ノード タイプごとの特性 (たとえば、サイズ、プライマリ/非プライマリ、インターネット接続、VM 数)
* クラスターの信頼性と耐久性の特徴

## <a name="download-and-explore-the-template"></a>テンプレートのダウンロードと詳細の確認

次の Resource Manager テンプレート ファイルをダウンロードします。

* [vnet-cluster.json][template]
* [vnet-cluster.parameters.json][parameters]

[vnet-cluster.json][template] を使用して、次の項目を含むいくつかのリソースをデプロイします。

### <a name="service-fabric-cluster"></a>Service Fabric クラスター

次の特性を備えた Windows クラスターがデプロイされます。

* 単一ノード型
* プライマリ ノード型に 5 つのノード (テンプレート パラメーターで構成可能)
* OS: コンテナー搭載 Windows Server 2016 Datacenter (テンプレート パラメーターで構成可能)
* 証明書の保護 (テンプレート パラメーターで構成可能)
* [リバース プロキシ](service-fabric-reverseproxy.md)が有効
* [DNS サービス](service-fabric-dnsservice.md)が有効
* ブロンズ[持続性レベル](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) (テンプレート パラメーターで構成可能)
 * シルバー[信頼性レベル](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) (テンプレート パラメーターで構成可能)
* クライアント接続エンドポイント: 19000 (テンプレート パラメーターで構成可能)
* HTTP ゲートウェイ エンドポイント: 19080 (テンプレート パラメーターで構成可能)

### <a name="azure-load-balancer"></a>Azure Load Balancer

ロード バランサーがデプロイされ、次のポートのプローブと規則が設定されます。

* クライアント接続エンドポイント: 19000
* HTTP ゲートウェイ エンドポイント: 19080
* アプリケーション ポート: 80
* アプリケーション ポート: 443
* Service Fabric リバース プロキシ: 19081

他のアプリケーション ポートが必要な場合は、Microsoft.Network/loadBalancers resource リソースと Microsoft.Network/networkSecurityGroups リソースを調整してトラフィックを許可する必要があります。

### <a name="virtual-network-subnet-and-network-security-group"></a>仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループ

仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループの名前は、テンプレート パラメーターで宣言されます。  仮想ネットワークとサブネットのアドレス空間もテンプレート パラメーターで宣言されます。

* 仮想ネットワークのアドレス空間: 172.16.0.0/20
* Service Fabric サブネットのアドレス空間: 172.16.2.0/23

ネットワーク セキュリティ グループでは、次の受信トラフィック規則が有効になっています。 テンプレートの変数を変更することで、ポートの値を変更できます。

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* ノード間通信 - 1025、1026、1027
* 一時的なポート範囲 - 49152 ～ 65534 (256 ポート以上が必要)
* アプリケーション用のポート: 80 および 443
* アプリケーションのポート範囲 - 49152 ～ 65534 (サービス間通信用に使用され、他のポートはロード バランサーで開かれません)
* 他のすべてのポートをブロック

他のアプリケーション ポートが必要な場合は、Microsoft.Network/loadBalancers resource リソースと Microsoft.Network/networkSecurityGroups リソースを調整してトラフィックを許可する必要があります。

## <a name="set-template-parameters"></a>テンプレート パラメーターの設定

[vnet-cluster.parameters.json][parameters] パラメーター ファイルでは、クラスターおよび関連リソースをデプロイするための多くの値を宣言します。 実際のデプロイに合わせて変更する必要があるパラメーターの一部を次に示します。

|パラメーター|値の例|メモ|
|---|---||
|adminUserName|vmadmin| クラスター VM の管理者ユーザー名。[VM のユーザー名要件](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm) |
|adminPassword|Password#1234| クラスター VM の管理者パスワード。 [VM のパスワード要件](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm)|
|clusterName|mysfcluster123| クラスターの名前。 使用できる文字はアルファベットと数字のみです。 長さは 3 から 23 文字で指定できます。|
|location|southcentralus| クラスターの場所。 |
|certificateThumbprint|| <p>自己署名証明書を作成する場合または証明書ファイルを提供する場合は、値を空にする必要があります。</p><p>以前にキー コンテナーにアップロードされた既存の証明書を使用するには、証明書の拇印値を入力します。 例: "6190390162C988701DB5676EB81083EA608DCCF3"</p>が必要です。 |
|certificateUrlValue|| <p>自己署名証明書を作成する場合または証明書ファイルを提供する場合は、値を空にする必要があります。 </p><p>以前にキー コンテナーにアップロードされた既存の証明書を使用するには、証明書の URL を入力します。 (例: "https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346")。</p>|
|sourceVaultValue||<p>自己署名証明書を作成する場合または証明書ファイルを提供する場合は、値を空にする必要があります。</p><p>以前にキー コンテナーにアップロードされた既存の証明書を使用するには、ソース コンテナー値を入力します。 たとえば、"/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT" と入力します。</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>仮想ネットワークとクラスターのデプロイ

次に、ネットワーク トポロジを設定し、Service Fabric クラスターをデプロイします。 [vnet-cluster.json][template] Resource Manager テンプレートを使用して、仮想ネットワーク (VNET) を作成し、さらに Service Fabric のサブネットとネットワーク セキュリティ グループ (NSG) を作成します。 このテンプレートを使用すると、証明書セキュリティが有効なクラスターもデプロイできます。  運用環境クラスターの場合は、証明機関 (CA) から取得した証明書をクラスター証明書として使用します。 自己署名証明書を使用して、テスト クラスターを保護することができます。

### <a name="create-a-cluster-using-an-existing-certificate"></a>既存の証明書を使用したクラスターの作成

次のスクリプトでは、[New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) コマンドレットとテンプレートを使用して、Azure に新しいクラスターをデプロイします。 さらに、このコマンドレットでは、Azure に新しいキー コンテナーを作成し、証明書をアップロードします。

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # must match the clusterName parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-cluster.json" `
-ParameterFile "$templatepath\vnet-cluster.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>新しい自己署名証明書を使用したクラスターの作成

次のスクリプトでは、[New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) コマンドレットとテンプレートを使用して、Azure に新しいクラスターをデプロイします。 また、コマンドレットでは、Azure に新しい Key Vault を作成し、新しい自己署名証明書をその Key Vault に追加し、証明書ファイルをローカルにダウンロードします。

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-cluster.json" `
-ParameterFile "$templatepath\vnet-cluster.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>セキュリティで保護されたクラスターに接続する

Service Fabric SDK とともにインストールされた Service Fabric PowerShell モジュールを使用してクラスターに接続します。  まず、この証明書を現在のユーザーのコンピューターの個人用 (マイ) ストアにインストールする必要があります。  次の PowerShell コマンドを実行します。

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

これで、セキュリティで保護されたクラスターに接続する準備ができました。

**Service Fabric** PowerShell モジュールには、Service Fabric のクラスター、アプリケーション、およびサービスを管理するための多くのコマンドレットが用意されています。  セキュリティで保護されたクラスターに接続するには、[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) コマンドレットを使用します。 証明書の拇印と接続エンドポイントの詳細は、前の手順の出力で確認できます。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

接続とクラスターの正常性を確認するには、[Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) コマンドレットを使用します。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアル シリーズの他の記事では、ここで作成したクラスターを使用します。 次の記事にすぐに進まない場合は、料金の発生を避けるため、クラスターとキー コンテナーを削除することができます。 クラスターと、そのクラスターによって使用されるすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。

リソース グループとそのグループのクラスター リソースすべてを削除するには、[Remove-AzureRMResourceGroup cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドレットを使用します。  また、そのキー コンテナーを含むリソース グループも削除します。

```powershell
Remove-AzureRmResourceGroup -Name $groupname -Force
Remove-AzureRmResourceGroup -Name $vaultgroupname -Force
```

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * PowerShell を使用して Azure に VNET を作成する
> * Key Vault を作成して証明書をアップロードする
> * PowerShell を使用してセキュリティで保護された Service Fabric クラスターを Azure に作成する
> * X.509 証明書を持つクラスターを保護する
> * PowerShell を使用してクラスターに接続する
> * クラスターの削除

次のチュートリアルでは、お使いのクラスターを拡大/縮小する方法について説明します。
> [!div class="nextstepaction"]
> [クラスターを拡大/縮小する](service-fabric-tutorial-scale-cluster.md)

[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.parameters.json
