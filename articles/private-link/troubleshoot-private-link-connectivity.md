---
title: Azure Private Link 接続に関する問題のトラブルシューティング
description: プライベート リンク接続を診断するためのステップ バイ ステップのガイダンス
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 667fa1c85c63ffb87e49c4bf99112f57d0c85a72
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77032193"
---
# <a name="troubleshoot-private-link-service-connectivity-problems"></a>Private Link サービス接続に関する問題のトラブルシューティング

このガイドでは、プライベート リンク サービスのセットアップの接続性を検証して診断するためのステップ バイ ステップのガイダンスを提供します。 

Azure Private Link を使用すると、仮想ネットワーク内のプライベート エンドポイント経由で Azure PaaS サービス (Azure Storage、Azure Cosmos DB、SQL Database など) と Azure でホストされている顧客/パートナー サービスにアクセスできます。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由して、パブリック インターネットからの公開を排除します。 また、独自の Private Link サービスを仮想ネットワーク (VNet) 内に作成し、そのサービスを非公開で顧客に配信することもできます。 

Azure Standard Load Balancer の背後で実行されているサービスを Private Link アクセスに対して有効にすることができます。 サービスのコンシューマーは、仮想ネットワーク内にプライベート エンドポイントを作成し、それをこのサービスにマップして非公開でアクセスできます。

Private Link サービスで使用できる接続シナリオを次に示します
- 同じリージョンの仮想ネットワーク 
- リージョンでピアリングされた仮想ネットワーク
- グローバルにピアリングされた仮想ネットワーク
- VPN または Express Route 回線を介したオンプレミスのお客様

## <a name="deployment-troubleshooting"></a>デプロイのトラブルシューティング

Private Link サービス用に選択したサブネットからソース IP アドレスを選択できない場合のトラブルシューティングについては、[プライベート リンク サービスのネットワーク ポリシーの無効化](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)に関する情報を参照してください。

ソース IP アドレスの選択元のサブネットに対して **privateLinkServiceNetworkPolicies** 設定が無効になっていることを確認してください。

## <a name="diagnosing-connectivity-problems"></a>接続の問題の診断

プライベート リンクのセットアップで接続の問題が発生している場合は、以下の手順をよく検討して、通常の構成がすべて想定どおりであることを確認してください。

1. リソースを参照して Private Link サービスの構成を確認します 

    a) **プライベート リンク センター**に移動します

      ![プライベート リンク センター](./media/private-link-tsg/private-link-center.png)

    b) 左側のナビゲーション ウィンドウから **[Private Link Services]\(Private Link サービス\)** を選択します

      ![Private Link サービス](./media/private-link-tsg/private-link-service.png)

    c) 診断するプライベート リンク サービスをフィルター処理して選択します

    d) プライベート エンドポイント接続を確認します
     - 接続元とするプライベート エンドポイントが、 **[Approved]\(承認済み\)** の接続状態でリストされていることを確認します。 
     - **[Pending]\(保留中\)** の場合は、選択して承認します。 

       ![プライベート エンドポイント接続](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - 名前をクリックして、接続元のプライベート エンドポイントに移動します。 接続状態に **[Approved]\(承認済み\)** と表示されていることを確認します。

       ![プライベート エンドポイント接続の概要](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - 両方の側が承認されたら、接続を再試行します。

    e) [概要] タブで **[別名]** を確認し、[プロパティ] タブで **[リソース ID]** を確認します 
     - その**別名/リソース ID** が、このサービスへのプライベート エンドポイントを作成するために使用している**別名/リソース ID** と一致することを確認します。 

       ![別名を確認します](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![リソース ID を確認します](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f) 可視性 ([概要] セクション) の情報を確認します
     - サブスクリプションが **[Visibility]\(可視性\)** スコープの下にあることを確認します

       ![可視性を確認します](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g) Load Balancer (概要) の情報を確認します
     - ロード バランサーのリンクをクリックすると、ロード バランサーに移動できます

       ![Load Balancer を確認します](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Load Balancer の設定が期待どおりに構成されていることを確認します
       - フロントエンド IP 構成を確認します
       - バックエンド プールを確認します
       - 負荷分散規則を確認します

       ![Load Balancer のプロパティを確認します](./media/private-link-tsg/pls-ilb-properties.png)

     - Load Balancer が上記の設定に従って動作していることを確認します
       - Load Balancer バックエンド プールが使用可能なサブネット以外のサブネット内の VM を選択します
       - 前述の VM からロード バランサー フロントエンド にアクセスしてみます
       - 負荷分散規則に従ってバックエンド プールへの接続が行われる場合、ロード バランサーは動作しています
       - また、Azure Monitor を使用して Load Balancer のメトリックを確認し、データがロード バランサーを介して流れているかどうかを確認することもできます

2. [**Azure Monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) を使用して、データが流れていることを確認します

    a) Private Link サービス リソースで、 **[Metrics]\(メトリック\)** を選択します
     - **[bytes-in]\(受信バイト\)** または **[bytes-out]\(送信バイト\)** を選択します
     - Private Link サービスへの接続を試みたときに、データが流れていることを確認します。 約 10 分の遅延が予想されます。

       ![Private Link サービスのメトリックを確認します](./media/private-link-tsg/pls-metrics.png)

3. 問題が解決されず、接続の問題がまだ存在する場合は、[Azure サポート](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) チームにお問い合わせください。 

## <a name="next-steps"></a>次のステップ

 * [Private Link サービスを作成する (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)

 * [プライベート エンドポイントのトラブルシューティング ガイド](https://docs.microsoft.com/azure/private-link/private-endpoint-connectivity-troubleshooting)
