---
title: 連続エクスポートでは、Microsoft Defender for Cloud のアラートと推奨事項を Log Analytics ワークスペースまたはクラウド に送信Azure Event Hubs
description: セキュリティ アラートと推奨事項の Log Analytics ワークスペースまたは Azure Event Hubs への連続エクスポートの構成方法について説明します
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 9580ee5f915417d4195ba13277e4de8df7bf55d1
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525596"
---
# <a name="continuously-export-microsoft-defender-for-cloud-data"></a>Microsoft Defender for Cloud データを継続的にエクスポートする

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud では、詳細なセキュリティ アラートと推奨事項が生成されます。 これらは、ポータル内またはプログラム ツールで表示できます。 場合によっては、環境内の他の監視ツールでの追跡のために、この情報の一部または全部をエクスポートする必要があります。 

エクスポートする "*内容*" とエクスポート先の "*場所*" を完全にカスタマイズし、**連続エクスポート** に進みます。 たとえば、次のように構成できます。

- 重大度が高いすべてのアラートを Azure イベント ハブに送信する
- SQL サーバーの脆弱性評価スキャンで検出された重大度が中程度または高いすべての結果を、特定の Log Analytics ワークスペースに送信する
- 生成されるたびに特定の推奨事項を、イベント ハブまたは Log Analytics ワークスペースに送信する 
- サブスクリプションのセキュリティ スコアは、特定のコントロールについて 0.01 以上で変化するたびに、Log Analytics ワークスペースに送信されます 

この機能は *連続* と呼ばれていますが、週単位のスナップショットをエクスポートするオプションもあります。

この記事では、Log Analytics ワークスペースまたは Azure Event Hubs への連続エクスポートの構成方法について説明します。

> [!NOTE]
> Defender for Cloud と SIEM を統合する必要がある場合は [、「SIEM、SOAR、または IT サービス](export-to-siem.md)管理ソリューションへのアラートのストリーム配信」を参照してください。

> [!TIP]
> Defender for Cloud には、CSV への 1 回の手動エクスポートを実行するオプションも用意されています。 詳細については、[アラートと推奨事項の手動によるエクスポート](#manual-one-time-export-of-alerts-and-recommendations)に関する記事を参照してください。


## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|Free|
|必要なロールとアクセス許可:|<ul><li>リソース グループに対する **セキュリティ管理者** または **所有者**</li><li>ターゲット リソースに対する書き込みアクセス許可。</li><li>後述する Azure Policy の "DeployIfNotExist" ポリシーを使用する場合は、ポリシーを割り当てるためのアクセス許可も必要です。</li><li>Event Hub にデータをエクスポートするには、Event Hub ポリシーに対する書き込みアクセス許可が必要です。</li><li>Log Analytics ワークスペースにエクスポートするには:<ul><li>**SecurityCenterFree ソリューションがある** 場合は、ワークスペース ソリューションに対する少なくとも読み取りアクセス許可が必要です: `Microsoft.OperationsManagement/solutions/read`</li><li>**SecurityCenterFree ソリューションがない** 場合は、ワークスペース ソリューションに対する書き込みアクセス許可が必要です: `Microsoft.OperationsManagement/solutions/action`</li><li>[Azure Monitor と Log Analytics ワークスペース ソリューション](../azure-monitor/insights/solutions.md)についての詳細を確認してください</li></ul></li></ul>|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)| 
|||


## <a name="what-data-types-can-be-exported"></a>エクスポートできるデータの種類

連続エクスポートでは、次の種類のデータが、変更されるたびにエクスポートされます。

- セキュリティ アラート
- セキュリティに関する推奨事項。
- セキュリティの調査結果。 これらは、"サブ" の推奨事項と考えることができ、"親" の推奨事項に属します。 次に例を示します。
    - "システム更新プログラムをマシンにインストールする必要がある" という推奨事項には、すべての未完了のシステム更新プログラムに関する "サブ" の推奨事項があります。
    - "仮想マシンの脆弱性を修復する必要がある" という推奨事項には、脆弱性スキャナーによって特定されたすべての脆弱性に関する "サブ" の推奨事項があります。
    > [!NOTE]
    > REST API で連続エクスポートを構成している場合は、結果が含まれる親を常に含めます。 
- サブスクリプションまたはコントロールごとのセキュリティ スコア。
- 規制に関するコンプライアンス データ。


## <a name="set-up-a-continuous-export"></a>連続エクスポートを設定する 

Azure portal の Microsoft Defender for Cloud ページから、REST API を介して、または指定された Azure Policy テンプレートを使用して大規模に連続エクスポートを構成できます。 それぞれの詳細については、下の適切なタブを選択してください。

### <a name="use-the-azure-portal"></a>[**Azure portal を使用する**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-defender-for-cloud-pages-in-azure-portal"></a>Azure portal の Defender for Cloud ページから連続エクスポートを構成する

