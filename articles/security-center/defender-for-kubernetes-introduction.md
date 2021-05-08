---
title: Azure Defender for Kubernetes - 利点と機能
description: Azure Defender for Kubernetes の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 04/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c500c7b7afb36ffbe04fb63551c3a7d17c1347d9
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029082"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Azure Defender for Kubernetes の概要

Azure Defender for Kubernetes は、Kubernetes クラスターの保護を、その実行環境に関係なく提供する Azure Defender プランです。 

次の環境のクラスターを防御できます。

- **Azure Kubernetes Service (AKS)** - コンテナー化されたアプリケーションを開発、デプロイ、管理するための Microsoft のマネージド サービスです。

- **オンプレミス環境とマルチクラウド環境** - [Arc 対応 Kubernetes 用の拡張機能](defender-for-kubernetes-azure-arc.md)を使用します。

Azure Security Center と AKS は、「[Security Center のコンテナーのセキュリティ](container-security.md)」で概説する環境のセキュリティ強化、ワークロード保護、実行時保護を提供する、クラウドネイティブな Kubernetes セキュリティ オファリングを形成します。

[Azure Defender for servers](defender-for-servers-introduction.md) と Log Analytics エージェントを有効にすると、Linux AKS ノードのホストレベルの脅威検出を使用できます。 ただし、クラスターが仮想マシン スケール セットにデプロイされている場合、Log Analytics エージェントは現在サポートされていません。



## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|**Azure Defender for Kubernetes** の課金については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」をご覧ください|
|必要なロールとアクセス許可:|**セキュリティ管理者** はアラートを無視できます。<br>**セキュリティ閲覧者** は、結果を表示できます。|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Azure Defender for Kubernetes の利点

Azure Defender for Kubernetes は、クラスターのログを監視することで、**クラスターレベルの脅威の防止** を提供します。

Azure Defender for Kubernetes によって監視されるセキュリティ イベントの例として、Kubernetes ダッシュボードの公開、高い特権ロールの作成、機密性の高いマウントの作成などがあります。 クラスター レベルのアラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-akscluster)を参照してください。

> [!TIP]
> コンテナーのアラートをシミュレートするには、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)の手順に従います。

また、Microsoft のセキュリティ研究員から成るグローバル チームも、脅威の状況を絶えず監視しています。 コンテナー固有のアラートや脆弱性は、それらが検出された時点で追加されます。

>[!NOTE]
> ご利用のサブスクリプションで Defender for Kubernetes プランを有効にした後に生じたアクションやデプロイに対して、Azure Defender によってセキュリティのアラートが生成されます。




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender for Kubernetes - よくあるご質問

### <a name="can-i-still-get-cluster-protections-without-the-log-analytics-agent"></a>Log Analytics エージェントを使用しなくてもクラスターの保護を利用できますか。

**Azure Defender for Kubernetes** プランでは、クラスター レベルで保護を提供します。 **Azure Defender for servers** の Log Analytics エージェントもデプロイすると、そのプランで提供されているノードの脅威の防止を利用できます。 詳細については、「[Azure Defender for servers の概要](defender-for-servers-introduction.md)」をご覧ください。

可能なかぎり最も完全な保護を実現するためには、両方をデプロイすることをお勧めします。

ホストにエージェントをインストールしないことを選択した場合、脅威の防止の利点とセキュリティ アラートの一部だけが提供されます。 その場合でも、ネットワーク分析や悪意のあるサーバーとの通信に関連したアラートは通知されます。

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>AKS を使用すると、AKS ノードにカスタム VM 拡張機能をインストールできますか。
Azure Defender で AKS ノードを監視するには、それらのノードで Log Analytics エージェントが実行されている必要があります。 

AKS は管理サービスであり、Log Analytics エージェントは Microsoft マネージド拡張機能であるため、AKS クラスターでもサポートされています。

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>クラスターでコンテナー エージェントに対して Azure Monitor が既に実行されている場合、Log Analytics エージェントも必要ですか。
Azure Defender でノードを監視するには、それらのノードで Log Analytics エージェントが実行されている必要があります。

クラスターでコンテナー エージェントに対して Azure Monitor が既に実行されている場合は、Log Analytics エージェントもインストールできます。この 2 つのエージェントは、問題なく一緒に動作できます。

[コンテナー エージェントに対する Azure Monitor の詳細を学習](../azure-monitor/containers/container-insights-manage-agent.md)します。


## <a name="next-steps"></a>次のステップ

この記事では、Azure Defender for Kubernetes など、Security Center の Kubernetes 保護について説明しました。 

> [!div class="nextstepaction"]
> [Azure Defender を有効にする](enable-azure-defender.md)

関連資料については、次の記事をご覧ください。 

- [SIEM、SOAR、または IT サービス管理ソリューションにアラートをストリーミングする](export-to-siem.md)
- [アラートのリファレンス表](alerts-reference.md)
