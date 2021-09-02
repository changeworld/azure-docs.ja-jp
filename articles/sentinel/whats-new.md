---
title: Azure Sentinel の新着情報
description: この記事では、過去数か月間に追加された Azure Sentinel の新機能について説明します。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 08/09/2021
ms.openlocfilehash: 7c4a2958f8629b224cecf1e92fd0efcff6b1fdd6
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182181"
---
# <a name="whats-new-in-azure-sentinel"></a>Azure Sentinel の新着情報

この記事では、Azure Sentinel 用に最近追加された機能と、Azure Sentinel のユーザー エクスペリエンスを向上させる関連サービスの新機能について説明します。

6 か月以上前の項目をお探しの場合は、「[Azure Sentinel の最新情報のアーカイブ](whats-new-archive.md)」を参照してください。 以前に提供された機能については、[Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)を参照してください。

> [!IMPORTANT]
> 記載されている機能は、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!TIP]
> Microsoft の脅威ハンティング チームは、クエリ、プレイブック、ブック、およびノートブックを [Azure Sentinel コミュニティ](https://github.com/Azure/Azure-Sentinel) に投稿します。これには、ユーザーのチームが適応して使用できる特定の[ハンティング クエリ](https://github.com/Azure/Azure-Sentinel)などが含まれます。
>
> ユーザーも投稿することができます。 [Azure Sentinel Threat Hunters GitHub コミュニティ](https://github.com/Azure/Azure-Sentinel/wiki)にぜひご参加ください。
>

## <a name="august-2021"></a>2021 年 8 月

- [高度なインシデント検索 (パブリック プレビュー)](#advanced-incident-search-public-preview)
- [ランサムウェアの Fusion 検出 (パブリック プレビュー)](#fusion-detection-for-ransomware-public-preview)
- [UEBA データ用のウォッチリスト テンプレート](#watchlist-templates-for-ueba-data-public-preview)
- [ファイル イベントの正規化スキーマ (パブリック プレビュー)](#file-event-normalization-schema-public-preview)
- [ドキュメントの新事項: ベスト プラクティス ガイダンス](#new-in-docs-best-practice-guidance)

### <a name="advanced-incident-search-public-preview"></a>高度なインシデント検索 (パブリック プレビュー)

既定では、インシデントの検索は、**インシデント ID**、**タイトル**、**タグ**、**所有者**、および **製品名** の値に対してのみ実行されます。 Azure Sentinel では、アラートの詳細、説明、エンティティ、戦術など、より多くのデータを検索するための[高度な検索オプション](investigate-cases.md#search-for-incidents)が提供されるようになりました。

次に例を示します。

:::image type="content" source="media/tutorial-investigate-cases/advanced-search.png" alt-text="[インシデント] ページの高度な検索オプションのスクリーンショット。":::

詳細については、「[インシデントの検索](investigate-cases.md#search-for-incidents)」を参照してください。

### <a name="fusion-detection-for-ransomware-public-preview"></a>ランサムウェアの Fusion 検出 (パブリック プレビュー)

Azure Sentinel では、ランサムウェア アクティビティについて新しい Fusion 検出が提供されて、**ランサムウェア アクティビティ検出に関連する可能性のある複数のアラート** というタイトルのインシデントが生成されるようになりました。

アラートがランサムウェア アクティビティに関連付けられている可能性があり、これらが特定の期間に発生し、攻撃の実行および防御回避の段階に関連付けられているときに、インシデントが生成されます。 インシデントに示されているアラートを使用して、攻撃者がホストまたはデバイスに侵入し、検出を回避するために使用する可能性がある手法を分析します。

サポートされているデータ コネクタとして、以下があります。

- [Azure Defender (Azure Security Center)](connect-azure-security-center.md)
- [Microsoft Defender for Endpoint](connect-microsoft-defender-advanced-threat-protection.md)
- [Microsoft Defender for Identity](connect-azure-atp.md)
- [Microsoft Cloud App Security](connect-cloud-app-security.md)
- [Azure Sentinel のスケジュールされた分析ルール](detect-threats-built-in.md#scheduled)

詳細については、「[ランサムウェア アクティビティ検出に関連する可能性のある複数のアラート](fusion.md#multiple-alerts-possibly-related-to-ransomware-activity-detected-public-preview)」を参照してください。

### <a name="watchlist-templates-for-ueba-data-public-preview"></a>UEBA データ用のウォッチリスト テンプレート (パブリックプレビュー)

Azure Sentinel で提供されるようになった UEBA データ用の組み込みのウォッチリスト テンプレートは、環境に合わせてカスタマイズし、調査中に使用することができます。

UEBA ウォッチリストにデータが入力されると、そのデータを分析ルールに関連付けて、それをエンティティ ページや調査グラフに洞察として表示したり、カスタム使用を作成して VIP や機密性の高いユーザーを追跡したりすることができます。

ウォッチリスト テンプレートの現在の内容を以下に示します。

- **VIP ユーザー**。 組織で大きな影響値を持つ従業員のユーザー アカウントの一覧。
- **退職した従業員**。 退職した、または退職しようとしている従業員のユーザー アカウントの一覧。
- **サービス アカウント**。 サービス アカウントとその所有者の一覧。
- **ID 相関関係**。 同じ人物の所属下にある、関連するユーザー アカウントの一覧。
- **価値の高い資産**。 組織の中で重要な価値を持つデバイス、リソース、または他の資産の一覧。
- **ネットワーク マッピング**。 IP サブネットとそれぞれの組織コンテキストの一覧。

詳細については、[テンプレートを使用して新しいウォッチリストを作成する](watchlists.md#create-a-new-watchlist-using-a-template-public-preview)とに[組み込みウォッチリスト スキーマ](watchlist-schemas.md)に関するページをご覧ください。



### <a name="file-event-normalization-schema-public-preview"></a>ファイル イベントの正規化スキーマ (パブリック プレビュー)

Azure Sentinel Information Model (ASIM) でサポートされるようになったファイル イベント正規化スキーマは、ファイルやドキュメントの作成、変更、削除などのファイルのアクティビティを記述するために使用されます。 ファイル イベントは、オペレーティング システム、Azure Files などのファイル ストレージ システム、Microsoft SharePoint などのドキュメント管理システムによって報告されます。

詳細については、次を参照してください。

- [Azure Sentinel ファイル イベント正規化スキーマ リファレンス (パブリック プレビュー)](file-event-normalization-schema.md)
- [正規化と Azure Sentinel 情報モデル (ASIM)](normalization.md)


### <a name="new-in-docs-best-practice-guidance"></a>ドキュメントの新事項: ベスト プラクティス ガイダンス

お客様やサポート チームからの多数の要求に応じて、一連のベストプラクティス ガイダンスを当社のドキュメントに追加しました。

詳細については、次を参照してください。

- [Azure Sentinel をデプロイするための前提条件](prerequisites.md)
- [Azure Sentinel のベスト プラクティス](best-practices.md)
- [Azure Sentinel ワークスペース アーキテクチャのベスト プラクティス](best-practices-workspace-architecture.md)
- [Azure Sentinel ワークスペース アーキテクチャを設計する](design-your-workspace-architecture.md)
- [Azure Sentinel のサンプル ワークスペースの設計](sample-workspace-designs.md)
- [データ収集のベスト プラクティス](best-practices-data.md)

> [!TIP]
> ドキュメント全体で追加されたガイダンスについては、関連する概念と方法に関する記事を参照してください。 詳細については、「[その他のベスト プラクティス リファレンス](best-practices.md#additional-best-practice-references)」を参照してください。
>

## <a name="july-2021"></a>2021 年 7 月

- [Microsoft 脅威インテリジェンス照合分析 (パブリック プレビュー)](#microsoft-threat-intelligence-matching-analytics-public-preview)
- [Azure Sentinel の情報テーブルで Azure AD データを使用する (パブリック プレビュー)](#use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview)
- [API を介した地理位置情報データを使用したエンティティの強化 (パブリック プレビュー)](#enrich-entities-with-geolocation-data-via-api-public-preview)
- [ADX クロスリソース クエリのサポート (パブリック プレビュー)](#support-for-adx-cross-resource-queries-public-preview)
- [ウォッチリストは一般提供中です](#watchlists-are-in-general-availability)
- [データ レジデンシーをより多くの地域でサポート](#support-for-data-residency-in-more-geos)
- [Azure Defender コネクタでの双方向同期 (パブリック プレビュー)](#bidirectional-sync-in-azure-defender-connector-public-preview)


### <a name="microsoft-threat-intelligence-matching-analytics-public-preview"></a>Microsoft 脅威インテリジェンス照合分析 (パブリック プレビュー)

Azure Sentinel では、Microsoft が生成した脅威インテリジェンス データをログと照合する、組み込みの **Microsoft 脅威インテリジェンス照合分析** ルールが提供されるようになりました。 このルールで生成される忠実度の高いアラートとインシデントには、適切な重大度が、検出されたログのコンテキストに基づいて付けられています。 一致が検出されると、インジケーターは Azure Sentinel 脅威インテリジェンス リポジトリにも発行されます。

**Microsoft 脅威インテリジェンス照合分析** ルールは、現在、次のログ ソースに対するドメイン インジケーターと一致しています。

- [CEF](connect-common-event-format.md)
- [DNS](connect-dns.md)
- [Syslog](connect-syslog.md)

詳細については、「[照合分析を使用した脅威の検出 (パブリックプレビュー)](work-with-threat-indicators.md#detect-threats-using-matching-analytics-public-preview)」を参照してください。

### <a name="use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview"></a>Azure Sentinel の情報テーブルで Azure AD データを使用する (パブリック プレビュー)

攻撃者は多くの場合、組織独自のユーザーとサービス アカウントを使用するため、アナリストにとっては、ユーザー ID や特権など、それらのユーザー アカウントに関するデータが調査中に重要です。

これで、Azure Sentinel ワークスペースで [UEBA を有効に](enable-entity-behavior-analytics.md)すると、Azure AD データも Log Analytics の新しい **IdentityInfo** テーブルに同期されるようになりました。 Azure AD と **IdentifyInfo** テーブルの間の同期化によって、ユーザー メタデータ、グループ情報、各ユーザーに割り当てられた Azure AD ロールなど、ユーザー プロファイル データのスナップショットが作成されます。

調査中には、また、組織の分析ルールを微調整して偽陽性を減らすために、**IdentityInfo** テーブルを使用します。

詳細については、『UEBA エンリッチメント リファレンス』の「[IdentityInfo テーブル](ueba-enrichments.md#identityinfo-table-public-preview)」と「[UEBA データを使用して擬陽性を分析する](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives)」を参照してください。

### <a name="enrich-entities-with-geolocation-data-via-api-public-preview"></a>API を介した地理位置情報データを使用したエンティティの強化 (パブリック プレビュー)

Azure Sentinel では、地理位置情報を使用してデータを強化する API が提供されるようになりました。 その地理位置情報データを使用して、セキュリティ インシデントの分析と調査を行うことができます。

詳細については、「[REST API を介して地理位置情報データで Azure Sentinel のエンティティを強化する (パブリック プレビュー)](geolocation-data-api.md)」と「[Azure Sentinel でエンティティを使用してデータを分類および分析する](entities-in-azure-sentinel.md)」を参照してください。


### <a name="support-for-adx-cross-resource-queries-public-preview"></a>ADX クロスリソース クエリのサポート (パブリック プレビュー)

Azure Sentinel での検索エクスペリエンスでは、[ADX クロスリソース クエリ](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md#cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer)がサポートされるようになりました。
 
Log Analytics は Azure Sentinel で分析を実行するためのプライマリ データ ストレージの場所ですが、コスト、保有期間、または他の要因のためにデータを格納する際に ADX が必要になる場合があります。 この機能により、お客様は、より広範なデータセットを探して、[Azure Sentinel 検索エクスペリエンス](hunting.md)(検索クエリ、[ライブストリーム](livestream.md)、Log Analytics 検索ページなど) で結果を確認できます。

ADX クラスターに格納されているデータのクエリを実行するには、adx () 関数を使用して ADX クラスター、データベース名、および目的のテーブルを指定します。 それから他のテーブルのように、出力に対してクエリを実行できます。 詳細について上でリンクしたページを参照してください。




### <a name="watchlists-are-in-general-availability"></a>ウォッチリストは一般提供中です

[ウォッチリスト](watchlists.md)機能は現在一般提供されています。 ウォッチリストを使用してアラートをビジネス データで強化し、アクセス イベントを照合するための許可リストやブロック リストを作成し、脅威を調査してアラートの疲労を軽減しましょう。

### <a name="support-for-data-residency-in-more-geos"></a>データ レジデンシーをより多くの地域でサポート

Azure Sentinel では、次の追加地域で完全データ レジデンシーがサポートされるようになりました。

ブラジル、ノルウェー、南アフリカ、韓国、ドイツ、アラブ首長国連邦 (UAE)、スイス。

データ レジデンシーが[サポートされている地域の完全な一覧](quickstart-onboard.md#geographical-availability-and-data-residency)を参照してください。

### <a name="bidirectional-sync-in-azure-defender-connector-public-preview"></a>Azure Defender コネクタでの双方向同期 (パブリック プレビュー)

Azure Defender コネクタでは現在、アラートの状態の Defender と Azure Sentinel の間の双方向同期がサポートされています。 Defender アラートを含む Sentinel インシデントを閉じると、アラートは Defender ポータルでも自動的に閉じます。

この「[更新された Azure Defender コネクタの詳細](connect-azure-security-center.md)」を参照してください。

## <a name="june-2021"></a>2021 年 6 月

- [正規化と Azure Sentinel Information Model のアップグレード](#upgrades-for-normalization-and-the-azure-sentinel-information-model)
- [更新されたサービス間コネクタ](#updated-service-to-service-connectors)
- [分析ルールのエクスポートとインポート (パブリック プレビュー)](#export-and-import-analytics-rules-public-preview)
- [アラート エンリッチメント: アラートの詳細 (パブリック プレビュー)](#alert-enrichment-alert-details-public-preview)
- [プレイブックに関するその他のヘルプ](#more-help-for-playbooks)
- [新しいドキュメントの再編成](#new-documentation-reorganization)

### <a name="upgrades-for-normalization-and-the-azure-sentinel-information-model"></a>正規化と Azure Sentinel Information Model のアップグレード

Azure Sentinel Information Model を使用すると、ソースに依存しないコンテンツの使用と作成ができます。これにより、Azure Sentinel ワークスペース内のデータの分析が簡単になります。

今月の更新では、正規化に関するドキュメントを強化し、新たなレベルの詳細と完全 DNS、プロセス イベント、認証正規化スキーマを提供しています。

詳細については、次を参照してください。

- [正規化と Azure Sentinel Information Model (ASIM)](normalization.md)(更新版)
- [Azure Sentinel 認証正規化スキーマ リファレンス (パブリック プレビュー)](authentication-normalization-schema.md) (新登場)
- [Azure Sentinel データ正規化スキーマ リファレンス](normalization-schema.md)
- [Azure Sentinel DNS 正規化スキーマ リファレンス (パブリック プレビュー)](dns-normalization-schema.md) (新登場)
- [Azure Sentinel プロセス イベント正規化スキーマ リファレンス (パブリック プレビュー)](process-events-normalization-schema.md)(新登場)
- [Azure Sentinel レジストリ イベント正規化スキーマ リファレンス (パブリック プレビュー)](registry-event-normalization-schema.md)(新登場)


### <a name="updated-service-to-service-connectors"></a>更新されたサービス間コネクタ

最もよく使用されている 2 つのコネクタが、メジャー アップグレードされました。

- [Windows セキュリティ イベント コネクタ (パブリック プレビュー)](connect-windows-security-events.md)は、新しい Azure Monitor エージェント (AMA) に基づくため、取り込むデータを選択する柔軟性がはるかに高く、最小限のコストで最大限の可視性を実現できます。

- 現在、[Azure アクティビティ ログ コネクタ](connect-azure-activity.md)は診断設定パイプラインに基づいており、より完全なデータが提供され、取り込み遅延が大幅に減り、パフォーマンスと信頼性が向上します。

アップグレードは自動で行なわれません。 これらのコネクタのユーザーに、新しいバージョンを有効にしていただきます。

### <a name="export-and-import-analytics-rules-public-preview"></a>分析ルールのエクスポートとインポート (パブリック プレビュー)

Azure Sentinel デプロイをコードとして管理および制御する一環として、分析ルールを JSON 形式の Azure Resource Manager (ARM) テンプレート ファイルへエクスポートし、それらのルールを同様のファイルにインポートできます。 **Scheduled** だけでなく、すべての種類の[分析ルール](detect-threats-built-in.md)を ARM テンプレートにエクスポートできます。 テンプレート ファイルにはルールの情報が、そのクエリから、それに割り当てられた MITRE ATT&CK 戦術まで、すべて含まれます。

詳細については、「[ARM テンプレート間で分析ルールをエクスポートおよびインポートします](import-export-analytics-rules.md)」を参照してください。

### <a name="alert-enrichment-alert-details-public-preview"></a>アラート エンリッチメント: アラートの詳細 (パブリック プレビュー)

エンティティ マッピングとカスタム詳細を使用してアラートの内容を強化するだけでなく、アラートが提示および表示される方法 (拡張機能、インシデント別) を、それぞれのコンテンツに基づいてカスタマイズできるようになりました。 他のアラート エンリッチメント機能と同様に、これは[分析ルール ウィザード](detect-threats-custom.md)で構成できます。

詳細については、「[Azure Sentinel でアラートの詳細をカスタマイズする](customize-alert-details.md)」を参照してください。


### <a name="more-help-for-playbooks"></a>プレイブックに関するその他のヘルプ

2 つの新しいドキュメントが、プレイブックの作成や操作を始めたり、より快適に行うのに役立ちます。
- [Azure Sentinel に対するプレイブックの認証](authenticate-playbooks-to-sentinel.md)は、Logic Apps ベースのプレイブックが Azure Sentinel に接続して情報にアクセスできるさまざまな認証方法と、それぞれを使用する適切な場合を理解するのに役立ちます。
- [プレイブックでトリガーとアクションを使用する](playbook-triggers-actions.md)では、**インシデント トリガー** と **アラート トリガー** の違いと、いつ使用するのかについて説明し、[カスタムの詳細](playbook-triggers-actions.md#work-with-custom-details)の情報にアクセスする方法など、インシデントに対応してプレイブックで実行できるさまざまなアクションの一部を示しています。

プレイブック ドキュメントでは、マルチテナント MSSP シナリオにも明示的に対応しています。

### <a name="new-documentation-reorganization"></a>新しいドキュメントの再編成

今月は [Azure Sentinel ドキュメント](index.yml)を再編成し、よくある顧客体験を取り上げた直感的なカテゴリに再構成しました。 フィルター処理されたドキュメント検索と更新されたランディング ページを使用して、Azure Sentinel ドキュメントを検索してください。

:::image type="content" source="media/whats-new/new-docs.png" alt-text="新しい Azure Sentinel ドキュメントの再編成。" lightbox="media/whats-new/new-docs.png":::


## <a name="may-2021"></a>2021 年 5 月

- [Azure Sentinel PowerShell モジュール](#azure-sentinel-powershell-module)
- [アラート グループの機能強化](#alert-grouping-enhancements)
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


### <a name="azure-sentinel-powershell-module"></a>Azure Sentinel PowerShell モジュール

毎日の運用タスクを自動化するための Azure Sentinel PowerShell モジュール GA として正式リリースされました。

ダウンロードはこちらから: [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.SecurityInsights/)。

詳細については、PowerShell のドキュメントをご覧ください: [Az.SecurityInsights](/powershell/module/az.securityinsights/)

### <a name="alert-grouping-enhancements"></a>アラート グループの機能強化

分析ルールを構成してアラートを、特定のエンティティの種類に一致したときだけでなく、特定のアラート名、重大度、または構成されたエンティティの他のカスタム詳細と一致したときにも、1 つのインシデントにグループ化できるようになりました。 

分析ルール ウィザードの **[インシデントの設定]** タブで、アラートのグループ化を有効にし、 **[Group alerts into a single incident if the selected entity types and details match]\(選択したエンティティの種類と詳細が一致する場合にアラートを 1 つのインシデントにグループ化する\)** オプションを選択します。 

次に、エンティティの種類と、照合させたい関連する詳細を選択します。

:::image type="content" source="media/whats-new/alert-grouping-details.png" alt-text="エンティティの詳細を照合してアラートをグループ化します。":::

詳細については、「[アラートのグループ化](detect-threats-custom.md#alert-grouping)」を参照してください。

### <a name="azure-sentinel-solutions-public-preview"></a>Azure Sentinel のソリューション (パブリック プレビュー)

データ コネクタ、ブック、分析ルール、プレイブック、ハンティング クエリ、パーサー、ウォッチリストなど、Azure Sentinel の各種コンポーネントを組み合わせた **パッケージ コンテンツ** [ソリューション](sentinel-solutions-catalog.md)が新たに提供されます。

製品内での見つけやすさや、シングル ステップ デプロイ、エンド ツー エンドの製品シナリオが改良されたソリューションとなっています。 詳細については、「[Azure Sentinel ソリューションの検出とデプロイ](sentinel-solutions-deploy.md)」を参照してください。

### <a name="continuous-threat-monitoring-for-sap-solution-public-preview"></a>Continuous Threat Monitoring for SAP ソリューション (パブリック プレビュー)

Azure Sentinel ソリューションに **Continuous Threat Monitoring for SAP** が加わりました。SAP システムを監視して、ビジネス レイヤーおよびアプリケーション レイヤーにおける高度な脅威に対応することができます。

SAP データ コネクタは、SAP システム ランドスケープ全体から 14 もの多くのアプリケーション ログをストリーム配信し、NetWeaver RFC 呼び出しを介して Advanced Business Application Programming (ABAP) から、また OSSAP 制御インターフェイスを介してファイル ストレージ データからログを収集します。 SAP データ コネクタによって、基になる SAP インフラストラクチャを監視する機能が Azure Sentinels に追加されます。

SAP ログを Azure Sentinel に取り込むには、お使いの SAP 環境に Azure Sentinel SAP データ コネクタがインストールされている必要があります。 SAP データ コネクタをデプロイしたら、SAP ソリューションの多彩なセキュリティ コンテンツをデプロイし、組織の SAP 環境に関する分析情報をスムーズに取得して、関連するセキュリティ運用機能を向上させることができます。

詳細については、[SAP 用 Azure Sentinel ソリューション (パブリック プレビュー) のデプロイに関するチュートリアル](sap-deploy-solution.md)を参照してください。

### <a name="threat-intelligence-integrations-public-preview"></a>脅威インテリジェンスの統合 (パブリック プレビュー)

既知の脅威を検出して優先順位を付けるセキュリティ アナリストの能力を高めるために、Azure Sentinel には、[脅威インテリジェンスのフィードを使用](./understand-threat-intelligence.md)する方法がいくつか用意されています。

新たに提供されている数多くの統合脅威インテリジェンス プラットフォーム (TIP、Threat Intelligence Platform) 製品の 1 つを使用し、TAXII サーバーに接続して STIX に適合したあらゆる脅威インテリジェンス ソースを活用できるほか、[Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator) と直接やり取りするあらゆるカスタム ソリューションを利用できるようになりました。

効果的な調査や対応の措置につながる TI 情報でインシデントをエンリッチメント処理するために、プレイブックから脅威インテリジェンス ソースに接続することもできます。

詳細については、「[Azure Sentinel での脅威インテリジェンスの統合](threat-intelligence-integration.md)」を参照してください。

### <a name="fusion-over-scheduled-alerts-public-preview"></a>スケジュールされたアラートに対する Fusion (パブリック プレビュー)

機械学習の相関分析エンジンである **Fusion** でマルチステージ攻撃を検出できるようになりました。他のデータ ソースからインポートされたアラートに加え、一連の [スケジュール化された分析ルール](detect-threats-custom.md)から生成されるアラートが、その相関分析の中で使用されます。

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

詳細については、「[データの視覚化と監視](monitor-your-data.md)」を参照してください。

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

詳細については、「[チュートリアル:Azure Sentinel でインシデントを調査する](investigate-cases.md)」を参照してください。

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

詳細については、「[ データの視覚化と監視](monitor-your-data.md)」と [Azure Monitor のドキュメント](../azure-monitor/visualize/workbooks-overview.md)を参照してください。

### <a name="new-detections-for-azure-firewall"></a>Azure Firewall の新しい検出

Azure Sentinel の[分析](./understand-threat-intelligence.md)領域に、すぐに使用できる Azure Firewall のいくつかの検出が追加されました。 これらの新しい検出機能により、既知の IOC に関連するインターネット ドメイン名または IP アドレスに対して内部ネットワーク上のコンピューターがクエリを実行したり接続したりすると、検出ルール クエリで定義されているように、セキュリティ チームはアラートを受け取ることができます。

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

詳細については、「[データの視覚化と監視](monitor-your-data.md)」を参照してください。

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>インシデント フィルターと並べ替えの設定がセッションに保存されるようになりました (パブリック プレビュー)

製品の他の領域に移動している間も、インシデントのフィルターと並べ替えが Azure Sentinel セッション全体にわたって保存されるようになりました。
まだ同じセッション内にいる限り、Azure Sentinel の[インシデント](investigate-cases.md)領域に戻ると、そこから離れたときとまったく同じ状態でフィルターと並べ替えが表示されます。

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


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[Azure Sentinel をオンボードする](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[アラートの視覚化](get-visibility.md)