---
title: Azure Security Center のリリース ノート
description: Azure Security Center の新機能と変更点の説明
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 08/15/2021
ms.author: memildin
ms.openlocfilehash: 04cff79878c3110c6da8b40e7a207693e693e2ba
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122229038"
---
# <a name="whats-new-in-azure-security-center"></a>Azure Security Center の最新情報

Security Center のセキュリティは精力的な開発の下、継続的に改善されています。 常に最新の開発情報を把握していただけるよう、このページでは新しい機能、バグの修正、非推奨になった機能に関する情報を提供します。

このページは頻繁に更新されるため、定期的にアクセスしてご確認ください。 

Security Center で近日中に公開を "*予定されている*" 変更については、「[Azure Security Center への今後予定されている重要な変更](upcoming-changes.md)」を参照してください。 

> [!TIP]
> 6 か月以上前の項目を探す場合は、「[Azure Security Center の最新情報のアーカイブ](release-notes-archive.md)」をご覧ください。

## <a name="august-2021"></a>2021 年 8 月

8 月の更新プログラムには次のものが含まれます。

- [Microsoft Defender for Endpoint for Linux のサーバー用 Azure Defender によるサポート (プレビュー)](#microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview)
- [エンドポイント保護ソリューションを管理するための 2 つの新しい推奨事項 (プレビュー)](#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview)
- [一般的な問題を解決するための組み込みのトラブルシューティングとガイダンス](#built-in-troubleshooting-and-guidance-for-solving-common-issues)
- [規制コンプライアンス ダッシュボードの Azure 監査レポートを一般提供 (GA) としてリリース](#regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga)
- ["お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある" という推奨事項の廃止](#deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines)
- [コンテナー レジストリ用の Azure Defender が Azure Private Link で保護されたレジストリ内の脆弱性をスキャン](#azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link)
- [Security Center による Azure Policy のゲスト構成拡張機能の自動プロビジョニング (プレビュー)](#security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview)
- [Azure Defender プランを有効にする推奨事項での "強制" のサポート](#recommendations-to-enable-azure-defender-plans-now-support-enforce)
- [推奨事項データの CSV エクスポートを 20 MB に制限](#csv-exports-of-recommendation-data-now-limited-to-20-mb)
- [[推奨事項] ページに複数のビューが含まれる](#recommendations-page-now-includes-multiple-views)

### <a name="microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview"></a>Microsoft Defender for Endpoint for Linux のサーバー用 Azure Defender によるサポート (プレビュー)

[サーバー用 Azure Defender](defender-for-servers-introduction.md) には、[Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) の統合ライセンスが含まれます。 同時に、包括的なエンドポイントの検出と対応 (EDR) 機能が提供されます。

Defender for Endpoint で脅威が検出されると、アラートがトリガーされます。 アラートは Security Center に表示されます。 Security Center から、Defender for Endpoint コンソールにピボットし、詳細な調査を実行して攻撃の範囲を明らかにすることもできます。

プレビュー期間中は、既に Windows マシンにデプロイしたかどうかに応じて 2 つの方法のいずれかで、サポートされる Linux マシンに [Defender for Endpoint for Linux](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux) センサーをデプロイします。

- [Azure Defender および Microsoft Defender for Endpoint for Windows の既存ユーザー](security-center-wdatp.md?tabs=linux#existing-users-of-azure-defender-and-microsoft-defender-for-endpoint-for-windows)
- [Microsoft Defender for Endpoint for Windows との統合を有効にしたことがない新規ユーザー](security-center-wdatp.md?tabs=linux#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)

詳細については、「[Security Center の統合 EDR ソリューションを使用してエンドポイントを保護する: Microsoft Defender for Endpoint](security-center-wdatp.md)」を参照してください。

### <a name="two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview"></a>エンドポイント保護ソリューションを管理するための 2 つの新しい推奨事項 (プレビュー)

マシンにエンドポイント保護ソリューションをデプロイして維持するための 2 つの **プレビュー** 推奨事項が追加されています。 どちらの推奨事項にも、Azure 仮想マシンと Azure Arc 対応サーバーに接続されているコンピューターのサポートが含まれています。

|推奨 |Description |重大度 |
|---|---|---|
|[エンドポイント保護をマシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/4fb67663-9ab9-475d-b026-8c544cced439) |脅威と脆弱性からマシンを保護するには、サポートされているエンドポイント保護ソリューションをインストールします。  <br> <a href="/azure/security-center/security-center-endpoint-protection">マシンのエンドポイント保護を評価する方法の詳細をご覧ください。</a><br />(関連ポリシー:[エンドポイント保護の不足を Azure Security Center で監視する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |高 |
|[Endpoint Protection の正常性の問題を、お使いのコンピューターで解決する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/37a3689a-818e-4a0e-82ac-b1392b9bb000) |仮想マシンでエンドポイント保護の正常性の問題を解決して、それらを最新の脅威と脆弱性から保護します。 Azure Security Center でサポートされるエンドポイント保護ソリューションについては、[こちら](./security-center-services.md?tabs=features-windows)を参照してください。 エンドポイント保護の評価については、<a href='/azure/security-center/security-center-endpoint-protection'>こちら</a>で説明されています。<br />(関連ポリシー:[エンドポイント保護の不足を Azure Security Center で監視する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |中 |
|||

> [!NOTE]
> 推奨事項の更新間隔は 8 時間と表示されますが、シナリオによっては、さらに長くかかる場合があります。 たとえば、オンプレミスのマシンが削除されたとき、Security Center が削除を識別するのに 24 時間かかります。 その後、評価によって情報が返されるまでに最大 8 時間かかります。 したがって、この特定の状況では、影響を受けたリソースの一覧からマシンが削除されるまでに 32 時間かかることがあります。
>
> :::image type="content" source="media/release-notes/freshness-interval.png" alt-text="Security Center のこれら 2 つの新しい推奨事項の更新間隔インジケーター":::

### <a name="built-in-troubleshooting-and-guidance-for-solving-common-issues"></a>一般的な問題を解決するための組み込みのトラブルシューティングとガイダンス

Azure portal の Azure Security Center ページの新しい専用領域は、Azure Security Center と Azure Defender に関する一般的な課題を解決するための、拡大し続ける照合されたセルフヘルプ資料のセットを提供します。

問題が発生している場合、またはサポート チームからのアドバイスを求めている場合には、 **[問題の診断と解決]** は、ソリューションを探すために役立つもう 1 つのツールです。

:::image type="content" source="media/release-notes/solve-problems.png" alt-text="Security Center の [問題の診断と解決] ページ":::
 

### <a name="regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga"></a>規制コンプライアンス ダッシュボードの Azure 監査レポートを一般提供 (GA) としてリリース

規制コンプライアンス ダッシュボードのツールバーには、サブスクリプションに適用される基準に関する Azure および Dynamics の証明書レポートが用意されています 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="監査レポートを生成するためのボタンが表示された規制コンプライアンス ダッシュボードのツールバー。":::

関連するレポートの種類 (PCI、SOC、ISO など) のタブを選択し、フィルターを使用すると、必要なレポートを見つけることができます。

詳細については、[コンプライアンス状態レポートと証明書の生成](security-center-compliance-dashboard.md#generate-compliance-status-reports-and-certificates)に関するページを参照してください。

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard-ga.png" alt-text="使用可能な Azure 監査レポートのタブ リスト。ISO レポート、SOC レポート、PCI などのタブが表示されています。":::

### <a name="deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines"></a>"お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある" という推奨事項の廃止

**お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある** という推奨事項は、Security Center のクラウド セキュリティ態勢管理 (CSPM) の取り組みと整合性がないという点で、セキュリティ スコアに影響することがわかりました。 通常、CSPM はセキュリティ構成の誤りを特定する方法に関連するものです。 エージェントの正常性の問題は、このカテゴリの問題には該当しません。

また、この推奨事項は、Security Center に関連する他のエージェントと比較した場合、特異なものです。これは、正常性の問題に関する推奨事項を持つ唯一のエージェントだからです。

この推奨事項は廃止されました。

この廃止を受けて、Log Analytics エージェントのインストールに関する推奨事項についても少し変更しました (**Log Analytics エージェントを ... にインストールする必要があります**)。

この変更は、セキュリティ スコアに影響を与える可能性があります。 ほとんどのサブスクリプションでは、変更によってスコアが上がることが期待できますが、インストールの推奨事項を更新すると、場合によってはスコアが下がる可能性があります。

> [!TIP]
> また、[[資産インベントリ]](asset-inventory.md) ページも、この変更の影響も受けています。マシンの監視状態 (監視、監視なし、または部分的な監視 - 正常性の問題があるエージェントを表す状態) が表示されるためです。


### <a name="azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link"></a>コンテナー レジストリ用の Azure Defender が Azure Private Link で保護されたレジストリ内の脆弱性をスキャン
コンテナー レジストリ用の Azure Defender に、Azure Container Registry レジストリ内のイメージをスキャンする脆弱性スキャナーが含まれています。 レジストリをスキャンして結果を修復する方法については、「[コンテナー レジストリ用の Azure Defender を使用してイメージの脆弱性をスキャンする](defender-for-container-registries-usage.md)」を参照してください。

Azure Container Registry でホストされるレジストリへのアクセスを制限するには、「[Azure Private Link を使用して Azure Container Registry にプライベートで接続する](../container-registry/container-registry-private-link.md)」の説明に従って、仮想ネットワークのプライベート IP アドレスをレジストリ エンドポイントに割り当てて、Azure Private Link を使用します。

追加の環境やユースケースをサポートする継続的な取り組みの一環として、[Azure Private Link](../private-link/private-link-overview.md) で保護されるコンテナー レジストリのスキャンも Azure Defender によって行われるようになりました。


### <a name="security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview"></a>Security Center による Azure Policy のゲスト構成拡張機能の自動プロビジョニング (プレビュー)
Azure Policy では、Azure 内で実行するマシンと Arc に接続されたマシンの両方に対して、マシン内の設定を監査できます。 検証は、クライアントとゲスト構成拡張機能によって実行されます。 詳細については、「[Azure Policy のゲストの構成の理解](../governance/policy/concepts/guest-configuration.md)」を参照してください。

この更新プログラムにより、サポートされているすべてのコンピューターにこの拡張機能を自動的にプロビジョニングするように Security Center を設定できるようになりました。 

:::image type="content" source="media/release-notes/auto-provisioning-guest-configuration.png" alt-text="ゲスト構成拡張の自動デプロイを有効にします。":::

### <a name="recommendations-to-enable-azure-defender-plans-now-support-enforce"></a>Azure Defender プランを有効にする推奨事項での "強制" のサポート
Security Center には、新しく作成されたリソースが安全な方法でプロビジョニングされるために役立つ、**強制** と **拒否** という 2 つの機能があります。 推奨事項によってこれらのオプションが提供されると、だれかがリソースを作成しようとするとき常に、セキュリティ要件が満たされていることを保証できます。

- **[拒否]** によって、異常なリソースの作成が止められます。
- **[強制]** によって、準拠していないリソースが作成されたときに自動的に修復されます。

この更新プログラムによって、強制オプションを推奨事項で使用して Azure Defender プラン ( **[Azure Defender for App Service を有効にする必要がある]** 、 **[Azure Defender for Key Vault を有効にする必要がある]** 、 **[Azure Defender for Storage を有効にする必要がある]** など) を有効にできるようになりました。

これらのオプションの詳細については、「[適用/拒否の推奨事項を使用した構成ミスの防止](prevent-misconfigurations.md)」を参照してください。

### <a name="csv-exports-of-recommendation-data-now-limited-to-20-mb"></a>推奨事項データの CSV エクスポートを 20 MB に制限

Security Center の推奨事項データをエクスポートする際に 20 MB に制限します。

:::image type="content" source="media/upcoming-changes/download-csv-report.png" alt-text="推奨事項に関するデータをエクスポートするための Security Center の [CSV レポートのダウンロード] ボタン。":::

大量のデータをエクスポートする必要がある場合は、選択する前に使用可能なフィルターを使用するか、またはサブスクリプションのサブセットを選択してデータをバッチでダウンロードします。

:::image type="content" source="media/upcoming-changes/filter-subscriptions.png" alt-text="Azure portal でサブスクリプションをフィルター処理する":::

詳細については、[セキュリティに関する推奨事項の CSV エクスポートの実行](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations)に関するページを参照してください。



### <a name="recommendations-page-now-includes-multiple-views"></a>[推奨事項] ページに複数のビューが含まれる

[推奨事項] ページに、リソースに関連する推奨事項を別の方法で表示できるように 2 つのタブが用意されました。

- **[Secure score recommendations]\(セキュア スコア推奨事項\)** - このタブを使用して、セキュリティ コントロール別にグループ化された推奨事項の一覧を表示します。 これらのコントロールの詳細については、「[セキュリティ コントロールとその推奨事項](secure-score-security-controls.md#security-controls-and-their-recommendations)」を参照してください。
- **[すべての推奨事項]** - このタブを使用して、推奨事項の一覧をフラット リストとして表示します。 このタブは、推奨事項を生成したイニシアチブ (規制コンプライアンス基準を含む) を理解するのにも役立ちます。 イニシアチブと、推奨事項との関係の詳細については、「[セキュリティ ポリシー、イニシアチブ、および推奨事項とは](security-policy-concept.md)」を参照してください。

:::image type="content" source="media/release-notes/recommendations-tabs.png" alt-text="Azure Security Center の推奨事項一覧の表示を変更するためのタブ。":::

## <a name="july-2021"></a>2021 年 7 月

7 月の更新プログラムには次のものが含まれます。

- [Azure Sentinel コネクタにオプションの双方向アラート同期が含まれる (プレビュー)](#azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview)
- [Azure Resource Manager アラート用の Azure Defender の論理的な再編成](#logical-reorganization-of-azure-defender-for-resource-manager-alerts)                                           
- [Azure Disk Encryption (ADE) を有効にする推奨事項の強化](#enhancements-to-recommendation-to-enable-azure-disk-encryption-ade)                                     
- [セキュア スコアと規制コンプライアンス データの連続エクスポートを一般提供 (GA) としてリリース](#continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga)
- [規制コンプライアンス評価の変更によるワークフロー自動化のトリガー (GA)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga)
- [Assessments API フィールド 'FirstEvaluationDate' および 'StatusChangeDate' がワークスペース スキーマとロジック アプリで使用可能](#assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps)
- ['Compliance over time' ブック テンプレートが Azure Monitor ブック ギャラリーに追加](#compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery)

### <a name="azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview"></a>Azure Sentinel コネクタにオプションの双方向アラート同期が含まれる (プレビュー)

Security Center には、[Azure Sentinel](../sentinel/index.yml)(Azure のクラウドネイティブな SIEM および SOAR ソリューション) がネイティブに統合されています。 

Azure Sentinel には、サブスクリプションとテナントのレベルで Azure Security Center 用の組み込みコネクタが含まれています。 詳細については、「[Azure Sentinel にアラートをストリーミングする](export-to-siem.md#stream-alerts-to-azure-sentinel)」を参照してください。

Azure Defender を Azure Sentinel に接続すると、Azure Defender アラートの状態が Azure Sentinel に取り込まれる、2 つのサービス間で同期されます。 たとえば、Azure Defender でアラートが閉じられた場合、そのアラートは Azure Sentinel でも閉じられたと表示されます。 Azure Defender でアラートの状態を変更しても、同期された Azure Sentinel アラートを含む Azure Sentinel **インシデント** の状態には影響せず、同期されたアラートそのものの状態のみに影響します。

プレビュー機能である、この **双方向アラート同期** を有効にすると、元の Azure Defender アラートの状態が、それらの Azure Defender アラートのコピーを含む Azure Sentinel インシデントと自動的に同期されます。 そのため、たとえば、Azure Defender アラートを含む Azure Sentinel インシデントが閉じられると、対応する元のアラートが Azure Defender によって自動的に閉じられます。

詳細については、「[Azure Security Center からの Azure Defender アラートの接続](../sentinel/connect-azure-security-center.md)」を参照してください。

### <a name="logical-reorganization-of-azure-defender-for-resource-manager-alerts"></a>Azure Resource Manager アラート用の Azure Defender の論理的な再編成

次に示すアラートは、[Azure Defender For Resource Manager](defender-for-resource-manager-introduction.md) プランの一部として提供されていました。

Azure Defender プランの論理的な再編成の一環として、一部のアラートが **Azure Defender for Resource Manager** から **サーバー用 Azure Defender** に移動されました。

アラートは、次の 2 つの主要原則に従って編成されます。

- さまざまな種類の Azure リソースにわたるコントロールプレーンの保護を提供するアラートは、Azure Defender for Resource Manager の一部になります。
- 特定のワークロードを保護するアラートは、対応するワークロードに関連する Azure Defender プランに含まれます。

これらは、Azure Defender for Resource Manager に属するアラートでしたが、この変更の結果として、現在はサーバー用 Azure Defender にあります。

- ARM_AmBroadFilesExclusion
- ARM_AmDisablementAndCodeExecution
- ARM_AmDisablement
- ARM_AmFileExclusionAndCodeExecution
- ARM_AmTempFileExclusionAndCodeExecution
- ARM_AmTempFileExclusion
- ARM_AmRealtimeProtectionDisabled
- ARM_AmTempRealtimeProtectionDisablement
- ARM_AmRealtimeProtectionDisablementAndCodeExec
- ARM_AmMalwareCampaignRelatedExclusion
- ARM_AmTemporarilyDisablement
- ARM_UnusualAmFileExclusion
- ARM_CustomScriptExtensionSuspiciousCmd
- ARM_CustomScriptExtensionSuspiciousEntryPoint
- ARM_CustomScriptExtensionSuspiciousPayload
- ARM_CustomScriptExtensionSuspiciousFailure
- ARM_CustomScriptExtensionUnusualDeletion
- ARM_CustomScriptExtensionUnusualExecution
- ARM_VMAccessUnusualConfigReset
- ARM_VMAccessUnusualPasswordReset
- ARM_VMAccessUnusualSSHReset

[Azure Defender for Resource Manager](defender-for-resource-manager-introduction.md) と[サーバー用 Azure Defender](defender-for-servers-introduction.md) プランの詳細を参照してください。


### <a name="enhancements-to-recommendation-to-enable-azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE) を有効にする推奨事項の強化

ユーザーからのフィードバックに従い、 **[仮想マシンにディスク暗号化を適用する必要がある]** という推奨事項の名前を変更しました。

新しい推奨事項には同じ評価 ID が使用され、 **[コンピューティングとストレージのリソース間で一時ディスク、キャッシュ、データ フローを仮想マシンによって暗号化する必要がある]** と呼ばれます。

このセキュリティ強化推奨事項の目的をわかりやすくするために説明も更新しています。

| 推奨                                                                                               | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 重大度 |
|--------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------:|
| **コンピューティングとストレージのリソース間で一時ディスク、キャッシュ、データ フローを仮想マシンによって暗号化する必要がある** | 既定では、仮想マシンの OS とデータ ディスクは、プラットフォーム マネージド キーを使用して保存時に暗号化されます。一時ディスクとデータ キャッシュは暗号化されません。また、データがコンピューティング リソースとストレージ リソース間でやり取りされる際には暗号化されません。 Azure でのさまざまなディスク暗号化テクノロジの比較については、 https://aka.ms/diskencryptioncomparison を参照してください。<br>Azure Disk Encryption を使用してこのデータをすべて暗号化します。 次の場合は、この推奨事項を無視します。(1) ホスト上の暗号化機能を使用している場合。または、(2) Managed Disks のサーバー側暗号化がセキュリティ要件を満たしている場合。 詳細については、「Azure Disk Storage のサーバー側暗号化」を参照してください。 | 高     |
|                                                                                                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |          |


### <a name="continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga"></a>セキュア スコアと規制コンプライアンス データの連続エクスポートを一般提供 (GA) としてリリース

[連続エクスポート](continuous-export.md)によって、セキュリティ アラートと推奨事項を環境内の他の監視ツールで追跡するためにエクスポートするメカニズムが提供されます。

連続エクスポートを設定するときに、エクスポートする内容とエクスポート先の場所を構成します。 詳細については、[連続エクスポートの概要](continuous-export.md)に関するページを参照してください。

時間経過と共に、この機能の強化および拡張を行っています。

- 2020 年 11 月、**セキュア スコア** への変更をストリーミングするための **プレビュー** オプションを追加しました。<br/>詳細については、「[連続エクスポートでセキュア スコアが利用可能に (プレビュー)](release-notes-archive.md#secure-score-is-now-available-in-continuous-export-preview)」を参照してください。

- 2020 年 12 月、**規制コンプライアンス評価データ** への変更をストリーミングするための **プレビュー** オプションを追加しました。<br/>詳細については、「[連続エクスポートで新しいデータ型が利用可能に (プレビュー)](release-notes-archive.md#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)」を参照してください。

この更新プログラムでは、これら 2 つのオプションが一般提供 (GA) としてリリースされます。 


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga"></a>規制コンプライアンス評価の変更によるワークフロー自動化のトリガー (GA)

2021 年 2 月、ワークフロー自動化のトリガー オプションに、規制コンプライアンス評価の変更という、**プレビュー** の 3 つ目のデータの種類が追加されました。 詳細については、「[規制コンプライアンス評価の変更によって、ワークフロー自動化をトリガー可能](release-notes-archive.md#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)」を参照してください。

この更新プログラムでは、このトリガー オプションが一般提供 (GA) としてリリースされます。

ワークフローの自動化ツールの使い方については、「[Security Center のトリガーへの応答を自動化する](workflow-automation.md)」を参照してください。

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="規制コンプライアンス評価の変更を使用した、ワークフロー自動化のトリガー。" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

### <a name="assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps"></a>Assessments API フィールド 'FirstEvaluationDate' および 'StatusChangeDate' がワークスペース スキーマとロジック アプリで使用可能

2021 年 5 月、2 つのフィールド **FirstEvaluationDate** および **StatusChangeDate** を含むように Assessment API を更新しました。 詳細については、「[Assessments API を 2 つの新しいフィールドで拡張](#assessments-api-expanded-with-two-new-fields)」を参照してください。

これらのフィールドには、REST API、Azure Resource Graph、連続エクスポート、および CSV エクスポートを使用してアクセスできました。

この変更では、Log Analytics ワークスペースとロジック アプリで情報を使用できるようにしています。


### <a name="compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery"></a>'Compliance over time' ブック テンプレートが Azure Monitor ブック ギャラリーに追加

3 月に、Security Center での統合 Azure Monitor ブック エクスペリエンスを発表しました (「[Azure Monitor ブックを Security Center に統合し、テンプレートを 3 件提供](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)」を参照してください)。

最初のリリースには、組織のセキュリティ体制に関して動的レポートとビジュアル レポートを構築するための 3 つのテンプレートが含まれていました。

ここでは、適用される規制または業界標準に関するサブスクリプションのコンプライアンスを追跡する専用のブックを追加しました。 

ここに挙げたレポートを使用する方法や、独自のレポートを作成する方法の詳細については、「[豊富な機能を備えた対話型の Security Center データ レポートを作成する](custom-dashboards-azure-workbooks.md)」を参照してください。

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-details.png" alt-text="Azure Security Center のコンプライアンスの時間経過を示すブック":::


## <a name="june-2021"></a>2021 年 6 月

6 月の更新プログラムには次のものが含まれます。

- [Azure Defender for Key Vault の新しいアラート](#new-alert-for-azure-defender-for-key-vault)
- [既定では無効になっているカスタマー マネージド キー (CMK) を使用して暗号化するための推奨事項](#recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default)
- [Kubernetes アラートのプレフィックスを "AKS_" から "K8S_" に変更](#prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_)
- ["システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化](#deprecated-two-recommendations-from-apply-system-updates-security-control)


### <a name="new-alert-for-azure-defender-for-key-vault"></a>Azure Defender for Key Vault の新しいアラート

Azure Defender for Key Vault で提供される脅威の保護を拡張するために、次のアラートを追加しました。

| アラート (アラートの種類)                                                                 | 説明                                                                                                                                                                                                                                                                                                                                                      | MITRE の方針 | 重大度 |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| Access from a suspicious IP address to a key vault (疑わしい IP アドレスからのキー コンテナーへのアクセス)<br>(KV_SuspiciousIPAccess)  | キー コンテナーが、Microsoft 脅威インテリジェンスによって疑わしい IP アドレスとして識別された IP によって正常にアクセスされました。 これは、お使いのインフラストラクチャが侵害を受けたことを示しているおそれがあります。 さらに詳しく調査することをお勧めします。 [Microsoft の脅威インテリジェンス機能](https://go.microsoft.com/fwlink/?linkid=2128684)の詳細に関するページを参照してください。 | 資格情報アクセス                            | Medium   |
|||

詳細については、次を参照してください。
- [Azure Defender for Key Vault の概要](defender-for-resource-manager-introduction.md)
- [Azure Defender の Key Vault アラートに対応する](defender-for-key-vault-usage.md)
- [Azure Defender for Key Vault で提供されるアラートの一覧](alerts-reference.md#alerts-azurekv)


### <a name="recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default"></a>既定では無効になっているカスタマー マネージド キー (CMK) を使用して暗号化するための推奨事項

Security Center には、カスタマー マネージド キーを使用して保存データを暗号化するための次のような複数の推奨事項があります。

- コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある
- Azure Cosmos DB アカウントでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある
- Azure Machine Learning ワークスペースは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある

Azure のデータは、プラットフォーム マネージド キーを使用して自動的に暗号化されるため、カスタマー マネージド キーの使用は、組織が適用を選択した特定のポリシーに準拠するために必要な場合にのみ適用する必要があります。

この変更に伴い、CMK を使用するための推奨事項は、**既定では無効** になりました。 組織に関係がある場合は、対応するセキュリティ ポリシーの *Effect* パラメーターを **AuditIfNotExists** または **Enforce** に変更することにより、有効にすることができます。 詳細については、「[セキュリティ ポリシーの有効化](tutorial-security-policy.md#enable-a-security-policy)」を参照してください。

この変更は、次の例で示すように、新しいプレフィックス **[必要に応じて有効にする]** を使用して推奨事項の名前に反映されています。

- [必要に応じて有効にする] ストレージ アカウントでは、カスタマー マネージド キーを使用して保存データを暗号化する必要がある
- [必要に応じて有効にする] コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある
- [必要に応じて有効にする] Cosmos DB アカウントでは、カスタマー マネージド キーを使用して保存データを暗号化する必要がある

:::image type="content" source="media/upcoming-changes/customer-managed-keys-disabled.png" alt-text="Security Center の CMK に関する推奨事項は、既定では無効になります。" lightbox="media/upcoming-changes/customer-managed-keys-disabled.png":::


### <a name="prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_"></a>Kubernetes アラートのプレフィックスを "AKS_" から "K8S_" に変更

オンプレミスおよびマルチ クラウド環境でホストされている Kubernetes クラスターを保護するため、Azure Defender for Kubernetes が最近拡張されました。 詳細については、「[Azure Defender for Kubernetes を使用したハイブリッドおよびマルチクラウド Kubernetes デプロイの保護 (プレビュー)](release-notes.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)」をご覧ください。

Azure Defender for Kubernetes によって提供されるセキュリティ アラートが Azure Kubernetes Service 上のクラスターに制限されなくなったという事実を反映するために、アラートの種類のプレフィックスを "AKS_" から "K8S_" に変更しました。 必要に応じて、名前と説明も更新されています。 たとえば、このアラートです。

|アラート (アラートの種類)|説明|
|----|----|
|Kubernetes penetration testing tool detected (Kubernetes 侵入テスト ツールが検出されました)<br>(**AKS** _PenTestToolsKubeHunter)|Kubernetes 監査ログ分析によって、**AKS** クラスターでの Kubernetes 侵入テスト ツールの使用が検出されました。 この動作は、正当である可能性もありますが、攻撃者はこのような公開ツールを悪意のある目的で使用する可能性があります。
|||

変更後:

|アラート (アラートの種類)|説明|
|----|----|
|Kubernetes penetration testing tool detected (Kubernetes 侵入テスト ツールが検出されました)<br>(**K8S** _PenTestToolsKubeHunter)|Kubernetes 監査ログ分析によって、**Kubernetes** クラスターでの Kubernetes 侵入テスト ツールの使用が検出されました。 この動作は、正当である可能性もありますが、攻撃者はこのような公開ツールを悪意のある目的で使用する可能性があります。|
|||

"AKS_" から始まるアラートを参照する抑制ルールは自動的に変換されています。 SIEM エクスポート、またはアラートの種類別に Kubernetes アラートを参照するカスタム自動化スクリプトを設定している場合は、それらを新しいアラートの種類で更新する必要があります。

Kubernetes アラートの完全な一覧については、[Kubernetes クラスターのアラート](alerts-reference.md#alerts-k8scluster)に関するセクションをご覧ください。

### <a name="deprecated-two-recommendations-from-apply-system-updates-security-control"></a>"システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化

次の 2 つの推奨事項は非推奨になりました。

- **クラウド サービス ロールの OS バージョンを更新する必要がある** - Azure では既定で、ゲスト OS は、サービス構成 (.cscfg) に指定した Windows Server 2016 などの OS ファミリ内でサポートされている最新のイメージに定期的に更新されます。
- **Kubernetes Service を脆弱性のない Kubernetes バージョンにアップグレードする必要がある** - この推奨事項の評価は、目標とする広範囲には及んでいません。 推奨事項は、よりお客様のセキュリティ ニーズに合った強化バージョンに置き換えられる予定です。


## <a name="may-2021"></a>2021 年 5 月

5 月の更新プログラムには次のものが含まれます。

- [Azure Defender for DNS および Azure Defender for Resource Manager を一般提供 (GA) としてリリース](#azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga)
- [オープンソースのリレーショナル データベース向け Azure Defender を一般提供 (GA) としてリリース](#azure-defender-for-open-source-relational-databases-released-for-general-availability-ga)
- [Azure Defender for Resource Manager の新しいアラート](#new-alerts-for-azure-defender-for-resource-manager)
- [GitHub ワークフローおよび Azure Defender を使用したコンテナー イメージの CI/CD 脆弱性スキャン (プレビュー)](#cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview)
- [一部の推奨事項に使用できる Resource Graph クエリを増加](#more-resource-graph-queries-available-for-some-recommendations)
- [SQL データ分類の推奨事項の重要度を変更](#sql-data-classification-recommendation-severity-changed)
- [トラステッド起動機能を有効にするための新しい推奨事項 (プレビュー段階)](#new-recommendations-to-enable-trusted-launch-capabilities-in-preview)
- [Kubernetes クラスターを強化するための新しい推奨事項 (プレビュー段階)](#new-recommendations-for-hardening-kubernetes-clusters-in-preview)
- [Assessments API を 2 つの新しいフィールドで拡張](#assessments-api-expanded-with-two-new-fields)
- [資産インベントリでクラウド環境フィルターを取得](#asset-inventory-gets-a-cloud-environment-filter)


### <a name="azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga"></a>Azure Defender for DNS および Azure Defender for Resource Manager を一般提供 (GA) としてリリース

これら 2 つのクラウドネイティブの広範な脅威保護計画が GA になりました。

これらの新しい保護により、脅威アクターからの攻撃に対する回復性が強化され、Azure Defender によって保護される Azure リソースの数が大幅に増加します。

- **Azure Defender for Resource Manager** - 組織で実行されるすべてのリソース管理操作を自動的に監視します。 詳細については、次を参照してください。
    - [Azure Defender for Resource Manager の概要](defender-for-resource-manager-introduction.md)
    - [Azure Defender for Resource Manager アラートに対応する](defender-for-resource-manager-usage.md)
    - [Azure Defender for Resource Manager で提供されるアラートの一覧](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender for DNS** - Azure リソースからのすべての DNS クエリを継続的に監視します。 詳細については、次を参照してください。
    - [Azure Defender for DNS の概要](defender-for-dns-introduction.md)
    - [Azure Defender for DNS アラートに対応する](defender-for-dns-usage.md)
    - [Azure Defender for DNS で提供されるアラートの一覧](alerts-reference.md#alerts-dns)

これらの計画を有効にするプロセスを簡略化するには、次の推奨事項を使用します。

- **Azure Defender for Resource Manager を有効にする必要がある**
- **Azure Defender for DNS を有効にする必要がある**

> [!NOTE]
> Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページ https://aka.ms/pricing-security-center を参照してください。


### <a name="azure-defender-for-open-source-relational-databases-released-for-general-availability-ga"></a>オープンソースのリレーショナル データベース向け Azure Defender を一般提供 (GA) としてリリース

Azure Security Center では、オープンソースのリレーショナル データベースに対応するために、新しいバンドルを使用して SQL 保護のオファーを拡張します。

- **Azure SQL データベース サーバー向け Azure Defender** - Azure ネイティブの SQL Server を保護します
- **マシン上の SQL サーバー向け Azure Defender** - ハイブリッド、マルチクラウド、およびオンプレミスの環境で SQL サーバーに対して同様の保護を拡張します
- **オープンソースのリレーショナル データベース向け Azure Defender** - MySQL、PostgreSQL、MariaDB 単一サーバー向けの Azure Defender を防御します。

オープンソースのリレーショナル データベース向け Azure Defender では、サーバーに対するセキュリティ上の脅威を常に監視し、MySQL、PostgreSQL、MariaDB 向けの Azure Defender に対する潜在的な脅威を示す異常なデータベース アクティビティを検出します。 いくつかの例を次に示します。

- **ブルート フォース攻撃の詳細な検出** - オープンソースのリレーショナル データベース向け Azure Defender により、ブルート フォース攻撃の試行および成功について詳細な情報が提供されます。 これにより、調査を行い、お使いの環境に対する攻撃の性質や状態について理解を深め、対処することができます。
- **動作アラートの検出** - オープンソースのリレーショナル データベース向け Azure Defender により、データベースに対するアクセス パターンの変更など、サーバーに対する疑わしいおよび不測の動作が警告されます。
- **脅威インテリジェンスベースの検出** - Azure Defender では、Microsoft の脅威インテリジェンスと膨大なナレッジ ベースを適用して脅威アラートが表示されるため、それらに対処することができます。

詳細については、「[オープンソースのリレーショナル データベース向け Azure Defender の概要](defender-for-databases-introduction.md)」を参照してください。

### <a name="new-alerts-for-azure-defender-for-resource-manager"></a>Azure Defender for Resource Manager の新しいアラート

Azure Defender for Resource Manager によって提供される脅威保護を拡張するために、次のアラートを追加しました。

| アラート (アラートの種類)                                                                                                                                                | 説明                                                                                                                                                                                                                                                                                                                                                                                                                              | MITRE の方針 | 重大度 |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------:|----------|
|**Permissions granted for an RBAC role in an unusual way for your Azure environment (Preview) \(Azure 環境の通常の方法とは異なる方法で RBAC ロールに付与されたアクセス許可 \(プレビュー\)\)**<br>(ARM_AnomalousRBACRoleAssignment)|割り当て時間、任命者の場所、担当者、認証方法、割り当てられたエンティティ、使用されるクライアントソフトウェア、割り当ての範囲が異常であるため、テナント内の同じ任命者によって実行された、または同じ担当者に対して実行された他の割り当てとは異なる RBAC ロール割り当てが、Azure Defender for Resource Manager によって検出されました。 この操作は、組織内の正当なユーザーによって実行された可能性があります。 または、組織内のアカウントが侵害されたこと、および脅威アクターが、自分が所有する追加のユーザー アカウントにアクセス許可を付与しようとしていることを示している場合もあります。|侵入の拡大、防御回避|Medium|
|**Privileged custom role created for your subscription in a suspicious way (Preview) \(疑わしい方法でサブスクリプション用に作成された特権付きカスタム ロール \(プレビュー\)\)**<br>(ARM_PrivilegedRoleDefinitionCreation)|Azure Defender for Resource Manager によって、サブスクリプション内で、疑わしい方法で作成された特権付きカスタム ロールの定義が検出されました。 この操作は、組織内の正当なユーザーによって実行された可能性があります。 または、組織内のアカウントが侵害されたこと、および脅威アクターが、将来検出を回避するために使用する特権ロールを作成しようとしていることを示している場合もあります。|侵入の拡大、防御回避|低|
|**Azure Resource Manager operation from suspicious IP address (Preview) \(疑わしい IP アドレスからの Azure Resource Manager の操作 \(プレビュー\)\)**<br>(ARM_OperationFromSuspiciousIP)|Azure Defender for Resource Manager によって、脅威インテリジェンス フィードで疑わしいとしてマークされている IP アドレスからの操作が検出されました。|実行|Medium|
|**Azure Resource Manager operation from suspicious proxy IP address (Preview) \(疑わしいプロキシ IP アドレスからの Azure Resource Manager の操作 \(プレビュー\)\)**<br>(ARM_OperationFromSuspiciousProxyIP)|Azure Defender for Resource Manager によって、TOR などのプロキシ サービスに関連付けられた IP アドレスからのリソース管理操作が検出されました。 この動作は正当である可能性もありますが、悪意のあるアクティビティで脅威アクターがソース IP を隠そうとするときに多く見られます。|防御回避|Medium|
||||

詳細については、次を参照してください。
- [Azure Defender for Resource Manager の概要](defender-for-resource-manager-introduction.md)
- [Azure Defender for Resource Manager アラートに対応する](defender-for-resource-manager-usage.md)
- [Azure Defender for Resource Manager で提供されるアラートの一覧](alerts-reference.md#alerts-resourcemanager)


### <a name="cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview"></a>GitHub ワークフローおよび Azure Defender を使用したコンテナー イメージの CI/CD 脆弱性スキャン (プレビュー)

コンテナー レジストリ向け Azure Defender により、DevSecOps チームは、GitHub アクション ワークフローを監視できます。

Trivy を利用した、コンテナー イメージのための新しい脆弱性スキャン機能により、開発者は、イメージをコンテナー レジストリにプッシュする "*前*" に、コンテナー イメージ内の一般的な脆弱性をスキャンできます。

コンテナー スキャン レポートは Azure Security Center に要約されるため、セキュリティ チームは、脆弱なコンテナーイメージのソースと、それらが生成されたワークフローとリポジトリについて、より詳細な分析情報を入手して理解を深めることができます。

詳細については、「[CI/CD ワークフローで脆弱なコンテナー イメージを特定する](defender-for-container-registries-cicd.md)」を参照してください。

### <a name="more-resource-graph-queries-available-for-some-recommendations"></a>一部の推奨事項に使用できる Resource Graph クエリを増加

Security Center のすべての推奨事項には、 **[クエリを開く]** から Azure Resource Graph を使用して、影響を受けるリソースの状態に関する情報を表示するためのオプションがあります。 この強力な機能の詳細については、「[Azure Resource Graph Explorer (ARG) で推奨事項データを確認する](security-center-recommendations.md#review-recommendation-data-in-azure-resource-graph-explorer-arg)」を参照してください。

Security Center には、VM、SQL サーバーとそのホスト、コンテナー レジストリでセキュリティ上の脆弱性をスキャンするための脆弱性スキャンが組み込まれています。 結果は、各リソースの種類ごとに個別のすべての結果が単一のビューに収集され、推奨事項として返されます。 推奨事項は次のとおりです。

- Azure Container Registry イメージの脆弱性を修復する必要がある (Qualys を利用)
- 仮想マシンの脆弱性を修復する必要がある
- SQL データベースでは脆弱性の検出結果を解決する必要がある
- マシン上の SQL サーバーでは脆弱性の検出結果を解決する必要がある

この変更に伴い、 **[クエリを開く]** ボタンを使用して、セキュリティの結果を示すクエリを開くこともできます。

:::image type="content" source="media/release-notes/open-query-menu-security-findings.png" alt-text="[クエリを開く] ボタンを使用して、より詳細なクエリを実行し、脆弱性スキャナー関連の推奨事項に関するセキュリティの結果を表示できるようになりました。":::

**[クエリを開く]** ボタンを使用すると、関連するその他の推奨事項に関する追加のオプションも表示できます。

Security Center の脆弱性スキャナーの詳細については、次のページを参照してください。

- [Azure およびハイブリッドのマシンに対する Azure Defender の統合された脆弱性評価スキャナー](deploy-vulnerability-assessment-vm.md)
- [SQL サーバーに対して Azure Defender の統合された脆弱性評価スキャナーを使用する](defender-for-sql-on-machines-vulnerability-assessment.md)
- [コンテナー レジストリ向け Azure Defender の統合された脆弱性評価スキャナー](defender-for-container-registries-usage.md)

### <a name="sql-data-classification-recommendation-severity-changed"></a>SQL データ分類の推奨事項の重要度を変更

推奨事項 "**SQL データベースの機密データを分類する必要がある**" の重要度が **高** から **低** に変更されました。

これは、「[SQL データベースで機密データを分類するための推奨事項の強化](upcoming-changes.md#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)」で発表された、この推奨事項に対する継続的な変更の一環として行われたものです。

### <a name="new-recommendations-to-enable-trusted-launch-capabilities-in-preview"></a>トラステッド起動機能を有効にするための新しい推奨事項 (プレビュー段階)

Azure からは、[第 2 世代](../virtual-machines/generation-2.md)の VM のセキュリティを向上させるためのシームレスな方法として、トラステッド起動が提供されています。 トラステッド起動により、高度で永続的な攻撃手法から保護されます。 トラステッド起動は、個別に有効にできる、複数の連携するインフラストラクチャ テクノロジで構成されています。 テクノロジごとに、高度な脅威に対する防御の別のレイヤーが提供されます。 詳細については、「[Azure 仮想マシンのトラステッド起動](../virtual-machines/trusted-launch.md)」を参照してください。

> [!IMPORTANT]
> トラステッド起動を使用するには、新しい仮想マシンを作成する必要があります。 最初に作成されたときにトラステッド起動が有効にされていない既存の仮想マシンで、トラステッド起動を有効にすることはできません。
> 
> トラステッド起動は、現在パブリック プレビュー段階にあります。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

Security Center の推奨事項「**vTPM should be enabled on supported virtual machines \(サポートされる仮想マシンで vTPM を有効にする必要がある\)** 」により、Azure VM で vTPM が確実に使用されます。 この仮想化バージョンのハードウェア トラステッド プラットフォーム モジュールでは、VM のブート チェーン全体 (UEFI、OS、システム、ドライバー) を測定することにより、構成証明を有効にします。

vTPM を有効にすると、**Guest Attestation 拡張機能** により、セキュア ブートをリモートで検証できます。 次の推奨事項により、この拡張機能が確実にデプロイされます。

- **セキュア ブートを、サポートしている Windows 仮想マシンで有効にする必要があります**
- **Guest Attestation 拡張機能を、サポートしている Windows 仮想マシンにインストールする必要があります**
- **サポートされている Windows 仮想マシンのスケール セットに Guest Attestation 拡張機能をインストールする必要がある**
- **Guest Attestation 拡張機能を、サポートしている Linux 仮想マシンにインストールする必要があります**
- **サポートされている Linux 仮想マシンのスケール セットに Guest Attestation 拡張機能をインストールする必要がある**

詳細については、「[Azure 仮想マシンのトラステッド起動](../virtual-machines/trusted-launch.md)」を参照してください。 

### <a name="new-recommendations-for-hardening-kubernetes-clusters-in-preview"></a>Kubernetes クラスターを強化するための新しい推奨事項 (プレビュー段階)

次の推奨事項を使用すると、Kubernetes クラスターをさらに強化できます。

- **Kubernetes クラスターで既定の名前空間を使用しない** - ConfigMap、Pod、Secret、Service、ServiceAccount という種類のリソースを無許可アクセスから保護するために、Kubernetes クラスターで既定の名前空間を使用しないようにします。
- **Kubernetes クラスターでは、API 資格情報の自動マウントを無効にする必要がある** - 侵害されたおそれがある Pod リソースにより、Kubernetes クラスターに対して API コマンドが実行されるのを防ぐには、API 資格情報の自動マウントを無効にします。
- **Kubernetes クラスターでは、CAPSYSADMIN セキュリティ機能を許可しない**

お使いのコンテナー化された環境を Security Center で保護する方法については、「[Security Center のコンテナーのセキュリティ](container-security.md)」を参照してください。

### <a name="assessments-api-expanded-with-two-new-fields"></a>Assessments API を 2 つの新しいフィールドで拡張

[Assessments REST API](/rest/api/securitycenter/assessments) に次の 2 つのフィールドを追加しました。

- **FirstEvaluationDate** - 推奨事項が作成されて最初に評価された日時。 ISO 8601 形式の UTC 時刻として返されます。
- **StatusChangeDate** –推奨事項の状態が最後に変更された日時。 ISO 8601 形式の UTC 時刻として返されます。

これらのフィールドに使用される初期の既定値は、すべての推奨事項で `2021-03-14T00:00:00+0000000Z` です。

この情報には、次の表に示したいずれかの方法でアクセスできます。

| ツール                 | 詳細                                                                                                                                                                |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| REST API の呼び出し        | GET https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/providers/Microsoft.Security/assessments?api-version=2019-01-01-preview& $expand=statusEvaluationDates |
| Azure Resource Graph | `securityresources`<br>`where type == "microsoft.security/assessments"`                                                                                                |
| 連続エクスポート    | Log Analytics ワークスペース データで、2 つの専用フィールドが利用できるようになります                                                                                            |
| [CSV エクスポート](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations) | CSV ファイルには、2 つのフィールドが含まれています                                                        |
|                      |                                                                                                                                                                        |


[Assessments REST API](/rest/api/securitycenter/assessments) の詳細をご覧ください。


### <a name="asset-inventory-gets-a-cloud-environment-filter"></a>資産インベントリでクラウド環境フィルターを取得

Security Center の資産インベントリのページには、表示されるリソースの一覧をすばやく絞り込むために多数のフィルターが用意されています。 詳細については、「[資産インベントリを使用してリソースの調査と管理を行う](asset-inventory.md)」を参照してください。

新しいフィルターには、Security Center のマルチクラウド機能と接続しているクラウド アカウントに応じて、一覧を絞り込むためのオプションが用意されています。

:::image type="content" source="media/asset-inventory/filter-environment.png" alt-text="インベントリの環境フィルター":::

マルチクラウド機能の詳細については、次のページを参照してください。

- [Azure Security Center への AWS アカウントの接続](quickstart-onboard-aws.md)
- [Azure Security Center への GCP アカウントの接続](quickstart-onboard-gcp.md)


## <a name="april-2021"></a>2021 年 4 月

4 月の更新プログラムには次のものが含まれます。
- [更新された [リソース正常性] ページ (プレビュー)](#refreshed-resource-health-page-in-preview)
- [最近プルされたコンテナー レジストリ イメージを毎週再スキャン (一般提供 (GA) リリース)](#container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga)
- [Azure Defender for Kubernetes を使用したハイブリッドおよびマルチクラウド Kubernetes デプロイの保護 (プレビュー)](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)
- [Azure Defender と Microsoft Defender for Endpoint の統合で、Windows Server 2019 および Windows 10 Virtual Desktop (WVD) をサポート (一般提供 (GA) リリース)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga)
- [Azure Defender for DNS および Azure Defender for Resource Manager を有効にする際の推奨事項 (プレビュー)](#recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview)
- [追加された 3 つの規制コンプライアンス標準: Azure CIS 1.3.0、CMMC レベル 3、New Zealand ISM Restricted](#three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted)
- [ゲスト構成に関連する 4 つの新しい推奨事項 (プレビュー)](#four-new-recommendations-related-to-guest-configuration-in-preview)
- [CMK の推奨事項をベスト プラクティスのセキュリティ コントロールに移動](#cmk-recommendations-moved-to-best-practices-security-control)
- [11 個の Azure Defender アラートを非推奨化](#11-azure-defender-alerts-deprecated)
- ["システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)
- [[Azure Defender for SQL on machine] タイルを Azure Defender ダッシュボードから削除](#azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard)
- [21 個の推奨事項をセキュリティ コントロール間で移動](#21-recommendations-moved-between-security-controls)

### <a name="refreshed-resource-health-page-in-preview"></a>更新された [リソース正常性] ページ (プレビュー)

Security Center のリソース正常性が拡張、強化、改善され、1 つのリソースの全体的な正常性のスナップショット ビューが提供されるようになりました。 

リソースに関する詳細情報と、そのリソースに適用されるすべての推奨事項を確認できます。 また、[Azure Defender](azure-defender.md) を使用している場合は、その特定のリソースに関する未処理のセキュリティ アラートも表示されます。

リソースの [リソース正常性] ページを開くには、[資産インベントリ ページ](asset-inventory.md)でリソースを選択します。

Security Center のポータル ページにあるこのプレビュー ページには、次の情報が表示されます。

1. **リソース情報** - リソースが属するリソース グループとサブスクリプション、地理的な場所など。
1. **適用されているセキュリティ機能** - そのリソースに対して Azure Defender が有効になっているかどうか。
1. **未処理の推奨事項とアラートの数** - 未処理のセキュリティに関する推奨事項と Azure Defender アラートの数。
1. **実行可能な推奨事項とアラート** - リソースに適用される推奨事項とアラートが 2 つのタブに一覧表示されます。

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="仮想マシンの正常性情報を示す Azure Security Center の [リソース正常性] ページ":::

詳細については、「[チュートリアル: リソースの正常性を調査する](investigate-resource-health.md)」をご覧ください。


### <a name="container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga"></a>最近プルされたコンテナー レジストリ イメージを毎週再スキャン (一般提供 (GA) リリース)

コンテナー レジストリ用 Azure Defender には、組み込みの脆弱性スキャナーが含まれています。 このスキャナーは、レジストリにプッシュされたイメージと、過去 30 日以内にプルされたすべてのイメージを即座にスキャンします。

新しい脆弱性は毎日検出されます。 この更新により、過去 30 日間にレジストリからプルされたコンテナー イメージが毎週 **再スキャン** されます。 これにより、新しく検出された脆弱性をイメージ内で確実に識別できます。

スキャンはイメージごとに課金されるため、これらの再スキャンに対する追加料金は発生しません。

このスキャナーの詳細については、「[コンテナー レジストリ用の Azure Defender を使用してイメージの脆弱性をスキャンする](defender-for-container-registries-usage.md)」を参照してください。


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview"></a>Azure Defender for Kubernetes を使用したハイブリッドおよびマルチクラウド Kubernetes デプロイの保護 (プレビュー)

Azure Defender for Kubernetes は、クラスターがどこにデプロイされていても防御できるように、脅威保護機能が拡張されています。 これは、[Azure Arc 対応 Kubernetes](../azure-arc/kubernetes/overview.md) とその新しい[拡張機能](../azure-arc/kubernetes/extensions.md)との統合によって実現されています。 

非 Azure Kubernetes クラスターで Azure Arc を有効にすると、Azure Security Center の新しい推奨事項として、わずか数回のクリックで Azure Defender 拡張機能をデプロイすることが提案されます。

推奨事項 (**Azure Arc 対応 Kubernetes クラスターには Azure Defender の拡張機能がインストールされている必要がある**) と拡張機能を使用して、(マネージド Kubernetes サービス上ではなく) 他のクラウド プロバイダーにデプロイされた Kubernetes クラスターを保護します。

Azure Security Center、Azure Defender、および Azure Arc 対応 Kubernetes の間のこの統合により、次のことが実現されます。

- 保護されていない Azure Arc 対応 Kubernetes クラスターに対して Azure Defender 拡張機能を簡単にプロビジョニングする (手動および大規模)
- Azure Arc ポータルから Azure Defender 拡張機能とそのプロビジョニングの状態を監視する
- Security Center からのセキュリティに関する推奨事項が、Azure Arc ポータルの新しい [セキュリティ] ページでレポートされる
- Azure Defender で特定されたセキュリティ上の脅威が、Azure Arc ポータルの新しい [セキュリティ] ページでレポートされる
- Azure Arc 対応 Kubernetes クラスターが、Azure Security Center プラットフォームおよびエクスペリエンスに統合される

詳細については、[オンプレミスおよびマルチクラウドの Kubernetes クラスターでの Azure Defender for Kubernetes の使用](defender-for-kubernetes-azure-arc.md)に関する記事をご覧ください。

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Arc 対応 Kubernetes クラスター用の Azure Defender 拡張機能をデプロイするための Azure Security Center の推奨事項。" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga"></a>Azure Defender と Microsoft Defender for Endpoint の統合で、Windows Server 2019 および Windows 10 Virtual Desktop (WVD) をサポート (一般提供 (GA) リリース)

Microsoft Defender for Endpoint は、クラウドで提供される包括的なエンドポイント セキュリティ ソリューションです。 リスクベースによる脆弱性の管理と評価、およびエンドポイントでの検出と対応 (EDR) を提供します。 Defender for Endpoint を Azure Security Center と併用するメリットの全容については、「[Security Center に統合された EDR ソリューション Microsoft Defender for Endpoint でエンドポイントを保護する](security-center-wdatp.md)」を参照してください。

Windows サーバー上のサーバーに Azure Defender を有効にした場合、プランに Defender for Endpoint のライセンスが含まれています。 サーバーに対して既に Azure Defender を有効にしており、サブスクリプションに Windows 2019 サーバーが存在する場合には、今回の更新により自動的に Defender for Endpoint が追加されます。 特に何か操作をする必要はありません。 

このたびサポートが拡張され、Windows Server 2019 と [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md) が含まれるようになりました。

> [!NOTE]
> Windows Server 2019 のマシンで Defender for Endpoint を有効にする場合には、マシンが「[Microsoft Defender for Endpoint 統合を有効にする](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)」に記載されている前提条件を満たしていることを確認してください。


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview"></a>Azure Defender for DNS および Azure Defender for Resource Manager を有効にする際の推奨事項 (プレビュー)

[Azure defender For Resource Manager](defender-for-resource-manager-introduction.md) と [Azure Defender for DNS](defender-for-dns-introduction.md) を有効にするプロセスを簡略化するために、2 つの新しい推奨事項が追加されました。

- **Azure Defender for Resource Manager を有効にする必要がある** - Defender for Resource Manager は組織内のリソース管理操作を自動的に監視します。 Azure Defender では、脅威を検出し、疑わしいアクティビティについて警告します。
- **Azure Defender for DNS を有効にする必要がある** - Defender for DNS では、Azure リソースからのすべての DNS クエリを継続的に監視することにより、クラウド リソースに対して追加の保護層を提供します。 Azure Defender では、DNS 層での不審なアクティビティについて警告します。

Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページ https://aka.ms/pricing-security-center を参照してください。

> [!TIP]
> プレビューの推奨事項によってリソースが異常な状態になることはありません。これらの推奨事項は、セキュリティ スコアの計算には含まれません。 これらの推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復してください。 これらの推奨事項に対応する方法については、「[Azure Security Center の修復レコメンデーション](security-center-remediate-recommendations.md)」を参照してください。


### <a name="three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted"></a>追加された 3 つの規制コンプライアンス標準: Azure CIS 1.3.0、CMMC レベル 3、New Zealand ISM Restricted

Azure Security Center で使用するための 3 つの標準が追加されました。 規制コンプライアンス ダッシュボードを使用して、次の標準への準拠を追跡できるようになりました。

- [CIS Microsoft Azure Foundations Benchmark 1.3.0](../governance/policy/samples/cis-azure-1-3-0.md)
- [CMMC レベル 3](../governance/policy/samples/cmmc-l3.md)
- [New Zealand ISM Restricted](../governance/policy/samples/new-zealand-ism.md)

「[規制コンプライアンス ダッシュボードで標準セットをカスタイマイズする](update-regulatory-compliance-packages.md)」で説明するように、これらをサブスクリプションに割り当てることができます。

:::image type="content" source="media/release-notes/additional-regulatory-compliance-standards.png" alt-text="Azure Security Center の規制コンプライアンス ダッシュボードで使用するために追加された 3 つの標準。" lightbox="media/release-notes/additional-regulatory-compliance-standards.png":::

詳細情報:
- [規制コンプライアンス ダッシュボードでの標準セットのカスタイマイズ](update-regulatory-compliance-packages.md)
- [チュートリアル:規制に対するコンプライアンスの向上](security-center-compliance-dashboard.md)
- [FAQ - 規制コンプライアンス ダッシュボード](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

### <a name="four-new-recommendations-related-to-guest-configuration-in-preview"></a>ゲスト構成に関連する 4 つの新しい推奨事項 (プレビュー)

Azure の[ゲスト構成拡張機能](../governance/policy/concepts/guest-configuration.md)により、仮想マシンのゲスト内設定の確実な強化を支援するように Security Center に対してレポートが行われます。 この拡張機能は Arc 接続マシン エージェントに含まれているため、Arc 対応サーバーには必要ありません。 この拡張機能を使用するには、マシンにシステムマネージド ID が必要です。

この拡張機能を最大限に活用するために、Security Center に 4 つの新しい推奨事項が追加されました。

- 2 つの推奨事項では、拡張機能と、その必須のシステムマネージド ID をインストールすることを求めています。
    - **ゲスト構成拡張機能をマシンにインストールする必要がある**
    - **仮想マシンのゲスト構成拡張機能はシステム割り当てマネージド ID を使用してデプロイする必要がある**

- 拡張機能がインストールされて実行されると、マシンの監査が開始され、オペレーティング システムの構成や環境設定などの設定を強化するように求められます。 これらの 2 つの推奨事項に従って、Windows および Linux マシンを強化するように求められます。
    - **マシンで Windows Defender Exploit Guard を有効にする必要がある**
    - **Linux マシンに対する認証では SSH キーを要求する必要がある**

詳細については、「[Azure Policy のゲストの構成の理解](../governance/policy/concepts/guest-configuration.md)」を参照してください。

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>CMK の推奨事項をベスト プラクティスのセキュリティ コントロールに移動

すべての組織のセキュリティ プログラムには、データ暗号化要件が含まれています。 既定では、Azure のお客様のデータは、サービス マネージド キーを使用して保存時に暗号化されます。 ただし、規制コンプライアンス基準を満たすためには、一般にカスタマー マネージド キー (CMK) が必要です。 CMK を使用すると、自分が作成して所有する [Azure Key Vault](../key-vault/general/overview.md) キーを使用してデータを暗号化できます。 これにより、ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。

Azure Security Center のセキュリティ コントロールは、関連するセキュリティ推奨事項の論理グループであり、脆弱な攻撃対象領域を反映しています。 コントロールごとに、すべてのリソースについて、コントロールに示されているすべての推奨事項を修復した場合にセキュリティ スコアに追加できる最大ポイント数があります。 **[セキュリティのベスト プラクティスの実装]** セキュリティ コントロールは、0 ポイントです。 したがって、このコントロールの推奨事項は、セキュリティ スコアに影響しません。

次に示す推奨事項は、オプションとしての性質をより適切に反映するために、 **[セキュリティのベスト プラクティスの実装]** セキュリティ コントロールに移動されています。 この移動によって、これらの推奨事項は、その目標を達成するために最も適したコントロールに含められます。

- Azure Cosmos DB アカウントでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある
- Azure Machine Learning ワークスペースは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある
- Cognitive Services アカウントでカスタマー マネージド キー (CMK) によるデータ暗号化を有効にする必要がある
- コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある
- SQL マネージド インスタンスでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある
- SQL サーバーでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある
- ストレージ アカウントでは暗号化にカスタマー マネージド キー (CMK) を使用する必要がある

各セキュリティ コントロールに含まれる推奨事項については、「[セキュリティ コントロールとその推奨事項](secure-score-security-controls.md#security-controls-and-their-recommendations)」を参照してください。


### <a name="11-azure-defender-alerts-deprecated"></a>11 個の Azure Defender アラートを非推奨化

次に示す 11 個の Azure Defender アラートは非推奨となりました。

- 次の 2 つのアラートは、新しいアラートで置き換えられ、より適切なカバレッジが提供されます。

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | PREVIEW - MicroBurst toolkit "Get-AzureDomainInfo" function run detected (プレビュー - MicroBurst ツールキット "Get-AzureDomainInfo" 関数の実行が検出されました) |
    | ARM_MicroBurstRunbook    | PREVIEW - MicroBurst toolkit "Get-AzurePasswords" function run detected (プレビュー - MicroBurst ツールキット "Get-AzurePasswords" 関数の実行が検出されました)  |
    |                          |                                                                          |

- 次の 9 つのアラートは、既に非推奨になっている Azure Active Directory Identity Protection コネクタ (IPC) に関連したものです。

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | 通常とは異なるサインイン プロパティ |
    | AnonymousLogin      | 匿名 IP アドレス          |
    | InfectedDeviceLogin | マルウェアにリンクした IP アドレス     |
    | ImpossibleTravel    | 特殊な移動               |
    | MaliciousIP         | 悪意のある IP アドレス          |
    | LeakedCredentials   | 漏洩した資格情報            |
    | PasswordSpray       | パスワード スプレー                |
    | LeakedCredentials   | Azure AD 脅威インテリジェンス  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 
    > [!TIP]
    > これらの 9 つの IPC アラートは、Security Center アラートではありませんでした。 これらは Azure Active Directory (AAD) Identity Protection コネクタ (IPC) の一部であり、IPC によって Security Center に送信されていました。 過去 2 年間に、これらのアラートが表示されたお客様は、2019 年以前に (コネクタから ASC への) エクスポートを構成した組織だけです。 AAD IPC は引き続き独自のアラート システムに表示されており、今後も Azure Sentinel で利用できます。 唯一の変更点は、Security Center に表示されなくなったことです。

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>"システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化 

次の 2 つの推奨事項は非推奨とされ、この変更によってセキュリティ スコアにわずかな影響が生じる可能性があります。

- **システムの更新プログラムを適用するには、マシンを再起動する必要があります**
- **お使いのマシンに監視エージェントをインストールする必要があります**。 この推奨事項は、オンプレミスのマシンにのみ関係します。また、そのロジックのいくつかは、別の推奨事項 (**お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある**) に移される予定です

連続エクスポートとワークフロー自動化の構成をチェックして、該当する推奨事項が含まれているかどうかを確認することをお勧めします。 また、ダッシュボードまたはそれらを使用する他の監視ツールを適宜更新する必要があります。

これらの推奨事項の詳細については、[セキュリティに関する推奨事項のリファレンス ページ](recommendations-reference.md)を参照してください。

### <a name="azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard"></a>[Azure Defender for SQL on machine] タイルを Azure Defender ダッシュボードから削除

Azure Defender ダッシュボードの適用範囲には、お使いの環境用の関連 Azure Defender プランのタイルが含まれています。 保護されているリソースと保護されていないリソースの数のレポートに関する問題が発生したため、問題が解決されるまでは、**Azure Defender for SQL on machine** のリソース適用範囲の状態を一時的に削除することにしました。


### <a name="21-recommendations-moved-between-security-controls"></a>21 個の推奨事項をセキュリティ コントロール間で移動 

次の推奨事項は、別のセキュリティ コントロールに移動されました。 セキュリティ コントロールは、関連するセキュリティ推奨事項の論理グループであり、脆弱な攻撃対象領域を反映しています。 この移動によって、これらの各推奨事項は、その目標を達成するために最も適したコントロールに含められます。

各セキュリティ コントロールに含まれる推奨事項については、「[セキュリティ コントロールとその推奨事項](secure-score-security-controls.md#security-controls-and-their-recommendations)」を参照してください。

|推奨 |変更と影響  |
|---------|---------|
|脆弱性評価を SQL サーバー上で有効にする必要がある<br>脆弱性評価を SQL マネージド インスタンス上で有効にする必要がある<br>SQL データベースの脆弱性を新たに修復する必要がある<br>VM 内の SQL データベースの脆弱性を修復する必要があります     |脆弱性の修復 (6 ポイント分) から<br>セキュリティ構成の修復 (4 ポイント分) に移動されます。<br>お客様の環境によっては、これらの推奨事項がスコアに与える影響が少なくなります。|
|複数の所有者がサブスクリプションに割り当てられている必要がある<br>Automation アカウント変数は、暗号化する必要がある<br> IoT デバイス - Auditd プロセスでイベントの送信が停止された<br> IoT デバイス - オペレーティング システムのベースラインの検証に失敗した<br> IoT デバイス - TLS 暗号スイートのアップグレードが必要<br> IoT デバイス - デバイス上でポートを開く<br> IoT デバイス - チェーンのうちの 1 つに制限の緩すぎるファイアウォール ポリシーが見つかりました<br> IoT デバイス - 入力チェーンに制限の緩すぎるファイアウォール ルールが見つかりました<br> IoT デバイス - 出力チェーンに制限の緩すぎるファイアウォール ルールが見つかりました<br>IoT Hub の診断ログを有効にする必要がある<br> IoT デバイス - エージェントで低使用率のメッセージが送信されている<br>IoT デバイス - 既定の IP フィルター ポリシーを拒否にする必要がある<br>IoT デバイス - IP フィルター ルールの IP 範囲が広い<br>IoT デバイス - エージェントのメッセージ間隔とサイズを調整する必要がある<br>IoT デバイス - 認証の資格情報が同一<br>IoT デバイス - 監査対象プロセスでイベントの送信が停止された<br>IoT デバイス - オペレーティング システム (OS) のベースライン構成を修正する必要がある|**セキュリティのベスト プラクティスの実装** に移動されます。<br>推奨事項が「セキュリティのベスト プラクティスの実装」セキュリティ コントロールに移動される (ポイントは発生しない) と、この推奨事項はセキュリティ スコアに影響を及ぼさなくなります。|
|||


## <a name="march-2021"></a>2021 年 3 月

3 月の更新プログラムには次のものが含まれます。

- [Azure Firewall の管理を Security Center に統合](#azure-firewall-management-integrated-into-security-center)
- [SQL 脆弱性評価に "ルールの無効化" エクスペリエンスを追加 (プレビュー)](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Azure Monitor ブックを Security Center に統合し、テンプレートを 3 件提供](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [規制コンプライアンス ダッシュボードに Azure 監査レポートを追加 (プレビュー)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [[Explore in ARG]\(ARG で探索\) を使用した Azure Resource Graph での推奨データの表示](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [ワークフローの自動化をデプロイするためのポリシーを更新](#updates-to-the-policies-for-deploying-workflow-automation)
- [従来の 2 つの推奨事項で Azure アクティビティ ログにデータが直接書き込まれなくなる](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [推奨事項ページの拡充](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Azure Firewall の管理を Security Center に統合

Azure Security Center を開いたときに、最初に表示されるページが概要ページです。 

この対話式のダッシュボードでは、ハイブリッド クラウド ワークロードのセキュリティ対策について一元的なビューが提供されます。 また、セキュリティ アラート、カバレッジ情報なども表示されます。

このたび、セキュリティの状態を一元的に確認できるようにするための取り組みの一環として、このダッシュボードに Azure Firewall Manager を統合しました。 全ネットワークをまたいでファイアウォールのカバレッジ状態を確認できるほか、Security Center から Azure Firewall のポリシーを一元管理できるようになりました。

このダッシュボードの詳細については [Azure Security Center の概要ページ](overview-page.md)を参照してください。

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Security Center の概要ダッシュボードに Azure Firewall のタイルが表示されているところ":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>SQL 脆弱性評価に "ルールの無効化" エクスペリエンスを追加 (プレビュー)

Security Center には、データベースの脆弱性となりうる問題を検出、追跡、および修復するうえで役立つ脆弱性スキャナーが組み込まれています。 評価スキャンの結果では、SQL マシンのセキュリティ状態の概要と、セキュリティに関する検出結果の詳細が示されます。

組織のニーズとして、検出結果を修復するのではなく無視する必要がある場合は、必要に応じて検出結果を無効にできます。 無効化された検出結果は、セキュリティ スコアに影響を与えたり、不要なノイズを生成したりすることはありません。

詳細については、「[特定の検出結果を無効にする](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview)」を参照してください。



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Azure Monitor ブックを Security Center に統合し、テンプレートを 3 件提供

Ignite Spring 2021 において、Security Center に Azure Monitor ブックを統合したエクスペリエンスを発表しました。

この新しい統合を使用すると、Security Center のギャラリーから面倒な設定のいらないテンプレートを使えるようになります。 ブック テンプレートを使用すると、組織のセキュリティ体制を追跡するための動的なビジュアル レポートを閲覧したり、作成したりできます。 また、Security Center のデータをはじめとするサポートされているデータ型に基づいて新しいブックを作成したり、Security Center の GitHub コミュニティから入手したコミュニティ ワークブックをすばやくデプロイしたりすることもできます。

用意されているテンプレート レポートは次の 3 つです。

- **セキュリティ スコアの推移** - サブスクリプションのスコアと、リソースに対する推奨事項の変化を追跡できます
- **システムの更新プログラム** - リソース、OS、重要度などに応じて、システムに適用できていない更新プログラムを表示します
- **脆弱性評価の検出結果** - Azure リソースを対象とした脆弱性スキャンの検出結果を表示します

ここに挙げたレポートを使用する方法や、独自のレポートを作成する方法の詳細については、「[豊富な機能を備えた対話型の Security Center データ レポートを作成する](custom-dashboards-azure-workbooks.md)」を参照してください。

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="経過時間に応じたセキュア スコア レポート。":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>規制コンプライアンス ダッシュボードに Azure 監査レポートを追加 (プレビュー)

規制コンプライアンス ダッシュボードのツール バーから、Azure と Dynamics の証明書レポートをダウンロードできるようになりました。 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="規制コンプライアンス ダッシュボードのツール バー":::

関連するレポートの種類 (PCI、SOC、ISO など) のタブを選択し、フィルターを使用すると、必要なレポートを見つけることができます。

詳細は、「[規制コンプライアンス ダッシュボードでの標準の管理](update-regulatory-compliance-packages.md)」を参照してください。

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="利用可能な Azure 監査レポートの一覧にフィルターを適用する。":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>[Explore in ARG]\(ARG で探索\) を使用した Azure Resource Graph での推奨データの表示

推奨事項の詳細ページに、[Explore in ARG]\(ARG で探索\) ツールバー ボタンが含まれるようになりました。 このボタンを使用して、Azure Resource Graph のクエリを開き、推奨データを探索、エクスポート、および共有します。

Azure Resource Graph (ARG) を使用すると、堅牢なフィルター処理、グループ化、および並べ替え機能を使用して、クラウド環境全体のリソース情報にすばやくアクセスできます。 これは、Azure サブスクリプション全体の情報を、プログラムから、または Azure portal 内ですばやく効率的に照会する方法です。

[Azure Resource Graph (ARG)](../governance/resource-graph/index.yml) の詳細について参照してください。

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Azure Resource Graph で推奨データを探索する。":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>ワークフローの自動化をデプロイするためのポリシーを更新

組織の監視とインシデント対応プロセスを自動化すると、セキュリティ インシデントの調査と軽減にかかる時間を大幅に短縮できます。

Microsoft では、ワークフローの自動化のプロシージャを作成および構成し、組織全体にデプロイするための Azure Policy "DeployIfNotExist" ポリシーを 3 つ提供しています。

|目標  |ポリシー  |ポリシー ID  |
|---------|---------|---------|
|セキュリティ アラートのワークフローの自動化|[Azure Security Center アラートに対してワークフローの自動化をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|セキュリティに関する推奨事項のワークフローの自動化|[Azure Security Center 推奨事項に対してワークフローの自動化をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|規制コンプライアンスの変化に関するワークフローの自動化|[Azure Security Center の法令遵守のためにワークフローの自動化をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

ここに挙げたポリシーの機能について、2 点更新を実施しました。

- 割り当てると適用され、そのまま有効な状態が維持されます。
- 既にデプロイされている場合でも、ポリシーをカスタマイズしたり、パラメーターを更新したりできるようになりました。 たとえば、別の評価キーを追加したり、既存の評価キーを編集したりする必要が生じた場合には、そのような操作を行うことができます。

[ワークフローの自動化テンプレート](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)を使用して開始するには、こちらを参照してください。

方法の詳細については、「[Security Center のトリガーへの応答を自動化する](workflow-automation.md)」を参照してください。


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>従来の 2 つの推奨事項で Azure アクティビティ ログにデータが直接書き込まれなくなる 

Security Center によって、ほぼすべてのセキュリティに関する推奨事項のデータが Azure Advisor に渡され、そこから [Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md)に書き込まれます。

2 つの推奨事項については、データは同時に Azure アクティビティ ログに直接書き込まれます。 この変更により、Security Center によって、これらの従来のセキュリティに関する推奨事項のデータがアクティビティ ログに直接書き込まれなくなります。 代わりに、他のすべての推奨事項と同様に、データは Azure Advisor にエクスポートされます。

この 2 つの従来の推奨事項は次のとおりです。
-  - お使いのマシンで Endpoint Protection の正常性の問題を解決する必要があります
- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある

アクティビティ ログの "TaskDiscovery タイプの推奨事項" カテゴリでこの 2 つの推奨事項の情報にアクセスしていた場合、これは使用できなくなります。


### <a name="recommendations-page-enhancements"></a>推奨事項ページの拡充 

推奨事項一覧の改良版がリリースされ、一目で詳細情報を確認できるようになりました。

これにより、ページには以下が表示されます。

1. 各セキュリティ コントロールの最大スコアと現在のスコア。
1. **修正** や **プレビュー** などのタグに代わるアイコン。
1. 各推奨事項に関連する[ポリシー イニシアチブ](security-policy-concept.md)を示す新しい列。これは、"コントロールでグループ化" が無効になっている場合に表示されます。

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Azure Security Center の推奨事項ページの機能強化 - 2021 年 3 月" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Azure Security Center の推奨事項の &quot;フラット&quot; リストの機能強化 - 2021 年 3 月" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

詳細については、[Azure Security Center でのセキュリティに関する推奨事項](security-center-recommendations.md)を参照してください。