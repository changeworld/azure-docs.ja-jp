---
title: Azure Sentinel を使用して Azure AD B2C をセキュリティで保護する
titleSuffix: Azure AD B2C
description: このチュートリアルでは、Azure Sentinel を使用して Azure AD B2C のセキュリティ分析を実行する方法を示します。
services: active-directory-b2c
author: agabrielle
manager: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: agher
ms.subservice: B2C
ms.date: 07/19/2021
ms.openlocfilehash: a405cb97a021c05a3b0c6aa004d3f92ce4657d24
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178072"
---
# <a name="tutorial-how-to-perform-security-analytics-for-azure-ad-b2c-data-with-azure-sentinel"></a>チュートリアル: Azure Sentinel を使用して Azure AD B2C データのセキュリティ分析を実行する方法

ログと監査情報を Azure Sentinel にルーティングすることで、Azure AD B2C 環境をさらにセキュリティで保護できます。 Azure Sentinel は、クラウドネイティブの **セキュリティ情報イベント管理 (SIEM) およびセキュリティ オーケストレーション自動応答 (SOAR)** ソリューションです。 Azure Sentinel では、**Azure AD B2C** に対してアラートの検出、脅威の可視性、予防的なハンティング、脅威への対応が提供されます。

Azure Sentinel を Azure AD B2C と組み合わせて利用することで、以下を行うことができます。

- 過去に検出されたことのない脅威を検出します。Microsoft の分析と類を見ない脅威インテリジェンスを使用して、誤判定を最小限に抑えます。
- 人工知能を使用して脅威を調査します。Microsoft の長年にわたるサイバー セキュリティ業務を活用しながら、大規模に疑わしいアクティビティを捜索します。
- インシデントに迅速に対応します。一般的なタスクの組み込みのオーケストレーションとオートメーションを使用します。
- 組織のセキュリティとコンプライアンスの要件を満たします。

このチュートリアルでは、次のことについて説明します。

1. B2C ログを Azure Monitor ログ ワークスペースに転送する方法。
1. Log Analytics ワークスペースで **Azure Sentinel** を有効にします。
1. インシデントをトリガーするサンプル ルールを Sentinel に作成します。
1. 最後に、自動応答を構成します。

## <a name="configure-aad-b2c-with-azure-monitor-logs-analytics"></a>Azure Monitor Logs Analytics を使用して AAD B2C を構成する

次の手順では、Azure AD B2C テナント内の Azure Active Directory で "**_診断設定_**" を有効にするプロセスを実行します。
診断設定では、リソースのログとメトリックを送信する場所を定義します。

「[Azure Monitor で Azure AD B2C を監視する](./azure-monitor.md)」の手順 **1 から 5** に従って、ログを Azure Monitor に送信するように Azure AD B2C を構成します。

## <a name="deploy-an-azure-sentinel-instance"></a>Azure Sentinel インスタンスをデプロイする

> [!IMPORTANT]
> Azure Sentinel を有効にするには、Azure Sentinel ワークスペースが存在するサブスクリプションへの **共同作成者のアクセス許可** が必要です。 Azure Sentinel を使用するには、ワークスペースが属しているリソース グループに対する共同作成者または閲覧者のいずれかのアクセス許可が必要です。

Azure Monitor にログを送信するように Azure AD B2C インスタンスを構成したら、Azure Sentinel インスタンスを有効にする必要があります。

1. Azure portal にサインインします。 LA (ログ分析) が前の手順で作成されたワークスペースであるサブスクリプションが選択されていることを確認します。

2. **Azure Sentinel** を検索して選択します。

3. **[追加]** を選択します。

   :::image type="content" source="./media/azure-sentinel/azure-sentinel-add.png" alt-text="Azure portal 内で Azure Sentinel を検索する":::

4. 前の手順で使用したワークスペースを選択します。

   :::image type="content" source="./media/azure-sentinel/create-new-workspace.png" alt-text="sentinel ワークスペースを選択する":::

5. **[Add Azure Sentinel]\(Azure Sentinel の追加\)** を選択します。

   > [!NOTE]
   > 複数のワークスペースで Azure Sentinel を実行できますが、データは 1 つのワークスペースに分離されます。 Sentinel の有効化の詳細については、こちらの[クイックスタート](../sentinel/quickstart-onboard.md)を参照してください。

