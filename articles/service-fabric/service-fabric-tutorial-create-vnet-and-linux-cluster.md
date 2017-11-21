---
title: "Azure に Linux Service Fabric クラスターを作成する | Microsoft Docs"
description: "Azure CLI を使用して Linux Service Fabric クラスターを既存の Azure 仮想ネットワークにデプロイする方法を学習します。"
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
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: 33a3474ed91194efbaf2ef96957ad268f43a717e
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2017
---
# <a name="deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Azure 仮想ネットワークに Service Fabric Linux クラスターをデプロイする
このチュートリアルは、シリーズの第 1 部です。 Azure CLI を使用して Linux Service Fabric クラスターを既存の Azure 仮想ネットワーク (VNET) とサブネットにデプロイする方法を学習します。 完了すると、クラウドで実行されているクラスターにアプリケーションをデプロイできるようになります。 PowerShell を使用して Windows クラスターを作成する場合は、[Azure でのセキュリティで保護された Windows クラスターの作成](service-fabric-tutorial-create-vnet-and-windows-cluster.md)に関するページを参照してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure CLI を使用して Azure に VNET を作成する
> * Azure CLI を使用してセキュリティで保護された Service Fabric クラスターを Azure に作成する
> * X.509 証明書を持つクラスターを保護する
> * Service Fabric CLI を使用してクラスターに接続する
> * クラスターの削除

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Azure にセキュリティで保護されたクラスターを作成する
> * [Service Fabric を使用して API Management をデプロイする](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に
- Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
- [Service Fabric CLI をインストール](service-fabric-cli.md)します。
- [Azure CLI 2.0](/cli/azure/install-azure-cli) をインストールします。

次の手順で、5 ノードの Service Fabric クラスターを作成します。 Azure で Service Fabric クラスターを実行することによって発生するコストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用します。

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Azure にサインインしてサブスクリプションを選択する
このガイドでは Azure CLI を使用します。 新しいセッションを開始するときに、Azure アカウントにサインインし、Azure のコマンドを実行する前にサブスクリプションを選択します。
 
Azure アカウントにサインインしてサブスクリプションを選択するには、次のスクリプトを実行します。

```azurecli
az login
az account set --subscription <guid>
```

## <a name="create-a-resource-group"></a>リソース グループの作成
デプロイ用の新しいリソース グループを作成し、リソース グループに名前を付けて、場所を指定します。

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
az group create --name $ResourceGroupName --location $Location
```

## <a name="deploy-the-network-topology"></a>ネットワーク トポロジをデプロイする
次に、API Management と Service Fabric クラスターのデプロイ先となるネットワーク トポロジを設定します。 [network.json][network-arm] Resource Manager テンプレートは、仮想ネットワーク (VNET) を作成するよう構成されています。また、Service Fabric のサブネットおよびネットワーク セキュリティ グループ (NSG)、API Management のサブネットおよび NSG を作成するようにも構成されています。 VNET、サブネット、NSG の詳細については、[こちら](../virtual-network/virtual-networks-overview.md)を参照してください。

[network.parameters.json][network-parameters-arm] パラメーター ファイルには、Service Fabric と API Management のデプロイ先となるサブネットと NSG の名前が含まれています。  API Management は、[次のチュートリアル](service-fabric-tutorial-deploy-api-management.md)でデプロイされます。 このガイドでは、パラメーターの値を変更する必要はありません。 Service Fabric Resource Manager テンプレートでは、これらのパラメーターの値を使用します。  ここで値を変更する場合は、このチュートリアルおよび [API Management のデプロイのチュートリアル](service-fabric-tutorial-deploy-api-management.md)で使用されている、他の Resource Manager テンプレート内の値も変更する必要があります。 

次の Resource Manager テンプレートとパラメーター ファイルをダウンロードします。
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

次のスクリプトを使用して、Resource Manager テンプレートと、ネットワーク セットアップ用のパラメーター ファイルをデプロイします。

```azurecli
az group deployment create \
    --name VnetDeployment \
    --resource-group $ResourceGroupName \
    --template-file network.json \
    --parameters @network.parameters.json
```
<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Service Fabric クラスターをデプロイする
ネットワーク リソースのデプロイが完了したら、次は Service Fabric クラスターを VNET の所定のサブネットと NSG にデプロイします。 既存の VNET とサブネットにクラスターをデプロイするには (この記事では既にデプロイ済み)、Resource Manager テンプレートが必要です。  詳細については、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)」を参照してください。 このチュートリアル シリーズで使用するテンプレートは、前の手順で設定した VNET、サブネット、NSG の名前を使用するようあらかじめ構成されています。  

次の Resource Manager テンプレートとパラメーター ファイルをダウンロードします。
- [linuxcluster.json][cluster-arm]
- [linuxcluster.parameters.json][cluster-parameters-arm]

セキュリティで保護されたクラスターを作成するには、このテンプレートを使用します。  クラスター証明書は、ノード間通信のセキュリティ保護と、クラスター管理エンドポイントの管理クライアントへの認証に使用される X.509 証明書です。  このクラスター証明書は、HTTPS 管理 API および HTTPS 経由の Service Fabric Explorer に対しても SSL を提供します。 Azure Key Vault を使用して、Azure で Service Fabric クラスター用の証明書を管理します。  Azure にクラスターがデプロイされると、Service Fabric クラスターの作成担当 Azure リソース プロバイダーにより Key Vault から証明書が取得されてクラスター VM にインストールされます。 

証明書機関 (CA) からの証明書をクラスター証明書として使用したり、テストを目的として自己署名証明書を作成したりできます。 クラスター証明書には、次の要件があります。

- 秘密キーが含まれている。
- キー交換のために作成されており、Personal Information Exchange (.pfx) ファイルにエクスポートできる。
- 保持しているサブジェクト名が、Service Fabric クラスターへのアクセスに使用されるドメインと一致している。 この整合性は、HTTPS 管理エンドポイントと Service Fabric Explorer 用の SSL を提供するために必要です。 証明機関 (CA) から .cloudapp.azure.com ドメインの SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書のサブジェクト名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

次のように、デプロイ用の *linuxcluster.parameters.json* ファイルにある空のパラメーターに値を入力します。

|パラメーター|値|
|---|---|
|adminPassword|Password#1234|
|adminUserName|vmadmin|
|clusterName|mysfcluster|

自己署名証明書を作成するには、**certificateThumbprint**、**certificateUrlValue**、**sourceVaultValue** パラメーターは空白のままにします。  以前に Key Vault にアップロードした既存の証明書を使用する場合、これらのパラメーター値を入力します。

次のスクリプトでは、Azure に新しいクラスターをデプロイするために、[az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) コマンドとテンプレートを使用しています。 また、コマンドレットでは、Azure に新しい Key Vault を作成し、新しい自己署名証明書をその Key Vault に追加し、証明書ファイルをローカルにダウンロードします。 [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) コマンドの他のパラメーターを使用して、既存の証明書と Key Vault の両方またはいずれかを指定できます。

```azurecli
Password="q6D7nN%6ck@6"
Subject="mysfcluster.southcentralus.cloudapp.azure.com"
VaultName="linuxclusterkeyvault"
az group create --name $ResourceGroupName --location $Location

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file linuxcluster.json --parameter-file linuxcluster.parameters.json

```

## <a name="connect-to-the-secure-cluster"></a>セキュリティで保護されたクラスターに接続する
Service Fabric CLI `sfctl cluster select` コマンドでキーを使用して、クラスターに接続します。  自己署名証明書には **--no-verify** オプションのみを使用します。

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

`sfctl cluster health` コマンドを使用して、接続できていることと、クラスターが正常であることを確認します。

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このチュートリアル シリーズの他の記事では、ここで作成したクラスターを使用します。 次の記事にすぐに進まない場合は、料金の発生を避けるため、クラスターを削除することができます。 クラスターと、そのクラスターによって使用されるすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。

Azure にログインして、クラスターを削除するサブスクリプション ID を選択します。  サブスクリプション ID は、[Azure Portal](http://portal.azure.com) にログインして確認できます。 リソース グループとクラスター リソースすべてを削除するには、[az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete) コマンドを使用します。

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>次のステップ
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure CLI を使用して Azure に VNET を作成する
> * Azure CLI を使用してセキュリティで保護された Service Fabric クラスターを Azure に作成する
> * X.509 証明書を持つクラスターを保護する
> * Service Fabric CLI を使用してクラスターに接続する
> * クラスターの削除

次のチュートリアルでは、お使いのクラスターを拡大/縮小する方法について説明します。
> [!div class="nextstepaction"]
> [クラスターを拡大/縮小する](service-fabric-tutorial-scale-cluster.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.parameters.json
