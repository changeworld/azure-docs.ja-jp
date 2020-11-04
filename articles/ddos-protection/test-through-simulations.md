---
title: Azure DDoS Protection シミュレーション テスト
description: シミュレーションを通じてテストする方法について説明します
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: eff738e24b3abce52e80291c55a3ae64c3c8c853
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905019"
---
# <a name="test-through-simulations"></a>シミュレーションを通じたテスト

定期的にシミュレーションを実施して、攻撃に対するサービスの応答方法の想定をテストすることをお勧めします。 テストでは、サービスまたはアプリケーションが期待どおりに機能し続けること、およびユーザー エクスペリエンスが中断しないことを確認します。 テクノロジとプロセスの両方の観点からギャップを明らかにし、それらを DDoS 対応戦略に組み込みます。 そのようなテストは、ステージング環境で実行するか、または運用環境への影響が最小限になるピーク時以外に実行することをお勧めします。

Microsoft は、セルフサービスのトラフィック ジェネレーターである [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) と提携して、Azure のお客様がシミュレーションのために DDoS Protection を有効にしたパブリック エンドポイントに対してトラフィックを生成できるインターフェイスを構築しました。 シミュレーションを使用すると、次のことができます。

- Azure DDoS Protection によって Azure リソースが DDoS 攻撃からどのように保護されるのかを検証する。
- DDoS 攻撃時のインシデント レスポンス プロセスを最適化する。
- DDoS コンプライアンスを文書化する。
- ネットワーク セキュリティ チームのトレーニングを行う。

## <a name="prerequisites"></a>前提条件

- このチュートリアルの手順を実行する前に、まず保護されたパブリック IP アドレスを使用して [Azure の DDoS 標準保護](manage-ddos-protection.md)プランを作成する必要があります。
- 最初に、[BreakingPoint Cloud](http://breakingpoint.cloud/) が設定されたアカウントを作成する必要があります。 

## <a name="configure-a-ddos-test-attack"></a>DDoS テスト攻撃を構成する

1. 次の値を入力または選択し、 **[テスト開始]** を選択します。

    |設定        |[値]                                              |
    |---------      |---------                                          |
    |ターゲット IP アドレス           | テストするパブリック IP アドレスのいずれかを入力します。                     |
    |ポート番号   | 「 _443_ 」を入力します。                       |
    |DDoS プロファイル | **[TCP SYN フラッド]** を選択します。|
    |テスト サイズ       | **[200K pps、100 Mbps、8 ソース IP]** を選択します。                                  |
    |テスト継続時間 | **[10 分]** を選択します。|

以下のようになります。

![DDoS 攻撃シミュレーションの例: BreakingPoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>監視と検証

1. https://portal.azure.com にログインして、サブスクリプションに移動します。
1. 攻撃をテストしたパブリック IP アドレスを選択します。
1. **[監視]** で **[メトリック]** を選びます。
1. **[メトリック]** で、 _[DDoS 攻撃中かどうか]_ を選択します。

リソースが攻撃を受けた場合は、次の図のように、値が **0** から **1** に変更されています。

![DDoS 攻撃シミュレーションの例: ポータル](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

## <a name="next-steps"></a>次のステップ

- [DDoS 保護テレメトリを表示および構成する](telemetry-monitoring-alerting.md)方法を学習します。
- [DDoS 攻撃の軽減策レポートとフロー ログを構成する](reports-and-flow-logs.md)方法を学習します。
- [DDoS Rapid Response (DRR) に参加する](ddos-rapid-response.md)方法を学習します。