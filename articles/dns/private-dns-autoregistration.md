---
title: Azure DNS プライベート ゾーンの自動登録機能とは
description: Azure DNS プライベート ゾーンの自動登録機能の概要。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/26/2021
ms.author: rohink
ms.openlocfilehash: befabd5e35957241fa640c28146e47f01153b72b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251682"
---
# <a name="what-is-the-auto-registration-feature-in-azure-dns-private-zones"></a>Azure DNS プライベート ゾーンの自動登録機能とは

Azure DNS プライベート ゾーンの自動登録機能では、仮想ネットワークにデプロイされている仮想マシンの DNS レコードが管理されます。 この設定を有効にした状態でプライベート DNS ゾーンと[仮想ネットワークをリンクする](./private-dns-virtual-network-links.md)と、 仮想ネットワークにデプロイされている仮想マシンごとに DNS レコードが作成されます。 

仮想マシンごとに、A レコードと PTR レコードが作成されます。 新しくデプロイされた仮想マシンの DNS レコードも、リンク先のプライベート DNS ゾーンで自動的に作成されます。 仮想マシンが削除されると、関連付けられている DNS レコードもプライベート DNS ゾーンから削除されます。

自動登録を有効にするには、仮想ネットワーク リンクの作成時、[自動登録を有効にする] のチェックボックスをオンにします。

:::image type="content" source="./media/privatedns-concepts/enable-autoregistration.png" alt-text="仮想ネットワーク リンクの追加ページの [自動登録を有効にする] のスクリーンショット。":::

## <a name="restrictions"></a>制限

* 自動登録は、仮想マシンに対してのみ機能します。 内部ロード バランサーなどの他のすべてのリソースについては、仮想ネットワークにリンクされているプライベート DNS ゾーンに DNS レコードを手動で作成できます。
* DNS レコードは、プライマリ仮想マシンの NIC に対してのみ自動的に作成されます。 仮想マシンに複数の NIC がある場合は、他のネットワーク インターフェイスの DNS レコードを手動で作成できます。
* DNS レコードは、プライマリ仮想マシンの NIC で DHCP が使用されている場合のみ自動的に作成されます。 [Azure に複数の IP アドレス](../virtual-network/ip-services/virtual-network-multiple-ip-addresses-portal.md#os-config)がある構成など、静的 IP を使用している場合、自動登録によってその仮想マシンにレコードが作成されることはありません。
* IPv6 (AAAA レコード) の自動登録はサポートされていません。
* 自動 VM DNS 登録が有効になっている場合、特定の仮想ネットワークを 1 つのプライベート DNS ゾーンにのみリンクできます。 ただし、複数の仮想ネットワークを 1 つの DNS ゾーンにリンクできます。

## <a name="next-steps"></a>次の手順

* [Azure PowerShell](./private-dns-getstarted-powershell.md) または [Azure CLI](./private-dns-getstarted-cli.md) を使用して Azure DNS のプライベート ゾーンを作成する方法を確認します。

* [プライベート ゾーンのシナリオ](./private-dns-scenarios.md)に関しては、Azure DNS のプライベート ゾーンで実現できるいくつかの一般的なシナリオを参照してください。

* Azure DNS のプライベート ゾーンに関する一般的な質問と回答については、[プライベート DNS の FAQ](./dns-faq-private.yml) に関する記事をご覧ください。特定の種類の操作で期待できる固有の動作についても説明があります。