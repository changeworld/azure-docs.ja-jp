---
title: プライベート リンクを使用してプライベート エンドポイントを設定する
description: コンテナー レジストリにプライベート エンドポイントを設定し、ローカル仮想ネットワークでプライベート リンク経由のアクセスを有効にします。 プライベート リンク アクセスは、Premium サービス レベルの機能です。
ms.topic: article
ms.date: 10/26/2021
ms.openlocfilehash: ef3f6cd30852f6c2bac5f5e9dce383d8f4501023
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523412"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Azure Private Link を使用して Azure Container Registry にプライベートで接続する

[Azure Private Link](../private-link/private-link-overview.md) を利用し、レジストリ エンドポイントに仮想ネットワーク プライベート IP アドレスを割り当てることでレジストリへのアクセスを制限します。 仮想ネットワーク上のクライアントとレジストリのプライベート エンドポイント間のネットワーク トラフィックは、仮想ネットワークおよび Microsoft バックボーン ネットワーク上のプライベート リンクを経由することで、パブリック インターネットにさらされないようにします。 また、Private Link を使用すると、[Azure ExpressRoute](../expressroute/expressroute-introduction.MD) プライベート ピアリングまたは [VPN ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpngateways.md)を介して、オンプレミスからのプライベート レジストリへのアクセスを有効にすることもできます。

