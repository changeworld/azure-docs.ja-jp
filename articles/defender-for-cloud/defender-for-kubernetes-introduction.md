---
title: Microsoft Defender for Kubernetes - 利点と機能
description: Microsoft Defender for Kubernetes の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: overview
ms.service: defender-for-cloud
manager: rkarlin
ms.openlocfilehash: b3e9b55fae1a7d00c191838f637d03248386c4dc
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525604"
---
# <a name="introduction-to-microsoft-defender-for-kubernetes"></a>Microsoft Defender for Kubernetes の概要

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Kubernetes は、Kubernetes クラスターの保護を、その実行環境に関係なく提供する強化されたセキュリティ プランです。

Defender for Cloud では、次の場所でクラスターを保護できます。

- **Azure Kubernetes Service (AKS)** - コンテナー化されたアプリケーションを開発、デプロイ、管理するための Microsoft のマネージド サービスです。

- **オンプレミスとマルチクラウドの環境** - [Azure Arc 対応 Kubernetes 用の拡張機能](defender-for-kubernetes-azure-arc.md)を使用します。

Microsoft Defender for Cloud と AKS により、クラウドネイティブな Kubernetes セキュリティ オファリングが形成されます。これにより、「[ Defender for Cloud のコンテナーのセキュリティ](container-security.md)」で概説する環境のセキュリティ強化、ワークロード保護、実行時保護が提供されます。

[Microsoft Defender for servers](defender-for-servers-introduction.md) とその Log Analytics エージェントを有効にすると、Linux AKS ノードのホストレベルの脅威検出を使用できます。 ただし、クラスターが Azure Kubernetes Service 仮想マシン スケール セットにデプロイされている場合、Log Analytics エージェントは現在サポートされていません。



## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|**Microsoft Defender for Kubernetes** の課金については、[価格に関するページ](https://azure.microsoft.com/pricing/details/security-center/)をご覧ください。|
|必要なロールとアクセス許可:|**セキュリティ管理者** はアラートを無視できます。<br>**セキュリティ閲覧者** は、結果を表示できます。|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 国 (Azure Government、Azure China 21Vianet)|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-kubernetes"></a>Microsoft Defender for Kubernetes の利点

Microsoft Defender for Kubernetes では、クラスターのログを監視することで、**クラスターレベルの脅威の防止** が提供されます。

Microsoft Defender for Kubernetes によって監視されるセキュリティ イベントの例として、Kubernetes ダッシュボードの公開、高い特権ロールの作成、機密性の高いマウントの作成などがあります。 クラスター レベルのアラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-k8scluster)を参照してください。

> [!TIP]
> コンテナーのアラートをシミュレートするには、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)の手順に従います。

また、Microsoft のセキュリティ研究員から成るグローバル チームも、脅威の状況を絶えず監視しています。 コンテナー固有のアラートや脆弱性は、それらが検出された時点で追加されます。

>[!NOTE]
> ご利用のサブスクリプションで Defender for Kubernetes プランを有効にした後に生じたアクションやデプロイに対して、Defender for Cloud によってセキュリティのアラートが生成されます。

## <a name="faq---microsoft-defender-for-kubernetes"></a>FAQ - Microsoft Defender for Kubernetes

- [Log Analytics エージェントを使用しなくてもクラスターの保護を利用できますか。](#can-i-still-get-cluster-protections-without-the-log-analytics-agent)
- [AKS を使用すると、AKS ノードにカスタム VM 拡張機能をインストールできますか。](#does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes)
- [クラスターでコンテナー エージェントに対して Azure Monitor が既に実行されている場合、Log Analytics エージェントも必要ですか。](#if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too)
- [Microsoft Defender for Kubernetes では、仮想マシン スケール セット ノードを含む AKS はサポートされていますか。](#does-microsoft-defender-for-kubernetes-support-aks-with-virtual-machine-scale-set-nodes)

### <a name="can-i-still-get-cluster-protections-without-the-log-analytics-agent"></a>Log Analytics エージェントを使用しなくてもクラスターの保護を利用できますか。

**Microsoft Defender for Kubernetes** プランでは、クラスター レベルで保護が提供されます。 **Microsoft Defender for servers** の Log Analytics エージェントもデプロイすると、そのプランで提供されているノードの脅威の防止を利用できます。 詳細については、「[Microsoft Defender for servers の概要](defender-for-servers-introduction.md)」を参照してください。

可能なかぎり最も完全な保護を実現するためには、両方をデプロイすることをお勧めします。

ホストにエージェントをインストールしないことを選択した場合、脅威の防止の利点とセキュリティ アラートの一部だけが提供されます。 その場合でも、ネットワーク分析や悪意のあるサーバーとの通信に関連したアラートは通知されます。

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>AKS を使用すると、AKS ノードにカスタム VM 拡張機能をインストールできますか。

Defender for Cloud で AKS ノードを監視するには、それらのノードで Log Analytics エージェントが実行されている必要があります。

AKS は管理サービスであり、Log Analytics エージェントは Microsoft マネージド拡張機能であるため、AKS クラスターでもサポートされています。 ただし、クラスターが Azure Kubernetes Service 仮想マシン スケール セットにデプロイされている場合、Log Analytics エージェントは現在サポートされていません。

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>クラスターでコンテナー エージェントに対して Azure Monitor が既に実行されている場合、Log Analytics エージェントも必要ですか。

Defender for Cloud でノードを監視するには、それらのノードで Log Analytics エージェントが実行されている必要があります。

クラスターでコンテナー エージェントに対して Azure Monitor が既に実行されている場合は、Log Analytics エージェントもインストールできます。この 2 つのエージェントは、問題なく一緒に動作できます。

[コンテナー エージェントに対する Azure Monitor の詳細を学習](../azure-monitor/containers/container-insights-manage-agent.md)します。

### <a name="does-microsoft-defender-for-kubernetes-support-aks-with-virtual-machine-scale-set-nodes"></a>Microsoft Defender for Kubernetes では、仮想マシン スケール セット ノードを含む AKS はサポートされていますか。

クラスターが Azure Kubernetes Service 仮想マシン スケール セットにデプロイされている場合、Log Analytics エージェントは現在サポートされていません。

## <a name="next-steps"></a>次の手順

この記事では、Microsoft Defender for Kubernetes を含む Defender for Cloud での Kubernetes 保護について説明しました。

> [!div class="nextstepaction"]
> [強化された保護を有効にする](enable-enhanced-security.md)

関連資料については、次の記事をご覧ください。

- [SIEM、SOAR、または IT サービス管理ソリューションにアラートをストリーミングする](export-to-siem.md)
- [アラートのリファレンス表](alerts-reference.md)
