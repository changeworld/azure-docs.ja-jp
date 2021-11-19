---
title: Microsoft Sentinel を使用して Azure AD B2C をセキュリティで保護する
titleSuffix: Azure AD B2C
description: このチュートリアルでは、Microsoft Sentinel を使用して Azure Active Directory B2C データに対するセキュリティ分析を実行します。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 5afd5535dc9bd9a6f0dce507e6279b664c90fe8e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281421"
---
# <a name="tutorial-configure-security-analytics-for-azure-active-directory-b2c-data-with-microsoft-sentinel"></a>チュートリアル: Microsoft Sentinel で Azure Active Directory B2C のデータに関するセキュリティ分析を構成する

ログと監査情報を Microsoft Sentinel にルーティングすることで、Azure Active Directory B2C (Azure AD B2C) 環境をさらにセキュリティで保護できます。 Microsoft Sentinel は、クラウドネイティブの SIEM (セキュリティ情報イベント管理) と SOAR (セキュリティ オーケストレーション、自動化、および対応) ソリューションです。 Microsoft Sentinel では、Azure AD B2C に対してアラートの検出、脅威の可視性、予防的なハンティング、脅威への対応が提供されます。

Azure AD B2C で Microsoft Sentinel を使用すると、次のことができます。

- Microsoft の分析と脅威インテリジェンスを使用して、過去に検出されたことのない脅威を検出し、擬陽性を最小限に抑えます。
- AI を使って脅威を調査します。 疑わしいアクティビティを大規模に探索し、Microsoft の長年にわたるサイバーセキュリティ関連の取り組みを活用します。
- インシデントに迅速に対応します。一般的なタスクの組み込みのオーケストレーションとオートメーションを使用します。
- 組織のセキュリティとコンプライアンスの要件を満たします。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C ログを Log Analytics ワークスペースに転送する。
> * Log Analytics ワークスペースで Microsoft Sentinel を有効にする。
> * インシデントをトリガーするサンプル ルールを Microsoft Sentinel で作成する。
> * 自動応答を構成する。

## <a name="configure-azure-ad-b2c-with-azure-monitor-log-analytics"></a>Azure Monitor Logs Analytics を使用して Azure AD B2C を構成する

リソースのログとメトリックの送信先を定義するには、Azure AD B2C テナント内の Azure AD で **[診断設定]** を有効にします。 その後、[Azure Monitor にログを送信するように Azure AD B2C を構成します](./azure-monitor.md)。

## <a name="deploy-a-microsoft-sentinel-instance"></a>Microsoft Sentinel インスタンスをデプロイする

Azure Monitor にログを送信するように Azure AD B2C インスタンスを構成したら、Microsoft Sentinel インスタンスを有効にする必要があります。

>[!IMPORTANT]
>Microsoft Sentinel を有効にするには、Microsoft Sentinel ワークスペースが存在するサブスクリプションへの共同作成者のアクセス許可が必要です。 Microsoft Sentinel を使用するには、ワークスペースが属しているリソース グループに対する共同作成者または閲覧者のいずれかのアクセス許可が必要です。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。 Log Analytics ワークスペースを作成するサブスクリプションを選択します。

2. **Microsoft Sentinel** を検索して選択します。

   ![Azure portal での Microsoft Sentinel の検索を示すスクリーンショット。](./media/azure-sentinel/azure-sentinel-add.png)

3. **[追加]** を選択します。

4. 新しいワークスペースを選択します。

   ![Microsoft Sentinel ワークスペースを選択する場所を示すスクリーンショット。](./media/azure-sentinel/create-new-workspace.png)

5. **[Microsoft Sentinel の追加]** を選びます。

>[!NOTE]
>複数のワークスペースで [Microsoft Sentinel を実行](../sentinel/quickstart-onboard.md)できますが、データは 1 つのワークスペースに分離されます。

## <a name="create-a-microsoft-sentinel-rule"></a>Microsoft Sentinel ルールを作成する

Microsoft Sentinel を有効にしたので、Azure AD B2C テナントで何か疑わしいことが発生したときに通知を受け取ります。

[カスタム分析ルール](../sentinel/detect-threats-custom.md)を作成すると、環境内の脅威や異常動作を検出できます。 これらのルールでは、特定のイベントまたはイベントのセットが検索され、特定のイベントのしきい値または条件に達したときにアラートが生成されます。 その後、さらに調査するためにインシデントが生成されます。

>[!NOTE]
>Microsoft Sentinel には、Microsoft のセキュリティの専門家とアナリストのチームによって設計された、脅威検出ルールの作成に役立つ組み込みテンプレートが用意されています。 これらのテンプレートから作成されるルールにより、データ全体にわたって疑わしいアクティビティが自動的に検索されます。 現時点で、Azure AD B2C で使用可能なネイティブ コネクタはありません。 このチュートリアルの例では、独自のルールを作成します。

次の例では、何者かが環境に強制的にアクセスしようとしたが失敗した場合に通知を受け取ります。 それは、ブルート フォース攻撃の可能性があります。 60 秒以内にログインが 2 回以上成功しなかった場合に通知を受け取ります。

1. Microsoft Sentinel の左側のメニューから **[分析]** を選択します。

2. 上部のアクション バーで、 **[+ 作成]**  >  **[スケジュール済みクエリ ルール]** を選択します。 

    ![スケジュール済みクエリ ルールを作成するための選択を示すスクリーンショット。](./media/azure-sentinel/create-scheduled-rule.png)

