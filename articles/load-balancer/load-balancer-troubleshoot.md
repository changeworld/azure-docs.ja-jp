---
title: Azure Load Balancer での一般的な問題のトラブルシューティング
description: Azure Load Balancer での一般的な問題をトラブルシューティングする方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: 6ab256d58284e79323b988b2b81f4c664fa3fc16
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456680"
---
# <a name="troubleshoot-azure-load-balancer"></a>Azure Load Balancer のトラブルシューティング

このページには、Basic および Standard Azure Load Balancer についてよく寄せられる質問のトラブルシューティング情報が示されています。 Standard Load Balancer について詳しくは、[Standard Load Balancer の概要](load-balancer-standard-diagnostics.md)に関するページをご覧ください。

Load Balancer の接続が利用できない場合の最も一般的な症状を次に示します。

- Load Balancer の背後にある VM が正常性プローブに応答しない 
- Load Balancer の背後にある VM が構成済みポートのトラフィックに応答しない

バックエンド VM に対する外部クライアントがロード バランサーを通過するときは、クライアントの IP アドレスが通信に使用されます。 クライアントの IP アドレスが NSG 許可リストに追加されていることを確認してください。

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Standard の内部ロード バランサー (ILB) からの送信接続がない

**検証と解決策**

Standard の ILB は、**既定でセキュリティによって保護されています**。 Basic ILB では、既定の送信アクセス IP を呼び出した *非表示の* パブリック IP アドレスを使用してインターネットにアクセスできます。 この IP アドレスは静的ではなく、所有する NSG によってロックダウンされることもないため、これは運用環境のワークロードには推奨されません。 最近、Basic の ILB から Standard の ILB に移行した場合は、NSG によって IP をロックダウンする[送信のみ](egress-only.md)の構成を使用して、パブリック IP を明示的に作成する必要があります。 また、サブネットで [NAT Gateway](../virtual-network/nat-gateway/nat-overview.md) を使用することもできます。 NAT Gateway は、送信に関して推奨されるソリューションです。

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>バックエンド プールにデプロイされている仮想マシン スケール セットがあるロード バランサーの既存の LB ルールのバックエンド ポートを変更できません。

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>原因:仮想マシン スケール セットによって参照されるロード バランサーに対する正常性プローブによって使用される負荷分散規則のバックエンド ポートを変更することはできません

**解決策**: ポートを変更するには、仮想マシン スケール セットを更新して正常性プローブを削除し、ポートを更新してから、正常性プローブを再度構成します。

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>ロード バランサーのバックエンド プールから VM を削除した後も、少量のトラフィックがロード バランサーを通過しています。

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>原因:バックエンド プールから削除された VM は、トラフィックを受信しなくなります。 少量のネットワーク トラフィックは、Azure 内のストレージ、DNS、およびその他の機能に関連している可能性があります。

確認するには、ネットワーク トレースを実行してください。 BLOB ストレージ アカウントに使用される FQDN は、各ストレージ アカウントのプロパティ内に一覧表示されます。  Azure サブスクリプション内の仮想マシンから nslookup を実行することで、そのストレージ アカウントに割り当てられている Azure IP を特定できます。

## <a name="additional-network-captures"></a>その他のネットワーク キャプチャ

サポート ケースを開く場合は、迅速に解決できるように次の情報を収集します。 1 つのバックエンド VM を選択して、次のテストを実行してください。

- VNet 内のいずれかのバックエンド VM から ps ping を使用してプローブ ポートの応答をテストし (例: ps ping 10.0.0.4:3389)、結果を記録します。 
- この ping テストで応答がなかった場合は、PsPing を実行しながら、バックエンド VM と VNet テスト VM で同時 Netsh トレースを実行し、その後、Netsh トレースを停止します。

## <a name="load-balancer-in-failed-state"></a>Load Balancer が失敗した状態になっている

**解像度**

- 失敗した状態になっているリソースを特定したら、[Azure Resource Explorer](https://resources.azure.com/) に移動し、この状態のリソースを特定します。
- 右上隅のトグルを、[読み取り/書き込み] に更新します。
- 失敗した状態のリソースの [編集] をクリックします。
- [PUT]、[GET] の順にクリックして、プロビジョニングの状態が [成功] に更新されたことを確認します。
- その後は、リソースが失敗した状態ではなくなったので、他のアクションに進むことができます。

## <a name="next-steps"></a>次のステップ

前の手順で問題を解決できない場合は、[サポート チケット](https://azure.microsoft.com/support/options/)を開きます。
