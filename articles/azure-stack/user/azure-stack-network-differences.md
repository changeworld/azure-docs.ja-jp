---
title: Azure Stack ネットワークの相違点と考慮事項 | Microsoft Docs
description: Azure Stack でネットワークを操作する際の違いと考慮事項について説明します。
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.author: mabrigg
ms.date: 05/21/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: scottnap
ms.openlocfilehash: faff52ba5b5e2f0d573a67633d3a8411b2d7de74
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606428"
---
# <a name="considerations-for-azure-stack-networking"></a>Azure Stack ネットワークに関する考慮事項

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack ネットワークは、Azure ネットワークで提供される機能の多くを備えています。 ただし、Azure Stack ネットワークをデプロイする前に理解しておくべき大きな違いがいくつか存在します。

この記事では、Azure Stack ネットワークとその機能に固有の考慮事項の概要を示します。 Azure Stack と Azure の違いの概要については、[重要な考慮事項](azure-stack-considerations.md)のトピックを参照してください。

## <a name="cheat-sheet-networking-differences"></a>チート シート: ネットワークの違い

|サービス | Feature | Azure (グローバル) | Azure Stack |
| --- | --- | --- | --- |
| DNS | マルチテナント DNS | サポートされています| まだサポートされていません|
| |DNS AAAA レコード|サポートされています|サポートされていません|
| |サブスクリプションあたりの DNS ゾーン数|100 (既定値)<br>ご希望により増やすことができます。|100|
| |ゾーンあたりの DNS レコード セット数|5000 (既定値)<br>ご希望により増やすことができます。|5000|
||ゾーンの委任用のネーム サーバー|Azure では、作成されるユーザー (テナント) ゾーンごとに 4 つのネーム サーバーを提供します。|Azure Stack では、作成されるユーザー (テナント) ゾーンごとに 2 つのネーム サーバーを提供します。|
| Virtual network|仮想ネットワーク ピアリング|同じリージョンに存在する 2 つの仮想ネットワークを Azure のバックボーン ネットワークを介して接続します。|まだサポートされていません|
| |IPv6 アドレス|[ネットワーク インターフェイス構成](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions)の一部として IPv6 アドレスを割り当てることができます。|IPv4 のみがサポートされています。|
|VPN ゲートウェイ|ポイント対サイト VPN ゲートウェイ|サポートされています|まだサポートされていません|
| |VNet 間ゲートウェイ|サポートされています|まだサポートされていません|
| |VPN ゲートウェイの SKU|Basic、GW1、GW2、GW3、Standard High Performance、Ultra-High Performance をサポートします。 |Basic、Standard、および High-Performance の SKU をサポートします。|
|Load Balancer|IPv6 パブリック IP アドレス|ロード バランサーへの IPv6 パブリック IP アドレスの割り当てをサポートします。|IPv4 のみがサポートされています。|
|Network Watcher|Network Watcher テナント ネットワーク監視機能|サポートされています|まだサポートされていません|
|CDN|Content Delivery Network のプロファイル|サポートされています|まだサポートされていません|
|Application gateway|レイヤー 7 の負荷分散|サポートされています|まだサポートされていません|
|Traffic Manager|アプリケーションのパフォーマンスと信頼性を最適にするように着信トラフィックをルーティングします。|サポートされています|まだサポートされていません|
|ExpressRoute|オンプレミス インフラストラクチャや共用施設から Microsoft クラウド サービスに高速なプライベート接続を設定します。|サポートされています|Express Route 回線への Azure Stack の接続をサポートします。|

## <a name="next-steps"></a>次の手順

[Azure Stack の DNS](azure-stack-dns.md)