---
title: Azure Sentinel の新着情報
description: この記事では、過去数か月間に追加された Azure Sentinel の新機能について説明します。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 05/13/2021
ms.openlocfilehash: 210be7199f5f28773c2dbbf913e4914918e7eeeb
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110087924"
---
# <a name="whats-new-in-azure-sentinel"></a>Azure Sentinel の新着情報

この記事では、Azure Sentinel 用に最近追加された機能と、Azure Sentinel のユーザー エクスペリエンスを向上させる関連サービスの新機能について説明します。

6 か月以上前の項目をお探しの場合は、「[Azure Sentinel の最新情報のアーカイブ](whats-new-archive.md)」を参照してください。 以前に提供された機能については、[Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)を参照してください。

> [!IMPORTANT]
> 記載されている機能は、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
> 

> [!TIP]
> Microsoft の脅威ハンティング チームは、クエリ、プレイブック、ブック、およびノートブックを [Azure Sentinel コミュニティ](https://github.com/Azure/Azure-Sentinel) に投稿します。これには、ユーザーのチームが適応して使用できる特定の[ハンティング クエリ](https://github.com/Azure/Azure-Sentinel)などが含まれます。
>
> ユーザーも投稿することができます。 [Azure Sentinel Threat Hunters GitHub コミュニティ](https://github.com/Azure/Azure-Sentinel/wiki)にぜひご参加ください。
>

## <a name="may-2021"></a>2021 年 5 月

- [Azure Sentinel のソリューション (パブリック プレビュー)](#azure-sentinel-solutions-public-preview)
- [Continuous Threat Monitoring for SAP ソリューション (パブリック プレビュー)](#continuous-threat-monitoring-for-sap-solution-public-preview)
- [脅威インテリジェンスの統合 (パブリック プレビュー)](#threat-intelligence-integrations-public-preview)
- [スケジュールされたアラートに対する Fusion (パブリック プレビュー)](#fusion-over-scheduled-alerts-public-preview)
- [SOC-ML 異常検知 (パブリック プレビュー)](#soc-ml-anomalies-public-preview)
- [IP エンティティ ページ (パブリック プレビュー)](#ip-entity-page-public-preview)
- [アクティビティのカスタマイズ (パブリック プレビュー)](#activity-customization-public-preview)
- [[Hunting]\(ハンティング\) ダッシュボード (パブリック プレビュー)](#hunting-dashboard-public-preview)
- [インシデント チーム - Microsoft Teams での共同作業 (パブリック プレビュー)](#azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview)
- [ゼロ トラスト (TIC3.0) ブック](#zero-trust-tic30-workbook)

### <a name="azure-sentinel-solutions-public-preview"></a>Azure Sentinel のソリューション (パブリック プレビュー)

データ コネクタ、ブック、分析ルール、プレイブック、ハンティング クエリ、パーサー、ウォッチリストなど、Azure Sentinel の各種コンポーネントを組み合わせた **パッケージ コンテンツ** [ソリューション](sentinel-solutions-catalog.md)が新たに提供されます。

製品内での見つけやすさや、シングル ステップ デプロイ、エンド ツー エンドの製品シナリオが改良されたソリューションとなっています。 詳細については、「[Azure Sentinel ソリューションの検出とデプロイ](sentinel-solutions-deploy.md)」を参照してください。

### <a name="continuous-threat-monitoring-for-sap-solution-public-preview"></a>Continuous Threat Monitoring for SAP ソリューション (パブリック プレビュー)

Azure Sentinel ソリューションに **Continuous Threat Monitoring for SAP** が加わりました。SAP システムを監視して、ビジネス レイヤーおよびアプリケーション レイヤーにおける高度な脅威に対応することができます。

SAP データ コネクタは、SAP システム ランドスケープ全体から 14 もの多くのアプリケーション ログをストリーム配信し、NetWeaver RFC 呼び出しを介して Advanced Business Application Programming (ABAP) から、また OSSAP 制御インターフェイスを介してファイル ストレージ データからログを収集します。 SAP データ コネクタによって、基になる SAP インフラストラクチャを監視する機能が Azure Sentinels に追加されます。

SAP ログを Azure Sentinel に取り込むには、お使いの SAP 環境に Azure Sentinel SAP データ コネクタがインストールされている必要があります。 SAP データ コネクタをデプロイしたら、SAP ソリューションの多彩なセキュリティ コンテンツをデプロイし、組織の SAP 環境に関する分析情報をスムーズに取得して、関連するセキュリティ運用機能を向上させることができます。

詳細については、[SAP 用 Azure Sentinel ソリューション (パブリック プレビュー) のデプロイに関するチュートリアル](sap-deploy-solution.md)を参照してください。

### <a name="threat-intelligence-integrations-public-preview"></a>脅威インテリジェンスの統合 (パブリック プレビュー)

既知の脅威を検出して優先順位を付けるセキュリティ アナリストの能力を高めるために、Azure Sentinel には、[脅威インテリジェンスのフィードを使用](import-threat-intelligence.md)する方法がいくつか用意されています。

新たに提供されている数多くの統合脅威インテリジェンス プラットフォーム (TIP、Threat Intelligence Platform) 製品の 1 つを使用し、TAXII サーバーに接続して STIX に適合したあらゆる脅威インテリジェンス ソースを活用できるほか、[Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator) と直接やり取りするあらゆるカスタム ソリューションを利用できるようになりました。

効果的な調査や対応の措置につながる TI 情報でインシデントをエンリッチメント処理するために、プレイブックから脅威インテリジェンス ソースに接続することもできます。

詳細については、「[Azure Sentinel での脅威インテリジェンスの統合](threat-intelligence-integration.md)」を参照してください。

### <a name="fusion-over-scheduled-alerts-public-preview"></a>スケジュールされたアラートに対する Fusion (パブリック プレビュー)

機械学習の相関分析エンジンである **Fusion** でマルチステージ攻撃を検出できるようになりました。他のデータ ソースからインポートされたアラートに加え、一連の[スケジュール化された分析ルール](tutorial-detect-threats-custom.md)から生成されるアラートが、その相関分析の中で使用されます。

詳細については、「[Azure Sentinel の高度なマルチステージ攻撃の検出](fusion.md)」を参照してください。

### <a name="soc-ml-anomalies-public-preview"></a>SOC-ML 異常検知 (パブリック プレビュー)

Azure Sentinel の SOC-ML 機械学習ベースの異常検知であれば、他の方法では検出の目をかいくぐられてしまうような異常な行動でも特定できます。

SOC-ML では、面倒な設定が要らない分析ルール テンプレートを使用することができます。 異常そのものが悪意のある行動や疑わしい動作を意味しているとは限りませんが、検出、調査、脅威ハンティングの正確さを高める目的で、それらを使用することはできます。

詳細については、「[Azure Sentinel で SOC-ML 異常検知を使用して脅威を検出する](soc-ml-anomalies.md)」を参照してください。

### <a name="ip-entity-page-public-preview"></a>IP エンティティ ページ (パブリック プレビュー)

Azure Sentinel が新たに IP アドレス エンティティに対応しました。新しい IP エンティティ ページで IP エンティティ情報を確認することができます。

ユーザー エンティティ ページとホスト エンティティ ページと同様、IP ページは、IP に関する一般情報のほか、その IP が関係していると判明したアクティビティの一覧など、さまざまな情報を含んでおり、かつてないほど豊富な情報を提供することで、セキュリティ インシデントの調査を強力に支援します。

詳細については、「[エンティティ ページ](identify-threats-with-entity-behavior-analytics.md#entity-pages)」を参照してください。

### <a name="activity-customization-public-preview"></a>アクティビティのカスタマイズ (パブリック プレビュー)

エンティティ ページについては言えば、必要なエンティティのカスタム アクティビティを新たに作成できるようになっています。このアクティビティは、既製のアクティビティと共に、それぞれのエンティティ ページで追跡および表示されます。

詳細については、「[エンティティ ページのタイムラインのアクティビティをカスタマイズする](customize-entity-activities.md)」を参照してください。

### <a name="hunting-dashboard-public-preview"></a>[Hunting]\(ハンティング\) ダッシュボード (パブリック プレビュー)

**[Hunting]\(ハンティング\)** ブレードが更新されました。 新しいダッシュボードではシングルクリックで、すべてのクエリを実行したり特定のサブセットだけを実行したりすることができます。

結果の件数やスパイク、24 時間における件数の変化に着目することによってハンティングの出発点を特定できます。 お気に入りやデータ ソース、MITRE ATT&CK の戦術や手法、結果、結果の差分を基準に並べ替えやフィルタリングを行うこともできます。 まだ必要なデータ ソースが接続されていないクエリを確認すると共に、それらのクエリを有効にする方法についての推奨事項を入手できます。

詳細については、「[Azure Sentinel で脅威を検出する](hunting.md)」を参照してください。

### <a name="azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview"></a>Azure Sentinel インシデント チーム - Microsoft Teams でコラボレーションを行う (パブリック プレビュー)

Azure Sentinel が Microsoft Teams との直接統合に対応しました。外部の関係者との間で、また組織の垣根を越えてシームレスにコラボレーションを行うことができます。

意思の疎通や調整において中心的な役割を果たす新しい "*インシデント チーム*" を、Azure Sentinel のインシデントから直接作成できます。

インシデント チームの能力が特に発揮されるのは、重要度が高い進行中のインシデントに特化した会議ブリッジとしての役割を担うときです。 コミュニケーションとコラボレーションに Microsoft Teams を既に使用している組織は、Azure Sentinel の統合を使用して、会話や日常業務に直接セキュリティ データを取り入れることができます。

Microsoft Teams では、最も妥当なデータをチームに提供できるよう、新しいチームの **[Incident page]\(インシデント ページ\)** タブに絶えず Azure Sentinel からの最新のデータが表示されます。

[ ![Microsoft Teams の [インシデント] ページ。](media/collaborate-in-microsoft-teams/incident-in-teams.jpg) ](media/collaborate-in-microsoft-teams/incident-in-teams.jpg#lightbox)

詳細については、「[Microsoft Teams での共同作業 (パブリック プレビュー)](collaborate-in-microsoft-teams.md)」を参照してください。

### <a name="zero-trust-tic30-workbook"></a>ゼロ トラスト (TIC3.0) ブック

新しい Azure Sentinel ゼロ トラスト (TIC3.0) ブックでは、[トラステッド インターネット接続](https://www.cisa.gov/trusted-internet-connections) (TIC) フレームワークを横断的に説明した[ゼロ トラスト](/security/zero-trust/)の原則を自動的に視覚化できます。

私たちは、コンプライアンスは単なる 1 年限りの要件ではなく、組織はその構成要素を細かく監視する必要があることを認識しています。 Azure Sentinel のゼロ トラスト ブックでは、Azure、Office 365、Teams、Intune、Windows Virtual Desktop などにわたり、Microsoft セキュリティ オファリング全体が使用されています。

[ ![ゼロ トラスト ブック。](media/zero-trust-workbook.gif) ](media/zero-trust-workbook.gif#lightbox)

**ゼロ トラスト ブック**:

- 実装者、SecOps アナリスト、評価者、セキュリティおよびコンプライアンスの意思決定者、MSSP などは、クラウド ワークロードのセキュリティ状態に関する状況認識を得ることができます。
- ナビゲーション用に選択可能な GUI ボタンを備えた、TIC 3.0 セキュリティ機能と連携する 75 を超えるコントロール カードを使用できます。
- 自動化、人工知能、機械学習、クエリ/アラート生成、視覚化、カスタマイズされたレコメンデーション、および各ドキュメントの参照によって、スタッフ配置が強化されるように設計されています。

詳細については、「[チュートリアル: データの視覚化と監視](tutorial-monitor-your-data.md)」を参照してください。

## <a name="april-2021"></a>2021 年 4 月

- [Azure Policy ベースのデータ コネクタ](#azure-policy-based-data-connectors)
- [インシデントのタイムライン (パブリック プレビュー)](#incident-timeline-public-preview)

### <a name="azure-policy-based-data-connectors"></a>Azure Policy ベースのデータ コネクタ

Azure Policy を使用すると、Azure Sentinel に取り込むログを持つ特定の種類のすべての (現在および将来の) リソースに、診断ログの設定の共通セットを適用できます。

[Azure Policy](../governance/policy/overview.md) の機能をデータ コレクション構成のタスクに活かすための取り組みを続けながら、[Azure ストレージ アカウント](connect-azure-storage-account.md) リソース用の別の Azure Policy 拡張データ コレクターを提供し、パブリック プレビューにリリースしています。

また、[Azure Key Vault](connect-azure-key-vault.md) と [Azure Kubernetes Service](connect-azure-kubernetes-service.md) 用の 2 つのプレビュー コネクタが、現在一般提供 (GA) され、[Azure SQL Databases](connect-azure-sql-logs.md) コネクタに加わっています。

### <a name="incident-timeline-public-preview"></a>インシデントのタイムライン (パブリック プレビュー)

インシデントの詳細ページの最初のタブが **[タイムライン]** になりました。このタブには、インシデントのアラートとブックマークのタイムラインが表示されます。 インシデントのタイムラインを使用すると、インシデントについて理解を深め、関連するアラートやブックマーク全体で攻撃者のアクティビティのタイムラインを再構築できます。

- タイムライン内の項目を選択すると、インシデントのコンテキストを離れることなく、その詳細が表示されます。
- タイムラインのコンテンツをフィルター処理して、アラートやブックマークのみ、または特定の重要度や MITRE の戦術の項目を表示します。
- **[システム アラート ID]** リンクを選択してレコード全体を表示したり、 **[イベント]** リンクを選択して **[ログ]** 領域に関連イベントを表示したりできます。

次に例を示します。

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="[インシデント] の [タイムライン] タブ":::

詳細については、「[チュートリアル:Azure Sentinel でインシデントを調査する](tutorial-investigate-cases.md)」を参照してください。

## <a name="march-2021"></a>2021 年 3 月

- [表示モード中に自動的に更新するようにブックを設定する](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Azure Firewall の新しい検出](#new-detections-for-azure-firewall)
- [自動化ルールとインシデントによってトリガーされるプレイブック (パブリック プレビュー)](#automation-rules-and-incident-triggered-playbooks-public-preview) (すべての新しいプレイブックのドキュメントを含む)
- [新しいアラート エンリッチメント: 強化されたエンティティ マッピングとカスタム詳細 (パブリック プレビュー)](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details-public-preview)
- [Azure Sentinel ブックを印刷するか PDF として保存する](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [インシデント フィルターと並べ替えの設定がセッションに保存されるようになりました (パブリック プレビュー)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Microsoft 365 Defender インシデント統合 (パブリック プレビュー)](#microsoft-365-defender-incident-integration-public-preview)
- [Azure Policy を使用した新しい Microsoft サービス コネクタ](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>表示モード中に自動的に更新するようにブックを設定する

Azure Sentinel ユーザーは、新しい [Azure Monitor 機能](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555)を使用して、表示セッション中にブック データを自動的に更新できるようになりました。

各ブックまたはブック テンプレートで、:::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **[自動更新]** を選択して間隔のオプションを表示します。 現在の表示セッションに使用するオプションを選択し、 **[適用]** を選択します。

- サポートされる更新間隔の範囲は **5 分** から **1 日** です。
- 既定では、自動更新はオフになっています。 パフォーマンスを最適化するために、ブックを閉じるたびに自動更新もオフになり、バックグラウンドでは実行されません。 次にブックを開いたときに、必要に応じて自動更新をオンにしてください。
- ブックの編集中は自動更新が一時停止します。自動更新の間隔は、編集モードから表示モードに戻るたびに再起動されます。

    また、:::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: **[更新]** ボタンを選択してブックを手動で更新した場合も、間隔が再起動されます。

詳細については、「[チュートリアル: データの視覚化と監視](tutorial-monitor-your-data.md)」と [Azure Monitor のドキュメント](../azure-monitor/visualize/workbooks-overview.md)を参照してください。

### <a name="new-detections-for-azure-firewall"></a>Azure Firewall の新しい検出

Azure Sentinel の[分析](import-threat-intelligence.md#analytics-puts-your-threat-indicators-to-work-detecting-potential-threats)領域に、すぐに使用できる Azure Firewall のいくつかの検出が追加されました。 これらの新しい検出機能により、既知の IOC に関連するインターネット ドメイン名または IP アドレスに対して内部ネットワーク上のコンピューターがクエリを実行したり接続したりすると、検出ルール クエリで定義されているように、セキュリティ チームはアラートを受け取ることができます。

新しい検出には次のものが含まれます。

- [Solorigate ネットワーク ビーコン](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [既知の GALLIUM ドメインとハッシュ](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [Known IRIDIUM IP (既知の IRIDIUM IP)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [既知の Phosphorus グループ ドメイン/IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [DCU 削除に含まれる THALLIUM ドメイン](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [ZINC に関連する、既知の悪意あるドキュメントのハッシュ](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [既知の STRONTIUM グループ ドメイン](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [NOBELIUM - ドメインおよび IP IOC - 2021 年 3 月](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


Azure Firewalls に対する検出は、組み込みのテンプレート ギャラリーに継続的に追加されます。 Azure Firewall の最新の検出を取得するには、 **[ルール テンプレート]** で **[データ ソース]** を **Azure Firewall** でフィルター処理します。

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="分析の効率ブックにおける新しい検出":::

詳細については、「[Azure Sentinel での Azure Firewall の新しい検出](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958)」を参照してください。

### <a name="automation-rules-and-incident-triggered-playbooks-public-preview"></a>自動化ルールとインシデントによってトリガーされるプレイブック (パブリック プレビュー)

自動化ルールは Azure Sentinel の新しい概念です。これを使用すると、インシデント処理の自動化を一元的に管理できます。 自動化ルールを使用すると、(以前のようにアラートにだけでなく) インシデントにもプレイブックを割り当てられるほか、一度に複数の分析ルールに対する応答を自動化したり、プレイブックを必要とせずにインシデントのタグ付け、割り当て、クローズを自動的に行ったり、実行されるアクションの順序を制御したりできます。 自動化ルールにより、Azure Sentinel での自動化の使用が効率化され、インシデント オーケストレーション プロセスの複雑なワークフローを簡略化できます。

詳細については、[自動化ルールの詳細な説明](automate-incident-handling-with-automation-rules.md)のページを参照してください。

前述のように、アラート トリガーに加えて、インシデント トリガーでもプレイブックをアクティブにできるようになりました。 インシデント トリガーでは、より大きな入力セットがプレイブックに提供されます (インシデントには、すべてのアラートとエンティティ データも含まれているため)。これにより、応答ワークフローの能力と柔軟性がさらに向上します。 インシデントによってトリガーされるプレイブックは、自動化ルールから呼び出されることによってアクティブ化されます。

[プレイブックの拡張機能](automate-responses-with-playbooks.md)の詳細と、自動化ルールと共にプレイブックを使用して[応答ワークフローを作成する](tutorial-respond-threats-playbook.md)方法について確認してください。

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details-public-preview"></a>新しいアラート エンリッチメント: 強化されたエンティティ マッピングとカスタム詳細 (パブリック プレビュー)

2 つの新しい方法でアラートをエンリッチして、より使いやすく、よりわかりやすくします。

最初に、エンティティ マッピングを次のレベルにします。 ユーザー、ホスト、および IP アドレスから、ファイルとプロセス、メールボックス、Azure リソース、および IoT デバイスへ、20 種類近くのエンティティをマップできるようになりました。 また、各エンティティに複数の識別子を使用して、一意の ID を強化することもできます。 これによって、さらに豊富なデータ セットがインシデント内に提供され、より広範な相関関係やより強力な調査が可能になります。 アラートで[エンティティをマップする新しい方法について](map-data-fields-to-entities.md)確認してください。

[エンティティの詳細](entities-in-azure-sentinel.md)に関する記事をご覧ください。また、[使用可能なエンティティとその識別子の完全なリスト](entities-reference.md)を参照してください。

未加工のイベントから詳細情報を引き出すようにアラートをカスタマイズすることによって、調査と応答の機能をさらに強化できます。 インシデントでのイベント コンテンツの可視性を向上させることで、セキュリティ上の脅威に対応し調査する際の能力と柔軟性を高めることができます。 アラートで[カスタム詳細を引き出す方法](surface-custom-details-in-alerts.md)について確認してください。



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Azure Sentinel ブックを印刷するか PDF として保存する

Azure Sentinel ブックを印刷できるようになりました。これにより、PDF にエクスポートし、ローカルに保存したり共有したりすることもできます。

ブックで、オプション メニュー > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **[Print content]\(コンテンツの印刷\)** を選択します。 次に、プリンターを選択するか、必要に応じて **[PDF として保存]** を選択します。

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="ブックを印刷するか、PDF として保存します。":::

詳細については、「[チュートリアル: データの視覚化と監視](tutorial-monitor-your-data.md)」を参照してください。

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>インシデント フィルターと並べ替えの設定がセッションに保存されるようになりました (パブリック プレビュー)

製品の他の領域に移動している間も、インシデントのフィルターと並べ替えが Azure Sentinel セッション全体にわたって保存されるようになりました。
まだ同じセッション内にいる限り、Azure Sentinel の[インシデント](tutorial-investigate-cases.md)領域に戻ると、そこから離れたときとまったく同じ状態でフィルターと並べ替えが表示されます。

> [!NOTE]
> Azure Sentinel を終了するか、ブラウザーを更新した後は、インシデントのフィルターと並べ替えは保存されません。

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Microsoft 365 Defender インシデント統合 (パブリック プレビュー)

Azure Sentinel の [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) インシデント統合により、すべての M365D インシデントを Azure Sentinel にストリーミングし、両方のポータル間で同期を保つことができます。 M365D (旧称 Microsoft Threat Protection または MTP) のインシデントには、関連するすべてのアラート、エンティティ、関連情報が含まれており、Azure Sentinel でトリアージと事前調査を実行するのに十分なコンテキストが提供されます。 Sentinel に入ると、インシデントは M365D と双方向で同期されたままになるため、インシデント調査で両方のポータルの利点を活用できます。

Azure Sentinel と Microsoft 365 Defender の両方を同時に使用することで、両方の長所を活用できます。 組織の情報リソースの範囲全体に対して SIEM によって提供される幅広い分析情報と、Microsoft 365 リソースを保護するために XDR によって提供されるカスタマイズおよび調整された詳細な調査能力を得ることができます。これらは、シームレスな SOC 操作を実現するために調整され、同期されています。

詳細については、「[Microsoft 365 Defender と Azure Sentinel の統合](microsoft-365-defender-sentinel-integration.md)」を参照してください。

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Azure Policy を使用した新しい Microsoft サービス コネクタ

[Azure Policy](../governance/policy/overview.md) は、ポリシーを使用してリソースのプロパティを適用および制御するための Azure サービスです。 ポリシーを使用すると、リソースが IT ガバナンス標準に準拠した状態を維持することができます。

ポリシーによって制御できるリソースのプロパティの中には、診断ログと監査ログの作成と処理があります。 Azure Sentinel では、Azure Policy を使用して、Azure Sentinel に取り込むログを持つ特定の種類のすべての (現在および将来の) リソースに、診断ログの設定の共通セットを適用できるようになりました。 Azure Policy を使用すると、診断ログの設定リソースをリソース別に設定する必要がなくなります。

Azure Policy ベースのコネクタを次の Azure サービスで使用できるようになりました。
- [Azure Key Vault](connect-azure-key-vault.md) (パブリック プレビュー)
- [Azure Kubernetes Service](connect-azure-kubernetes-service.md) (パブリック プレビュー)
- [Azure SQL データベースまたはサーバー](connect-azure-sql-logs.md) (GA)

お客様は、引き続き特定のインスタンスに対して手動でログを送信することができ、ポリシー エンジンを使用する必要はありません。

## <a name="february-2021"></a>2021 年 2 月

- [Cybersecurity Maturity Model Certification (CMMC) ブック](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [サードパーティのデータ コネクタ](#third-party-data-connectors)
- [エンティティ ページの UEBA 分析情報 (パブリック プレビュー)](#ueba-insights-in-the-entity-page-public-preview)
- [インシデント検索の改善 (パブリック プレビュー)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Cybersecurity Maturity Model Certification (CMMC) ブック

Azure Sentinel CMMC ブックには、Microsoft のセキュリティ製品、Office 365、Teams、Intune、Windows 仮想デスクトップなど、Microsoft ポートフォリオ全体にわたり、CMMC コントロールに合わせてログ クエリを表示するためのメカニズムが用意されています。

CMMC ブックを使用すると、セキュリティ アーキテクト、エンジニア、セキュリティ オペレーション アナリスト、マネージャー、IT プロフェッショナルは、クラウド ワークロードのセキュリティ体制に関する状況認識を可視化することができます。 また、それぞれの CMMC 要件およびプラクティスに合わせて Microsoft 製品の選択、設計、デプロイ、構成を行うための推奨事項もあります。

CMMC に準拠する必要がない場合でも、CMMC ブックを使用すると、セキュリティ オペレーション センターの構築、アラートの開発、脅威の視覚化、ワークロードの状況認識を実現できます。

Azure Sentinel **ブック** 領域で、CMMC ブックにアクセスします。 **[テンプレート]** を選択し、**CMMC** を検索します。

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="CMMC ブック ガイドのオンとオフの切り替え" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


詳細については、次を参照してください。

- [Azure Sentinel Cybersecurity Maturity Model Certification (CMMC) ブック](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [チュートリアル: データの視覚化と監視](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>サードパーティのデータ コネクタ

サードパーティ統合のコレクションは増え続け、過去 2 か月間で 30 個のコネクタが追加されています。 リンクを次に示します。

- [Agari のフィッシング対策とブランド保護](connect-agari-phishing-defense.md)
- [Akamai セキュリティ イベント](connect-akamai-security-events.md)
- [Active Directory 用の Alsid](connect-alsid-active-directory.md)
- [Apache HTTP Server](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google Workspace (旧 G Suite)](connect-google-workspace.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS ログ](connect-nxlog-dns.md)
- [NXLog Linux Audit](connect-nxlog-linuxaudit.md)
- [Onapsis Platform](connect-data-sources.md)
- [Proofpoint On Demand Email Security (POD)](connect-proofpoint-pod.md)
- [Qualys Vulnerability Management ナレッジ ベース](connect-data-sources.md)
- [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
- [SonicWall ファイアウォール](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>エンティティ ページの UEBA 分析情報 (パブリック プレビュー)

Azure Sentinel エンティティの詳細ページには、[分析情報ウィンドウ](identify-threats-with-entity-behavior-analytics.md#entity-insights)が提供されています。これには、エンティティに関する行動分析情報が表示され、異常とセキュリティの脅威をすばやく特定するのに役立ちます。

[UEBA を有効](ueba-enrichments.md)にし、4 日以上の期間を選択した場合、この分析情報ウィンドウには、UEBA 分析情報の次の新しいセクションも含まれます。

|Section  |説明  |
|---------|---------|
|**UEBA Insights\(UEBA 分析情報\)**     | 異常なユーザー アクティビティの概要を示します。 <br>- 地理的な場所、デバイス、環境にまたがっている<br>- ユーザー独自の履歴と比較して時間と頻度の範囲を越えている <br>- ピアの動作比較した場合 <br>- 組織の動作と比較した場合     |
|**User Peers Based on Security Group Membership\(セキュリティ グループのメンバーシップに基づくユーザー ピア\)**     |   Azure AD セキュリティ グループのメンバーシップに基づいてユーザー ピアを一覧表示し、同様のアクセス許可を共有する他のユーザーの一覧をセキュリティ オペレーション チームに提供します。  |
|**User Access Permissions to Azure Subscription\(Azure サブスクリプションへのユーザーのアクセス許可\)**     |     Azure サブスクリプションへのユーザーのアクセス許可を直接、または Azure AD グループまたはサービス プリンシパルを介して表示します。   |
|**Threat Indicators Related to The User\(ユーザーに関連する脅威インジケーター\)**     |  ユーザーのアクティビティで表される、IP アドレスに関連する既知の脅威のコレクションを一覧表示します。 脅威は、脅威の種類とファミリ別に一覧表示され、Microsoft の脅威インテリジェンス サービスによって強化されています。       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>インシデント検索の改善 (パブリック プレビュー)

Azure Sentinel インシデント検索エクスペリエンスが改善され、特定の脅威を調査するときにインシデント間をすばやく移動できるようになりました。

Azure Sentinel でインシデントを検索するときに、インシデントの次の詳細によって検索できるようになりました。

- ID
- タイトル
- 製品
- 所有者
- タグ

## <a name="january-2021"></a>2021 年 1 月

- [分析ルール ウィザード: クエリの編集エクスペリエンスの向上 (パブリック プレビュー)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Az.SecurityInsights PowerShell モジュール (パブリック プレビュー)](#azsecurityinsights-powershell-module-public-preview)
- [SQL データベース コネクタ](#sql-database-connector)
- [Dynamics 365 コネクタ (パブリック プレビュー)](#dynamics-365-connector-public-preview)
- [インシデント コメントの改善](#improved-incident-comments)
- [専用の Log Analytics クラスター](#dedicated-log-analytics-clusters)
- [ロジック アプリのマネージド ID](#logic-apps-managed-identities)
- [分析ルールのプレビュー グラフによるルール調整の改善](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>分析ルール ウィザード:クエリの編集エクスペリエンスの向上 (パブリック プレビュー)

Azure Sentinel のスケジュールされた分析ルールウィザードで、クエリの作成と編集に関して次のような機能強化が行われました。

-   拡張可能な編集ウィンドウにより、クエリを表示する画面のスペースが増えました。
-   クエリ コードでキーワードが強調表示されるようになりました。
-   オートコンプリートのサポートが拡張されました。
-   リアルタイムのクエリ検証。 クエリ内のエラーが、スクロール バー上の赤いブロックと、 **[ルールのロジックを設定]** タブ名の赤い点として表示されるようになりました。 また、エラーのあるクエリを保存することはできません。

詳細については、「[チュートリアル:脅威を検出するためのカスタム分析規則を作成する](tutorial-detect-threats-custom.md)」を参照してください。
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Az.SecurityInsights PowerShell モジュール (パブリック プレビュー)

Azure Sentinel では、新しい [Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) PowerShell モジュールがサポートされるようになりました。

**Az.SecurityInsights** モジュールでは、状態、重大度、所有者などを変更するためのインシデントとのやり取り、インシデントへのコメントやラベルの追加、ブックマークの作成など、一般的な Azure Sentinel ユース ケースがサポートされます。

CI/CD パイプラインには、[Azure Resource Manager (ARM)](../azure-resource-manager/templates/index.yml) を使用することをお勧めしますが、**Az.SecurityInsights** モデルは、デプロイ後のタスクに役立ち、SOC オートメーションを対象としています。  たとえば、SOC オートメーションには、データ コネクタの構成、分析ルールの作成、分析ルールへのオートメーション アクションの追加などが含まれる場合があります。

使用可能なコマンドレットの完全な一覧と説明、パラメーターの説明、例などの詳細については、[Az.SecurityInsights PowerShell のドキュメント](/powershell/module/az.securityinsights/)を参照してください。

### <a name="sql-database-connector"></a>SQL データベース コネクタ

Azure Sentinel では、Azure SQL データベース コネクタが提供されるようになりました。これを使用すると、データベースの監査と診断ログを Azure Sentinel にストリーミングし、すべてのインスタンス内のアクティビティを継続的に監視できます。

Azure SQL は、アップグレード、修正プログラムの適用、バックアップ、監視などのほとんどのデータベース管理機能をユーザーの介入なしで処理する、フル マネージドの PaaS (サービスとしてのプラットフォーム) データベース エンジンです。

詳細については、「[Azure SQL データベースの診断と監査のログを接続する](connect-azure-sql-logs.md)」を参照してください。

### <a name="dynamics-365-connector-public-preview"></a>Dynamics 365 コネクタ (パブリック プレビュー)

Azure Sentinel では、Microsoft Dynamics 365 用のコネクタが提供されるようになりました。これにより、Dynamics 365 アプリケーションのユーザー、管理者、サポート アクティビティ ログを Azure Sentinel に収集できます。 このデータを使用すると、実行されているデータ処理アクション全体を監査し、考えられるセキュリティ違反を分析することができます。

詳細については、「[Dynamics 365 アクティビティログを Azure Sentinel に接続する](connect-dynamics-365.md)」を参照してください。

### <a name="improved-incident-comments"></a>インシデント コメントの改善

アナリストは、インシデント コメントを使用して、インシデントで共同作業を行い、プロセスや手順を手動で、またはプレイブックの一部として文書化します。 

インシデント コメントの作成エクスペリエンスが改善されたことにより、コメントの書式設定や、既存のコメントの編集または削除を行うことができるようになりました。

詳細については、「[Microsoft セキュリティ アラートからインシデントを自動的に作成する](create-incidents-from-alerts.md)」を参照してください。
### <a name="dedicated-log-analytics-clusters"></a>専用の Log Analytics クラスター

Azure Sentinel では、デプロイ オプションとして専用の Log Analytics クラスターがサポートされるようになりました。 次の場合には、専用クラスターを検討することお勧めします。

- Azure Sentinel ワークスペースに **1 日あたり 1 TB 以上を取り込む**
- Azure 登録に **複数の Azure Sentinel ワークスペースがある**

専用クラスターを使用すると、カスタマー マネージド キー、ロックボックス、二重暗号化、より高速のワークスペース間クエリ (同じクラスター上に複数のワークスペースがある場合) などの機能を使用できます。

詳細については、「[Azure Monitor ログ専用クラスター](../azure-monitor/logs/logs-dedicated-clusters.md)」を参照してください。

### <a name="logic-apps-managed-identities"></a>ロジック アプリのマネージド ID

Azure Sentinel では、Azure Sentinel Logic Apps コネクタのマネージド ID がサポートされるようになりました。これにより、余分な ID を作成する代わりに、Azure Sentinel で操作するためのアクセス許可を特定のプレイブックに直接付与することができます。

- **マネージド ID を使用しない場合**、Logic Apps コネクタを Azure Sentinel 上で実行するには、Azure Sentinel RBAC の役割を持つ個別の ID が必要です。 個別の ID には、Azure AD ユーザーまたはサービス プリンシパル (Azure AD に登録済みのアプリケーションなど) を指定できます。

- **ロジック アプリでマネージド ID のサポートを有効にする** と、ロジック アプリが Azure AD に登録され、オブジェクト ID が提供されます。 Azure Sentinel のオブジェクト ID を使用して、Azure Sentinel ワークスペースでロジック アプリに Azure RBAC の役割を割り当てます。 

詳細については、次を参照してください。

- [Azure Logic Apps でのマネージド ID を使用した認証](../logic-apps/create-managed-service-identity.md)に関する記事
- [Azure Sentinel Logic Apps コネクタのドキュメント](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>分析ルールのプレビュー グラフによるルール調整の改善 (パブリック プレビュー)

Azure Sentinel では、分析ルールをより適切に調整できるようになりました。これは、精度の向上やノイズの減少に役立ちます。

**[ルールのロジックを設定]** タブで分析ルールを編集すると、右側に **[Results simulation]\(結果のシミュレーション\)** 領域が表示されます。 

**[Test with current data]\(現在のデータでテストする\)** を選択して、Azure Sentinel で、最後の 50 回について分析ルールのシミュレーションを実行します。 評価された未加工のイベント データに基づいて、ルールによって生成された平均アラート数を表示するグラフが生成されます。 

詳細については、「[ルールのクエリ ロジックを定義して設定を構成する](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings)」を参照してください。

## <a name="december-2020"></a>2020 年 12 月

- [80 の新しい組み込みハンティング クエリ](#80-new-built-in-hunting-queries)
- [Log Analytics エージェントの改善](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 の新しい組み込みハンティング クエリ
 
Azure Sentinel の組み込みハンティング クエリを利用すると、SOC アナリストは、現在の検出の対象範囲内にあるギャップを埋めて、新しいハンティング リードを開始できます。

Azure Sentinel のこの更新には、MITRE ATT&CK フレームワーク マトリックス全体にわたる対象範囲を提供する新しいハンティング クエリが含まれます。

- **コレクション**
- **コマンドとコントロール**
- **資格情報へのアクセス**
- **検出**
- **実行**
- **流出**
- **影響**
- **初期アクセス**
- **永続化**
- **特権エスカレーション**

追加されたハンティング クエリは、ご使用の環境で疑わしいアクティビティを検出しやすくなるように設計されています。 正当なアクティビティや悪意がある可能性のあるアクティビティが返される場合もありますが、ハンティングを導くのに役立ちます。 

これらのクエリを実行した後、結果に自信がある場合は、それらを分析ルールに変換するか、既存または新規のインシデントにハンティング結果を追加することができます。

追加されたすべてのクエリは、Azure Sentinel の [ハンティング] ページから利用できます。 詳細については、「[Azure Sentinel で脅威を検出する](hunting.md)」を参照してください。

### <a name="log-analytics-agent-improvements"></a>Log Analytics エージェントの改善

Azure Sentinel ユーザーは、Log Analytics エージェントの次の改善機能を利用することができます。

- CentOS 8、RedHat 8、SUSE Linux 15 など、**より多くのオペレーティング システムをサポート**。
- Python 2 に加えて、**Python 3 をサポート**

Azure Sentinel では、Log Analytics エージェントを使用して、Windows セキュリティ イベント、Syslog イベント、CEF ログなどのイベントをワークスペースに送信します。

> [!NOTE]
> Log Analytics エージェントは、OMS エージェントまたは Microsoft Monitoring Agent (MMA) と呼ばれる場合もあります。 
> 

詳細については、[Log Analytics のドキュメント](../azure-monitor/agents/log-analytics-agent.md)および [Log Analytics エージェントのリリース ノート](https://github.com/microsoft/OMS-Agent-for-Linux/releases)を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[Azure Sentinel をオンボードする](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[アラートの視覚化](quickstart-get-visibility.md)
