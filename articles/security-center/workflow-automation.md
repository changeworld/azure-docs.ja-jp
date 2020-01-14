---
title: Azure Security Center のワークフローの自動化 (プレビュー) | Microsoft Docs
description: Azure Security Center でワークフローを作成して自動化する方法について説明します。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 57351ccf0c6155a1a3532ec9e6481a724e3219aa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462448"
---
# <a name="workflow-automation-preview"></a>ワークフローの自動化 (プレビュー)

すべてのセキュリティ プログラムには、インシデント対応のための複数のワークフローが含まれています。 これらのプロセスには、直接の利害関係者への通知、変更管理プロセスの開始、および特定の修復手順の適用が含まれます。 これらのプロシージャの手順をできるだけ多く自動化することがセキュリティの専門家によって推奨されています。 自動化によってオーバーヘッドが削減されます。 また、迅速かつ一貫した方法で、定義済みの要件に従ってプロセスの手順が実行されるようにすることで、セキュリティを向上させることもできます。

この記事では、Azure Security Center のワークフローの自動化機能 (プレビュー) について説明します。 このプレビュー機能は、セキュリティ アラートと推奨事項が出されたときにロジック アプリをトリガーできます。 たとえば、アラートが発生したときに Security Center から特定のユーザーに電子メールが送信されるようにすることができます。 また、[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) を使用してロジック アプリを作成する方法についても説明します。

> [!NOTE]
> サイドバーでプレイブック (プレビュー) ビューを以前に使用していた場合は、新しいワークフローの自動化 (プレビュー) のページに、拡張された機能と共に同じ機能が表示されます。


## <a name="requirements"></a>必要条件

* Azure Logic Apps のワークフローを操作するには、次のロジック アプリのロール/アクセス許可が必要です。

    * [ロジック アプリのオペレーター](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator)のアクセス許可が必要。または、ロジック アプリの読み取り/トリガーのアクセス権 (このロールでは、ロジック アプリを作成したり編集したりすることはできません。既存のものを*実行*するだけです)

    * [ロジック アプリの共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor)のアクセス許可は、ロジック アプリの作成と変更に必要です

* ロジック アプリのコネクタを使用する場合は、それぞれのサービス (たとえば、Outlook/Teams/Slack のインスタンス) にサインインするために追加の資格情報が必要になることがあります。


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>ロジック アプリを作成し、自動的に実行するタイミングを定義する 

1. Security Center のサイドバーで、 **[Workflow automation (Preview)]\(ワークフローの自動化 (プレビュー)\)** を選択します。

    [![ワークフローの自動化の一覧](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    このページで、新しい自動化ルールを作成したり、既存のものを有効化、無効化、または削除したりすることができます。  
1. 新しいワークフローを定義するには、 **[Add workflow automation]\(ワークフローの自動化の追加\)** をクリックします。 

    新しい自動化のオプションを含むウィンドウが表示されます。 ここで、以下を入力できます。
    1. 自動化の名前と説明。
    1. この自動ワークフローを開始するトリガー。 たとえば、"SQL" を含むセキュリティ警告が生成されたときにロジック アプリを実行することができます。
    1. トリガー条件が満たされたときに実行されるロジック アプリ。 

        [![ワークフローの自動化の一覧](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. [Actions]\(アクション\) セクションで、 **[Create a new one]\(新規作成する\)** をクリックして、ロジック アプリの作成プロセスを開始します。

    Azure Logic Apps が表示されます。

    [![新しいロジック アプリの作成](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. 名前、リソース グループ、および場所を入力し、 **[Create]\(作成\)** をクリックします。

1. 新しいロジック アプリでは、セキュリティ カテゴリの組み込みの定義済みテンプレートから選択できます。 または、このプロセスがトリガーされたときに発生するイベントのカスタム フローを定義することもできます。

    ロジック アプリ デザイナーでは、Security Center コネクタからの次のトリガーがサポートされています。

    * **When an Azure Security Center Recommendation is created or triggered (プレビュー)** (Azure Security Center の推奨事項が作成またはトリガーされたとき)
    * **When an Azure Security Center Alert is created or triggered (プレビュー)** (Azure Security Center のアラートが作成またはトリガーされたとき)
    
    > [!NOTE]
    > レガシー トリガーの [When a response to an Azure Security Center alert is triggered]\(Azure Security Center アラートへの応答がトリガーされるとき\) を使用している場合、ロジック アプリはワークフローの自動化機能によって起動されません。 代わりに、前述のいずれかのトリガーを使用してください。 

    [![サンプル ロジック アプリ](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. ロジック アプリを定義したら、ワークフローの自動化の定義ウィンドウに戻ります ([Add workflow automation]\(ワークフローの自動化の追加\))。 **[Refresh]\(最新の情報に更新\)** をクリックして、新しいロジック アプリが選択可能であることを確認します。

    ![更新](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. ロジック アプリを選択し、自動化を保存します。 ロジック アプリ ドロップダウンには、Logic Apps とそれをサポートする前述の Security Center コネクタのみが表示されることにご注意ください。


## <a name="manually-trigger-a-logic-app"></a>ロジック アプリを手動でトリガーする

セキュリティの推奨事項を表示しているときに、ロジック アプリを手動で実行することもできます。

ロジック アプリを手動で実行するには、推奨事項を開き、[Trigger Logic App (Preview)]\(ロジック アプリのトリガー (プレビュー)\) をクリックします。

[![ロジック アプリを手動でトリガーする](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>データ型のスキーマ

ロジック アプリ インスタンスに渡されたセキュリティアラートまたはおすすめ候補イベントの未加工イベントスキーマを表示するには、[ワークフロー オートメーション データ型スキーマ](https://aka.ms/ASCAutomationSchemas) を参照してください。 これは、上記で説明した、 Security Center の組み込みロジック アプリ コネクタを使用していない場合に便利です。この場合には、ロジックアプリの汎用 HTTP コネクタを使用します。すなわち、イベントの JSON スキーマを使用して、必要に応じて手動で解析することができます。

## <a name="next-steps"></a>次のステップ

この記事では、ロジック アプリの作成、Security Center での手動実行、およびそれらの実行の自動化について学習しました。 

その他の関連資料については、次の記事を参照してください。 

- [Azure Security Center でのセキュリティに関する推奨事項](security-center-recommendations.md)
- [Security alerts in Azure Security Center](security-center-alerts-overview.md)
- [Azure Logic Apps の概要](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Logic Apps コネクタ](https://docs.microsoft.com/connectors/)
- [ワークフロー オートメーション データ型スキーマ](https://aka.ms/ASCAutomationSchemas)