3. 分析ルール ウィザードで、 **[全般]** タブにアクセスし、次の情報を入力します。

    | フィールド | 値 |
    |:--|:--|
    |**名前** | Azure AD B2C の失敗したログインに適切な名前を入力します。 |
    |**説明** | このルールでは 60 秒以内に 2 回以上のログインが失敗した場合に通知することを示す説明を入力します。 |
    | **方針** | ルールを分類する攻撃のカテゴリを選択します。 これらのカテゴリは、[MITRE ATT&CK](https://attack.mitre.org/) フレームワークの方針に基づいています。<BR>たとえば、 **[PreAttack]\(攻撃前\)** を選択します。 <BR> MITRE ATT&CK は、グローバルにアクセスできる、実際の世界の観測に基づいた敵対者の方針と手法に関するナレッジ ベースです。 このナレッジ ベースは、具体的な脅威モデルと手法を開発するための基盤として使用されています。
    | **重大度** | 適切な重要度レベルを選択します。 |
    | **状態** | ルールを作成すると、その状態は既定で **[有効]** になります。 この状態は、作成が完了した直後にルールが実行されることを意味します。 すぐに実行しない場合は、 **[無効]** を選択します。 その場合、ルールは **[Active rules]\(アクティブなルール\)** タブに追加され、必要に応じてそこから有効にできます。|

    ![基本的なルール プロパティを示すスクリーンショット。](./media/azure-sentinel/create-new-rule.png)

4. ルールのクエリ ロジックを定義し、設定を構成するには、 **[ルールのロジックを設定]** タブで、 **[ルールのクエリ]** ボックスにクエリを直接記述します。 

    ![ルール ロジックを設定するためのタブにルール クエリを入力しているのを示すスクリーンショット。](./media/azure-sentinel/rule-query.png)

    このクエリでは、Azure AD B2C テナントに対して成功しなかったログインが 60 秒間に 2 回以上ある場合にアラートが生成されます。 ログインは、`UserPrincipalName` 別に整理されます。

5. **[クエリのスケジュール設定]** セクションで、次のパラメーターを設定します。

    ![クエリのスケジュール パラメーターの設定を示すスクリーンショット。](./media/azure-sentinel/query-scheduling.png)

6. **[Next:Incident settings (Preview)]\(次: インシデントの設定 (プレビュー)\)** を選択します。 後で自動応答を構成して追加します。

7. **[確認と作成]** タブに移動し、新しいアラート ルールのすべての設定を確認します。 "**検証に成功しました**" というメッセージが表示されたら、 **[作成]** を選択してアラート ルールを初期化します。

    ![ルールを確認および作成するためのタブを示すスクリーンショット。](./media/azure-sentinel/review-create.png)

8. ルールと生成されるインシデントを表示します。 メインの **[分析]** 画面の **[アクティブなルール]** タブの下のテーブルで、新しく作成された **[スケジュール設定]** という種類のカスタム ルールを見つけます。 この一覧から、対応するボタンを使用してルールを編集、有効化、無効化、または削除できます。

    ![アクティブなルールと、編集、有効化、無効化、または削除のオプションを示すスクリーンショット。](./media/azure-sentinel/rule-crud.png)

9. Azure AD B2C の失敗したログインに関する新しいルールの結果を表示します。 **[インシデント]** ページに移動します。ここでは、脅威のトリアージ、調査、修復を行うことができます。 

    インシデントには複数のアラートを含めることができます。 ケースは、特定の調査に関連するすべての証拠を集計したものです。 重大度や状態などのプロパティは、インシデント レベルで設定できます。

    > [!NOTE]
    > Microsoft Sentinel の重要な機能は、[インシデントの調査](../sentinel/investigate-cases.md)です。
    
10. 調査を開始するには、特定のインシデントを選択します。 

    右側に、インシデントの詳細情報が表示されます。 この情報には、重大度、関連するエンティティ、このインシデントをトリガーした未加工イベント、インシデントの一意の ID などが含まれます。

    ![インシデント情報を示すスクリーンショット。](./media/azure-sentinel/select-incident.png)

11. [インシデント] ペインで **[View full details]\(すべての詳細を表示\)** を選択します。 インシデント情報の概要と詳細情報を示すタブを確認します。

    ![インシデント情報のタブを示すスクリーンショット。](./media/azure-sentinel/full-details.png)

12. **[証拠]**  >  **[イベント]**  >  **[Link to Log Analytics]\(Log Analytics へのリンク\)** を選択します。 結果には、ログインを試行している ID の `UserPrincipalName` 値と試行回数が表示されます。

    ![選択されたインシデントのすべての詳細情報を示すスクリーンショット。](./media/azure-sentinel/logs.png)

## <a name="automated-response"></a>自動応答

Microsoft Sentinel では、[堅牢な SOAR 機能](../sentinel/automation-in-azure-sentinel.md)が提供されます。 Microsoft Sentinel のプレイブックと呼ばれる自動化されたアクションを、要件に合わせて分析ルールにアタッチできます。

この例では、ルールによって作成されるインシデントのメール通知を追加します。 このタスクを実行するには、[Microsoft Sentinel GitHub リポジトリにある既存のプレイブック](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification)を使用します。 プレイブックを構成したら、既存のルールを編集し、 **[自動応答]** タブでプレイブックを選択します。

![ルールに関連付けられている自動応答の構成画面の画像を示すスクリーンショット。](./media/azure-sentinel/automation-tab.png)

## <a name="related-information"></a>関連情報

Microsoft Sentinel と Azure AD B2C の詳細については、以下を参照してください。

- [サンプル ブック](https://github.com/azure-ad-b2c/siem#workbooks)

- [Microsoft Sentinel のドキュメント](../sentinel/index.yml)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Microsoft Sentinel での擬陽性の処理](../sentinel/false-positives.md)
