---
title: Azure パブリック IP アドレス プレフィックスを作成、変更、または削除する
titlesuffix: Azure Virtual Network
description: パブリック IP アドレス プレフィックスとその作成、変更、削除の方法について説明します。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 824d89126252a0690b93a2129f47d8e02b728694
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560114"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>パブリック IP アドレス プレフィックスの作成、変更、削除

パブリック IP アドレス プレフィックスについて説明します。 パブリック IP アドレス プレフィックスは、Standard SKU のパブリック IP アドレスの連続した範囲です。  パブリック IP アドレス リソースを作成するときは、プレフィックスから静的パブリック IP を割り当て、そのアドレスを Azure リソースに関連付けることができます。 詳細については、[パブリック IP アドレス プレフィックスの概要](public-ip-address-prefix.md)に関するページを参照してください。

## <a name="create-a-public-ip-address-prefix"></a>パブリック IP アドレス プレフィックスの作成

次のセクションでは、パブリック IP プレフィックスを作成するときのパラメーターの詳細について説明します。

   |設定|必須|詳細|
   |---|---|---|
   |サブスクリプション|はい|パブリック IP アドレスを関連付けるリソースと同じ[サブスクリプション](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)内に存在する必要があります。|
   |Resource group|はい|所属する[リソース グループ](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)は、パブリック IP アドレスを関連付けるリソースと同じであっても異なっていてもかまいません。|
   |名前|はい|選択したリソース グループ内で一意となる名前を使用してください。|
   |リージョン|はい|範囲からアドレスを割り当てるパブリック IP アドレスと同じ[リージョン](https://azure.microsoft.com/regions)に存在する必要があります。|
   |IP バージョン|はい| プレフィックスの IP バージョン (v4 または v6)。
   |プレフィックス サイズ|はい| 必要なプレフィックスのサイズ。 16 個の IP アドレスを含む範囲 (v4 の場合は /28、v6 の場合は /124) が既定値です。

あるいは、次の CLI および PowerShell コマンドを使用してパブリック IP アドレス プレフィックスを作成することもできます。

**コマンド**

|ツール|command|
|---|---|
|CLI|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>可用性ゾーンがあるリージョンでは、PowerShell または CLI コマンドを使用して、パブリック IP アドレス プレフィックスを、非ゾーンとして、特定のゾーンに関連付けて、またはゾーン冗長を使用するように作成できます。  API バージョン 2020-08-01 以降では、ゾーン パラメーターが指定されていない場合、非ゾーンのパブリック IP アドレス プレフィックスが作成されます。 2020-08-01 より前のバージョンの API では、ゾーン冗長パブリック IP アドレス プレフィックスが作成されます。 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>プレフィックスから静的パブリック IP アドレスを作成する

次のセクションでは、プレフィックスから静的パブリック IP アドレスを作成するときに必要なパラメーターの詳細について説明します。

   |設定|必須|詳細|
   |---|---|---|
   |名前|はい|パブリック IP アドレスには、選択したリソース グループ内で一意の名前を付ける必要があります。|
   |アイドル タイムアウト (分)|いいえ|クライアントからキープアライブ メッセージを送信しなくても TCP 接続または HTTP 接続が開いたまま維持される時間 (分)。 |
   |DNS 名ラベル|いいえ|作成する Azure リージョン (すべてのサブスクリプション、すべての顧客) で一意の名前を付けます。 指定した名前を使用してリソースに接続できるよう、DNS 内の名前と IP アドレスが Azure によって自動的に登録されます。 Azure では、指定された名前に既定のサブネット *location.cloudapp.azure.com* を追加して、完全修飾 DNS 名を作成します。 詳細については、[Azure パブリック IP アドレスを使用した Azure DNS の使用](../../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)に関するページを参照してください。|

あるいは、次の CLI および PowerShell コマンドを **--public-ip-prefix (CLI)** および **-PublicIpPrefix (PowerShell)** パラメーターと共に使用して、プレフィックスからパブリック IP アドレス リソースを作成することもできます。 

|ツール|command|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

>[!NOTE]
>パブリック IP プレフィックスからパブリック IP アドレスを要求した場合、割り当ての実行は固定でも順次でもありません。 パブリック IP プレフィックスから特定のパブリック IP アドレスを要求する場合は、PowerShell または CLI コマンドを使用して行うことができます。  PowerShell の場合は `IpAddress` パラメーター (その後に希望する IP を記述) を使用し、CLI の場合は `ip-address` パラメーター (その後に希望する IP を記述) を使用する必要があります。

>[!NOTE]
>プレフィックスの範囲から割り当てることができるのは、Standard SKU で作成された静的パブリック IP アドレスのみです。 パブリック IP アドレスの SKU の詳細については、「[パブリック IP アドレス](public-ip-addresses.md#public-ip-addresses)」をご覧ください。

## <a name="view-or-delete-a-prefix"></a>プレフィックスの表示または削除

プレフィックスを表示または削除するには、Azure CLI と Azure PowerShell で次のコマンドを使用できます。

**コマンド**

|ツール|command|
|---|---|
|CLI|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_list) でパブリック IP アドレスを一覧表示する<br>[az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show) で設定を表示する<br> [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_update) で更新する<br>[az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete) で削除する|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) でパブリック IP アドレス オブジェクトを取得したり、その設定を表示したりする<br>[Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) で設定を更新する<br> [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) で削除する|

## <a name="permissions"></a>アクセス許可

アクセス許可でパブリック IP アドレス プレフィックスを管理するには、アカウントが[ネットワーク共同作成者](../../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロールまたは[カスタム](../../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ロールに割り当てられている必要があります。 

| アクション                                                            | 名前                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | パブリック IP アドレス プレフィックスの読み取り                                |
| Microsoft.Network/publicIPPrefixes/write                          | パブリック IP アドレス プレフィックスの作成または更新                    |
| Microsoft.Network/publicIPPrefixes/delete                         | パブリック IP アドレス プレフィックスの削除                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | プレフィックスからのパブリック IP アドレスの作成 |

## <a name="next-steps"></a>次のステップ

- [パブリック IP プレフィックス](public-ip-address-prefix.md)を使用するシナリオと利点について説明します
