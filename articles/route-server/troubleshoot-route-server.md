---
title: Azure Route Server の問題のトラブルシューティング
description: Azure Route Server の問題をトラブルシューティングする方法について説明します。
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 02dd9aa74da42f0a5d70de4513b88756a97bea1e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678757"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Azure Route Server の問題のトラブルシューティング

> [!IMPORTANT]
> Azure Route Server (プレビュー) は現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="bgp-connectivity-issues"></a>BGP 接続の問題

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>NVA と Azure Route Server の間で BGP ピアリングが稼働と停止を繰り返している ("フラッピング") のはなぜですか。

フラッピングの原因として、BGP タイマーの設定が考えられます。 既定では、Azure Route Server のキープアライブ タイマーは 60 秒、停止タイマーは 180 秒に設定されています。

### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-goes-down"></a>NVA から Azure Route Server の BGP ピア IP に ping を実行できますが、BGP ピアリングをそれらの間に設定すると、同じ IP に ping を実行できなくなるのはなぜですか。 BGP ピアリングが停止するのはなぜですか。

一部の NVA では、Azure Route Server サブネットに静的ルートを追加する必要があります。 たとえば、Azure Route Server が 10.0.255.0/27 にあり、お使いの NVA が 10.0.1.0/24 にある場合は、NVA のルーティング テーブルに次のルートを追加する必要があります。

| ルート | 次ホップ |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1 は、お使いの NVA (より正確には NIC の 1 つ) がホストされているサブネットの既定のゲートウェイ IP です。

## <a name="bgp-route-issues"></a>BGP ルートの問題

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>BGP ピアリングが稼働しているのに、NVA が Azure Route Server からルートを受信しないのはなぜですか。

Azure Route Server が使用する ASN は 65515 です。 ルート伝達が自動的に行われるように、お使いの NVA に異なる ASN を構成して、NVA と Azure Route Server の間に "eBGP" セッションを確立してください。

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>NVA と Azure Route Server の間で BGP ピアリングが稼働しています。 これらの間で正しく交換されたルートが表示されます。 NVA ルートが、VM の有効なルーティング テーブルにないのはなぜですか。 

* VM がお使いの NVA と Azure Route Server と同じ VNet 内にある場合は、次のようにします。

     Azure Route Server によって公開される 2 つの BGP ピア IP は、仮想ネットワーク内で実行されている他のすべての VM にルートを送信する責任を共有する 2 つの VM でホストされます。 お使いの各 NVA で、2 つの同一の BGP セッションを 2 つの VM に設定 (同じ AS 番号、同じ AS パスを使用し、同じルートのセットをアドバタイズするなど) して、仮想ネットワーク内の VM が Azure Route Server からの一貫したルーティング情報を取得できるようにする必要があります。 下の図を参照してください。

    ![ネットワーク仮想アプライアンスと Route Server の図。](./media/faq/network-virtual-appliances.png)

    NVA のインスタンスが 2 つ以上ある場合は、1 つの NVA インスタンスをアクティブ、その他をパッシブとして指定したければ、異なる NVA インスタンスから同じルートの異なる AS パスをアドバタイズ "*できます*"。

* VM が存在する仮想ネットワークが、お使いの NVA と Azure Route Server をホストするものとは異なる場合は、次のようにします。 2 つの VNet 間で VNet ピアリングが有効になっているか、"*かつ*" VM の VNet で [Use Remote Route Server]\(リモート ルート サーバーを使用する\) が有効になっているかを確認します。

## <a name="next-steps"></a>次のステップ

[Azure Route Server を構成する](quickstart-configure-route-server-powershell.md)方法を確認する
