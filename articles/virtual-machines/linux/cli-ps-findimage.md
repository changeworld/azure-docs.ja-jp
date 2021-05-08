---
title: CLI を使用してマーケットプレース購入プランの情報を検索および使用する
description: Azure CLI を使用して、Marketplace VM イメージ用のパブリッシャー、プラン、SKU、バージョンなどのイメージ URN および購入プラン パラメーターを検索する方法を説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal, devx-track-azurecli
ms.openlocfilehash: be0535a49b47c45cad49abd1bf720b6347a660b8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484202"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Azure CLI を使用して Azure Marketplace イメージ情報を検索する

このトピックでは、Azure CLI を使用して Azure Marketplace で VM イメージを見つける方法を説明します。 これらの情報は、CLI、Resource Manager テンプレート、またはその他のツールを使用して、VM をプログラムによって作成する際、Marketplace イメージを指定するために使用できます。

また、[Azure Marketplace](https://azuremarketplace.microsoft.com/) または [Azure PowerShell](../windows/cli-ps-findimage.md) を使用して、使用できるイメージとプランを参照することもできます。 

## <a name="terminology"></a>用語

Azure Marketplace イメージには、次の属性があります。

* **発行元**: イメージを作成した組織です。 例: Canonical、MicrosoftWindowsServer
* **プラン**: 発行元によって作成された関連するイメージのグループ名です。 例: UbuntuServer、WindowsServer
* **SKU**: ディストリビューションのメジャー リリースなど、プランのインスタンス。 例: 18.04-LTS、2019-Datacenter
* **バージョン**: イメージの SKU のバージョン番号。 

これらの値は、個別に、またはイメージ *URN* として渡すことができます。その際、値はコロン (:) で区切って指定します。 例: *Publisher*:*Offer*:*Sku*:*Version*。 URN 内のバージョン番号を `latest` に置き換えると、イメージの最新バージョンを使用することができます。 

イメージの発行元が追加のライセンスや購入契約条件を提示している場合、イメージを使用するには、それらに事前に同意する必要があります。  詳細については、「[購入プランの情報を確認する](#check-the-purchase-plan-information)」を参照してください。



## <a name="list-popular-images"></a>よく使われるイメージを一覧表示する

`--all` オプションを指定せずに [az vm image list](/cli/azure/vm/image) コマンドを実行して、Azure Marketplace でよく使われる VM イメージの一覧を確認します。 たとえば、次のコマンドを実行して、よく使用されるイメージのキャッシュされた一覧を表形式で表示します。

```azurecli
az vm image list --output table
```

出力には、イメージの URN が含まれます。 また、*UbuntuLTS* のような一般的なイメージ用に作成された短縮版である *UrnAlias* を使用することもできます。

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
debian-10      Debian                  10                  Debian:debian-10:10:latest                                      Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-LVM               RedHat:RHEL:7-LVM:latest                                        RHEL                 latest
SLES           SUSE                    15                  SUSE:SLES:15:latest                                             SLES                 latest
UbuntuServer   Canonical               18.04-LTS           Canonical:UbuntuServer:18.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2019-Datacenter     MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest     Win2019Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="find-specific-images"></a>特定のイメージを検索する

Marketplace で特定の VM イメージを検索するには、`az vm image list` コマンドと `--all` オプションを使用します。 このバージョンのコマンドは完了するまでに少し時間がかかります。また、大量の出力が返されることがあるため、通常は `--publisher` または他のパラメーターを使用してリストをフィルター処理します。 

たとえば、次のコマンドではすべての Debian プランが表示されます (`--all` スイッチがなければ、一般的なイメージのローカル キャッシュを検索するだけであることを忘れないでください)。

```azurecli
az vm image list --offer Debian --all --output table 
```

出力の一部を次に示します。 

```output
Offer                                    Publisher                         Sku                                      Urn                                                                                                   Version
---------------------------------------  --------------------------------  ---------------------------------------  ----------------------------------------------------------------------------------------------------  --------------
apache-solr-on-debian                    apps-4-rent                       apache-solr-on-debian                    apps-4-rent:apache-solr-on-debian:apache-solr-on-debian:1.0.0                                         1.0.0
atomized-h-debian10-v1                   atomizedinc1587939464368          hdebian10plan                            atomizedinc1587939464368:atomized-h-debian10-v1:hdebian10plan:1.0.0                                   1.0.0
atomized-h-debian9-v1                    atomizedinc1587939464368          hdebian9plan                             atomizedinc1587939464368:atomized-h-debian9-v1:hdebian9plan:1.0.0                                     1.0.0
atomized-r-debian10-v1                   atomizedinc1587939464368          rdebian10plan                            atomizedinc1587939464368:atomized-r-debian10-v1:rdebian10plan:1.0.0                                   1.0.0
atomized-r-debian9-v1                    atomizedinc1587939464368          rdebian9plan                             atomizedinc1587939464368:atomized-r-debian9-v1:rdebian9plan:1.0.0                                     1.0.0
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.7                         1.0.7
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.8                         1.0.8
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.9                         1.0.9
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.18                          1.0.18
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.19                          1.0.19
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.20                          1.0.20
apache-web-server-with-debian-10         cognosys                          apache-web-server-with-debian-10         cognosys:apache-web-server-with-debian-10:apache-web-server-with-debian-10:1.2019.1008                1.2019.1008
docker-ce-with-debian-10                 cognosys                          docker-ce-with-debian-10                 cognosys:docker-ce-with-debian-10:docker-ce-with-debian-10:1.2019.0710                                1.2019.0710
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201602010                                                                       8.0.201602010
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201603020                                                                       8.0.201603020
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201604050                                                                       8.0.201604050
...
```


## <a name="look-at-all-available-images"></a>使用可能なすべてのイメージを確認する
 
任意の場所にあるイメージを検索する別の方法として、[az vm image list-publishers](/cli/azure/vm/image)、[az vm image list-offers](/cli/azure/vm/image)、および[az vm image list-skus](/cli/azure/vm/image) コマンドを連続で実行します。 これらのコマンドを使用する際は、以下の値を決定します。

1. 1 つの場所のイメージ発行元を一覧表示します。 この例では、*米国西部* リージョンを見ています。
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. 指定された発行元について、そのプランを一覧表示する。 この例では、パブリッシャーとして *Canonical* を追加します。
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. 指定されたプランについて、その SKU を一覧表示する。 この例では、プランとして *UbuntuServer* を追加します。
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. 特定の発行元、プラン、および SKU について、イメージのすべてのバージョンを表示します。 この例では、SKU として *18.04-LTS* を追加します。

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

[az vm create](/cli/azure/vm) コマンドで VM を作成するときに、URN 列のこの値を `--image` パラメーターを使用して渡します。 必要に応じて、URN のバージョン番号を "latest" に置き換えて、最新バージョンのイメージを使用できます。 

Resource Manager テンプレートを使って VM をデプロイする場合は、`imageReference` プロパティでイメージ パラメーターを個別に設定します。 [テンプレート リファレンス](/azure/templates/microsoft.compute/virtualmachines)をご覧ください。


## <a name="check-the-purchase-plan-information"></a>購入プランの情報を確認する

Azure Marketplace の一部の VM イメージには、プログラムでデプロイする前に同意する必要がある追加のライセンスと購入契約条件があります。  

このようなイメージから VM をデプロイするには、最初に使用するときに、サブスクリプションごとに 1 回、イメージの使用条件に同意する必要があります。 また、そのイメージから VM をデプロイするために、*購入プラン* のパラメーターも指定する必要があります

イメージの購入プラン情報を表示するには、イメージの URN を使用して [az vm image show](/cli/azure/image) コマンドを実行します。 出力内の `plan` プロパティが `null` ではない場合、イメージには、プログラムによるデプロイの前に同意しなければならない使用条件があります。

たとえば、Canonical Ubuntu Server 18.04 LTS イメージに追加条項がないのは、`plan` 情報が `null` であるためです。

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

出力:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

RabbitMQ Certified by Bitnami イメージに対して同様のコマンドを実行すると、次の `plan` プロパティが表示されます: `name`、 `product`、および `publisher` (イメージによっては、`promotion code` プロパティもあります)。 

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
出力:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

このイメージをデプロイするには、そのイメージを使用して VM をデプロイするときに、使用条件に同意し、購入プラン パラメーターを指定する必要があります。

## <a name="accept-the-terms"></a>使用条件への同意

ライセンス条項を表示し、それらに同意するには、[az vm image accept-terms](/cli/azure/vm/image/terms) コマンドを使用します。 使用条件に同意すると、サブスクリプション内で、プログラムによるデプロイが有効になります。 使用条件に同意する必要があるのは、イメージのサブスクリプションごとに 1 回だけです。 次に例を示します。

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
``` 

出力では、ライセンス条項への `licenseTextLink` が示され、`accepted` の値が `true` であることが示されます。

```output
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

条項に同意するには、次のように入力します。

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>イメージ パラメーターを使用して新しい VM をデプロイする

イメージに関する情報を使用して、`az vm create` コマンドでデプロイできます。 

Canonical からの最新の Ubuntu Server 18.04 イメージのように、プラン情報がないイメージをデプロイするには、次のように `--image` の URN を渡します。

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


Bitnami イメージによって認定された RabbitMQ のように、購入プランパラメーターを含むイメージの場合は、`--image` の URN を渡し、次のように 購入プランのパラメーターも指定します。

```azurecli
az group create --name myPurchasePlanRG --location westus

az vm create \
   --resource-group myPurchasePlanRG \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image bitnami:rabbitmq:rabbitmq:latest \
   --plan-name rabbitmq \
   --plan-product rabbitmq \
   --plan-publisher bitnami
```

イメージの使用条件への同意に関するメッセージが表示された場合は、「[使用条件への同意](#accept-the-terms)」セクションを確認してください。 `az vm image accept-terms` の出力で、画像の使用条件に同意したことを示す値 `"accepted": true,` が返されていることを確認します。


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>既存の VHD と購入プラン情報を使用する

有料の Azure Marketplace イメージを使用して作成された、VM からの既存の VHD がある場合は、その VHD から新しい VM を作成するときに、購入プランの情報を指定することが必要になる場合があります。 

元の VM、または同じ Marketplace イメージを使用して作成した他の VM がある場合は、[az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view) を使用して、その VM からプラン名、発行元、および製品情報を取得できます。 この例では、*myResourceGroup* リソース グループの *myVM* という名前の VM を取得し、購入プラン情報を表示します。

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

元の VM が削除される前にプラン情報を取得しなかった場合は、[サポート リクエスト](https://ms.portal.azure.com/#create/Microsoft.Support)を提出できます。 VM 名、サブスクリプション ID、および削除操作のタイム スタンプが必要になります。

プラン情報を取得したら、`--attach-os-disk` パラメーターを使用して VHD を指定することで、新しい VM を作成できます。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```


## <a name="next-steps"></a>次のステップ
イメージ情報を使って仮想マシンをすぐに作成するには、「[Azure CLI を使用した Linux VM の作成と管理](tutorial-manage-vm.md)」をご覧ください。
