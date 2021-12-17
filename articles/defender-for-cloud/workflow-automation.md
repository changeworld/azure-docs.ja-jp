---
title: Microsoft Defender for Cloud でのワークフローの自動化 |Microsoft Docs
description: Microsoft Defender for Cloud でワークフローを作成および自動化する方法について説明します
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 8c0b1cc8a74f71b843150e482860336ed22db5a4
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525575"
---
# <a name="automate-responses-to-microsoft-defender-for-cloud-triggers"></a>クラウドトリガーに対する Microsoft Defender への応答を自動化する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

すべてのセキュリティ プログラムには、インシデント対応のための複数のワークフローが含まれています。 これらのプロセスには、直接の利害関係者への通知、変更管理プロセスの開始、および特定の修復手順の適用が含まれます。 これらのプロシージャの手順をできるだけ多く自動化することがセキュリティの専門家によって推奨されています。 自動化によってオーバーヘッドが削減されます。 また、迅速かつ一貫した方法で、定義済みの要件に従ってプロセスの手順が実行されるようにすることで、セキュリティを向上させることもできます。

この記事では、Microsoft Defender for Cloud のワークフロー自動化機能について説明します。 この機能を使用すると、セキュリティ アラートや推奨事項、および規制コンプライアンスへの変更があったときに Logic Apps をトリガーできます。 例えば、アラートが発生したときに、ユーザーに特定のユーザーを電子メールで送信することができます。 また、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用してロジック アプリを作成する方法についても説明します。


## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|Free|
|必要なロールとアクセス許可:|リソース グループ上での **セキュリティ管理者ロール** または **所有者**<br>ターゲット リソースに対する書き込みアクセス許可も必要になります<br><br>Azure Logic Apps のワークフローを操作するには、次のロジック アプリのロール/アクセス許可も必要です。<br> - [ロジック アプリのオペレーター](../role-based-access-control/built-in-roles.md#logic-app-operator)のアクセス許可が必要。または、ロジック アプリの読み取り/トリガーのアクセス権 (このロールでは、ロジック アプリを作成したり編集したりすることはできません。既存のものを *実行* するだけです)<br> - [ロジック アプリの共同作成者](../role-based-access-control/built-in-roles.md#logic-app-contributor)のアクセス許可は、ロジック アプリの作成と変更に必要です<br>ロジック アプリのコネクタを使用する場合は、それぞれのサービス (たとえば、Outlook/Teams/Slack のインスタンス) にサインインするために追加の資格情報が必要になることがあります。|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>ロジック アプリを作成し、自動的に実行するタイミングを定義する 

1. Defender for Cloud サイドバーで、 **ワークフロー自動化** を選択します。

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="定義された自動化の一覧を示すワークフロー自動化ページのスクリーンショット。" lightbox="./media/workflow-automation/list-of-workflow-automations.png":::

    このページで、新しい自動化ルールを作成したり、既存のものを有効化、無効化、または削除したりすることができます。

1. 新しいワークフローを定義するには、 **[Add workflow automation]\(ワークフローの自動化の追加\)** をクリックします。 新しい自動化のオプション ウィンドウが開きます。

    :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="ワークフロー自動化ウィンドウの追加。":::

    ここで、以下を入力できます。
    1. 自動化の名前と説明。
    1. この自動ワークフローを開始するトリガー。 たとえば、"SQL" を含むセキュリティ警告が生成されたときにロジック アプリを実行することができます。

        > [!NOTE]
        > たとえば、**SQL データベースの脆弱性評価の結果を修復する必要がある** ことを示す "サブの推奨事項" という推奨事項がトリガーに含まれている場合は、セキュリティに関する新しい結果が得られるたびにロジック アプリがトリガーされることはありません。親の推奨事項の状態が変更された場合のみトリガーされます。

    1. トリガー条件が満たされたときに実行されるロジック アプリ。 

1. アクション セクションで  **ロジックアプリにアクセス** を選択して、ロジックアプリの作成プロセスを開始します。

    Azure Logic Apps が表示されます。

1. **[追加]** を選択します。 

    [![新しいロジック アプリの作成。](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. 名前、リソースグループ、および場所を入力し、[**レビューと** 作成] を選択し ます。

    「 **展開が進行** 中です」というメッセージが表示されます。 デプロイの完了通知が表示されるまで待ってから、「通知から **リソースへのアクセス** 」 を選択します。

1. 新しいロジック アプリでは、セキュリティ カテゴリの組み込みの定義済みテンプレートから選択できます。 または、このプロセスがトリガーされたときに発生するイベントのカスタム フローを定義することもできます。

    > [!TIP]
    > ロジック アプリでは、パラメーターは、独自のフィールドではなく、文字列の一部としてコネクタに含まれることがあります。 パラメーターを抽出する方法の例については、「 [クラウドワークフロー用に Microsoft Defender を構築する自動化](https://techcommunity.microsoft.com/t5/azure-security-center/working-with-logic-app-parameters-while-building-azure-security/ba-p/1342121)」の「ロジックアプリのパラメーターの使用」の手順 #14 を参照してください。

    ロジックアプリデザイナーでは、次のような Defender for Cloud トリガーがサポートされています。

    - **Microsoft Defender For Cloud の推奨事項が作成またはトリガーされたとき**：ロジックアプリが非推奨または置換された推奨事項に依存している場合は、自動化が動作を停止し、トリガーを更新する必要があります。 推奨事項の変更を追跡するには、 [リリースノート](release-notes.md)を使用します。

    - **クラウドの Defender アラートが作成またはトリガーされたとき** に、対象となる重大度レベルのアラートのみに関連するようにトリガーをカスタマイズできます。
    
    - **クラウドの規制遵守評価のための Defender が作成またはトリガーされたとき**：規制遵守評価の更新に基づいて自動化をトリガーします。

    > [!NOTE]
    > 従来のトリガー [Microsoft Defender for Cloud アラートへの応答がトリガーされたとき] を使用している場合は、ロジック アプリがワークフローの自動化機能によって起動されません。 代わりに、前述のいずれかのトリガーを使用してください。 

    [![サンプル ロジック アプリ。](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. ロジック アプリを定義したら、ワークフローの自動化の定義ウィンドウに戻ります ([ワークフロー自動化の追加])。 **[Refresh]\(最新の情報に更新\)** をクリックして、新しいロジック アプリが選択可能であることを確認します。

    ![[更新]](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. ロジック アプリを選択し、自動化を保存します。 ロジックアプリのドロップダウンリストには、前述のクラウドコネクタのサポート Defender に関するロジックアプリのみが表示されます。


## <a name="manually-trigger-a-logic-app"></a>ロジック アプリを手動でトリガーする

セキュリティのアラートまたは推奨事項を表示しているときに、ロジック アプリを手動で実行することもできます。

ロジック アプリを手動で実行するには、アラートまたは推奨事項を開き、 **[ロジック アプリのトリガー]** をクリックします。

[![ロジック アプリを手動でトリガーする。](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="configure-workflow-automation-at-scale-using-the-supplied-policies"></a>提供されているポリシーを使用してワークフローの自動化を大規模に構成する

組織の監視とインシデント対応プロセスを自動化すると、セキュリティ インシデントの調査と軽減にかかる時間を大幅に短縮できます。

組織全体に自動化の構成をデプロイするには、後述の提供されている Azure Policy の "DeployIfNotExist" ポリシーを使用して、ワークフローの自動化手順を作成して構成します。

[ワークフローの自動化テンプレート](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)を使用して開始するには、こちらを参照してください。

これらのポリシーを実装するには、次の手順に従います。

1. 次の表から、適用するポリシーを選択します。

    |目標  |ポリシー  |ポリシー ID  |
    |---------|---------|---------|
    |セキュリティ アラートのワークフローの自動化              |[クラウドアラート用に Microsoft Defender のワークフロー自動化を展開する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
    |セキュリティに関する推奨事項のワークフローの自動化     |[クラウドに関する推奨事項に対する Microsoft Defender のワークフロー自動化のデプロイ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
    |規制コンプライアンスの変化に関するワークフローの自動化|[クラウドの規制遵守のために Microsoft Defender のワークフロー自動化を展開する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
    ||||

    > [!TIP]
    > これらは、Azure Policy を検索して見つけることもできます。
    > 1. Azure Policy を開きます。
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Azure Policy にアクセスします。":::
    > 2. Azure Policy のメニューから **[定義]** を選択し、名前で検索します。 

1. 関連する Azure Policy ページで、 **[割り当てる]** を選択します。
    :::image type="content" source="./media/workflow-automation/export-policy-assign.png" alt-text="Azure Policy を割り当てます。":::

1. 各タブを開き、必要に応じてパラメーターを設定します。
    1. **[基本]** タブで、ポリシーのスコープを設定します。 集中管理を使用するには、ワークフローの自動化の構成を使用するサブスクリプションが含まれている管理グループにポリシーを割り当てます。 
    1. **[パラメーター]** タブで、リソース グループとデータ型の詳細を設定します。 
        > [!TIP]
        > 各パラメーターには、使用可能なオプションを説明するツールヒントがあります。
        >
        > Azure Policy の [パラメーター] タブ (1) を使用すると、クラウドのワークフロー自動化ページ (2) の Defender と同様の構成オプションにアクセスできます。
        > :::image type="content" source="./media/workflow-automation/azure-policy-next-to-workflow-automation.png" alt-text="ワークフローの自動化のパラメーターを Azure Policy と比較します。" lightbox="./media/workflow-automation/azure-policy-next-to-workflow-automation.png":::

    1. 必要に応じて、この割り当てを既存のサブスクリプションに適用するには、 **[修復]** タブを開き、修復タスクを作成するためのオプションを選択します。

1. 概要ページを確認し、 **[作成]** を選択します。


## <a name="data-types-schemas"></a>データ型のスキーマ

ロジック アプリ インスタンスに渡されたセキュリティアラートまたはおすすめ候補イベントの未加工イベントスキーマを表示するには、[ワークフロー オートメーション データ型スキーマ](https://aka.ms/ASCAutomationSchemas) を参照してください。 これは、上記のDefender for Cloudのロジックアプリ内蔵コネクタを使用せず、ロジックアプリの汎用HTTPコネクタを使用している場合に便利です。イベントのJSONスキーマを使用して、必要に応じて手動で解析することができます。


## <a name="faq---workflow-automation"></a>FAQ - ワークフロー自動化

### <a name="does-workflow-automation-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>ワークフローの自動化では、ビジネス継続性またはディザスター リカバリー (BCDR) のシナリオはサポートされていますか。

ターゲット リソースに停止やその他の障害が発生する BCDR シナリオに対して環境を準備する場合は、Azure Event Hubs、Log Analytics ワークスペース、ロジック アプリのガイドラインに従ってバックアップを確立することで、データの損失を防ぐ必要があります。

アクティブな自動化ごとに、同じ (無効な) 自動化を作成し、別の場所に保存することをお勧めします。 障害が発生した場合は、これらのバックアップの自動化を有効にし、通常の操作を継続することができます。

詳細については、「[Azure Logic Apps の事業継続とディザスター リカバリー](../logic-apps/business-continuity-disaster-recovery-guidance.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、ロジックアプリの作成、クラウドの Defender での実行の自動化、および手動での実行について説明しました。

関連資料については、以下を参照してください。 

- [ワークフローの自動化を使用してセキュリティ対応を自動化する方法に関する Microsoft Learn モジュール](/learn/modules/resolve-threats-with-azure-security-center/)
- [Microsoft Defender for Cloud のセキュリティに関する推奨事項](review-security-recommendations.md)
- [Microsoft Defender for Cloud のセキュリティアラート](alerts-overview.md)
- [Azure Logic Apps の概要](../logic-apps/logic-apps-overview.md)
- [Azure Logic Apps のコネクタ](../connectors/apis-list.md)
- [ワークフロー オートメーション データ型スキーマ](https://aka.ms/ASCAutomationSchemas)
