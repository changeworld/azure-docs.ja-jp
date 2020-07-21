---
title: Azure Load Balancer の最新情報
description: 最新のリリース ノート、既知の問題、バグの修正、非推奨の機能、予定されている変更点など、Azure Load Balancer の新着情報について説明します。
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: overview
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 4725aaaf7a2c1e0aa49ea8dbe046b720727a5d54
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147480"
---
# <a name="whats-new-in-azure-load-balancer"></a>Azure Load Balancer の最新情報

Azure Load Balancer は定期的に更新されます。 最新のお知らせで最新情報を入手してください。 この記事では、次の項目に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正
- 非推奨の機能 (該当する場合)

また、[こちら](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)で最新の Azure Load Balancer 更新プログラムを検索し、RSS フィードを購読することもできます。

## <a name="new-features"></a>新機能

|特徴量  |説明  |追加日  |
|---------|---------|---------|
| IP ベースのバックエンド プール管理のサポート (プレビュー) | Azure Load Balancer では、IPv4 または IPv6 アドレスを使用したバックエンド プールのリソースの追加と削除がサポートされます。 これにより、Load Balancer に関連付けられたコンテナー、仮想マシン、および仮想マシン スケール セットを簡単に管理できるようになります。 また、関連付けられたリソースが作成される前に、バックエンド プールの一部として IP アドレスを予約することもできます。 | 2020 年 7 月 |
| Azure Monitor を使用した Azure Load Balancer の分析情報 | Azure Monitor for Networks の一部として構築された、すべての Load Balancer 構成のトポロジ・マップと事前にメトリックを使用して構成された Standard Load Balancer 用の正常性ダッシュボードを Azure portal で利用できます。 [詳細を確認して作業を開始する](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | 2020 年 6 月 |
| Azure Load Balancer の IPv6 サポート (一般公開) | Azure Load Balancer のフロントエンドとして IPv6 アドレスを使用できます。 [こちらでデュアル スタック アプリケーションの作成](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)方法について説明します。 |2020 年 4 月|
| アイドル タイムアウト時の TCP リセット (一般公開)| TCP リセットを使用して、アプリケーションの動作をより予測可能にします。 [詳細情報](load-balancer-tcp-reset.md)| 2020 年 2 月 |

## <a name="next-steps"></a>次のステップ

Azure Load Balancer の詳細については、「[Azure Load Balancer の概要](load-balancer-overview.md)」および[よくあるご質問](load-balancer-faqs.md)に関する記事を参照してください。