---
title: Azure portal で Logic Apps を管理する
description: Azure portal を使用して、Logic Apps を編集、有効化、無効化、または削除します。
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: d80972cd200b8f85e14d316c4c06a38f88ac81b5
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598166"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Azure portal で Logic Apps を管理する

Logic Apps は 、[Azure portal](https://portal.azure.com) または [Visual Studio](manage-logic-apps-with-visual-studio.md) を使用して管理できます。 この記事では、Azure portal で、Logic Apps を編集、有効化、無効化、または削除する方法について説明します。 Azure Logic Apps を初めて使用する場合は、「[Azure Logic Apps とは](logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 既存の Logic Apps。 Azure portal を使用して Logic Apps を作成する方法を学習するには、「[クイック スタート: Azure Logic Apps を使用して初めてのワークフローを作成する - Azure portal](quickstart-create-first-logic-app-workflow.md)」を参照してください。

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Logic Apps を検索する

Logic Apps を検索して開くには、次の手順を行います。

1. Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure の検索バーに「`logic apps`」と入力して **[Logic Apps]** を選択します。

   !["Logic Apps" を検索して選択する](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. **[Logic Apps]** ページで、管理する Logic Apps を検索して選びます。

   Logic Apps の **[概要]** ペインが開いたら、次の方法で **[Logic Apps]** ページに表示される一覧をフィルター処理できます。

   * Logic Apps を名前で検索する
   * Logic Apps をサブスクリプション、リソース グループ、場所、タグでフィルター処理する
   * Logic Apps をリソース グループ、種類、サブスクリプション、場所でグループ化する

## <a name="view-logic-app-properties"></a>Logic Apps のプロパティを表示する

1. Azure portal で、[ご利用の Logic Apps を検索して開きます](#find-logic-app)。

1. Logic Apps のメニューの **[設定]** の下で、 **[プロパティ]** を選択します。

1. **[プロパティ]** ペインで、Logic Apps に関する次の情報を表示してコピーできます。

   * **名前**
   * **リソース ID**
   * **リソース グループ**
   * **場所**
   * **Type** 
   * **サブスクリプション名**
   * **サブスクリプション ID**
   * **アクセス エンドポイント**
   * **ランタイム発信 IP アドレス**
   * **アクセス エンドポイントの IP アドレス**
   * **コネクタ発信 IP アドレス**

## <a name="disable-or-enable-logic-apps"></a>Logic Apps を無効または有効にする

Azure portal で、[1 つの Logic Apps](#disable-enable-single-logic-app)を、または[複数の Logic Apps を同時に](#disable-or-enable-multiple-logic-apps)有効または無効にすることができます。 また、[Visual Studio で Logic Apps を有効または無効にする](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app)ことができます。

Logic Apps を無効にすると、次のようにワークフロー インスタンスと実行に影響します。

* 進行中および保留中のすべての実行は完了するまで引き続き実行されます。 実行の数により、このプロセスには時間がかかる場合があります。

* Logic Apps エンジンでは、新しいワークフロー インスタンスが作成または実行されません。

* トリガーは、次にその条件が満たされたときに起動されません。

* トリガーの状態には、Logic Apps が停止したポイントが記憶されます。 そのため、Logic Apps を再度有効にすると、前回の実行以降のすべての未処理の項目に対してトリガーが起動されます。

  前回の実行以降の未処理の項目に対して Logic Apps が起動しないようにするには、Logic Apps を再度有効にする前に、トリガーの状態をクリアします。

  1. Azure portal で、[ご利用の Logic Apps を検索して開きます](#find-logic-app)。

  1. Logic Apps のトリガーの任意の部分を編集します。

  1. 変更を保存します。 この手順により、トリガーの現在の状態がリセットされます。

  1. [Logic Apps を有効にします](#disable-enable-single-logic-app)。

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>1 つの Logic Apps を無効または有効にする

1. Azure portal で、[ご利用の Logic Apps を検索して開きます](#find-logic-app)。

1. Logic Apps のメニューで、 **[概要]** を選択します。 次のオプションから選択します。

   * ツールバーで、 **[Disable]\(無効化\)** を選択します。

     ![Azure portal で 1 つの Logic Apps を無効にする](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Logic Apps が既に無効になっている場合は、 **[有効化]** オプションのみが表示されます。

   * ツールバーで、 **[Enable]\(有効化\)** を選択します。

     ![Azure portal で 1 つの Logic Apps を有効にする](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Logic Apps が既に有効になっている場合は、 **[無効化]** オプションのみが表示されます。 

   Azure portal で、操作が成功したか失敗したかを確認する通知がメインの Azure ツールバーに表示されます。

   ![操作の状態を確認する通知](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>複数の Logic Apps を無効または有効にする

1. Azure portal で、無効または無効にする[Logic Apps を検索します](#find-logic-app)。

1. Logic Apps が現在有効になっているか無効になっているかを確認するには、 **[Logic Apps]** ページで、その Logic Apps の **[状態]** 列を確認します。 

   ![Logic Apps の [状態] 列](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   **[状態]** 列が表示されていない場合は、 **[Logic Apps]** ツール バーで、 **[プレビューを試す]** を選択します。

   ![プレビューを有効にする](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. チェックボックスの列で、無効または有効にする Logic Apps を検索します。 ツール バーで、 **[無効化]** または **[有効化]** を選択します。

   ![Azure portal で複数の Logic Apps を有効または無効にする](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. 確認ボックスが表示されたら、 **[はい]** を選択して続行します。

   Azure portal で、操作が成功したか失敗したかを確認する通知がメインの Azure ツールバーに表示されます。

## <a name="delete-logic-apps"></a>Logic Apps を削除する

Azure portal で、[1 つの Logic Apps を削除する](#delete-single-logic-app)か、または[複数の Logic Apps を同時に削除する](#delete-multiple-logic-apps)ことができます。 また、[Visual Studio で Logic Apps を削除する](manage-logic-apps-with-visual-studio.md#delete-your-logic-app)ことができます。

Logic Apps を削除すると、次のようにワークフロー インスタンスに影響します。

* 進行中および保留中のすべての実行は完了するまで引き続き実行されます。 実行の数により、このプロセスには時間がかかる場合があります。

* Logic Apps エンジンでは、新しいワークフロー インスタンスが作成または実行されません。

> [!NOTE]
> 子 Logic Apps を削除して再作成する場合は、親 Logic Apps を再保存する必要があります。 再作成された子アプリのメタデータは異なります。
> 子を再作成した後に親 Logic Apps を再保存しないと、子 Logic Apps への呼び出しは失敗し、"未承認" というエラーが表示されます。 この動作は、統合アカウントのアーティファクトを使用する親子 Logic Apps や、Azure Functions を呼び出す親子 Logic Apps に当てはまります。

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>1 つの Logic Apps を削除する

1. Azure portal で、[ご利用の Logic Apps を検索して開きます](#find-logic-app)。

1. Logic Apps のメニューで、 **[概要]** を選択します。 Logic Apps のツール バーで、 **[削除]** を選択します。

   ![Logic Apps のツール バーで、[削除] を選択する](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. 確認ボックスが表示されたら、Logic Apps 名を入力して、 **[削除]** を選択します。

   ![Logic Apps の削除を確認する](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Azure portal で、操作が成功したか失敗したかを確認する通知がメインの Azure ツールバーに表示されます。

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>複数の Logic Apps を削除する

1. Azure portal で、削除する[Logic Apps を検索します](#find-logic-app)。

1. チェックボックスの列で、削除する Logic Apps を検索します。 ツールバーの **[削除]** を選択します。

   ![複数の Logic Apps を削除する](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. 確認ボックスが表示されたら、「`yes`」と入力して、 **[削除]** を選択します。

   ![Logic Apps の削除を確認する](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Azure portal で、操作が成功したか失敗したかを確認する通知がメインの Azure ツールバーに表示されます。

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>Logic Apps のバージョンを管理する

Logic Apps のバージョン コントロールには、Azure portal を使用できます。 Logic Apps のバージョン履歴を確認し、以前のバージョンのレベルを上げることができます。

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>以前のバージョンを検索して表示する

1. Azure portal で、[管理する Logic Apps を検索します](#find-logic-app)。

1. Logic Apps のメニューで、 **[開発ツール]** の下にある **[バージョン]** を選択します。

   ![Logic Apps のメニューで、[開発ツール] の下にある [バージョン] を選択します。](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. 管理する Logic Apps の**バージョン**を一覧から選択します。 検索バーに**バージョン** ID を入力すると、一覧をフィルター処理できます。

1. **[履歴のバージョン]** ページで、以前のバージョンの詳細が読み取り専用モードで表示されます。 Logic Apps **デザイナー** モードか、**コード ビュー** モードのどちらかを選択できます。

   ![コード ビューと Logic Apps デザイナー ビューを使用した Logic Apps の [履歴のバージョン] ページ](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>以前のバージョンのレベルを上げる

1. Logic Apps のバージョン履歴で、[レベルを上げるバージョンを見つけて選択します](#find-version-history)。

1. **[履歴のバージョン]** ページで、 **[レベル上げ]** を選択します。

   ![Logic Apps のバージョン履歴の [レベル上げ] ボタン](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. 開いた **[Logic Apps デザイナー]** ページで、必要に応じてレベルを上げるバージョンを編集します。 **[デザイナー]** モードと **[コード ビュー]** モードを切り替えることができます。 **[パラメーター]** 、 **[テンプレート]** 、 **[コネクタ]** を更新することもできます。

   ![以前のバージョンのレベルを上げるための [Logic Apps デザイナー] ページ](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. 更新内容を保存し、以前のバージョンのレベル上げを完了するには、 **[保存]** を選択します。 (または、変更を取り消すには、 **[破棄]** を選択します。) 

   [Logic Apps のバージョン履歴をもう一度表示する](#find-version-history)と、レベルの上がったバージョンがリストの先頭に表示され、新しい識別子が含まれています。

## <a name="next-steps"></a>次のステップ

* [Logic Apps の監視](monitor-logic-apps.md)
