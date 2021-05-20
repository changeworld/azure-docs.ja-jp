---
title: Azure Security Center に対する今後の重要な変更
description: 知っておく必要があり、計画する必要がある Azure Security Center の今後の変更
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 05/09/2021
ms.author: memildin
ms.openlocfilehash: ed9ea3abf984f537ab693ccbadb90e2ba091f52d
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2021
ms.locfileid: "109683571"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center に対する今後の重要な変更

> [!IMPORTANT]
> このページの情報は、販売を開始する前に大幅に変更される可能性があるプレリリース製品または機能に関連しています。 Microsoft は、ここで提供される情報に対して、明示または黙示を問わず、一切の確約または保証を行わないものとします。

このページでは、Security Center に対して計画されている変更について説明します。 ここでは、セキュリティ スコアやワークフローなどに影響する可能性がある、製品に対して計画されている変更について説明します。

最新のリリース ノートをお探しの場合は、「[Azure Security Center の最新情報](release-notes.md)」をご覧ください。


## <a name="planned-changes"></a>計画されている変更

| 計画されている変更                                                                                                                                                        | 変更予定日 |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| ["システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | 2021 年 4 月                |
| [Kubernetes アラートのプレフィックスを "AKS_" から "K8s_" に変更](#prefix-for-kubernetes-alerts-changing-from-aks_-to-k8s_)                                               | 2021 年 6 月                 |
| [ISO 27001 のレガシ実装を新しい ISO 27001:2013 で置き換え予定](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)          | 2021 年 6 月                 |
| [AWS からの推奨事項を一般提供 (GA) 用にリリース](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | **2021 年 8 月**           |
| [SQL データ分類の推奨事項を改善](#enhancements-to-sql-data-classification-recommendation)                                                     | 2021 年第 2 四半期                   |
|                                                                                                                                                                       |                           |


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>"システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化

**変更予定日:** 2021 年 4 月

次の 2 つの推奨事項は非推奨になります。

- **クラウド サービス ロールの OS バージョンを更新する必要がある** - Azure では既定で、ゲスト OS は、サービス構成 (.cscfg) に指定した Windows Server 2016 などの OS ファミリ内でサポートされている最新のイメージに定期的に更新されます。
- **Kubernetes Service を脆弱性のない Kubernetes バージョンにアップグレードする必要がある** - この推奨事項の評価は、目標とする広範囲には及んでいません。 この推奨事項の現在のバージョンは、最終的に、よりお客様のセキュリティ ニーズに合った拡張バージョンに置き換えられます。


### <a name="prefix-for-kubernetes-alerts-changing-from-aks_-to-k8s_"></a>Kubernetes アラートのプレフィックスを "AKS_" から "K8s_" に変更

**変更予定日:** 2021 年 6 月

オンプレミスおよびマルチ クラウド環境でホストされている Kubernetes クラスターを保護するため、Azure Defender for Kubernetes が最近拡張されました。 詳細については、「[Azure Defender for Kubernetes を使用したハイブリッドおよびマルチクラウド Kubernetes デプロイの保護 (プレビュー)](release-notes.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)」をご覧ください。

Azure Defender for Kubernetes によって提供されるセキュリティ アラートが Azure Kubernetes Service 上のクラスターのみに制限されなくなったという事実を反映するために、アラートの種類のプレフィックスが "AKS_" から "K8s_" に変更されます。 必要に応じて、名前と説明も更新されます。 たとえば、このアラートです。

|アラート (アラートの種類)|説明|
|----|----|
|Kubernetes penetration testing tool detected (Kubernetes 侵入テスト ツールが検出されました)<br>(**AKS** _PenTestToolsKubeHunter)|Kubernetes 監査ログ分析によって、**AKS** クラスターでの Kubernetes 侵入テスト ツールの使用が検出されました。 この動作は、正当である可能性もありますが、攻撃者はこのような公開ツールを悪意のある目的で使用する可能性があります。
|||

次のようになります。

|アラート (アラートの種類)|説明|
|----|----|
|Kubernetes penetration testing tool detected (Kubernetes 侵入テスト ツールが検出されました)<br>(**K8s** _PenTestToolsKubeHunter)|Kubernetes 監査ログ分析によって、**Kubernetes** クラスターでの Kubernetes 侵入テスト ツールの使用が検出されました。 この動作は、正当である可能性もありますが、攻撃者はこのような公開ツールを悪意のある目的で使用する可能性があります。|
|||

"AKS_" から始まるアラートを参照する抑制ルールは自動的に変換されます。 SIEM エクスポート、またはアラートの種類別に Kubernetes アラートを参照するカスタム自動化スクリプトを設定している場合は、それらを新しいアラートの種類で更新する必要があります。

Kubernetes アラートの完全な一覧については、[Kubernetes クラスターのアラート](alerts-reference.md#alerts-akscluster)に関するセクションをご覧ください。

### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>ISO 27001 のレガシ実装を新しい ISO 27001:2013 で置き換え予定

ISO 27001 のレガシ実装は Security Center の規制コンプライアンス ダッシュボードから削除されます。 Security Center で ISO 27001 へのコンプライアンスを追跡している場合は、関連するすべての管理グループまたはサブスクリプションに対して新しい ISO 27001:2013 標準をオンボードすると、現在のレガシ ISO 27001 が間もなくダッシュボードから削除されます。

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="ISO 27001 のレガシ実装の削除に関するメッセージが表示されている Security Center の規制コンプライアンス ダッシュボード。" lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>AWS からの推奨事項を一般提供 (GA) 用にリリース

**変更予定日:** 2021 年 8 月

Azure Security Center は、Azure、アマゾン ウェブ サービス (AWS)、および Google Cloud Platform (GCP) のワークロードを保護します。

クラウド コネクタが導入されて以来、AWS Security Hub からの推奨事項はプレビュー段階にあります。 **プレビュー** としてフラグ付けされた推奨事項は、セキュリティ スコアの計算には含まれませんが、プレビュー期間が終了したときにスコアに貢献できるように、可能な限り修復する必要があります。

この変更により、AWS の推奨事項の 2 つのセットが GA に移行します。

- [Security Hub の PCI DSS コントロール](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Security Hub の CIS AWS Foundations Benchmark コントロール](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

これらが一般提供され、AWS リソースで評価が実行されると、結果はすべてのマルチおよびハイブリッド クラウド リソースの統合されたセキュリティ スコアに影響します。



### <a name="enhancements-to-sql-data-classification-recommendation"></a>SQL データ分類の推奨事項を改善

**変更予定日:** 2021 年第 2 四半期

"**データ分類の適用**" セキュリティ コントロールにおける推奨事項 "**SQL データベースの機密データを分類する必要がある**" は、より Microsoft のデータ分類戦略に沿った新しいバージョンで置き換えられる予定です。 これにより、推奨事項の ID も変更されます (現在は b0df6f56-862d-4730-8597-38c0fd4ebd59)。



## <a name="next-steps"></a>次のステップ

製品に対する最近のすべての変更については、「[Azure Security Center の最新情報](release-notes.md)」を参照してください。