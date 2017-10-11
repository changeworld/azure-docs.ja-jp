---
title: "Azure CLI 1.0 を使用して DNS を Azure の概要 |Microsoft ドキュメント"
description: "Azure DNS 内の DNS ゾーンとレコードを作成する方法を説明します。 これは、最初の DNS ゾーンおよび Azure CLI 1.0 を使用してレコードを作成および管理する方法について説明します。"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
ms.openlocfilehash: f7943b71bbd16c36df09436973d92539eb62b210
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-dns-using-azure-cli-10"></a>Azure CLI 1.0 を使用して DNS を Azure の概要します。

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

この記事では、最初の DNS ゾーンと、クロスプラット フォーム Azure CLI 1.0 を使用して、Windows、Mac および Linux で使用されているレコードを作成する手順について説明します。 また、Azure ポータルまたは Azure PowerShell を使用してこれらの手順を実行することができます。

DNS ゾーンを使用して、特定のドメインの DNS レコードをホストします。 Azure dns、ドメインのホストを開始するには、そのドメイン名の DNS ゾーンを作成する必要があります。 この DNS ゾーンの内部ドメインの場合は、各 DNS レコードが作成されます。 最後に、DNS ゾーンをインターネットに公開するには、ドメインのネーム サーバーを構成する必要があります。 これらの各手順のとおりです。

これらの手順では、既にインストールして Azure CLI 1.0 にサインインするいると仮定します。 詳細については、次を参照してください。 [Azure CLI 1.0 を使用する DNS ゾーンを管理する方法](dns-operations-dnszones-cli-nodejs.md)です。

## <a name="create-the-resource-group"></a>リソース グループを作成します。

DNS ゾーンを作成する前に、DNS ゾーンを格納する、リソース グループが作成されます。 コマンドを次に示します。

```azurecli
azure group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-zone"></a>DNS ゾーンを作成します。

使用して DNS ゾーンを作成、`azure network dns zone create`コマンド。 表示する型は、このコマンドのヘルプ`azure network dns zone create -h`です。

次の例と呼ばれる DNS ゾーンを作成する*contoso.com*リソース グループと呼ばれる*MyResourceGroup*です。 独自の値に置き換えて、DNS ゾーンを作成するのに例を使用します。

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```


## <a name="create-a-dns-record"></a>DNS レコードを作成します。

DNS レコードを作成するを使用して、`azure network dns record-set add-record`コマンド。 詳細については、次を参照してください。`azure network dns record-set add-record -h`です。

次の例では、相対名で DNS ゾーンのリソース グループ"MyResourceGroup"で"contoso.com"、"www"をレコードを作成します。 レコード セットの完全修飾名は、"www.contoso.com"です。 レコードの種類は、IP アドレス、「1.2.3.4」の"A",、既定値は 3600 秒 (1 時間) の TTL が使用されます。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

その他の種類、代替の TTL 値を 1 つ以上のレコードにレコード セットのレコードし、既存のレコードを変更するを参照してください。 [Manage DNS レコードと、Azure CLI 1.0 を使用して、レコード セット](dns-operations-recordsets-cli-nodejs.md)です。


## <a name="view-records"></a>レコードの表示

ゾーンでの DNS レコードを表示するには、次のように使用します。

```azurecli
azure network dns record-set list MyResourceGroup contoso.com
```


## <a name="update-name-servers"></a>ネーム サーバーを更新します。

入力が完了したら、DNS ゾーンとレコードが正しく設定されて、Azure DNS ネーム サーバーを使用するドメイン名を構成する必要があります。 これにより、DNS レコードを検索するインターネット上の他のユーザーです。

ゾーンのネーム サーバーがで指定された、`azure network dns zone show`コマンド。

```azurecli
azure network dns zone show MyResourceGroup contoso.com

info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 3
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            :
info:    network dns zone show command OK
```

(ドメイン名の購入元) ドメイン名レジストラーでこれらの名前のサーバーを構成する必要があります。 レジストラーはドメインのネーム サーバーを設定するオプションを提供します。 詳細については、次を参照してください。 [Azure DNS ドメインの委任](dns-domain-delegation.md)です。

## <a name="delete-all-resources"></a>すべてのリソースを削除します。
 
この記事で作成されたすべてのリソースを削除するには、次の手順を実行します。

```azurecli
azure group delete --name MyResourceGroup
```

## <a name="next-steps"></a>次のステップ

Azure DNS の詳細については、次を参照してください。 [Azure DNS 概要](dns-overview.md)です。

Azure DNS 内の DNS ゾーンの管理に関する詳細についてを参照してください。 [Azure CLI 1.0 を使用して DNS を Azure 内の管理の DNS ゾーン](dns-operations-dnszones-cli-nodejs.md)です。

Azure DNS 内の DNS レコードの管理に関する詳細についてを参照してください。 [Manage DNS レコードとレコードは、Azure CLI 1.0 を使用して DNS を Azure で設定](dns-operations-recordsets-cli-nodejs.md)です。