## <a name="create-a-sentinel-rule"></a>sentinel ルールの作成

> [!NOTE]
> Azure Sentinel には、Microsoft のセキュリティの専門家とアナリストのチームによって設計された脅威検出ルールを作成するのに役立つ、すぐに使用できる組み込みテンプレートが用意されています。 これらのテンプレートから作成されるルールにより、データ全体にわたって疑わしいアクティビティが自動的に検索されます。 現在、ネイティブの Azure AD B2C コネクタはないため、この例ではネイティブ ルールを使用しません。 このチュートリアルでは、独自のルールを作成します。

Sentinel を有効にしたので、B2C テナント内で何か疑わしいことが起こったときに通知を受け取る必要があります。

カスタム分析ルールを作成すると、環境内に存在する脅威や異常動作の検出に役立てることができます。 これらのルールでは、特定のイベントまたはイベントのセットを検索し、特定のイベントのしきい値または条件に達したときにアラートを生成して、さらに調査するためにインシデントを生成します。

> [!NOTE]
> 分析ルールの詳細なレビューについては、こちらの[チュートリアル](/azure/active-directory-b2c/articles/sentinel/detect-threats-custom.md)を参照してください。

このシナリオでは、だれかが環境に強制的にアクセスしようとしているが成功していない場合に通知を受け取る必要があります。これはブルートフォース攻撃を意味する可能性があるので、**_60 秒以内に 2 回以上の成功していないログイン_** について通知を受け取ります

1. Azure Sentinel のナビゲーション メニューから **[分析]** を選択します。
2. 上部のアクション バーで、 **[+ 作成]** を選択し、 **[スケジュール済みクエリ ルール]** を選択します。 **分析ルール ウィザード** が開きます。

   :::image type="content" source="./media/azure-sentinel/create-scheduled-rule.png" alt-text="スケジュールされたクエリ ルールの作成を選択する":::

