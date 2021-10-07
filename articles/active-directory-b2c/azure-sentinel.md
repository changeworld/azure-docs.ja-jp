---
title: Azure Sentinel を使用して Azure AD B2C をセキュリティで保護する
titleSuffix: Azure AD B2C
description: Azure Sentinel での Azure Active Directory B2C データに対するセキュリティ分析の実行に関するチュートリアル
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: ec68ca976b52c50c09bf86c90c56304f05051b66
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740264"
---
# <a name="tutorial-configure-security-analytics-for-azure-active-directory-b2c-data-with-azure-sentinel"></a>チュートリアル: Azure Sentinel で Azure Active Directory B2C のデータに関するセキュリティ分析を構成する

ログと監査情報を Azure Sentinel にルーティングすることで、Azure Active Directory (AD) B2C 環境をさらにセキュリティ保護できます。 Azure Sentinel は、クラウドネイティブのセキュリティ情報イベント管理 (SIEM) およびセキュリティ オーケストレーション自動応答 (SOAR) ソリューションです。 Azure Sentinel では、Azure AD B2C に対してアラートの検出、脅威の可視性、予防的なハンティング、脅威への対応が提供されます。

Azure AD B2C で Azure Sentinel を使用すると、次のことができます。

- Microsoft の分析と類を見ない脅威インテリジェンスを使用して、過去に検出されたことのない脅威を検出し、擬陽性を最小限に抑えます。
- 人工知能を使用して脅威を調査します。 大規模に疑わしいアクティビティを誅求し、Microsoft の長年にわたるサイバーセキュリティ関連の作業を利用します。
- インシデントに迅速に対応します。一般的なタスクの組み込みのオーケストレーションとオートメーションを使用します。
- 組織のセキュリティとコンプライアンスの要件を満たします。

このチュートリアルでは、以下について説明します。