連続エクスポート先として Log Analytics ワークスペースまたは Azure Event Hubs のどちらを設定する場合でも、次の手順を実行する必要があります。

1. [Defender for Cloud] メニューで、 **[環境設定]** を開きます。

1. データのエクスポートを構成する特定のサブスクリプションを選択します。

1. そのサブスクリプションの設定ページのサイドバーで、 **[連続エクスポート]** を選択します。

    :::image type="content" source="./media/continuous-export/continuous-export-options-page.png" alt-text="Microsoft Defender for Cloud のエクスポート オプション。":::

    ご覧のようにエクスポート オプションが表示されます。 使用可能なエクスポート ターゲットごとにタブがあります。 

1. エクスポートするデータの種類を選択し、それぞれの種類に対するフィルターを選択します (たとえば、重大度が高いアラートのみをエクスポートするなど)。

1. 適切なエクスポート頻度を選択します。
    - **ストリーミング** – リソースの正常性状態が更新されると、評価が送信されます (更新がなければ、データは送信されません)。
    - **スナップショット** –選択したデータ型の現在の状態のスナップショットが、サブスクリプションごとに 1 週間に 1 回送信されます。 スナップショット データを識別するには、``IsSnapshot`` フィールドを探します。

1. 必要に応じて、選択範囲に次の推奨事項のいずれかが含まれている場合は、脆弱性評価の結果を含めることができます。
    - SQL データベースでは脆弱性の検出結果を解決する必要がある
    - マシン上の SQL サーバーでは脆弱性の検出結果を解決する必要がある
    - Azure Container Registry イメージの脆弱性を修復する必要がある (Qualys を利用)
    - 仮想マシンの脆弱性を修復する必要がある
    - システム更新プログラムをマシンにインストールする必要がある

    結果とこれらの推奨事項を含めるには、 **[セキュリティに関する調査結果を含める]** オプションを有効にします。

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="連続エクスポート構成でのセキュリティに関する調査結果トグルを含める。" :::

1. [エクスポート ターゲット] 領域で、データを保存する場所を選択します。 データは別のサブスクリプションのターゲットにも保存できます (たとえば、中央のイベント ハブ インスタンスや中央の Log Analytics ワークスペースなど)。
1. **[保存]** を選択します。

### <a name="use-the-rest-api"></a>[**REST API を使用する**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>REST API を使用して連続エクスポートを構成する

継続的エクスポートは、Microsoft Defender for Cloud Automations API を使用して [構成および管理できます](/rest/api/securitycenter/automations)。 この API を使用して、以下の可能な宛先のいずれかにエクスポートするためのルールを作成または更新します。

- Azure Event Hub
- Log Analytics ワークスペース
- Azure Logic Apps 

API には、Azure portal からは使用できない追加の機能が用意されています。以下に例を示します。

* **より大きなボリューム** - API を使用して 1 つのサブスクリプションに複数のエクスポート構成を作成できます。 Defender for **Cloud の** ポータル UI の [連続エクスポート] ページでは、サブスクリプションごとに 1 つのエクスポート構成しかサポートされません。

* **追加の機能** - API には、UI には表示されない追加のパラメーターが用意されています。 たとえば、Automation リソースにタグを追加し、Defender for Cloud のポータル UI の [連続エクスポート] ページで提供されるプロパティよりも広範なアラートと推奨事項のプロパティセットに基づいてエクスポートを定義できます。

* **より焦点を絞ったスコープ** - API では、エクスポート構成のスコープに対してよりきめ細かなレベルが提供されます。 API を使用してエクスポートを定義するときには、リソース グループ レベルで定義できます。 Defender for Cloudのポータル UI で [連続エクスポート] ページを使用している場合は、サブスクリプション レベルで定義する必要があります。

    > [!TIP]
    > API を使用して複数のエクスポート構成を設定した場合、または API 専用パラメーターを使用した場合、これらの追加機能は Defender for Cloud UI に表示されません。 代わりに、他の構成が存在することを通知するバナーが表示されます。

自動化 API の詳細については、[REST API のドキュメント](/rest/api/securitycenter/automations)を参照してください。

### <a name="deploy-at-scale-with-azure-policy"></a>[**Azure Policy を使用して大規模にデプロイする**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>提供されているポリシーを使用して連続エクスポートを大規模に構成する

組織の監視とインシデント対応プロセスを自動化すると、セキュリティ インシデントの調査と軽減にかかる時間を大幅に短縮できます。

組織全体に連続エクスポートの構成をデプロイするには、後述の提供されている Azure Policy の "DeployIfNotExist" ポリシーを使用して、連続エクスポート手順を作成して構成します。

**これらのポリシーを実装するには**