3. 分析ルール ウィザード - [全般] タブ

   - 一意の **[名前]** と **[説明]** を指定します
     - **名前**: _B2C の成功しなかったログイン_ **説明**: _成功しないログインが 60 秒間に 2 回以上あった場合に通知する_
   - **[方針]** フィールドでは、ルールを分類する攻撃のカテゴリを選択できます。 これらは、[MITRE ATT&CK](https://attack.mitre.org/) フレームワークの方針に基づいています。

     - たとえば、 _[PreAttack]\(攻撃前\)_ を選択します

       > [!Tip]
       > MITRE ATT&CK® は、グローバルにアクセスできる、実際の世界の観測に基づいた敵対者の方針と手法のナレッジ ベースです。 ATT&CK のナレッジ ベースは、具体的な脅威モデルと手法を開発するための基盤として使用されています。

   - 必要に応じて、アラートの **[重大度]** を設定します。
     - これが最初のルールなので、 _[高]_ を選択します。 ルールは後で変更できます
   - ルールを作成すると、その **[状態]** は既定で **[有効]** になります。これは、作成が終わるとすぐに実行されることを意味します。 すぐに実行したくない場合は、 **[無効]** を選択します。ルールは **[アクティブな規則]** タブに追加され、必要に応じてそこから有効にすることができます。

     :::image type="content" source="./media/azure-sentinel/create-new-rule.png" alt-text="基本的な規則のプロパティを指定する":::

4. ルールのクエリ ロジックを定義して設定を構成します。

   **[ルールのロジックを設定]** タブで、 **[ルールのクエリ]** フィールドにクエリを直接記述します。 このクエリは、B2C テナントに対して成功しなかったログインが 60 秒間に 2 回以上ある場合にアラートを生成し、_UserPrincipalName_ で整理します

   ```kusto
   SigninLogs
   | where ResultType != "0"
   | summarize Count = count() by bin(TimeGenerated, 60s), UserPrincipalName
   | project Count = toint(Count), UserPrincipalName
   | where Count >= 1
   ```

   :::image type="content" source="./media/azure-sentinel/rule-query.png" alt-text="ロジック タブにルールのクエリを入力する":::

   [クエリのスケジュール設定] セクションで、次のパラメーターを設定します。

   :::image type="content" source="./media/azure-sentinel/query-scheduling.png" alt-text="クエリのスケジュール設定パラメーターを設定する":::

5. **[Incident Settings (Preview)]\(インシデントの設定 (プレビュー)\)** と **[自動応答]** で [次へ] をクリックします。 後で自動応答を構成および追加します。

6. [次へ] をクリックして **[確認と作成]** タブに進み、新しいアラート ルールのすべての設定を確認します。 "検証に成功しました" というメッセージが表示されたら、 **[作成]** を選択してアラート ルールを初期化します。

   :::image type="content" source="./media/azure-sentinel/review-create.png" alt-text="ルールの確認と作成":::

7. 生成されたルールとインシデントを表示します。

   新しく作成されたカスタムルール (種類は "スケジュールされた") は、メインの **[分析]** 画面の **[アクティブなルール]** タブの下のテーブルにあります。 この一覧から、ルールを "**_編集_**"、"**_有効化_**"、"**_無効化_**"、または "**_削除_**" できます。

   :::image type="content" source="./media/azure-sentinel/rule-crud.png" alt-text="ルールの編集、有効化、無効化、または削除のオプションを表示している分析画面":::

   新しい B2C の成功しなかったログイン ルールの結果を表示するには、 **[インシデント]** ページに移動します。ここでは、脅威のトリアージ、調査、修復を行うことができます。

   インシデントには複数のアラートを含めることができます。 ケースは、特定の調査に関連するすべての証拠を集計したものです。 重大度や状態などのプロパティは、インシデント レベルで設定できます。

   > [!NOTE]
   > インシデント調査の詳細なレビューについては、[こちらのチュートリアル](/azure/active-directory-b2c/articles/sentinel/investigate-cases.md)を参照してください

   調査を開始するには、特定のインシデントを選択します。 右側に、その重大度、関連するエンティティ、このインシデントをトリガーした生イベント、インシデントの一意の ID など、インシデントの詳細情報が表示されます。

   :::image type="content" source="./media/azure-sentinel/select-incident.png" alt-text="インシデント画面":::

   インシデント内のアラートとエンティティの詳細を表示するには、インシデントのページで **[View full details]\(完全な詳細の表示\)** を選択し、インシデントの情報がまとめられている関連タブを確認します

   :::image type="content" source="./media/azure-sentinel/full-details.png" alt-text="ルール 73":::

   インシデントの詳細をさらに確認するには、 **[証拠] -> [イベント]** または **[イベント] -> [Link to Log Analytics]\(Log Analytics へのリンク\)** を選択します

   結果には、試行の "_回数_" ログインを試みている ID の _UserPrincipalName_ が表示されます。

   :::image type="content" source="./media/azure-sentinel/logs.png" alt-text="選択したインシデントの詳細":::

## <a name="automated-response"></a>自動応答

Azure Sentinel には、堅牢な SOAR 機能も用意されています。詳細については、[こちら](../sentinel/automation-in-azure-sentinel.md)で Sentinel の公式ドキュメントを参照してください。

Sentinel のプレイブックと呼ばれる自動化されたアクションを、要件に合わせて分析ルールにアタッチできます。

この例では、ルールによって作成されたインシデントの発生時の電子メール通知を追加します。

このタスクを実行するには、Sentinel GitHub リポジトリ [Incident-Email-Notification](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification) からの既存のプレイブックを使用します

プレイブックを構成した後、必要なのは既存のルールを編集し、[自動化] タブでプレイブックを選択することだけです。

:::image type="content" source="./media/azure-sentinel/automation-tab.png" alt-text="ルールに関連付けられている自動応答の構成画面":::

## <a name="next-steps"></a>次のステップ

- 規則は完璧ではないので、必要に応じて、偽陽性を除外するルール クエリを更新できます。 詳細については、「[Azure Sentinel での偽陽性の処理](../sentinel/false-positives.md)」を参照してください

- データ分析と充実したビジュアル レポートの作成に役立てるために、データに基づいて分析情報を明らかにする優れた設計のブックのギャラリーから選択してダウンロードしてください。 [これらのブック](https://github.com/azure-ad-b2c/siem#workbooks)は、ニーズに合わせて簡単にカスタマイズできます。

- [Azure sentinel のドキュメント](../sentinel/index.yml)の Sentinel に関する詳細情報