設定がレジストリの割り当てられたプライベート IP アドレスに解決されるように、レジストリのプライベート エンドポイント用に [DNS 設定を構成する](../private-link/private-endpoint-overview.md#dns-configuration)ことができます。 DNS 構成では、ネットワーク内のクライアントとサービスは、レジストリの完全修飾ドメイン名 (*myregistry.azurecr.io* など) で引き続きレジストリにアクセスできます。 

この記事では、Azure portal (推奨) または Azure CLI を使用して、レジストリのプライベート エンドポイントを構成する方法について説明します。 この機能は、**Premium** コンテナー レジストリ サービス レベルで使用できます。 レジストリ サービスのレベルと制限については、[Azure Container Registry のレベル](container-registry-skus.md)に関するページを参照してください。

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

> [!NOTE]
> 2021 年 10 月以降、新しいコンテナー レジストリには、最大 200 個のプライベート エンドポイントを使えるようになります。 それより前に作成したレジストリの場合、最大 10 個のプライベート エンドポイントを使うことができます。 レジストリの制限を確認するには、[az acr show-usage](/cli/azure/acr#az_acr_show_usage) コマンドを使います。

## <a name="prerequisites"></a>前提条件

* プライベート エンドポイントを設定する仮想ネットワークとサブネット。 必要に応じて、[新しい仮想ネットワークとサブネットを作成](../virtual-network/quick-create-portal.md)してください。
* テストのために、仮想ネットワークに VM を設定することをお勧めします。 レジストリにアクセスするためのテスト仮想マシンを作成する手順については、「[Docker 対応仮想マシンの作成](container-registry-vnet.md#create-a-docker-enabled-virtual-machine)」を参照してください。 
* この記事の Azure CLI の手順を使用する場合は、Azure CLI バージョン 2.6.0 以降をお勧めします。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。 または、[Azure Cloud Shell](../cloud-shell/quickstart.md) で実行します。
* コンテナー レジストリがまだない場合は、1 つ作成し (Premium レベルが必要)、Microsoft Container Registry から `mcr.microsoft.com/hello-world` などのサンプル パブリック イメージを[インポート](container-registry-import-images.md)します。 たとえば、[Azure portal][quickstart-portal] または [Azure CLI][quickstart-cli] を使用してレジストリを作成します。

### <a name="register-container-registry-resource-provider"></a>コンテナー レジストリ リソース プロバイダーを登録する

別の Azure サブスクリプションまたはテナント内のプライベート リンクを使用してレジストリ アクセスを構成するには、そのサブスクリプションで Azure Container Registry の[リソース プロバイダーを登録する](../azure-resource-manager/management/resource-providers-and-types.md)必要があります。 Azure portal、Azure CLI、またはその他のツールを使用します。

例:

```azurecli
az account set --subscription <Name or ID of subscription of private link>

az provider register --namespace Microsoft.ContainerRegistry
``` 

## <a name="set-up-private-endpoint---portal-recommended"></a>プライベート エンドポイントを設定する - ポータル (推奨)

レジストリを作成するときにプライベート エンドポイントを設定するか、または既存のレジストリにプライベート エンドポイントを追加します。 

### <a name="create-a-private-endpoint---new-registry"></a>プライベート エンドポイントの作成 - 新しいレジストリ

1. ポータルでレジストリを作成するとき、 **[基本]** タブの **[SKU]** で **[Premium]** を選択します。
1. **[ネットワーク]** タブを選択します。
1. **[ネットワーク接続]** で **[プライベート エンドポイント]** を選択し、 **[+ 追加]** を選択します。
1. 次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | 既存のグループの名前を入力するか、新しいものを作成します。|
    | 名前 | 一意の名前を入力します。 |
    | レジストリのサブリソース |**レジストリ** を選択します|
    | **ネットワーク** | |
    | 仮想ネットワーク| プライベート エンドポイントの仮想ネットワークを選択します。 例: *myDockerVMVNET*。 |
    | Subnet | プライベート エンドポイントのサブネットを選択します。 例: *myDockerVMSubnet*。 |
    |**プライベート DNS の統合**||
    |プライベート DNS ゾーンとの統合 |**[はい]** を選択します。 |
    |プライベート DNS ゾーン |*(新規) privatelink.azurecr.io* を選択します |
    |||
1. 残りのレジストリ設定を構成し、 **[確認と作成]** を選択します。
  
:::image type="content" source="media/container-registry-private-link/private-link-create-portal.png" alt-text="プライベート エンドポイントを使用してレジストリを作成する":::



これで、プライベート リンクが構成され、使用できるようになりました。

### <a name="create-a-private-endpoint---existing-registry"></a>プライベート エンドポイントの作成 - 既存のレジストリ

1. ポータルで、自分のコンテナー レジストリに移動します。
1. **[設定]** で **[ネットワーク]** を選択します。
1. **[プライベート エンドポイント]** タブで **[+ プライベートエンドポイント]** を選択します。
    :::image type="content" source="media/container-registry-private-link/private-endpoint-existing-registry.png" alt-text="レジストリにプライベート エンドポイントを追加する":::

1. **[基本]** タブで、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | 既存のグループの名前を入力するか、新しいものを作成します。|
    | **インスタンスの詳細** |  |
    | 名前 | 名前を入力します。 |
    |リージョン|リージョンを選択します。|
    |||
1. **次へ:リソース** を選択します。
1. 次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    |接続方法  | この例では、 **[マイ ディレクトリ内の Azure リソースに接続する]** を選択します。|
    | サブスクリプション| サブスクリプションを選択します。 |
    | リソースの種類 | **[Microsoft.ContainerRegistry/registries]** を選択します。 |
    | リソース |レジストリの名前を選択します|
    |ターゲット サブリソース |**レジストリ** を選択します|
    |||
1. **次へ:構成** を選択します。
1. 次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    |**ネットワーク**| |
    | 仮想ネットワーク| プライベート エンドポイントの仮想ネットワークを選択します。 |
    | Subnet | プライベート エンドポイントのサブネットを選択します。 |
    |**プライベート DNS の統合**||
    |プライベート DNS ゾーンとの統合 |**[はい]** を選択します。 |
    |プライベート DNS ゾーン |*(新規) privatelink.azurecr.io* を選択します |
    |||

1. **[Review + create]\(レビュー + 作成\)** を選択します。 **[確認および作成]** ページが表示され、Azure によって構成が検証されます。 
1. "**証に成功しました**" というメッセージが表示されたら、 **[作成]** を選択します。

### <a name="confirm-endpoint-configuration"></a>エンドポイント構成を確認する

プライベート エンドポイントが作成されると、ポータルの **[プライベート エンドポイント]** 設定にプライベート ゾーンの DNS 設定が表示されます。

1. ポータルで、コンテナー レジストリに移動し、 **[設定]、[ネットワーク]** の順に選択します。
1. **[プライベート エンドポイント]** タブで、作成したプライベートエンドポイントを選択します。 
1. **[DNS の構成]** を選択します。
1. リンク設定とカスタム DNS 設定を確認します。

:::image type="content" source="media/container-registry-private-link/private-endpoint-overview.png" alt-text="ポータルのエンドポイント DNS 設定":::
## <a name="set-up-private-endpoint---cli"></a>プライベート エンドポイントを設定する - CLI

この記事の Azure CLI 例では、次の環境変数を使用します。 プライベート エンドポイントを設定するには、既存のコンテナー レジストリ、仮想ネットワーク、サブネットの名前が必要になります。 ご利用の環境に適した値に置き換えてください。 すべての例は、Bash シェル用に書式設定されています。

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name> # Resource group for your existing virtual network and subnet
NETWORK_NAME=<virtual-network-name>
SUBNET_NAME=<subnet-name>
```
### <a name="disable-network-policies-in-subnet"></a>サブネットでネットワーク ポリシーを無効にする

プライベート エンドポイントのサブネットで、ネットワーク セキュリティ グループなどの[ネットワーク ポリシーを無効にします](../private-link/disable-private-endpoint-network-policy.md)。 [az network vnet subnet update][az-network-vnet-subnet-update] を使用して、サブネット構成を更新します。

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>プライベート DNS ゾーンを構成する

プライベート Azure コンテナー レジストリ ドメイン用の[プライベート Azure DNS ゾーン](../dns/private-dns-privatednszone.md)を作成します。 後の手順で、この DNS ゾーン内にレジストリ ドメインの DNS レコードを作成します。 詳細については、この記事で後述する「[DNS の構成オプション](#dns-configuration-options)」を参照してください。

プライベート ゾーンを使用して Azure コンテナー レジストリの既定の DNS 解決をオーバーライドするには、ゾーンに **privatelink.azurecr.io** という名前を付ける必要があります。 次の [az network private-dns zone create][az-network-private-dns-zone-create] コマンドを実行して、プライベート ゾーンを作成します。

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>関連付けリンクを作成する

[az network private-dns link vnet create][az-network-private-dns-link-vnet-create] を実行して、プライベート ゾーンを仮想ネットワークに関連付けます。 この例では、*myDNSLink* というリンクを作成します。

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>プライベート レジストリ エンドポイントを作成する

このセクションでは、仮想ネットワーク内にレジストリのプライベート エンドポイントを作成します。 まず、レジストリのリソース ID を取得します。

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

[az network private endpoint create][az-network-private-endpoint-create] コマンドを実行して、レジストリのプライベート エンドポイントを作成します。

次の例では、*myPrivateEndpoint* というエンドポイントと *myConnection* というサービス接続を作成します。 エンドポイントのコンテナー レジストリ リソースを指定するには、`--group-ids registry` を渡します。

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-endpoint-ip-configuration"></a>エンドポイントの IP 構成を取得する

DNS レコードを構成するには、プライベート エンドポイントの IP 構成を取得します。 この例でのプライベート エンドポイントのネットワーク インターフェイスには、コンテナー レジストリの 2 つのプライベート IP アドレスが関連付けられています。1 つはレジストリ自体用で、もう 1 つはレジストリのデータ エンドポイント用です。 レジストリが geo レプリケートされている場合は、各レプリカに追加の IP アドレスが関連付けられます。

最初に [az network private endpoint show][az-network-private-endpoint-show] を実行し、ネットワーク インターフェイス ID について、プライベート エンドポイントにクエリを実行します。

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

次の [az network nic show][az-network-nic-show] コマンドでは、コンテナー レジストリとレジストリのデータ エンドポイント用のプライベート IP アドレスと FQDN を取得します。

```azurecli
REGISTRY_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateIpAddress" \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateIpAddress" \
  --output tsv)

# An FQDN is associated with each IP address in the IP configurations

REGISTRY_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateLinkConnectionProperties.fqdns" \
  --output tsv)

DATA_ENDPOINT_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateLinkConnectionProperties.fqdns" \
  --output tsv)
```

#### <a name="additional-endpoints-for-geo-replicas"></a>geo レプリカの追加のエンドポイント

レジストリが [geo レプリケート](container-registry-geo-replication.md)されている場合、レジストリ レプリカごとに追加のデータ エンドポイントを照会します。 たとえば、*eastus* リージョンでは、次のようになります。 

```azurecli
REPLICA_LOCATION=eastus
GEO_REPLICA_DATA_ENDPOINT_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REPLICA_LOCATION'].privateIpAddress" \
  --output tsv) 

GEO_REPLICA_DATA_ENDPOINT_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REPLICA_LOCATION'].privateLinkConnectionProperties.fqdns" \
  --output tsv)
```
### <a name="create-dns-records-in-the-private-zone"></a>プライベート ゾーンに DNS レコードを作成する

次のコマンドでは、レジストリ エンドポイントとそのデータ エンドポイントのプライベート ゾーンに DNS レコードを作成します。 たとえば、*westeurope* リージョンに *myregistry* という名前のレジストリがある場合、エンドポイント名は `myregistry.azurecr.io` と `myregistry.westeurope.data.azurecr.io` になります。 

まず、[az network private-dns record-set a create][az-network-private-dns-record-set-a-create] を実行して、レジストリ エンドポイントとデータ エンドポイント用の空の A レコード セットを作成します。

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

[az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record] コマンドを実行して、レジストリ エンドポイントとデータ エンドポイント用の A レコードを作成します。

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $REGISTRY_PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

#### <a name="additional-records-for-geo-replicas"></a>geo レプリカの追加のレコード

レジストリが geo レプリケートされている場合は、レプリカごとに追加の DNS 設定を作成します。 この例では、*eastus* リージョンで続行します。

```azurecli
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REPLICA_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REPLICA_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $GEO_REPLICA_DATA_ENDPOINT_PRIVATE_IP
```

これで、プライベート リンクが構成され、使用できるようになりました。

## <a name="disable-public-access"></a>パブリック アクセスを無効にする

さまざまなシナリオで、パブリック ネットワークからのレジストリ アクセスを無効にします。 この構成により、仮想ネットワークの外にいるクライアントがレジストリ エンドポイントに到達するのを防ぐことができます。 

### <a name="disable-public-access---portal"></a>パブリック アクセスを無効にする - ポータル

1. ポータルで、コンテナー レジストリに移動し、 **[設定] > [ネットワーク]** の順に選択します。
1. **[パブリック アクセス]** タブの **[Allow public network access]\(パブリック ネットワーク アクセスを許可する\)** で、 **[無効]** を選択します。 次に、 **[保存]** を選択します。

### <a name="disable-public-access---cli"></a>パブリック アクセスを無効にする - CLI

Azure CLI を使用してパブリック アクセスを無効にするには、[az acr update][az-acr-update] を実行し、`--public-network-enabled` を `false` に設定します。 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```

## <a name="validate-private-link-connection"></a>プライベート リンク接続を確認する

プライベート エンドポイントのサブネット内のリソースがプライベート IP アドレスを使用してレジストリに接続され、適切なプライベート DNS ゾーンの統合が行われていることを確認する必要があります。

プライベート リンク接続を検証するには、仮想ネットワークに設定した仮想マシンに接続します。

`nslookup` や `dig` などのユーティリティを実行し、プライベート リンク経由でレジストリの IP アドレスを検索します。 次に例を示します。

```bash
dig $REGISTRY_NAME.azurecr.io
```

出力例では、サブネットのアドレス空間にあるレジストリの IP アドレスが示されています。

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

この結果と、パブリック エンドポイント経由の同じレジストリの `dig` 出力のパブリック IP アドレスを比較します。

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
```

### <a name="registry-operations-over-private-link"></a>プライベート リンク経由のレジストリ操作

ネットワーク内の仮想マシンからレジストリ操作を実行できることも確認します。 仮想マシンへの SSH 接続を行い、[az acr login][az-acr-login] を実行してレジストリにログインします。 VM の構成によっては、次のコマンドの前に `sudo` を付けることが必要になる場合があります。

```bash
az acr login --name $REGISTRY_NAME
```

`docker pull` などのレジストリ操作を実行して、サンプル イメージをレジストリからプルします。 `hello-world:v1` を、レジストリに適したイメージとタグに置き換え、前にレジストリ ログイン サーバー名 (すべて小文字) を付けます。

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker によってイメージが VM に正常にプルされます。

## <a name="manage-private-endpoint-connections"></a>プライベート エンドポイント接続を管理する

Azure portal を使用するか、[az acr private-endpoint-connection][az-acr-private-endpoint-connection] コマンド グループのコマンドを使って、レジストリのプライベート エンドポイント接続を管理します。 操作には、レジストリのプライベート エンドポイント接続の承認、削除、一覧表示、拒否、または詳細の表示が含まれます。

たとえば、レジストリのプライベート エンドポイント接続の一覧を表示するには、[az acr private-endpoint-connection list][az-acr-private-endpoint-connection-list] コマンドを実行します。 次に例を示します。

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

この記事の手順を使用してプライベート エンドポイント接続を設定すると、レジストリでは、レジストリに対する Azure RBAC アクセス許可を持つクライアントとサービスからの接続が自動的に受け入れられます。 エンドポイントを設定して、手動による接続の承認を要求することができます。 プライベート エンドポイント接続を承認および拒否する方法については、「[プライベート エンドポイント接続を管理する](../private-link/manage-private-endpoint.md)」を参照してください。

> [!IMPORTANT]
> 現在、プライベート エンドポイントをレジストリから削除すると、プライベート ゾーンへの仮想ネットワークのリンクも削除することが必要になる場合があります。 リンクが削除されない場合、`unresolvable host` のようなエラーが表示されることがあります。

## <a name="dns-configuration-options"></a>DNS の構成オプション

この例のプライベート エンドポイントは、基本仮想ネットワークに関連付けられているプライベート DNS ゾーンと統合されます。 このセットアップでは、Azure で提供される DNS サービスを直接利用して、レジストリのパブリック FQDN を仮想ネットワーク内のそのプライベート IP アドレスに解決します。 

プライベート リンクでは、カスタム DNS ソリューションによるものも含め、プライベート ゾーンを使用する追加の DNS 構成シナリオがサポートされます。 たとえば、カスタム DNS ソリューションを仮想ネットワークにデプロイしたり、ネットワークのオンプレミスで VPN ゲートウェイまたは Azure ExpressRoute を利用して仮想ネットワークに接続したりします。 

そのようなシナリオでレジストリのパブリック FQDN をプライベート IP アドレスに解決するには、Azure DNS サービスのサーバーレベル フォワーダーを構成する必要があります (168.63.129.16)。 構成のオプションと手順は、厳密には、既存のネットワークや DNS によって異なります。 サンプルが必要であれば、[Azure プライベート エンドポイント DNS 構成](../private-link/private-endpoint-dns.md)に関するページを参照してください。

> [!IMPORTANT]
> 高可用性を実現するために複数のリージョンでプライベート エンドポイントを作成した場合は、リージョンごとに個別のリソース グループを使用し、仮想ネットワークとそれに関連付けられたプライベート DNS ゾーンをそこに配置することをお勧めします。 この構成を使用すると、同じプライベート DNS ゾーンの共有による予期しない DNS 解決が発生するのを防ぎます。

### <a name="manually-configure-dns-records"></a>DNS レコードを手動で構成する

一部のシナリオでは、Azure で提供されるプライベート ゾーンを使用するのではなく、プライベート ゾーンで DNS レコードを手動で構成することが必要になる場合があります。 必ず、各エンドポイント (レジストリ エンドポイント、レジストリのデータ エンドポイント、追加のリージョン レプリカ用のデータ エンドポイント) のそれぞれにレコードを作成してください。 すべてのレコードが構成されていないと、レジストリにアクセスできない可能性があります。

> [!IMPORTANT]
> 新しいレプリカを後で追加する場合、そのリージョンでデータ エンドポイントの新しい DNS レコードを手動で追加する必要があります。 たとえば、場所 northeurope で *myregistry* のレプリカを作成する場合、`myregistry.northeurope.data.azurecr.io` のレコードを追加します。

DNS レコードの作成に必要な FQDN とプライベート IP アドレスは、プライベート エンドポイントのネットワーク インターフェイスに関連付けられます。 これらの情報は、Azure portal または Azure CLI を使用して取得できます。

* ポータルで、プライベート エンドポイントに移動し、 **[DNS の構成]** を選択します。 
* Azure CLI を使用して、[az network nic show][az-network-nic-show] コマンドを実行します。 コマンドの例については、この記事で前述した「[エンドポイントの IP 構成を取得する](#get-endpoint-ip-configuration)」を参照してください。

DNS レコードを作成したら、レジストリの FQDN がそれぞれのプライベート IP アドレスに適切に解決されることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ポータルでリソースをクリーンアップするには、リソース グループに移動します。 リソース グループが読み込まれたら、 **[リソース グループの削除]** をクリックして、リソース グループとそこに格納されているリソースを削除します。

すべての Azure リソースを同じリソース グループ内に作成し、それらが不要になった場合は、1 つの [az group delete](/cli/azure/group) コマンドを使用してリソースを削除することもできます。

```azurecli
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>次のステップ

* Private Link の詳細については、[Azure Private Link](../private-link/private-link-overview.md) に関するドキュメントを参照してください。

* プライベート エンドポイントにルーティングされる仮想ネットワーク内の DNS 設定を確認するには、`--vnet` パラメーターを指定して [az acr check-health](/cli/azure/acr#az_acr_check_health) コマンドを実行します。 詳細については、「[Azure コンテナー レジストリの正常性のチェック](container-registry-check-health.md)」を参照してください。 

* クライアント ファイアウォールの内側からレジストリ アクセス規則を設定する必要がある場合は、「[ファイアウォールの内側から Azure コンテナー レジストリにアクセスする規則を構成する](container-registry-firewall-access-rules.md)」を参照してください。

* [Azure プライベート エンドポイント接続に関する問題のトラブルシューティング](../private-link/troubleshoot-private-endpoint-connectivity.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az_acr_private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az_acr_private_endpoint_connection_approve
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az_network_private_endpoint_create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az_network_private_endpoint_show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az_network_private_dns_zone_create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az_network_private_dns_link_vnet_create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_add_record
[az-network-nic-show]: /cli/azure/network/nic#az_network_nic_show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
