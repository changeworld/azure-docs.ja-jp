---
title: Azure CLI 2.0 で Azure DNS Private Zones の使用を開始する | Microsoft Docs
description: Azure DNS で、プライベート DNS ゾーンとレコードを作成する方法について説明します。 Azure CLI 2.0 を使用して最初の DNS プライベート ゾーンとレコードを作成して管理するためのステップ バイ ステップ ガイドです。
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: d10f3201adb972468d8de7e66a940232ed19562d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
ms.locfileid: "30191451"
---
# <a name="get-started-with-azure-dns-private-zones-using-azure-cli-20"></a>Azure CLI 2.0 で Azure DNS Private Zones の使用を開始する

> [!div class="op_single_selector"]
> * [PowerShell](private-dns-getstarted-powershell.md)
> * [Azure CLI 2.0](private-dns-getstarted-cli.md)

この記事では、Windows、Mac、Linux で使用できるクロスプラットフォーム Azure CLI 2.0 を使用して、最初の DNS プライベート ゾーンとレコードを作成する手順について説明します。 これらの手順は、Azure PowerShell を使用して実行することもできます。

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成する必要があります。 ドメインの DNS レコードはすべて、この DNS ゾーン内に作成されます。 仮想ネットワークにプライベート DNS ゾーンを発行するには、そのゾーン内のレコードを解決することが認められた仮想ネットワークの一覧を指定します。  それらを "解決仮想ネットワーク" と呼ぶことにします。  VM が作成されたときや IP が変更されたとき、または VM が破棄されたときに絶えず Azure DNS によってホスト名レコードが維持される仮想ネットワークを指定することもできます。  それを "登録仮想ネットワーク" と呼ぶことにします。

以降の手順は、Azure CLI 2.0 がインストール済みで、既にサインインしていること、また、Private Zones に対応した必要な CLI 拡張機能がインストールされていることを前提としています。 詳細については、[Azure CLI 2.0 を使用して DNS ゾーンを管理する方法](dns-operations-dnszones-cli.md)に関するページをご覧ください。

## <a name="to-installuse-azure-dns-private-zones-feature-public-preview"></a>Azure DNS Private Zones の機能 (パブリック プレビュー) のインストール/使用
Azure DNS Private Zone の機能は、Azure CLI を拡張したものであり、パブリック プレビューとしてリリースされます。 "dns" Azure CLI 拡張機能のインストール 

```
az extension add --name dns
``` 

## <a name="create-the-resource-group"></a>リソース グループの作成

DNS ゾーンを作成する前に、DNS ゾーンが含まれるリソース グループを作成します。 コマンドを次に示します。

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-private-zone"></a>DNS プライベート ゾーンの作成

DNS プライベート ゾーンは、`az network dns zone create` コマンドを使用して作成します。 このコマンドのヘルプを表示するには、「`az network dns zone create --help`」と入力します。

次の例では、*MyResourceGroup* というリソース グループに *contoso.local* という DNS プライベート ゾーンを作成し、resolution-vnets パラメーターを使って、そのゾーンを *MyAzureVnet* という仮想ネットワークから利用できるようにします (リンクさせます)。 この例の値を実際の値に置き換えて、DNS ゾーンを作成できます。

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --resolution-vnets MyAzureVnet
```

注: 上の例で、仮想ネットワーク "MyAzureVnet" は、プライベート ゾーンと同じリソース グループおよびサブスクリプションに属しています。 異なるリソース グループまたはサブスクリプションに属している仮想ネットワークをリンクさせる必要がある場合、--resolution-vnets パラメーターには、仮想ネットワーク名だけでなく、完全な Azure Resource Manager ID を指定する必要があります。 

このゾーンにホスト名レコードを Azure で自動的に作成する必要がある場合は、*resolution-vnets* ではなく *registration-vnets* パラメーターを使用してください。  登録仮想ネットワークの解決が自動的に有効になります。

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --registration-vnets MyAzureVnet
```

## <a name="create-a-dns-record"></a>DNS レコードの作成

DNS レコードを作成するには、`az network dns record-set [record type] add-record` コマンドを使用します。 A レコードなどの詳細については、「`azure network dns record-set A add-record --help`」を参照してください。

