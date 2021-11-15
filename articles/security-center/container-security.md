---
title: Microsoft Defender for Cloud でのコンテナーのセキュリティ
description: Microsoft Defender for Cloud のコンテナーのセキュリティ機能について説明します
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 11/02/2021
ms.author: memildin
ms.openlocfilehash: 947aa4c5506b01ee3d22cd4372a4e5a5f0ca4992
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579314"
---
# <a name="container-security-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud のコンテナーのセキュリティ

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud は、コンテナーをセキュリティで保護するためのクラウド ネイティブ ソリューションです。

Defender for Cloud を使用すると、次のコンテナー リソースの種類を保護できます。

| リソースの種類 | Defender for Cloud で提供される保護 |
|:--------------------:|-----------|
| ![Kubernetes サービス。](./media/container-security/icon-kubernetes-service-rec.png)<br>**Kubernetes クラスター** | クラスターの継続的な評価。構成ミスを可視化すると共に、特定された脅威を軽減するためのガイドラインを提供します。 詳細については、[セキュリティ推奨事項による環境のセキュリティ強化](#environment-hardening)に関する情報を参照してください。<br><br>クラスターおよび Linux ノードの脅威の防止。 不審なアクティビティのアラートは、[Microsoft Defender for Kubernetes](defender-for-kubernetes-introduction.md) によって提供されます。 Kubernetes クラスターは、ホストされている場所が Azure Kubernetes Service (AKS)、オンプレミス、他のクラウド プロバイダーのいずれであっても、このプランで防御することができます。 クラスター。 <br>[Kubernetes ノードとクラスターの実行時の保護](#run-time-protection-for-kubernetes-nodes-and-clusters)に関する詳細情報をご覧ください。|
| ![コンテナー ホスト。](./media/container-security/icon-container-host-rec.png)<br>**コンテナー ホスト**<br>(Docker を実行している VM) | お使いの Docker 環境の継続的な評価。構成ミスを可視化すると共に、オプションの [Microsoft Defender for servers](defender-for-servers-introduction.md) によって特定された脅威を軽減するためのガイドラインを提供します。<br>詳細については、[セキュリティ推奨事項による環境のセキュリティ強化](#environment-hardening)に関する情報を参照してください。|
| ![コンテナー レジストリ。](./media/container-security/icon-container-registry-rec.png)<br>**Azure Container Registry (ACR) レジストリ** | オプションの [Microsoft Defender for container registries](defender-for-container-registries-introduction.md) を使用した Azure Resource Manager ベースの ACR レジストリ内のイメージを対象とした脆弱性評価および管理ツール。<br>詳細については、[コンテナー イメージの脆弱性スキャン](#vulnerability-management---scanning-container-images)に関するページを参照してください。 |
|||

この記事では、コンテナー レジストリ、サーバー、Kubernetes 用のオプションの強化された保護と共に Defender for Cloud を使用して、コンテナーとそのアプリのセキュリティを改善、監視、維持する方法について説明します。

ここでは、コンテナーのセキュリティのこうした主要な側面について、Defender for Cloud がどのように役立つかを説明します。

- [脆弱性管理 - コンテナー イメージのスキャン](#vulnerability-management---scanning-container-images)
- [環境のセキュリティ強化](#environment-hardening)
- [Kubernetes ノードとクラスターの実行時の保護](#run-time-protection-for-kubernetes-nodes-and-clusters)

次のスクリーンショットは、資産インベントリ ページと、Defender for Cloud によって保護されるさまざまなコンテナー リソースの種類を示しています。

:::image type="content" source="./media/container-security/inventory-container-resources.png" alt-text="Defender for Cloud の資産インベントリ ページのコンテナー関連のリソース" lightbox="./media/container-security/inventory-container-resources.png":::

## <a name="vulnerability-management---scanning-container-images"></a>脆弱性管理 - コンテナー イメージのスキャン

Azure Resource Manager ベースの Azure コンテナー レジストリ内のイメージを監視するには、[Microsoft Defender for container registries](defender-for-container-registries-introduction.md) を有効にします。 過去 30 日間にプルされたイメージ、レジストリにプッシュされたイメージ、またはインポートされたイメージが、Defender for Cloud によってスキャンされます。 統合されたスキャナーは、業界トップレベルの脆弱性スキャン ベンダー Qualys によって提供されます。

Qualys または Defender for Cloud で問題が見つかった場合は、[ワークロード保護ダッシュボード](workload-protections-dashboard.md)で通知されます。 Defender for Cloud には、すべての脆弱性について、実行可能な推奨事項、重大度の分類、問題の修正方法に関するガイダンスが表示されます。 Defender for Cloud のコンテナーに関する推奨事項の詳細については、[推奨事項の参照リスト](recommendations-reference.md#recs-compute)を参照してください。

スキャナーによる検出結果が、Defender for Cloud によってフィルター処理され、分類されます。 イメージが正常な場合は、Defender for Cloud によってそのようにマークされます。 解決の必要な問題があるイメージに対してのみ、Defender for Cloud によってセキュリティに関する推奨事項が生成されます。 問題があるときにだけ通知することにより、Defender for Cloud での不要な情報アラートの可能性が減ります。

## <a name="environment-hardening"></a>環境のセキュリティ強化

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Docker 構成の継続的な監視

Microsoft Defender for Cloud では、IaaS Linux VM 上、または Docker コンテナーを実行している他の Linux マシン上でホストされているアンマネージド コンテナーが識別されます。 Defender for Cloud は、これらのコンテナーの構成を継続的に評価します。 その後、[Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/) と比較されます。

Defender for Cloud には、CI Docker ベンチマークのルールセット全体が含まれており、コンテナーがどのコントロールも満たさない場合は警告が表示されます。 誤った設定が検出されると、Defender for Cloud によってセキュリティの推奨事項が生成されます。 Defender for Cloud の **推奨事項ページ** を使用して、推奨事項を表示したり、問題を修復したりします。 CIS ベンチマーク チェックは、AKS マネージド インスタンスまたは Databricks マネージド VM では実行されません。

この機能に表示される可能性がある、Defender for Cloud の関連する推奨事項の詳細については、推奨事項参照テーブルの[コンピューティング セクション](recommendations-reference.md#recs-compute)をご覧ください。

VM のセキュリティ問題を調査している場合、Defender for Cloud にはマシン上のコンテナーに関する追加情報が表示されます。 こうした情報には、Docker のバージョンとホストで実行されているイメージの数が含まれます。 

IaaS Linux VM でホストされているアンマネージド コンテナーを監視するには、オプションの[Microsoft Defender for servers](defender-for-servers-introduction.md) を有効にします。


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Kubernetes クラスターの継続的な監視
Defender for Cloud は Azure Kubernetes Service (AKS) と連携して機能します。これは、コンテナー化されたアプリケーションを開発、デプロイ、および管理するための Microsoft のマネージド コンテナー オーケストレーション サービスです。

AKS には、クラスターのセキュリティ体制をセキュリティで制御し、把握する機能があります。 Defender for Cloud では、これらの機能を使用して、AKS クラスターの構成を常に監視し、業界標準に沿ったセキュリティの推奨事項を生成します。

Microsoft Defender for Cloud、Azure Kubernetes Service、Azure Policy 間の相互作用の概要図を次に示します。

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Microsoft Defender for Cloud、Azure Kubernetes Service、Azure Policy 間の相互作用のアーキテクチャの概要" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::

Defender for Cloud によって受信されて分析される項目には、以下が含まれていることがわかります。

- API サーバーからの監査ログ
- Log Analytics エージェントからの生のセキュリティ イベント

    > [!NOTE]
    > 仮想マシン スケール セットで実行されている Azure Kubernetes Service クラスターへの Log Analytics エージェントのインストールは、現在サポートされていません。

- AKS クラスターからのクラスター構成情報
- Azure Policy からのワークロード構成 (**Kubernetes 用の Azure Policy アドオン** を使用)

この機能に表示される可能性がある、Defender for Cloud の関連する推奨事項の詳細については、推奨事項参照テーブルの[コンピューティング セクション](recommendations-reference.md#recs-compute)をご覧ください。


###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Kubernetes 受付制御を使用したワークロード保護のベストプラクティス

Kubernetes コンテナーのワークロードを保護する推奨事項のバンドルを取得するには、**Kubernetes 用 Azure Policy アドオン** をインストールします。 「[Log Analytics エージェントと拡張機能の自動プロビジョニングの有効化](enable-data-collection.md#auto-provision-mma)」の説明に従って、このアドオンを自動でデプロイすることもできます。 アドオンの自動プロビジョニングが "オン" に設定されている場合は、既存のクラスターおよび今後作成されるクラスターすべて (アドオンのインストール要件を満たすもの) で、拡張機能が既定で有効になります。

[こちらの Kubernetes 用 Azure Policy ページ](../governance/policy/concepts/policy-for-kubernetes.md)で説明するように、アドオンによって、 [Open Policy Agent](https://www.openpolicyagent.org/) 用オープンソース [Gatekeeper v3](https://github.com/open-policy-agent/gatekeeper)  アドミッション コントローラー Webhook が拡張されます。 Kubernetes アドミッション コントローラーは、クラスターの使用方法を規定するプラグインです。 アドオンは Webhook として Kubernetes 受付制御に登録され、大規模な実施内容と安全対策を、一元的で一貫性のある方法でお使いのクラスターに適用できるようにします。 

AKS クラスターにアドオンが存在する場合、Kubernetes API サーバーに対するすべての要求が、クラスターに保存される前に、事前定義された一連のベスト プラクティスを基準にして監視されます。 その後、ベスト プラクティスを **適用** し、それを将来のワークロードに対して要求するように構成できます。 

たとえば、特権コンテナーが作成されないように要求することができます。また、今後の特権コンテナー作成要求はすべてブロックされます。

詳細については、「[Kubernetes ワークロードを保護する](kubernetes-workload-protections.md)」を参照してください。


## <a name="run-time-protection-for-kubernetes-nodes-and-clusters"></a>Kubernetes ノードとクラスターの実行時の保護

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>次のステップ

この概要では、Microsoft Defender for Cloud でのコンテナー セキュリティの中核となる要素について説明しました。 関連資料については、以下を参照してください。

- [Microsoft Defender for Kubernetes の概要](defender-for-kubernetes-introduction.md)
- [Microsoft Defender for container registries の概要](defender-for-container-registries-introduction.md)