1. [Azure AD B2C ログを Azure Monitor ログ ワークスペースに転送する](#configure-azure-ad-b2c-with-azure-monitor-logs-analytics)
2. [Log Analytics ワークスペースで Azure Sentinel を有効にする](#deploy-an-azure-sentinel-instance)
3. [インシデントをトリガーするサンプル ルールを Azure Sentinel で作成する](#create-an-azure-sentinel-rule)
4. [自動応答を構成する](#automated-response)

## <a name="configure-azure-ad-b2c-with-azure-monitor-logs-analytics"></a>Azure Monitor Logs Analytics を使用して Azure AD B2C を構成する

Azure AD B2C テナント内の Azure AD で **診断設定** を有効にして、リソースのログとメトリックを送信する場所を定義します。

その後、[Azure Monitor にログを送信するように Azure AD B2C を構成します](./azure-monitor.md)。

## <a name="deploy-an-azure-sentinel-instance"></a>Azure Sentinel インスタンスをデプロイする

>[!IMPORTANT]
>Azure Sentinel を有効にするには、Azure Sentinel ワークスペースが存在するサブスクリプションへの **共同作成者のアクセス許可** が必要です。 Azure Sentinel を使用するには、ワークスペースが属しているリソース グループに対する共同作成者または閲覧者のいずれかのアクセス許可が必要です。

Azure Monitor にログを送信するように Azure AD B2C インスタンスを構成したら、Azure Sentinel インスタンスを有効にする必要があります。

1. [Azure portal](https://portal.azure.com) にアクセスします。 Log Analytics ワークスペースを作成するサブスクリプションを選択します。

2. **Azure Sentinel** を検索して選択します。

3. **[追加]** を選択します。

![画像には、Azure portal での Azure Sentinel の検索が示されています](./media/azure-sentinel/azure-sentinel-add.png)

4. 新しいワークスペースを選択します。

![画像では、Sentinel のワークスペースが選択されています](./media/azure-sentinel/create-new-workspace.png)

5. **[Add Azure Sentinel]\(Azure Sentinel の追加\)** を選択します。

>[!NOTE]
>複数のワークスペースで [Azure Sentinel を実行](../sentinel/quickstart-onboard.md)できますが、データは 1 つのワークスペースに分離されます。

## <a name="create-an-azure-sentinel-rule"></a>Azure Sentinel のルールを作成する

>[!NOTE]
>Azure Sentinel には、Microsoft のセキュリティの専門家とアナリストのチームによって設計された脅威検出ルールを作成するのに役立つ、すぐに使用できる組み込みテンプレートが用意されています。 これらのテンプレートから作成されるルールにより、データ全体にわたって疑わしいアクティビティが自動的に検索されます。 現時点で、Azure AD B2C で使用可能なネイティブ コネクタはありません。 このチュートリアルの例では、独自のルールを作成します。

Azure Sentinel を有効にしたので、Azure AD B2C テナントで何か疑わしいことが発生したときに通知を受け取ります。

[カスタム分析ルール](../sentinel/detect-threats-custom.md)を作成すると、環境内に存在する脅威や異常動作を検出できます。 これらのルールでは、特定のイベントまたはイベントのセットを検索し、特定のイベントのしきい値または条件に達したときにアラートを生成します。 その後、さらに調査するためにインシデントを生成します。

次の例で説明するシナリオでは、自分の環境に誰かがむりやりアクセスしようとして失敗した場合に通知を受け取ります。 それは、ブルートフォース攻撃の可能性があります。 60 秒以内にログインが 2 回以上成功しなかった場合に通知を受け取ります。

1. Azure Sentinel のナビゲーション メニューから **[分析]** を選択します。

2. 上部のアクション バーで、 **[+ 作成]** を選択し、 **[スケジュール済みクエリ ルール]** を選択します。 これによって **分析ルール ウィザード** が開きます。

![画像には、スケジュール済みクエリ ルールの作成の選択が示されています](./media/azure-sentinel/create-scheduled-rule.png)

3. 分析ルール ウィザードで、 **[全般]** タブに移動します。

   | フィールド | 値 |
   |:--|:--|
   |Name | B2C の失敗したログイン |
   |説明 | 60 秒以内にログインが 2 回以上失敗したら通知する |
   | 方針 | ルールを分類する攻撃のカテゴリを選択します。 これらのカテゴリは、[MITRE ATT&CK](https://attack.mitre.org/) フレームワークの方針に基づいています。<BR>この例では、`PreAttack` を選択します <BR> MITRE ATT&CK® は、グローバルにアクセスできる、実際の世界の観測に基づいた敵対者の方針と手法のナレッジ ベースです。 ATT&CK のナレッジ ベースは、具体的な脅威モデルと手法を開発するための基盤として使用されています。
   | 重大度 | 適切なもの |
   | Status | ルールを作成すると、その [状態] は既定で [`Enabled`] になります。これは、作成が終わるとすぐに実行されることを意味します。 すぐに実行したくない場合は、[`Disabled`] を選択します。ルールは [アクティブな規則] タブに追加され、必要に応じてそこから有効にすることができます。|

![画像では、基本的なルールのプロパティが提供されています](./media/azure-sentinel/create-new-rule.png)

4.  ルールのクエリ ロジックを定義し、設定を構成するには、 **[ルールのロジックを設定]** タブで、 **[ルールのクエリ]** フィールドにクエリを直接記述します。 このクエリは、Azure AD B2C テナントに対して成功しなかったログインが 60 秒間に 2 回以上ある場合にアラートを生成し、`UserPrincipalName` で整理します。

![画像には、ロジック タブでのルールのクエリの入力が示されています](./media/azure-sentinel/rule-query.png)

[クエリのスケジュール設定] セクションで、次のパラメーターを設定します。

![画像では、クエリのスケジュール設定パラメーターが設定されています](./media/azure-sentinel/query-scheduling.png)

5. **[Next:Incident settings (Preview)]\(次: インシデントの設定 (プレビュー)\)** を選択します。 後で自動応答を構成して追加します。

6. **[確認と作成]** タブに移動し、新しいアラート ルールのすべての設定を確認します。 "**検証に成功しました**" というメッセージが表示されたら、 **[作成]** を選択してアラート ルールを初期化します。

![ルールの確認と作成の画像](./media/azure-sentinel/review-create.png)

7. 生成されたルールとインシデントを表示します。 メインの **[分析]** 画面の **[アクティブなルール]** タブの下のテーブルで、新しく作成された **[スケジュール設定]** という種類のカスタム ルールを見つけます。 この一覧から、ルールの **編集**、**有効化**、**無効化**、または **削除** を行うことができます。

![ルールの編集、有効化、無効化、または削除のオプションを表示している分析画面の画像](./media/azure-sentinel/rule-crud.png)

8. 新しい Azure AD B2C の失敗ログイン ルールの結果を表示します。 **[インシデント]** ページに移動します。ここでは、脅威のトリアージ、調査、修復を行うことができます。 インシデントには複数のアラートを含めることができます。 ケースは、特定の調査に関連するすべての証拠を集計したものです。 重大度や状態などのプロパティは、インシデント レベルで設定できます。

>[!Note]
>Azure Sentinel の重要な機能は、[インシデントの調査](../sentinel/investigate-cases.md)です。

9. 調査を開始するには、特定のインシデントを選択します。 右側に、その重大度、関連するエンティティ、このインシデントをトリガーした生イベント、インシデントの一意の ID など、インシデントの詳細情報が表示されます。

![image alt-text="インシデント画面](./media/azure-sentinel/select-incident.png)

10. インシデントのページで **[すべての詳細を表示]** を選択し、インシデントの情報がまとめられていて詳細が提供されている関連タブを確認します。

![画像ルール 73](./media/azure-sentinel/full-details.png)

11. **[証拠]**  >  **[イベント]**  >  **[Link to Log Analytics]\(Log Analytics へのリンク\)** を選択します。 結果には、試行回数だけログインを試みている ID の `UserPrincipalName` が表示されます。

![選択したインシデントの詳細の画像](./media/azure-sentinel/logs.png)

## <a name="automated-response"></a>自動応答

Azure Sentinel では、[堅牢な SOAR 機能](../sentinel/automation-in-azure-sentinel.md)が提供されます。 Azure Sentinel のプレイブックと呼ばれる自動化されたアクションを、要件に合わせて分析ルールにアタッチできます。

この例では、ルールによってインシデントが作成されたときのメール通知を追加します。 [Sentinel GitHub リポジトリの既存のプレイブック](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification)を使用して、このタスクを実行します。 プレイブックを構成した後、既存のルールを編集し、[自動化] タブでプレイブックを選択します。

![ルールに関連付けられている自動応答の構成画面の画像](./media/azure-sentinel/automation-tab.png)

## <a name="next-steps"></a>次の手順

- [Azure Sentinel での偽陽性の処理](../sentinel/false-positives.md)

- [サンプル ブック](https://github.com/azure-ad-b2c/siem#workbooks)

- [Azure Sentinel のドキュメント](../sentinel/index.yml)