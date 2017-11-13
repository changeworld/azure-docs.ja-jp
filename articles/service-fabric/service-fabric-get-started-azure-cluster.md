---
title: "Azure Service Fabric クラスターのセットアップ | Microsoft Docs"
description: "このクイック スタートは、Azure で Windows または Linux の Service Fabric クラスターを作成する際に役立ちます。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: ryanwi
ms.openlocfilehash: caf76bb739fa92982c511c8e3e6c6aaf2bf9d6c1
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2017
---
# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Azure で初めての Service Fabric クラスターを作成する
[Azure Service Fabric クラスター](service-fabric-deploy-anywhere.md)は、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 このクイック スタートにより、Windows または Linux 上で実行される 5 ノード クラスターを [Azure PowerShell](https://msdn.microsoft.com/library/dn135248) または [Azure Portal](http://portal.azure.com) を使用してほんの数分で作成できるようになります。 このタスクには Azure CLI も使用できます。  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


## <a name="use-the-azure-portal"></a>Azure Portal の使用

Azure Portal ([http://portal.azure.com](http://portal.azure.com)) にサインインします。

### <a name="create-the-cluster"></a>クラスターを作成する

1. Azure Portal の左上隅にある **[新規]** をクリックします。
2. 「**Service Fabric**」を検索し、返された結果から **[Service Fabric クラスター]** を選択します。 **[作成]** を選択します。
3. Service Fabric の **[基本]** フォームに入力します。 **[オペレーティング システム]** では、クラスター ノードを実行する Windows または Linux のバージョンを選択します。 ここに入力したユーザー名とパスワードが、仮想マシンへのサインインに使用されます。 **[リソース グループ]** では、新しいグループを作成します。 リソース グループとは、Azure リソースの作成と一括管理に使用する論理コンテナーです。 終了したら、**[OK]** を選択します。

    ![クラスターのセットアップに関する出力のスクリーンショット][cluster-setup-basics]

4. **[クラスター構成]** フォームに入力します。 **[ノード タイプ数]** で「**1**」と入力します。

5. **[ノード タイプ 1 (プライマリ)]** を選択し、**[ノード タイプの構成]** フォームに入力します。 ノード タイプの名前を入力し、[[持続性層]](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) を **[ブロンズ]** に設定します。 その後、VM サイズを選択します。

    ノード タイプは、VM のサイズ、VM の数、カスタム エンドポイント、そのタイプの VM のその他の設定を定義します。 定義した各ノード タイプは、個別の仮想マシン スケール セットとして設定され、仮想マシンをセットとしてデプロイおよび管理するために使用されます。 各ノード タイプは、個別にスケールアップまたはスケールダウンできるほか、異なるポートのセットを開いたり、異なる容量メトリックを設定したりできます。 第 1 (プライマリ) ノード タイプは、Service Fabric システム サービスがホストされる場所です。 このノード タイプには、5 つ以上の VM が必要です。

    運用環境デプロイメントでは、[容量計画](service-fabric-cluster-capacity.md)が重要なステップです。 しかし、このクイック スタートではアプリケーションを実行していないため、*DS1_v2 Standard* VM サイズを選択します。 [[信頼性層]](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) には **[シルバー]** を選択し、初期の仮想マシン スケール セット容量として 5 を指定します。  

    クラスターで実行されているアプリケーションに接続できるように、カスタム エンドポイントは、Azure Load Balancer でポートを開きます。  「**80, 8172**」と入力して、ポート 80 と 8172 を開きます。

    **[詳細設定の構成]** チェック ボックスはオンにしません。これは、TCP/HTTP 管理エンドポイント、アプリケーションのポート範囲、[配置の制約](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints)、[容量プロパティ](service-fabric-cluster-resource-manager-metrics.md)をカスタマイズするために使用されます。    
    
    ![ノード タイプの構成のスクリーンショット][node-type-config]

    **[OK]**を選択します。

6. **[クラスター構成]** フォームで、**[診断]** を **[有効]** に設定します。 このクイック スタートでは、どの[ファブリック設定](service-fabric-cluster-fabric-settings.md)プロパティも入力する必要はありません。  **[Fabric バージョン]** で **[自動]** アップグレード モードを選択し、クラスターを実行しているファブリック コードのバージョンが Microsoft によって自動的に更新されるようにします。  [サポートされているバージョンを選択](service-fabric-cluster-upgrade.md)して、そのバージョンにアップグレードする場合は、モードを **[手動]** に設定します。     

    **[OK]**を選択します。

7. **[セキュリティ]** フォームに入力します。 このクイック スタートでは、**[セキュリティ保護なし]** を選択します。 一般に、運用環境のワークロード用にはセキュリティで保護されたクラスターを作成する必要があることに注意してください。 セキュリティで保護されていないクラスターには、だれでも匿名で接続し、管理操作を実行できてしまいます。  

   Service Fabric は証明書を使用して、クラスターとそのアプリケーションのさまざまな側面をセキュリティで保護するための認証と暗号化を提供します。 詳細については、[Service Fabric クラスターのセキュリティに関するシナリオ](service-fabric-cluster-security.md)に関するページを参照してください。 Azure Active Directory を使用してユーザー認証を有効にするか、アプリケーション セキュリティ用の証明書を設定するには、[Resource Manager テンプレートからのクラスターの作成](service-fabric-cluster-creation-via-arm.md)に関するページを参照してください。

    **[OK]**を選択します。

8. 概要を確認します。 自分で入力した設定で構築された Azure Resource Manager テンプレートをダウンロードする場合は、**[テンプレートとパラメーターのダウンロード]** を選択します。 **[作成]** を選択して、クラスターを作成します。

    通知には作成の進行状況が表示されます (画面の右上にあるステータス バーの近くの "ベル" アイコンを選択します)。クラスターの作成中に **[スタート画面にピン留めする]** を選択した場合、**[Deploying Service Fabric Cluster]\(Service Fabric クラスターのデプロイ)** が**スタート**画面にピン留めされていることがわかります。

### <a name="connect-to-the-cluster-by-using-powershell"></a>PowerShell を使用してクラスターに接続する
PowerShell を使用して接続することで、クラスターが実行されていることを確認します。 Service Fabric PowerShell モジュールは、[Service Fabric SDK](service-fabric-get-started.md) と共にインストールされます。 クラスターへの接続は、[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) コマンドレットで確立します。   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint quickstartcluster.westus2.cloudapp.azure.com:19000
```
その他の方法でクラスターに接続する例については、「[セキュリティ保護されたクラスターに接続する](service-fabric-connect-to-secure-cluster.md)」を参照してください。 クラスターに接続した後、[Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) コマンドレットを使って、クラスターに含まれているノードの一覧と各ノードの状態情報を表示します。 各ノードの **HealthState** は *OK* になっている必要があります。

```powershell
PS C:\Users\sfuser> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
```

### <a name="remove-the-cluster"></a>クラスターの削除
Service Fabric クラスターは、クラスター リソース自体に加え、その他の Azure リソースで構成されます。 Service Fabric クラスターを完全に削除するには、それを構成するすべてのリソースも削除する必要があります。 クラスターと、そのクラスターによって使用されるすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。 クラスターを削除したり、リソース グループ内の (すべてではなく) 一部のリソースを削除したりする他の方法については、[クラスターの削除](service-fabric-cluster-delete.md)に関するページを参照してください。

Azure Portal でリソース グループを削除するには:
1. 削除する Service Fabric クラスターを参照します。
2. クラスターの [Essentials]\(要点\) ページで、**リソース グループ**の名前を選択します。
3. リソース グループの **[Essentials]\(要点\)** ページで、**[リソース グループの削除]** を選択します。 次に、そのページの指示に従って、リソース グループの削除を完了します。
    ![[リソース グループの削除] が強調表示されている、リソース グループの [Essentials]\(要点\) ページのスクリーンショット][cluster-delete]


## <a name="use-azure-powershell"></a>Azure PowerShell の使用
クラスターを作成する別の方法として、PowerShell を使用します。 その方法は次のとおりです。

1. [Azure PowerShell モジュール バージョン 4.0 以降](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)をコンピューターにダウンロードします。

2. [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster) コマンドレットを実行して、X.509 証明書で保護される 5 ノードの Service Fabric クラスターを作成します。 このコマンドで自己署名証明書を作成し、それを新しいキー コンテナーにアップロードします。 証明書はローカル ディレクトリにもコピーされます。 *-OS* パラメーターを設定して、クラスター ノードで実行される Windows または Linux のバージョンを選択します。 必要に応じてパラメーターをカスタマイズします。 

    ```powershell
    #Provide the subscription Id
    $subscriptionId = 'yourSubscriptionId'

    # Certificate variables.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
    $certfolder="c:\mycertificates\"

    # Variables for VM admin.
    $adminuser="vmadmin"
    $adminpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 

    # Variables for common values
    $clusterloc="SouthCentralUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=5 

    # Set the context to the subscription ID where the cluster will be created
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Create the Service Fabric cluster.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -VmUserName $adminuser -VmPassword $adminpwd -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
    ```

    このコマンドが完了するまでに 10 ～ 30 分かかる場合があります。 出力には、証明書、そのアップロード先のキー コンテナー、証明書のコピー先のローカル フォルダーに関する情報が含まれています。     

3. 出力全体をコピーしてテキスト ファイルに保存します (後で参照します)。 出力の次の情報をメモしておきます。 

    - CertificateSavedLocalPath
    - CertificateThumbprint
    - ManagementEndpoint
    - ClientConnectionEndpointPort

### <a name="install-the-certificate-on-your-local-machine"></a>証明書をローカル コンピューターにインストールする
  
クラスターに接続するには、この証明書を現在のユーザーの個人用 (マイ) ストアにインストールします。 

次のコマンドレットを実行します。

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\<certificatename>.pfx `
        -Password $certpwd
```

これで、セキュリティで保護されたクラスターに接続する準備ができました。

### <a name="connect-to-a-secure-cluster"></a>セキュリティ保護されたクラスターに接続する 

セキュリティで保護されたクラスターに接続するには、[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) コマンドレットを実行します。 証明書の詳細は、クラスターのセットアップに使用された証明書と一致する必要があります。 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <ManagementEndpoint>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <CertificateThumbprint> `
          -FindType FindByThumbprint -FindValue <CertificateThumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

次の例で、パラメーターの例を示します。 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mycluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

次のコマンドを実行して、接続できていることと、クラスターが正常なことを確認します。

```powershell

Get-ServiceFabricClusterHealth

```
### <a name="remove-the-cluster"></a>クラスターの削除
クラスターは、クラスター リソース自体に加え、その他の Azure リソースで構成されます。 クラスターと、そのクラスターによって使用されるすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。 

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```
## <a name="use-azure-cli"></a>Azure CLI の使用
クラスターを作成する別の方法として、CLI を使用します。 その方法は次のとおりです。

1. コンピューターに [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) をインストールします。
2. Azure にサインインし、クラスターを作成するサブスクリプションを選択します。
   ```azurecli
   az login
   az account set --subscription <GUID>
   ```
3. [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) コマンドを実行して、X.509 証明書で保護される 5 ノードの Service Fabric クラスターを作成します。 このコマンドで自己署名証明書を作成し、それを新しいキー コンテナーにアップロードします。 証明書はローカル ディレクトリにもコピーされます。 *-os* パラメーターを設定して、クラスター ノードで実行される Windows または Linux のバージョンを選択します。 必要に応じてパラメーターをカスタマイズします。

    ```azurecli
    #!/bin/bash

    # Variables
    ResourceGroupName="aztestclustergroup" 
    ClusterName="aztestcluster" 
    Location="southcentralus" 
    Password="q6D7nN%6ck@6" 
    Subject="aztestcluster.southcentralus.cloudapp.azure.com" 
    VaultName="aztestkeyvault" 
    VaultGroupName="testvaultgroup"
    VmPassword="Mypa$$word!321"
    VmUserName="sfadminuser"

    # Create resource groups
    az group create --name $ResourceGroupName --location $Location 
    az group create --name $VaultGroupName --location $Location

    # Create secure five node Linux cluster. Creates a key vault in a resource group
    # and creates a certficate in the key vault. The certificate's subject name must match 
    # the domain that you use to access the Service Fabric cluster. The certificate is downloaded locally.
    az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . \
        --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName \
        --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $VaultGroupName \
        --vm-password $VmPassword --vm-user-name $VmUserName
    ```
    
### <a name="connect-to-the-cluster"></a>クラスターへの接続
次の CLI コマンドを実行して、証明書を使用してクラスターに接続します。  認証にクライアント証明書を使用している場合は、証明書の詳細がクラスター ノードにデプロイされている証明書と一致することを確認してください。 自己署名証明書には `--no-verify` オプションを使用します。

```azurecli
az sf cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 --pem ./linuxcluster201709161647.pem --no-verify
```

次のコマンドを実行して、接続できていることと、クラスターが正常なことを確認します。

```azurecli
az sf cluster health
```

### <a name="connect-to-the-nodes-directly"></a>ノードへの直接接続 

Linux クラスター内のノードに接続するには、Secure Shell (SSH) コマンドを使用できます。 3389 以降のポート番号を指定します。 たとえば、先ほど作成した 5 つのノード クラスターであれば、以下のようなコマンドになります。
```bash
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3389
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3390
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3391
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3392
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3393
```

### <a name="remove-the-cluster"></a>クラスターの削除
クラスターは、クラスター リソース自体に加え、その他の Azure リソースで構成されます。 クラスターと、そのクラスターによって使用されるすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。 

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>次のステップ
開発クラスターをセットアップしたら、次の作業を試してみてください。
* [Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)
* [クラスターの削除](service-fabric-cluster-delete.md) 
* [PowerShell を使ってアプリをデプロイ](service-fabric-deploy-remove-applications.md)します。
* [CLI を使用したアプリのデプロイ](service-fabric-application-lifecycle-sfctl.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
