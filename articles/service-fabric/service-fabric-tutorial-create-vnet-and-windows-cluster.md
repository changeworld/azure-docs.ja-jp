---
title: "Azure に Windows Service Fabric クラスターを作成する | Microsoft Docs"
description: "PowerShell を使用して Windows Service Fabric クラスターを既存の Azure 仮想ネットワークにデプロイする方法を学習します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: ryanwi
ms.openlocfilehash: b06d0196f1f911f2f6cf87242d70455ba22b1f88
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Azure 仮想ネットワークに Service Fabric Windows クラスターをデプロイする
このチュートリアルは、シリーズの第 1 部です。 PowerShell を使用して Windows Service Fabric クラスターを既存の Azure 仮想ネットワーク (VNET) とサブネットにデプロイする方法を学習します。 完了すると、クラウドで実行されているクラスターにアプリケーションをデプロイできるようになります。  Azure CLI を使用して Linux クラスターを作成する場合は、[Azure でのセキュリティで保護された Linux クラスターの作成](service-fabric-tutorial-create-vnet-and-linux-cluster.md)に関するページを参照してください。

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
> * [クラスターをスケールインまたはスケールアウトする](/service-fabric-tutorial-scale-cluster.md)
> * [Service Fabric を使用して API Management をデプロイする](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に
- Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する
- [Service Fabric SDK と PowerShell モジュール](service-fabric-get-started.md)をインストールする
- [Azure PowerShell モジュール Version 4.1 以上](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)をインストールする

次の手順で、5 ノードの Service Fabric クラスターを作成します。 Azure で Service Fabric クラスターを実行することによって発生するコストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用します。

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Azure にサインインしてサブスクリプションを選択する
このガイドでは Azure PowerShell を使用します。 新しい PowerShell セッションを開始するときに、Azure アカウントにサインインし、Azure のコマンドを実行する前にサブスクリプションを選択します。
 
Azure アカウントにサインインしてサブスクリプションを選択するには、次のスクリプトを実行します。

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>リソース グループの作成
デプロイ用の新しいリソース グループを作成し、リソース グループに名前を付けて、場所を指定します。

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc
```

## <a name="deploy-the-network-topology"></a>ネットワーク トポロジをデプロイする
次に、API Management と Service Fabric クラスターのデプロイ先となるネットワーク トポロジを設定します。 [network.json][network-arm] Resource Manager テンプレートは、仮想ネットワーク (VNET) を作成するよう構成されています。また、Service Fabric のサブネットおよびネットワーク セキュリティ グループ (NSG)、API Management のサブネットおよび NSG を作成するようにも構成されています。 VNET、サブネット、NSG の詳細については、[こちら](../virtual-network/virtual-networks-overview.md)を参照してください。

[network.parameters.json][network-parameters-arm] パラメーター ファイルには、Service Fabric と API Management のデプロイ先となるサブネットと NSG の名前が含まれています。  API Management は、[次のチュートリアル](service-fabric-tutorial-deploy-api-management.md)でデプロイされます。 このガイドでは、パラメーターの値を変更する必要はありません。 Service Fabric Resource Manager テンプレートでは、これらのパラメーターの値を使用します。  ここで値を変更する場合は、このチュートリアルおよび [API Management のデプロイのチュートリアル](service-fabric-tutorial-deploy-api-management.md)で使用されている、他の Resource Manager テンプレート内の値も変更する必要があります。 

次の Resource Manager テンプレートとパラメーター ファイルをダウンロードします。
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

次の PowerShell コマンドを使用して、ネットワーク設定用の Resource Manager テンプレートとパラメーター ファイルをデプロイします。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile C:\winclustertutorial\network.json -TemplateParameterFile C:\winclustertutorial\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Service Fabric クラスターをデプロイする
ネットワーク リソースのデプロイが完了したら、次は Service Fabric クラスターを VNET の所定のサブネットと NSG にデプロイします。 既存の VNET とサブネットにクラスターをデプロイするには (この記事では既にデプロイ済み)、Resource Manager テンプレートが必要です。  このチュートリアル シリーズで使用するテンプレートは、前の手順で設定した VNET、サブネット、NSG の名前を使用するようあらかじめ構成されています。  

次の Resource Manager テンプレートとパラメーター ファイルをダウンロードします。
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

セキュリティで保護されたクラスターを作成するには、このテンプレートを使用します。  クラスター証明書は、ノード間通信のセキュリティ保護と、クラスター管理エンドポイントの管理クライアントへの認証に使用される X.509 証明書です。  このクラスター証明書は、HTTPS 管理 API および HTTPS 経由の Service Fabric Explorer に対しても SSL を提供します。 Azure Key Vault を使用して、Azure で Service Fabric クラスター用の証明書を管理します。  Azure にクラスターがデプロイされると、Service Fabric クラスターの作成担当 Azure リソース プロバイダーにより Key Vault から証明書が取得されてクラスター VM にインストールされます。 

証明書機関 (CA) からの証明書をクラスター証明書として使用したり、テストを目的として自己署名証明書を作成したりできます。 クラスター証明書には、次の要件があります。

- 秘密キーが含まれている。
- キー交換のために作成されており、Personal Information Exchange (.pfx) ファイルにエクスポートできる。
- 保持しているサブジェクト名が、Service Fabric クラスターへのアクセスに使用されるドメインと一致している。 この整合性は、HTTPS 管理エンドポイントと Service Fabric Explorer 用の SSL を提供するために必要です。 証明機関 (CA) から .cloudapp.azure.com ドメインの SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書のサブジェクト名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

次のように、デプロイ用の *cluster.parameters.json* ファイルにある空のパラメーターに値を入力します。

|パラメーター|値|
|---|---|
|adminPassword|Password#1234|
|adminUserName|vmadmin|
|clusterName|mysfcluster|
|location|southcentralus|

自己署名証明書を作成するには、*certificateThumbprint*、*certificateUrlValue*、および *sourceVaultValue* パラメーターは空白のままにします。  以前に Key Vault にアップロードした既存の証明書を使用する場合、これらのパラメーター値を入力します。

次のスクリプトでは、[New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) コマンドレットとテンプレートを使用して Azure に新しいクラスターをデプロイします。 また、コマンドレットでは、Azure に新しい Key Vault を作成し、新しい自己署名証明書をその Key Vault に追加し、証明書ファイルをローカルにダウンロードします。 [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) コマンドレットの他のパラメーターを使用して、既存の証明書と Key Vault の両方またはいずれかを指定できます。

```powershell
# Variables.
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster"
$vaultname = "clusterkeyvault111"
$vaultgroupname="clusterkeyvaultgroup111"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile 'C:\winclustertutorial\cluster.json' `
-ParameterFile 'C:\winclustertutorial\cluster.parameters.json' -CertificatePassword $certpwd `
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
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
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

## <a name="next-steps"></a>次のステップ
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


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json
