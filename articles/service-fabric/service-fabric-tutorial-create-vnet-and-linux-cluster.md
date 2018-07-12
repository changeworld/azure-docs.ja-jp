---
title: Azure に Linux Service Fabric クラスターを作成する | Microsoft Docs
description: このチュートリアルでは、Azure CLI を使用して Linux Service Fabric クラスターを既存の Azure 仮想ネットワークにデプロイする方法を学習します。
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
ms.openlocfilehash: 161687ec2275558adb235dc63b5244a0a8ff7e47
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110795"
---
# <a name="tutorial-deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>チュートリアル: Azure 仮想ネットワークに Linux Service Fabric クラスターをデプロイする

このチュートリアルは、シリーズの第 1 部です。 Azure CLI とテンプレートを使用して、Linux Service Fabric クラスターを [Azure 仮想ネットワーク (VNET)](../virtual-network/virtual-networks-overview.md) および[ネットワーク セキュリティ グループ (NSG)](../virtual-network/virtual-networks-nsg.md)にデプロイする方法を説明します。 完了すると、クラウドで実行されているクラスターにアプリケーションをデプロイできるようになります。 PowerShell を使用して Windows クラスターを作成する場合は、[Azure でのセキュリティで保護された Windows クラスターの作成](service-fabric-tutorial-create-vnet-and-windows-cluster.md)に関するページを参照してください。

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
> * [クラスターをスケールインまたはスケールアウトする](service-fabric-tutorial-scale-cluster.md)
> * [クラスターのランタイムをアップグレードする](service-fabric-tutorial-upgrade-cluster.md)
> * [Service Fabric を使用して API Management をデプロイする](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
* [Service Fabric CLI をインストール](service-fabric-cli.md)します。
* [Azure CLI 2.0](/cli/azure/install-azure-cli) をインストールします。

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

このチュートリアルでは、1 つのノード タイプで 5 つのノードを持つクラスターをデプロイします。 ただし、運用環境クラスター デプロイの場合、[キャパシティ プランニング](service-fabric-cluster-capacity.md)は重要なステップです。 ここでは、そのプロセスの一環として考慮すべき事柄の一部を取り上げます。

* クラスターで必要となるノードの数とノード タイプ
* ノード タイプごとの特性 (たとえば、サイズ、プライマリ/非プライマリ、インターネット接続、VM 数)
* クラスターの信頼性と耐久性の特徴

## <a name="download-and-explore-the-template"></a>テンプレートのダウンロードと詳細の確認

次の Resource Manager テンプレート ファイルをダウンロードします。

* [vnet-linuxcluster.json][template]
* [vnet-linuxcluster.parameters.json][parameters]

[vnet-linuxcluster.json][template] を使用して、次の項目を含むいくつかのリソースをデプロイします。

### <a name="service-fabric-cluster"></a>Service Fabric クラスター

次の特性を備えた Linux クラスターがデプロイされます。

* 単一ノード型
* プライマリ ノード型に 5 つのノード (テンプレート パラメーターで構成可能)
* OS: Ubuntu 16.04 LTS (テンプレート パラメーターで構成可能)
* 証明書の保護 (テンプレート パラメーターで構成可能)
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

### <a name="virtual-network-subnet-and-network-security-group"></a>仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループ

仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループの名前は、テンプレート パラメーターで宣言されます。  仮想ネットワークとサブネットのアドレス空間もテンプレート パラメーターで宣言されます。

* 仮想ネットワークのアドレス空間: 10.0.0.0/16
* Service Fabric サブネットのアドレス空間: 10.0.2.0/24

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
|adminUserName|vmadmin| クラスター VM の管理者ユーザー名。 |
|adminPassword|Password#1234| クラスター VM の管理者パスワード。|
|clusterName|mysfcluster123| クラスターの名前。 |
|location|southcentralus| クラスターの場所。 |
|certificateThumbprint|| <p>自己署名証明書を作成する場合または証明書ファイルを提供する場合は、値を空にする必要があります。</p><p>以前にキー コンテナーにアップロードされた既存の証明書を使用するには、証明書の拇印値を入力します。 例: "6190390162C988701DB5676EB81083EA608DCCF3"。 </p>|
|certificateUrlValue|| <p>自己署名証明書を作成する場合または証明書ファイルを提供する場合は、値を空にする必要があります。</p><p>以前にキー コンテナーにアップロードされた既存の証明書を使用するには、証明書の URL を入力します。 (例: "https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346")。</p>|
|sourceVaultValue||<p>自己署名証明書を作成する場合または証明書ファイルを提供する場合は、値を空にする必要があります。</p><p>以前にキー コンテナーにアップロードされた既存の証明書を使用するには、ソース コンテナー値を入力します。 たとえば、"/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT" と入力します。</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>仮想ネットワークとクラスターのデプロイ

次に、ネットワーク トポロジを設定し、Service Fabric クラスターをデプロイします。 [vnet-linuxcluster.json][template] Resource Manager テンプレートを使用して、仮想ネットワーク (VNET) を作成し、さらに Service Fabric のサブネットとネットワーク セキュリティ グループ (NSG) を作成します。 このテンプレートを使用すると、証明書セキュリティが有効なクラスターもデプロイできます。  運用環境クラスターの場合は、証明機関 (CA) から取得した証明書をクラスター証明書として使用します。 自己署名証明書を使用して、テスト クラスターを保護することができます。

次のスクリプトでは、既存の証明書で保護された新しいクラスターをデプロイするために、[az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) コマンドとテンプレートを使用しています。 また、このコマンドを使用して、Azure に新しいキー コンテナーを作成し、証明書をアップロードします。

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file vnet-linuxcluster.json --parameter-file vnet-linuxcluster.parameters.json
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

## <a name="next-steps"></a>次の手順

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

[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.parameters.json