1. 次の表から、適用するポリシーを選択します。

    |目標  |ポリシー  |ポリシー ID  |
    |---------|---------|---------|
    |イベント ハブへの連続エクスポート|[Microsoft Defender for Cloud のアラートと推奨事項のために Event Hub にエクスポートをデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |Log Analytics ワークスペースへの連続エクスポート|[Microsoft Defender for Cloud のアラートと推奨事項のために Log Analytics ワークスペースにエクスポートをデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > これらは、Azure Policy を検索して見つけることもできます。
    > 1. Azure Policy を開きます。
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Azure Policy にアクセスします。":::
    > 2. Azure Policy のメニューから **[定義]** を選択し、名前で検索します。 

1. 関連する Azure Policy ページで、 **[割り当てる]** を選択します。
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="Azure Policy を割り当てます。":::

1. 各タブを開き、必要に応じてパラメーターを設定します。
    1. **[基本]** タブで、ポリシーのスコープを設定します。 集中管理を使用するには、連続エクスポートの構成を使用するサブスクリプションが含まれている管理グループにポリシーを割り当てます。 
    1. **[パラメーター]** タブで、リソース グループとデータ型の詳細を設定します。 
        > [!TIP]
        > 各パラメーターには、使用可能なオプションを説明するツールヒントがあります。
        >
        > Azure Policyの [パラメーター] タブ (1) を使用すると、Defender for Cloud の連続エクスポート ページ (2) と同様の構成オプションにアクセスできます。
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="連続エクスポートのパラメーターを Azure Policy と比較する。" lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. 必要に応じて、この割り当てを既存のサブスクリプションに適用するには、 **[修復]** タブを開き、修復タスクを作成するためのオプションを選択します。
1. 概要ページを確認し、 **[作成]** を選択します。

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>Log Analytics ワークスペースへのエクスポートについての情報

Log Analytics ワークスペース内の Microsoft Defender for Cloud データを分析する場合、または Defender for Cloud アラートと共に Azure アラートを使用する場合は、Log Analytics ワークスペースへの連続エクスポートを設定します。

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics のテーブルとスキーマ

セキュリティのアラートと推奨事項はそれぞれ、*SecurityAlert* テーブルと *SecurityRecommendation* テーブルに格納されます。 

これらのテーブルを含む Log Analytics ソリューションの名前は、セキュリティ ('Security and Audit') または SecurityCenterFree の強化されたセキュリティ機能を有効にしたかどうかによって異なります。 

> [!TIP]
> 宛先ワークスペースのデータを表示するには、**Security and Audit** または **SecurityCenterFree** ソリューションのいずれかを有効にする必要があります。

![Log Analytics の *SecurityAlert* テーブル。](./media/continuous-export/log-analytics-securityalert-solution.png)

エクスポートされたデータ型のイベント スキーマを表示するには、[Log Analytics テーブル スキーマ](https://aka.ms/ASCAutomationSchemas)にアクセスします。


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>エクスポートされたアラートと推奨事項を Azure Monitor で表示する

エクスポートされたセキュリティ アラートや推奨事項を [Azure Monitor](../azure-monitor/alerts/alerts-overview.md) で表示することもできます。 

Azure Monitor は、診断ログ、メトリック アラート、および Log Analytics ワークスペース クエリに基づくカスタム アラートなど、さまざまな Azure アラートの統合されたアラート エクスペリエンスを提供します。

Azure Monitor で Defender for Cloud からのアラートと推奨事項を表示するには、Log Analytics クエリ (ログ アラート) に基づいてアラート ルールを構成します。

1. Azure Monitor の **[アラート]** ページから、 **[新しいアラート ルール]** を選択します。

    ![Azure Monitor の [アラート] ページ。](./media/continuous-export/azure-monitor-alerts.png)

1. ルールの作成ページで、([Azure Monitor でログ アラート ルール](../azure-monitor/alerts/alerts-unified-log.md)を構成するのと同じ方法で) 新しいルールを構成します。

    * **[リソース]** には、セキュリティ アラートと推奨事項のエクスポート先の Log Analytics ワークスペースを選択します。

    * **[条件]** には、 **[Custom log search]\(カスタム ログ検索\)** を選択します。 表示されたページで、クエリ、ルックバック期間、および頻度の期間を構成します。 検索クエリでは *、「SecurityAlert」* または *「SecurityRecommendation」* と入力して、Log Analytics への連続エクスポート機能を有効にした場合に、Defender for Cloud が継続的にエクスポートするデータ型に対してクエリを実行できます。 
    
    * 必要に応じて、トリガーする[アクション グループ](../azure-monitor/alerts/action-groups.md)を構成します。 アクション グループは、メール送信、ITSM チケット、Webhook などをトリガーできます。
    ![Azure Monitor のアラート ルール。](./media/continuous-export/azure-monitor-alert-rule.png)

Azure Monitor アラートに新しい Microsoft Defender for Cloud アラートまたは推奨事項 (構成済みの連続エクスポート ルールと Azure Monitor アラート ルールで定義した条件に応じて) が表示され、アクション グループが自動的にトリガーされます (指定されている場合)。

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>アラートと推奨事項の手動による 1 回限りのエクスポート

アラートまたは推奨事項の CSV レポートをダウンロードするには、 **[セキュリティ アラート]** または **[推奨事項]** ページを開き、 **[レポートを CSV にダウンロード]** ボタンを選択します。

> [!TIP]
> Azure Resource Graph の制限により、レポートのファイル サイズは 13,000 行に制限されています。 エクスポートされるデータが多すぎることに関連するエラーが表示される場合は、エクスポート対象として少ないサブスクリプション セットを選択して出力を制限してみてください。

:::image type="content" source="./media/continuous-export/download-alerts-csv.png" alt-text="アラート データを CSV ファイルとしてダウンロードする。" lightbox="./media/continuous-export/download-alerts-csv.png":::

> [!NOTE]
> これらのレポートには、現在選択されているサブスクリプションのリソースに関するアラートと推奨事項が含まれています。


## <a name="faq---continuous-export"></a>FAQ - 連続エクスポート

### <a name="what-are-the-costs-involved-in-exporting-data"></a>データのエクスポートには、どのようなコストがかかりますか。

連続エクスポートを有効にするためのコストはありません。 Log Analytics ワークスペースでの構成によっては、そこでのデータの取り込みと保持についてコストが発生する可能性があります。 

[Log Analytics ワークスペースの価格](https://azure.microsoft.com/pricing/details/monitor/)の詳細を確認してください。

[Azure Event Hub の価格](https://azure.microsoft.com/pricing/details/event-hubs/)の詳細を確認してください。


### <a name="does-the-export-include-data-about-the-current-state-of-all-resources"></a>エクスポートには、すべてのリソースの現在の状態に関するデータが含まれますか?

いいえ。 連続エクスポートは、**イベント** のストリーミング用に構築されています。

- エクスポートを有効にする前に受信した **アラート** はエクスポートされません。
- **推奨事項** は、リソースのコンプライアンスの状態が変化するたびに送信されます。 たとえば、リソースの状態が正常から異常に変わった場合などです。 そのため、アラートと同様に、エクスポートを有効にした後に状態が変わっていないリソースの推奨事項はエクスポートされません。
- セキュリティ コントロールまたはサブスクリプションごとの **セキュリティ スコア** は、セキュリティ コントロールのスコアが 0.01 以上で変化したときに送信されます。 
- **規制に対するコンプライアンス対応状態** は、リソースのコンプライアンスの状態が変化したときに送信されます。



### <a name="why-are-recommendations-sent-at-different-intervals"></a>推奨事項が異なる間隔で送信されるのはなぜですか?

さまざまな推奨事項には、異なるコンプライアンス評価間隔があり、これは数分から数日おきまで様々です。 そのため、エクスポートに表示されるまでにかかる時間は推奨事項によって異なります。

### <a name="does-continuous-export-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>連続エクスポートでは、ビジネス継続性またはディザスター リカバリー (BCDR) のシナリオはサポートされていますか。

ターゲット リソースで停止やその他の障害が発生する BCDR シナリオに対して環境を準備する場合、組織では Azure Event Hubs、Log Analytics ワークスペース、ロジック アプリのガイドラインに従ってバックアップを確立することで、データの損失を防ぐ必要があります。

詳細については、「[Azure Event Hubs - geo ディザスター リカバリー](../event-hubs/event-hubs-geo-dr.md)」を参照してください。


### <a name="is-continuous-export-available-for-free"></a>連続エクスポートは無料で使用できますか?

はい、ご利用いただけます。 多くのアラートは、高度な保護を有効にした場合にのみ提供されます。 エクスポートされたデータに表示されるアラートをプレビューする良い方法は、Defender for Cloud のページに表示されるアラートを Azure portal に表示することです。



## <a name="next-steps"></a>次のステップ

この記事では、推奨事項とアラートの連続エクスポートを構成する方法について説明しました。 また、アラート データを CSV ファイルとしてダウンロードする方法も説明しました。 

関連資料については、次のドキュメントを参照してください。 

- [ワークフローの自動化テンプレート](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)の詳細を確認します。
- [Azure Event Hubs のドキュメント](../event-hubs/index.yml)
- [Microsoft Sentinel のドキュメント](../sentinel/index.yml)
- [Azure Monitor のドキュメント](../azure-monitor/index.yml)
- [データ型スキーマをエクスポートする](https://aka.ms/ASCAutomationSchemas)
