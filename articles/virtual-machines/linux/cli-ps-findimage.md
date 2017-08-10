---
title: "Azure CLI を使用した Linux VM イメージの選択 |Microsoft Docs"
description: "Azure CLI を使用して Marketplace VM イメージの発行元、プラン、SKU、バージョンを決定する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/11/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 72c5c2efe2c8a60f13d18b595062448e6a0d1816
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Azure CLI を使用して Azure Marketplace の Linux VM イメージを見つける方法
このトピックでは、Azure CLI 2.0 を使用して Azure Marketplace で VM イメージを見つける方法を説明します。 この情報は、Linux VM の作成時に Marketplace イメージを指定するためにご利用ください。

[Azure CLI 2.0 ](/cli/azure/install-az-cli2)の最新版がインストール済みであることと、Azure アカウントにログインしていること (`az login`) を確認してください。

## <a name="list-popular-images"></a>よく使われるイメージを一覧表示する

`--all` オプションを指定せずに [az vm image list](/cli/azure/vm/image#list) コマンドを実行して、Azure Marketplace でよく使われる VM イメージの一覧を確認します。 たとえば、次のコマンドを実行して、よく使用されるイメージのキャッシュされた一覧を表形式で表示します。

```azurecli
az vm image list --output table
```

出力には、*Publisher*:*Offer*:*Sku*:*Version* という形式で、URN (*Urn* 列の値) が含まれます。 `az vm create` で VM を作成するときは、この値を使ってイメージを指定します。 よく使われる VM イメージのいずれかを使用して VM を作成する場合は、代わりに、*UbuntuLTS* などの URN の別名を指定することもできます。

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="list-all-current-images"></a>現在のすべてのイメージを一覧表示する

Marketplace にあるすべての VM イメージの現在の一覧を取得するには、`az vm image list` コマンドを `--all` オプションと組み合わせて使用します。 このバージョンのコマンドは、完了するまでに少し時間がかかります。


```azurecli
az vm image list --all
```

`--location` オプションを使って特定の場所を指定しない場合は、既定で `westus` の値が返されます  (`az configure --defaults location=<location>` を実行すると、別の既定の場所を設定できます)。


対話的に調査したい場合は、出力をローカル ファイルに指定します。 For example:

```azurecli
az vm image list --all > allImages.json
```




## <a name="find-specific-images"></a>特定のイメージを検索する

追加のオプションと組み合わせて `az vm image list` を使用して、特定の場所、プラン、発行元、または SKU に検索範囲を制限します。 たとえば、次のコマンドではすべての Debian プランが表示されます (`--all` スイッチがなければ、一般的なイメージのローカル キャッシュを検索するだけであることを忘れないでください)。

```azurecli
az vm image list --offer Debian --all --output table 
```

出力は長い一覧になる場合があるため、ここでは一部を示しています。 
```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  --------------
Debian   credativ     7                  credativ:Debian:7:7.0.201602010                  7.0.201602010
Debian   credativ     7                  credativ:Debian:7:7.0.201603020                  7.0.201603020
Debian   credativ     7                  credativ:Debian:7:7.0.201604050                  7.0.201604050
Debian   credativ     7                  credativ:Debian:7:7.0.201604200                  7.0.201604200
Debian   credativ     7                  credativ:Debian:7:7.0.201606280                  7.0.201606280
Debian   credativ     7                  credativ:Debian:7:7.0.201609120                  7.0.201609120
Debian   credativ     7                  credativ:Debian:7:7.0.201611020                  7.0.201611020
...
```


`--location`、`--publisher`、および `--sku` オプションを指定して、同様のフィルターを適用します。 すべての Debian イメージを見つけるために `--offer Deb` を検索するなど、フィルターに対して部分一致を実行することもできます。

たとえば、次のコマンドでは、`westeurope` にあるすべての Debian 8 SKU を一覧表示します。

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

出力:

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
...
```


## <a name="navigate-the-images"></a>イメージの移動 
任意の場所にあるイメージを検索する別の方法として、[az vm image list-publishers](/cli/azure/vm/image#list-publishers)、[az vm image list-offers](/cli/azure/vm/image#list-offers)、および[az vm image list-skus](/cli/azure/vm/image#list-skus) コマンドを連続で実行します。 これらのコマンドを使用する際は、以下の値を決定します。

1. イメージの発行元を一覧表示する。
2. 指定された発行元について、そのプランを一覧表示する。
3. 指定されたプランについて、その SKU を一覧表示する。


たとえば、次のコマンドでは、米国西部の場所にあるイメージの発行元を一覧表示します。

```azurecli
az vm image list-publishers --location westus --output table
```

出力:

```
Location    Name
----------  ----------------------------------------------------
westus      4psa
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      Acronis
westus      Acronis.Backup
westus      actian_matrix
westus      actifio
westus      activeeon
westus      adatao
...
```
これらの一覧は長くなる場合があるため、この例の出力は一部のみを示しています。 特定の発行元からのプランを検索するには、この情報を使用します。 たとえば、Canonical が米国西部の場所になっているイメージ発行元の場合、`azure vm image list-offers` を実行することでプランを検索します。 次の例のように、場所と発行元を渡します。

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

出力:

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
westus      Ubuntu_Snappy_Core
westus      Ubuntu_Snappy_Core_Docker
```
米国西部のリージョンで、Canonical が Azure で **UbuntuServer** プランを発行していることが分かります。 どんな SKU でしょうか。 その値を入手するには、`azure vm image list-skus` を実行して、見つけた場所、発行元、およびプランを設定します。

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

出力:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-DAILY-LTS
westus      12.04.5-LTS
westus      12.10
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-beta
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      16.10
westus      16.10-DAILY
westus      17.04
westus      17.04-DAILY
westus      17.10-DAILY
```

最後に、`az vm image list` コマンドを使用して、**14.04-LTS**など、目的とする特定のバージョンの SKU を検索します。

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --all --output table
```

出力:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611220  16.04.201611220
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611300  16.04.201611300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612050  16.04.201612050
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612140  16.04.201612140
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612210  16.04.201612210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201701130  16.04.201701130
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702020  16.04.201702020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702200  16.04.201702200
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702210  16.04.201702210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702240  16.04.201702240
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703020  16.04.201703020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703030  16.04.201703030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703070  16.04.201703070
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703270  16.04.201703270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703280  16.04.201703280
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703300  16.04.201703300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705080  16.04.201705080
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705160  16.04.201705160
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706100  16.04.201706100
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706191  16.04.201706191
```
## <a name="next-steps"></a>次のステップ
これで、URN 値をメモして、使用するイメージを正確に選べるようになりました。 イメージを指定するときに、必要に応じて URN のバージョン番号を "latest" に置き換えることができます。 このバージョンは常に、ディストリビューションの最新バージョンです。 検索した URN 情報を使って仮想マシンをすぐに作成するには、[Azure CLI を使用した Linux VM の作成](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。