下の例では、リソース グループ "MyResourceGroup" の DNS ゾーン "contoso.local" に、相対名が "ip1" のレコードを作成します。 レコード セットの完全修飾名は、"ip1.contoso.local" になります。 また、レコードの種類は "A"、IP アドレスは "10.0.0.1" です。

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.local -n ip1 -a 10.0.0.1
```

その他のレコードの種類、複数のレコードを持つレコード セット、代替 TTL 値、既存のレコードの変更については、[Azure CLI 2.0 を使用した DNS レコードおよびレコード セットの管理](dns-operations-recordsets-cli.md)に関するページをご覧ください。

## <a name="view-records"></a>レコードの表示

ゾーンで DNS レコードを表示するには、次を使用します。

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="get-a-dns-private-zone"></a>DNS プライベート ゾーンの取得

DNS プライベート ゾーンを取得するには、`az network dns zone show` を使用します。 `az network dns zone show --help` を使用すると、ヘルプが表示されます。

次の例では、リソース グループ *MyResourceGroup* から DNS ゾーン *contoso.local* とその関連データを返します。 

```azurecli
az network dns zone show --resource-group MyResourceGroup --name contoso.local
```

次の例は応答です。

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.local",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.local",
  "nameServers": null,
  "numberOfRecordSets": 1,
  "registrationVirtualNetworks": [],
  "resolutionVirtualNetworks": [
    {
      "additionalProperties": {},
      "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAzureVnet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
  "resourceGroup": "MyResourceGroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones",
  "zoneType": "Private"
}
```

`az network dns zone show` では DNS レコードが返されないことにご注意ください。 DNS レコードを一覧表示するには、`az network dns record-set list` を使用します。


## <a name="list-dns-zones"></a>DNS ゾーンの一覧表示

DNS ゾーンを列挙するには、`az network dns zone list` を使用します。 `az network dns zone list --help` を使用すると、ヘルプが表示されます。

リソース グループを指定すると、リソース グループ内のゾーンのみが一覧表示されます。

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

リソース グループを省略すると、サブスクリプション内のすべてのゾーンが一覧表示されます。

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>DNS ゾーンの更新

DNS ゾーンのリソースへの変更は、 `az network dns zone update`を使用して行うことができます。 `az network dns zone update --help` を使用すると、ヘルプが表示されます。

このコマンドによって、ゾーン内の DNS レコード セットが更新されることはありません (「 [DNS レコードの管理方法](dns-operations-recordsets-cli.md)」を参照)。 この操作は、ゾーンのリソース自体のプロパティを更新するためだけに使用します。 プライベート ゾーンでは、ゾーンにリンクされている登録仮想ネットワークまたは解決仮想ネットワークを更新することができます。 

DNS プライベート ゾーンにリンクされた解決仮想ネットワークを更新する例を次に示します。 リンクされている既存の解決仮想ネットワークが、指定した新しい仮想ネットワークに置き換えられます。

```azurecli
az network dns zone update --resource-group MyResourceGroup --name contoso.local --zone-type Private --resolution-vnets MyNewAzureVnet
```

## <a name="delete-a-dns-zone"></a>DNS ゾーンの削除

DNS ゾーンは、`az network dns zone delete` を使用して削除できます。 `az network dns zone delete --help` を使用すると、ヘルプが表示されます。

> [!NOTE]
> DNS ゾーンを削除すると、ゾーン内の DNS レコードもすべて削除されます。 削除操作は元に戻すことができません。 DNS ゾーンを使用している場合、ゾーンを削除すると、ゾーンを使用するサービスは機能しなくなります。
>
>ゾーンを誤って削除しないようにするには、「[DNS ゾーンとレコードを保護する](dns-protect-zones-recordsets.md)」をご覧ください。

このコマンドは、確認を求めるプロンプトを表示します。 オプションの `--yes` スイッチはこのプロンプトを表示しません。

次の例は、リソース グループ *MyResourceGroup* から *contoso.local* ゾーンを削除する方法を示します。

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.local
```

## <a name="delete-all-resources"></a>すべてのリソースの削除
 
この記事で作成したすべてのリソースを削除するには、次の手順を実行します。

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>次の手順

Azure DNS の詳細については、「[Azure DNS の概要](dns-overview.md)」を参照してください。

Azure DNS での DNS ゾーンの管理の詳細については、[Azure CLI 2.0 を使用した Azure DNS での DNS ゾーンの管理](dns-operations-dnszones-cli.md)に関するページをご覧ください。

Azure DNS での DNS レコードの管理の詳細については、「[Azure CLI 2.0 を使用して Azure DNS のレコードおよびレコード セットを管理する](dns-operations-recordsets-cli.md)」を参照してください。
