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
ms.date: 02/19/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 64ba17053179d428f5ef7e5ce9685240bde6665f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822993"
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>チュートリアル:Azure 仮想ネットワークに Service Fabric Windows クラスターをデプロイする

このチュートリアルは、シリーズの第 1 部です。 PowerShell とテンプレートを使用して、Windows を実行している Service Fabric クラスターを [Azure 仮想ネットワーク (VNET)](../virtual-network/virtual-networks-overview.md) および[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)にデプロイする方法を説明します。 完了すると、クラウドで実行されているクラスターにアプリケーションをデプロイできるようになります。  Azure CLI を使用して Linux クラスターを作成する場合は、[Azure でのセキュリティで保護された Linux クラスターの作成](service-fabric-tutorial-create-vnet-and-linux-cluster.md)に関するページを参照してください。

このチュートリアルでは、本番環境シナリオについて説明します。  テスト目的で小規模のクラスターをすばやく作成する場合は、[テスト クラスターの作成](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)に関するページを参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * PowerShell を使用して Azure に VNET を作成する
> * Key Vault を作成して証明書をアップロードする
> * Azure Active Directory 認証を設定する
> * セキュリティで保護された Service Fabric クラスターを Azure PowerShell に作成する
> * X.509 証明書を持つクラスターを保護する
> * PowerShell を使用してクラスターに接続する
> * クラスターの削除

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Azure にセキュリティで保護されたクラスターを作成する
> * [クラスターをスケールインまたはスケールアウトする](service-fabric-tutorial-scale-cluster.md)
> * [クラスターのランタイムをアップグレードする](service-fabric-tutorial-upgrade-cluster.md)
> * [クラスターの削除](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する
* [Service Fabric SDK と PowerShell モジュール](service-fabric-get-started.md)をインストールする
* [Azure PowerShell モジュール Version 4.1 以上](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)をインストールする
* [Azure クラスター](service-fabric-azure-clusters-overview.md)の主要な概念を確認する

次の手順で、7 ノードの Service Fabric クラスターを作成します。 Azure で Service Fabric クラスターを実行することによって発生するコストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用します。

## <a name="download-and-explore-the-template"></a>テンプレートのダウンロードと詳細の確認

次の Resource Manager テンプレート ファイルをダウンロードします。

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

このテンプレートでは、7 つの仮想マシンと 3 つのノード タイプから成るセキュリティ保護されたクラスターが、仮想ネットワークとネットワーク セキュリティ グループにデプロイされます。  [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates) には他のサンプル テンプレートがあります。  [azuredeploy.json][template] では、次のものを含むいくつかのリソースがデプロイされます。

### <a name="service-fabric-cluster"></a>Service Fabric クラスター

**Microsoft.ServiceFabric/clusters** リソースでは、以下の特性によって Windows クラスターが構成されます。

* 3 つのノード タイプ
* プライマリ ノード タイプに 5 つのノード (テンプレート パラメーターで構成可能)、他の 2 つのノード タイプにそれぞれ 1 つのノード
* OS: コンテナー搭載 Windows Server 2016 Datacenter (テンプレート パラメーターで構成可能)
* 証明書の保護 (テンプレート パラメーターで構成可能)
* [リバース プロキシ](service-fabric-reverseproxy.md)が有効
* [DNS サービス](service-fabric-dnsservice.md)が有効
* ブロンズ[持続性レベル](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) (テンプレート パラメーターで構成可能)
* シルバー[信頼性レベル](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) (テンプレート パラメーターで構成可能)
* クライアント接続エンドポイント: 19000 (テンプレート パラメーターで構成可能)
* HTTP ゲートウェイ エンドポイント: 19080 (テンプレート パラメーターで構成可能)

### <a name="azure-load-balancer"></a>Azure Load Balancer

**Microsoft.Network/loadBalancers** リソースでは、ロード バランサーが構成され、以下のポートに対してプローブとルールが設定されます。

* クライアント接続エンドポイント: 19000
* HTTP ゲートウェイ エンドポイント: 19080
* アプリケーション ポート: 80
* アプリケーション ポート: 443
* Service Fabric リバース プロキシ: 19081

その他のアプリケーション ポートが必要な場合は、トラフィックが入ることを許可するように **Microsoft.Network/loadBalancers** リソースと **Microsoft.Network/networkSecurityGroups** リソースを調整する必要があります。

### <a name="virtual-network-subnet-and-network-security-group"></a>仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループ

仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループの名前は、テンプレート パラメーターで宣言されます。  仮想ネットワークとサブネットのアドレス空間も、テンプレート パラメーターで宣言され、**Microsoft.Network/virtualNetworks** リソース内に構成されます。

* 仮想ネットワークのアドレス空間: 172.16.0.0/20
* Service Fabric サブネットのアドレス空間: 172.16.2.0/23

**Microsoft.Network/networkSecurityGroups** リソースでは、以下の受信トラフィック ルールが有効になります。 テンプレートの変数を変更することで、ポートの値を変更できます。

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* ノード間通信 - 1025、1026、1027
* 一時的なポート範囲 - 49152 ～ 65534 (256 ポート以上が必要)
* アプリケーション用のポート: 80 および 443
* アプリケーションのポート範囲 - 49152 ～ 65534 (サービス間通信用に使用され、他のポートはロード バランサーで開かれません)
* 他のすべてのポートをブロック

その他のアプリケーション ポートが必要な場合は、トラフィックが入ることを許可するように **Microsoft.Network/loadBalancers** リソースと **Microsoft.Network/networkSecurityGroups** リソースを調整する必要があります。

### <a name="windows-defender"></a>Windows Defender
既定では、Windows Server 2016 には、[Windows Defender ウイルス対策](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)がインストールされ、機能します。 一部の SKU ではユーザー インターフェイスが既定でインストールされますが、必須ではありません。  テンプレートで宣言されている各ノード タイプ/VM スケール セットに対して、Service Fabric ディレクトリとプロセスを除外するために、[Azure VM マルウェア対策拡張機能](/azure/virtual-machines/extensions/iaas-antimalware-windows)が使用されます。

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>テンプレート パラメーターの設定

[azuredeploy.parameters.json][parameters] パラメーター ファイルでは、クラスターおよび関連リソースをデプロイするための多くの値が宣言されています。 実際のデプロイに合わせて変更する必要があるパラメーターの一部を次に示します。

|パラメーター|値の例|メモ|
|---|---||
|adminUserName|vmadmin| クラスター VM の管理者ユーザー名。[VM のユーザー名要件](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm) |
|adminPassword|Password#1234| クラスター VM の管理者パスワード。 [VM のパスワード要件](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm)|
|clusterName|mysfcluster123| クラスターの名前。 使用できる文字はアルファベットと数字のみです。 長さは 3 から 23 文字で指定できます。|
|location|southcentralus| クラスターの場所。 |
|certificateThumbprint|| <p>自己署名証明書を作成する場合または証明書ファイルを提供する場合は、値を空にする必要があります。</p><p>以前にキー コンテナーにアップロードされた既存の証明書を使用するには、証明書の SHA1 サムプリントの値を入力します。 例: "6190390162C988701DB5676EB81083EA608DCCF3"</p>。 |
|certificateUrlValue|| <p>自己署名証明書を作成する場合または証明書ファイルを提供する場合は、値を空にする必要があります。 </p><p>以前にキー コンテナーにアップロードされた既存の証明書を使用するには、証明書の URL を入力します。 (例: "https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346")。</p>|
|sourceVaultValue||<p>自己署名証明書を作成する場合または証明書ファイルを提供する場合は、値を空にする必要があります。</p><p>以前にキー コンテナーにアップロードされた既存の証明書を使用するには、ソース コンテナー値を入力します。 たとえば、"/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT" と入力します。</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Azure Active Directory クライアント認証の設定
Azure でホストされているパブリック ネットワークにデプロイされた Service Fabric クラスターの場合、クライアントとノードの間の相互認証は次のようにすることを推奨します。
* クライアント ID には Azure Active Directory を使用する
* サーバー ID のための証明書と http 通信の SSL 暗号化

Service Fabric クラスターのクライアントを認証するための Azure AD の設定は、[クラスターを作成する](#createvaultandcert)前に行う必要があります。  Azure AD は組織 (テナントと呼ばれます) を有効にしてアプリケーションに対するユーザー アクセスを管理します。 

Service Fabric クラスターでは、Web ベースの [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) や [Visual Studio](service-fabric-manage-application-in-visual-studio.md) など、いくつかのエントリ ポイントから管理機能にアクセスできます。 このため、クラスターへのアクセスを制御するには、2 つの Azure AD アプリケーション (Web アプリケーションとネイティブ アプリケーション) を作成します。  アプリケーションを作成した後は、ユーザーを読み取り専用ロールおよび管理者ロールに割り当てます。

> [!NOTE]
> クラスターを作成する前に、次の手順を完了する必要があります。 スクリプトはクラスター名とエンドポイントを想定しているため、値は作成済みのものではなく、計画する必要があります。

この記事では、既にテナントを作成していることを前提としています。 まだ作成していない場合は、まず、[Azure Active Directory テナントを取得する方法](../active-directory/develop/quickstart-create-new-tenant.md)に関するページをお読みください。

Azure AD の Service Fabric クラスターでの構成に関する手順の一部を簡略化するため、一連の Windows PowerShell スクリプトが作成されています。 コンピューターに[スクリプトをダウンロード](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool)します。

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure AD アプリケーションを作成し、ユーザーをロールに割り当てる
クラスターへのアクセスを制御するために、Web アプリケーションとネイティブ アプリケーションの 2 つの Azure AD アプリケーションを作成します。 クラスターを表すアプリケーションを作成したら、[Service Fabric によってサポートされるロール](service-fabric-cluster-security-roles.md) (read-only と admin) にユーザーを割り当てます。

`SetupApplications.ps1` を実行します。パラメーターとして、テナント ID、クラスター名、および Web アプリケーション応答 URL を指定します。  ユーザー名とユーザーのパスワードも指定します。  例: 

```PowerShell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> 国内のクラウド (たとえば Azure Government、Azure China、Azure Germany) については、`-Location` パラメーターも指定する必要があります。

*TenantId* (ディレクトリ ID) は、[Azure portal](https://portal.azure.com) で確認できます。 **[Azure Active Directory]、[プロパティ]** の順に選択し、**[ディレクトリ ID]** の値をコピーします。

*ClusterName* は、スクリプトによって作成される Azure AD アプリケーションのプレフィックスとして使用されます。 実際のクラスター名と完全に一致している必要はありません。 これは、一緒に使用される Service Fabric クラスターに対して、Azure AD のアーティファクトのマッピングを容易にするためだけに使用します。

*WebApplicationReplyUrl* は、サインインの完了後に Azure AD によってユーザーに返される既定のエンドポイントです。 このエンドポイントをクラスターの Service Fabric Explorer エンドポイントして設定します。既定値は次のとおりです。

https://&lt;cluster_domain&gt;:19080/Explorer

Azure AD テナント用の管理特権を持っているアカウントにサインインすることを求められます。 サインインすると、スクリプトは、Service Fabric クラスターを表す Web アプリケーションとネイティブ アプリケーションを作成します。 [Azure portal](https://portal.azure.com) でテナントのアプリケーションを調べると、次の 2 つの新しいエントリがあることがわかります。

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

クラスターを作成するとき、スクリプトによって、Azure Resource Manager テンプレートが必要とする JSON が出力されます。このため、PowerShell ウィンドウは開いたままにしてください。

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>クライアント アクセスに Azure AD を使用する Azure AD の構成を追加する
[azuredeploy.json][template] の **Microsoft.ServiceFabric/clusters** セクションで、Azure AD を構成します。  テナント ID、クラスター アプリケーション ID、およびクライアント アプリケーション ID のためのパラメーターを追加します。  

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

[azuredeploy.parameters.json][parameters] パラメーター ファイルで、パラメーター値を追加します。  例: 

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>仮想ネットワークとクラスターのデプロイ

次に、ネットワーク トポロジを設定し、Service Fabric クラスターをデプロイします。 [azuredeploy.json][template] Resource Manager テンプレートを使用して、仮想ネットワーク (VNET) を作成し、さらに Service Fabric のサブネットとネットワーク セキュリティ グループ (NSG) を作成します。 このテンプレートを使用すると、証明書セキュリティが有効なクラスターもデプロイできます。  運用環境クラスターの場合は、証明機関 (CA) から取得した証明書をクラスター証明書として使用します。 自己署名証明書を使用して、テスト クラスターを保護することができます。

この記事のテンプレートでは、クラスター証明書を識別するために証明書の拇印を使用するクラスターがデプロイされます。  2 つの証明書が同じ拇印を持つことはできず、そのことが証明書の管理をより困難にしています。 デプロイされたクラスターで使用するのを、証明書の拇印から証明書共通名に切り替えることで、証明書の管理が大幅に単純化します。  証明書の管理に証明書共通名を使用するようにクラスターを更新する方法については、[証明書共通名管理へのクラスターの変更](service-fabric-cluster-change-cert-thumbprint-to-cn.md)に関するページを参照してください。

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
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
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
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

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

**Service Fabric** PowerShell モジュールには、Service Fabric のクラスター、アプリケーション、およびサービスを管理するための多くのコマンドレットが用意されています。  セキュリティで保護されたクラスターに接続するには、[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) コマンドレットを使用します。 証明書の SHA1 サムプリントと接続エンドポイントの詳細は、前の手順の出力で確認できます。

以前に AAD クライアント認証を設定した場合は、次のように実行します。 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

AAD クライアント認証を設定しなかった場合は、次のように実行します。
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

このチュートリアル シリーズの他の記事では、ここで作成したクラスターを使用します。 次の記事にすぐに進まない場合は、料金の発生を避けるため、[クラスターを削除](service-fabric-cluster-delete.md)することができます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * PowerShell を使用して Azure に VNET を作成する
> * Key Vault を作成して証明書をアップロードする
> * Azure Active Directory 認証を設定する
> * PowerShell を使用してセキュリティで保護された Service Fabric クラスターを Azure に作成する
> * X.509 証明書を持つクラスターを保護する
> * PowerShell を使用してクラスターに接続する
> * クラスターの削除

次のチュートリアルでは、お使いのクラスターを拡大/縮小する方法について説明します。
> [!div class="nextstepaction"]
> [クラスターを拡大/縮小する](service-fabric-tutorial-scale-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
