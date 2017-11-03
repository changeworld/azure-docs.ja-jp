---
title: "Azure に Service Fabric クラスターを作成する | Microsoft Docs"
description: "PowerShell を使用して Azure に Windows または Linux の Service Fabric クラスターを作成する方法について説明します。"
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
ms.date: 10/03/2017
ms.author: cristyg
ms.custom: mvc
ms.openlocfilehash: 29a8d5cf9a59a08dc63df1dbd601aa1f509cad95
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2017
---
# <a name="create-a-secure-cluster-on-azure-using-powershell"></a>PowerShell を使用して Azure にセキュリティで保護されたクラスターを作成する
これは、Service Fabric クラスターとコンテナーを使用して、.NET アプリケーションをクラウドに移動する方法を示す一連のチュートリアルの最初の手順です。 次の手順では、Azure で実行される Service Fabric クラスター (Windows または Linux) を作成する方法について説明します。 完了すると、クラウドで実行されているセキュリティ保護されたクラスターにアプリケーションをデプロイできるようになります。

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に
- Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
- [Service Fabric SDK](service-fabric-get-started.md) をインストールします。
- [Azure Powershell モジュール バージョン 4.1 以上](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)をインストールします (必要に応じて、[Azure PowerShell をインストール](/powershell/azure/overview)するか、または[新しいバージョンに更新](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0#update-azps)します)。


# <a name="create-a-service-fabric-cluster"></a>Service Fabric クラスターの作成

このスクリプトは、単一ノードのプレビュー Service Fabric クラスターを作成します。 このクラスターは、クラスターと共に作成される自己署名証明書によって保護され、キー コンテナーに配置されます。 単一ノード クラスターは 1 台の仮想マシンを超えて拡張することはできず、Preview クラスターを新しいバージョンにアップグレードすることはできません。

Azure で Service Fabric クラスターを実行することによって発生するコストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用します。
Service Fabric クラスターを作成する方法について詳しくは、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)」をご覧ください。

## <a name="log-in-to-azure"></a>Azure へのログイン
PowerShell コンソールを開き、Azure にログインし、クラスターをデプロイするサブスクリプションを選択します。

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

## <a name="cluster-parameters"></a>クラスター パラメーター

   このスクリプトは、次のパラメーターと概念を使用しています。 パラメーターは、要件に合わせてカスタマイズします。

   | パラメーター       | Description | 推奨値 |
   | --------------- | ----------- | --------------- |
   | 場所 | クラスターのデプロイ先の Azure リージョン。 | *westeurope、eastasia、eastus など* |
   | 名前     | 作成するクラスターの名前。 名前は、4 ～ 23 文字にする必要があり、小文字、数字、およびハイフンのみを含めることができます。 | *bobs-sfpreviewcluster など* |
   | ResourceGroupName   | クラスターを作成するリソース グループの名前。 | *myresourcegroup など* |
   | VmSku  | ノードで使用する仮想マシン SKU。 | *任意の有効な仮想マシン SKU* |
   | OS  | ノードで使用する仮想マシン OS。 | *任意の有効な仮想マシン OS* |
   | KeyVaultName | クラスターに関連付ける新しい KeyVault の名前。 | *mykeyvault など* |
   | ClusterSize | クラスター内の仮想マシンの数 (1 または 3 ～ 99 で指定できます)。| *プレビュー クラスターには、1 つの仮想マシンのみを指定します* |
   | CertificateSubjectName | 作成される証明書のサブジェクト名。 | *<name>.<location>.cloudapp.azure.com* の形式に従います。 |

## <a name="default-parameter-values"></a>既定のパラメーター値
**仮想マシン**設定は省略可能です。 それらを指定しない場合、管理者ユーザー名の既定値は "vmadmin" に設定され、クラスターを作成する前に、PowerShell によって仮想マシンのパスワードの入力が求められます。

**ポート**の既定値はポート 80 および 8081 に設定されます。 [Service Fabric クラスター内のポート](https://docs.microsoft.com/en-us/azure/service-fabric/create-load-balancer-rule)についてのガイダンスに従って、追加のポートを指定できます。

**診断**は既定で有効になっています。

**DNS サービス**は既定で無効になっています。

**リバース プロキシ**も既定で無効になっています。

## <a name="create-the-cluster-with-your-parameters"></a>パラメーターによるクラスターの作成

要件に適合するパラメーターを決定したら、次のコマンドを実行して、セキュリティで保護された Service Fabric クラスターとその証明書を生成します。

このスクリプトを変更して、追加のパラメーターを含めることができます。 クラスター作成のためのパラメーターの詳細については、[New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster.md) コマンドレットを参照してください。

>[!NOTE]
>このコマンドを実行する前に、クラスター証明書を出力するフォルダーを作成する必要があります。

```powershell

    # Certificate variables. This will create and encrypt a password to be used by Service Fabric.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force

    # You must create the folder where you want to output the certificate on your machine before executing this step.
    $certfolder="c:\mycertificates\"

    # Variables for common values. Change the values to fit your needs.
    $clusterloc="WestUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=1

    # Create the Service Fabric cluster and its self-signed certificate. The OS specified here allows you use this cluster with any applications that are also leveraging containers.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
```

作成プロセスには数分かかることがあります。 構成が完了すると、Azure に作成されたクラスターに関する情報が出力されます。 また、このパラメーターに指定されているパスの -CertificateOutp ディレクトリにもクラスター証明書がコピーされます。

クラスターの **ManagementEndpoint** URL をメモします。これは *https://mycluster.westeurope.cloudapp.azure.com:19080* のような URL です。

## <a name="import-the-certificate"></a>証明書のインポート

クラスターが正常に作成されたら、次のコマンドを実行して、自己署名証明書を使用できることを確認します。

```powershell

    # Connect to the cluster using by installing the certificate into the Personal (My) store of the current user on your computer.
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\mycertificates\mysfclustergroup20170531104310.pfx `
            -Password $certpwd
```

このコマンドは、コンピューターの現在のユーザーに証明書をインストールします。  Service Fabric Explorer にアクセスし、クラスターの正常性を表示するには、この証明書が必要です。


## <a name="view-your-cluster-optional"></a>クラスターの表示 (省略可能)

クラスターとインポートした証明書の両方を用意したら、クラスターに接続し、その正常性を表示できます。 接続するには、Service Fabric Explorer または PowerShell を使用するいくつかの方法があります。

### <a name="service-fabric-explorer"></a>Service Fabric Explorer
Service Fabric Explorer を開いて、クラスターの正常性を表示できます。 Service Fabric Explorer を開くには、Web ブラウザーを使用してクラスターの **ManagementEndpoint** URL にアクセスし、コンピューターに保存した証明書を選択します。

>[!NOTE]
>自己署名証明書を使用しているため、Service Fabric Explorer を開くときに、証明書エラーが表示されます。 Edge では、*[詳細]* をクリックし、*[Web ページへ移動]* リンクをクリックする必要があります。 Chrome では、*[詳細設定]* をクリックし、*[続行]* リンクをクリックする必要があります。

### <a name="powershell"></a>PowerShell

**Service Fabric** PowerShell モジュールには、Service Fabric のクラスター、アプリケーション、およびサービスを管理するための多くのコマンドレットが用意されています。  セキュリティで保護されたクラスターに接続するには、[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) コマンドレットを使用します。 証明書の拇印と接続エンドポイントの詳細は、前の手順の出力で確認できます。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

[Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) コマンドレットを使用して、接続とクラスターの正常性を確認することもできます。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、PowerShell を使用して、Azure でセキュリティ保護された Service Fabric クラスターを作成する方法を説明しました。

次のチュートリアルでは、既存のアプリケーションをデプロイする方法について説明します。
> [!div class="nextstepaction"]
> [Docker Compose を使用して既存の .NET アプリケーションをデプロイする](service-fabric-host-app-in-a-container.md)

---
