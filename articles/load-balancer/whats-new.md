---
title: Azure Load Balancer の最新情報
description: 最新のリリース ノート、既知の問題、バグの修正、非推奨の機能、予定されている変更点など、Azure Load Balancer の新着情報について説明します。
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 94cf343ae8b8f55aa20b6ac4f25949e571ed6aa8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258660"
---
# <a name="whats-new-in-azure-load-balancer"></a>Azure Load Balancer の最新情報

Azure Load Balancer は定期的に更新されます。 最新のお知らせで最新情報を入手してください。 この記事では、次の項目に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正
- 非推奨の機能 (該当する場合)

また、[こちら](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)で最新の Azure Load Balancer 更新プログラムを検索し、RSS フィードを購読することもできます。

## <a name="recent-releases"></a>最新のリリース

| Type |名前 |説明  |追加日  |
| ------ |---------|---------|---------|
| 特徴量 | [IP ベースのバックエンド プールのサポート (一般提供)](https://azure.microsoft.com/updates/iplbga/) | Azure Load Balancer では、IPv4 または IPv6 アドレスを使用したバックエンド プールのリソースの追加と削除がサポートされます。 これにより、Load Balancer に関連付けられたコンテナー、仮想マシン、および仮想マシン スケール セットを簡単に管理できるようになります。 また、関連付けられたリソースが作成される前に、バックエンド プールの一部として IP アドレスを予約することもできます。 詳しくは[こちら](backend-pool-management.md)をご覧ください|2021 年 3 月 |
| 機能 | [Standard SKU Load Balancers とパブリック IP のインスタンス メタデータ サポート](https://azure.microsoft.com/updates/standard-load-balancer-and-ip-addresses-metadata-now-available-through-azure-instance-metadata-service-imds/)|Standard のパブリック IP アドレスと Standard のロード バランサーのメタデータは、Azure Instance Metadata Service (IMDS) から取得できるようになりました。 このメタデータは、仮想マシン (VM) インスタンスと仮想マシン スケール セット (VMSS) インスタンスの実行中インスタンス内から利用できます。 このメタデータを活用し、仮想マシンを管理できます。 詳しくは[こちら](instance-metadata-service-load-balancer.md)をご覧ください| 2021 年 2 月 |
| 機能 | [IP アドレスを失うことなく、パブリック IP SKU を Basic から Standard にアップグレードする](https://azure.microsoft.com/updates/public-ip-sku-upgrade-generally-available/) | Basic から Standard のロード バランサーに移行するとき、パブリック IP アドレスを保持します。 詳しくは[こちら](../virtual-network/ip-services/public-ip-upgrade-portal.md)をご覧ください| 2021 年 1 月|
| 特徴量 | リソース グループ間の移動のサポート | Standard Load Balancer および Standard パブリック IP は、[リソース グループの移動](https://azure.microsoft.com/updates/standard-resource-group-move/)をサポートしています。 | 2020 年 10 月 |
| 特徴量| Azure Monitor を使用した Azure Load Balancer の分析情報 | Azure Monitor for Networks の一部として構築された、すべての Load Balancer 構成のトポロジ・マップと事前にメトリックを使用して構成された Standard Load Balancer 用の正常性ダッシュボードを Azure portal で利用できます。 [詳細を確認して作業を開始する](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | 2020 年 6 月 |
| 検証 | HA ポートの検証の追加 | Floating IP が有効になっているときにのみ、HA ポート ルールと非 HA ポート ルールを確実に構成できるようにする検証が追加されました。 以前は、この構成をしても意図どおりに機能しませんでした。 機能は変更されていません。 詳細については、[こちら](load-balancer-ha-ports-overview.md#limitations)を参照してください。| 2020 年 6 月 |
| 特徴量| Azure Load Balancer の IPv6 サポート (一般公開) | Azure Load Balancer のフロントエンドとして IPv6 アドレスを使用できます。 [こちらでデュアル スタック アプリケーションの作成](./virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)方法について説明します。 |2020 年 4 月|
| 特徴量| アイドル タイムアウト時の TCP リセット (一般公開)| TCP リセットを使用して、アプリケーションの動作をより予測可能にします。 [詳細情報](load-balancer-tcp-reset.md)| 2020 年 2 月 |

## <a name="known-issues"></a>既知の問題

製品グループは、次の既知の問題の解決に積極的に取り組んでいます。

|問題 |説明  |対応策  |
| ---------- |---------|---------|
| IP ベースの LB 送信 IP | IP ベースの LB では、送信ルールが構成されていないとき、Azure の既定の送信アクセス IP が活用されます。 | この IP からの送信アクセスを防ぐには、IP アドレスを予測できるよう、あるいは SNAT ポートの枯渇を防ぐよう、送信ルールか NAT ゲートウェイを活用してください。 |

  

## <a name="next-steps"></a>次のステップ

Azure Load Balancer の詳細については、「[Azure Load Balancer の概要](load-balancer-overview.md)」および[よくあるご質問](load-balancer-faqs.yml)に関する記事を参照してください。