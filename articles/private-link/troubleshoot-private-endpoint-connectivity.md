---
title: Azure プライベート エンドポイント接続に関する問題のトラブルシューティング
description: プライベート エンドポイント接続を診断するためのステップ バイ ステップのガイダンス
services: private-endpoint
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
ms.openlocfilehash: df4ec6ddbba029eb29d2440717697968f8c79302
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191062"
---
# <a name="troubleshoot-private-endpoint-connectivity-problems"></a>プライベート エンドポイント接続に関する問題のトラブルシューティング

このガイドでは、プライベート エンドポイント接続のセットアップを検証して診断するためのステップ バイ ステップのガイダンスを提供します。 

Azure プライベート エンドポイントは、Private Link サービスにプライベートかつ安全に接続するネットワーク インターフェイスです。 このソリューションは、仮想ネットワークから Azure サービス リソースへのプライベート接続を提供することによって、Azure でワークロードをセキュリティで保護するのに役立ちます。 これにより、これらのサービスが仮想ネットワークに効果的に導入されます。 

プライベート エンドポイントで使用できる接続シナリオを次に示します 
- 同じリージョンの仮想ネットワーク 
- リージョンでピアリングされた仮想ネットワーク
- グローバルにピアリングされた仮想ネットワーク
- VPN または Express Route 回線を介したオンプレミスのお客様

## <a name="diagnosing-connectivity-problems"></a>接続の問題の診断 
以下の手順をよく検討して、通常の構成がすべて想定どおりであることを確認し、プライベート エンドポイント セットアップでの接続の問題を解決してください。

1. リソースを参照して、プライベート エンドポイントの構成を確認します 

    a) **[プライベート リンク センター]** に移動します

      ![プライベート リンク センター](./media/private-endpoint-tsg/private-link-center.png)

    b) 左側のナビゲーション ウィンドウから [プライベート エンドポイント] を選択します
    
      ![プライベート エンドポイント](./media/private-endpoint-tsg/private-endpoints.png)

    c) 診断するプライベート エンドポイントをフィルター処理して選択します

    d) 仮想ネットワークと DNS 情報を確認します
    
     - 接続状態が **[Approved]\(承認済み\)** になっていることを確認します
     - プライベート エンドポイントをホストしている VNet に VM が接続されていることを確認します
     - FQDN 情報 (コピー) と割り当てられたプライベート IP アドレスを確認します
    
       ![VNet と DNS の構成](./media/private-endpoint-tsg/vnet-dns-configuration.png)    
    
2. [**Azure Monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) を使用して、データが流れていることを確認します

    a) プライベート エンドポイント リソースで **[Monitor]** を選択します
     - 受信データまたは送信データを選択して、プライベート エンドポイントへの接続を試みているときにデータが流れているかどうかを確認します。 約 10 分の遅延が予想されます。
    
       ![プライベート エンドポイント テレメトリの検証](./media/private-endpoint-tsg/private-endpoint-monitor.png)

3. **Network Watcher** から VM の接続のトラブルシューティングを使用します

    a) クライアント VM を選択します

    b) **[Connection troubleshoot]\(接続のトラブルシューティング\)** セクションで、 **[Outbound connection]\(送信接続\)** タブを選択します
    
      ![Network Watcher - 送信接続のテスト](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c) **[Use Network Watcher for detail connection tracing]\(Network Watcher を使用して接続を詳細にトレースする\)** を選択します
    
      ![Network Watcher - 接続のトラブルシューティング](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d) **[Test by FQDN]\(FQDN でテスト\)** を選択します
     - プライベート エンドポイント リソースから FQDN を貼り付けます
     - ポート (*通常、Azure Storage または COSMOS の場合は 443、SQL の場合は 1336*) を指定します

    e) **[テスト]** をクリックして、テスト結果を検証します
    
      ![Network Watcher - テスト結果](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
4. テスト結果からの DNS 解決では、プライベート エンドポイントに割り当てられているのと同じプライベート IP アドレスが必要になります

    a) DNS 設定が正しくない場合は、次の手順を実行します
     - プライベート ゾーンの使用: 
       - クライアント VM VNet がプライベート ゾーンに関連付けられていることを確認します
       - プライベート DNS ゾーン レコードが存在することを確認し、存在しない場合は作成します
    
     - カスタム DNS の使用:
       - お客様の DNS 設定を確認し、DNS 構成が正しいことを検証します。
       ガイダンスについては、[プライベート エンドポイントの概要に関する記事の「DNS の構成」](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)を参照してください。

    b) NSG/UDR が原因で接続が失敗する場合
     - NSG アウトバウンド規則を確認し、トラフィックを許可するための適切なアウトバウンド規則を作成します
    
       ![NSG アウトバウンド規則](./media/private-endpoint-tsg/nsg-outbound-rules.png)

5. 接続の結果が検証されている場合は、接続の問題は、アプリケーション層でのシークレット、トークン、パスワードなどの他の側面に関連している可能性があります。
   - この場合、プライベート エンドポイントに関連付けられている Private Link リソースの構成を確認してください。 [Private Link トラブルシューティング ガイド](troubleshoot-private-link-connectivity.md)を参照してください。 

6. 問題が解決されず、接続の問題がまだ存在する場合は、[Azure サポート](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) チームにお問い合わせください。 

## <a name="next-steps"></a>次のステップ

 * [更新されたサブネットにプライベート エンドポイントを作成する (Azure portal)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)

 * [Private Link トラブルシューティング ガイド](troubleshoot-private-link-connectivity.md)
