---
title: Microsoft Azure Data Explorer Flow コネクタ (プレビュー) の使用例
description: 一般的な Microsoft Flow コネクタの使用例について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 1b9d593b0f0895e2ba75fae7ab7e78ea883c8907
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521677"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Microsoft Flow コネクタ (プレビュー) の使用例

Azure Data Explorer Flow コネクタを使用すると、Azure Data Explorer は [Microsoft Power Automate の Flow 機能](https://flow.microsoft.com/)を使用して、スケジュールされたタスクまたはトリガーされたタスクの一部として Kusto クエリとコマンドを自動的に実行することができます。 このドキュメントには、一般的な Microsoft Flow コネクタの使用例がいくつか含まれています。

詳細については、[Microsoft Flow コネクタ (プレビュー)](flow.md) に関する説明を参照してください。

* [Microsoft Flow コネクタと SQL](#microsoft-flow-connector-and-sql)
* [Power BI データセットにデータをプッシュする](#push-data-to-power-bi-dataset)
* [条件付きクエリ](#conditional-queries)
* [複数の Azure Data Explorer Flow のグラフをメールで送信する](#email-multiple-azure-data-explorer-flow-charts)

## <a name="microsoft-flow-connector-and-sql"></a>Microsoft Flow コネクタと SQL

Microsoft Flow コネクタを使用して、データに対してクエリを実行し、SQL データベースで集計します。

> [!Note]
> SQL の挿入は、行ごとに個別に行われます。 少量の出力データには、Microsoft Flow コネクタのみを使用してください。 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> *[クラスター名]* フィールドに、クラスターの URL を入力します。

## <a name="push-data-to-power-bi-dataset"></a>Power BI データセットにデータをプッシュする

Microsoft Flow コネクタは、Power BI コネクタと共に使用して、Kusto クエリから Power BI ストリーミング データセットにデータをプッシュすることができます。

1. 新しい [クエリの実行と結果の一覧表示] アクションを作成します。
1. **[新しいステップ]** を選択します。
1. **[アクションの追加]** を選択し、「Power BI」を検索します。
1. **[Power BI]** を選択します。
1. **[データセットに行を追加する]** を選択します。 

    ![Flow Power BI コネクタ](./media/flow-usage/flow-powerbiconnector.png)
1. データのプッシュ先となる **[ワークスペース]** 、 **[データセット]** 、および **[テーブル]** を入力します。
1. 動的コンテンツのダイアログで、データセットスキーマと関連する Kusto クエリ結果を含むペイロードを追加します。

    ![Flow Power BI のフィールド](./media/flow-usage/flow-powerbifields.png)

Flow は、Kusto クエリ結果テーブルの各行に対して、Power BI アクションを自動的に適用します。 

![各行の Flow Power BI アクション](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>条件付きクエリ

Kusto クエリの結果は、次のフローアクションの入力または条件として使用できます。

次の例では、Kusto に対して、過去 1 日間に発生したインシデントのクエリを実行します。 解決されたインシデントごとに、Slack メッセージが投稿され、プッシュ通知が作成されます。
引き続きアクティブになっているインシデントごとに、Kusto に対して、同様のインシデントに関する詳細情報のクエリが実行されます。 この情報をメールとして送信し、関連する TFS タスクを開きます。

同様のフローを作成するには、以下の手順を実行します。

1. 新しい [クエリの実行と結果の一覧表示] アクションを作成します。
1. **[新しいステップ]** を選択します。
1. **[条件] コントロール**を選択します。
1. 動的コンテンツのウィンドウで、次のアクションの条件として使用するパラメーターを選択します。
1. 指定されたパラメーターに特定の条件を設定するには、*リレーションシップ*の種類と*値*を選択します。

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    Flow は、クエリ結果テーブルの各行にこの条件を適用します。
1. 条件が true および false の場合のアクションを追加します。

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

Kusto クエリの結果値を、次のアクションの入力として使用できます。 動的コンテンツのウィンドウで、結果値を選択します。
以下の例では、[Slack - メッセージの投稿] アクションと、Kusto クエリのデータを含む [Visual Studio - 新しい作業項目を作成します] アクションが追加されました。

![[Slack - メッセージの投稿] アクション](./media/flow-usage/flow-slack.png)

![Visual Studio のアクション](./media/flow-usage/flow-visualstudio.png)

この例では、インシデントがまだアクティブである場合、Kusto に対してクエリを実行して、過去に同じソースからのインシデントが解決された方法に関する情報を取得します。

![フロー条件のクエリ](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> *[クラスター名]* フィールドに、クラスターの URL を入力します。

この情報を円グラフとして視覚化し、チームにメールで送信します。

![フロー条件のメール](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>複数の Azure Data Explorer Flow のグラフをメールで送信する

1. 繰り返しトリガーを含む新しいフローを作成し、フローの間隔と頻度を定義します。 
1. 1 つ以上の [Kusto - クエリの実行と結果の視覚化] アクションを使用して、新しいステップを追加します。 

    ![フローで複数のクエリを実行する](./media/flow-usage/flow-severalqueries.png)
1. 各 [Kusto - クエリの実行と結果の視覚化] ごとに、次のフィールドを定義します。
    * クラスター URL
    * データベース名
    * クエリおよびグラフの種類 (HTML テーブル、円グラフ、時間グラフ、横棒グラフ、カスタム値の入力)。

    ![複数の添付ファイルを使用して結果を視覚化する](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. メールの送信 (v2) アクションを追加します。 
    1. 本文セクションで、コード ビュー アイコンを選択します。
    1. **[本文]** フィールドに必要な BodyHtml を挿入し、クエリの視覚化された結果がメールの本文に含まれるようにします。
    1. メールに添付ファイルを追加するには、[添付ファイル名] と [添付ファイルのコンテンツ] を追加します。
    
    ![複数の添付ファイルをメールで送信する](./media/flow-usage/flow-email-multiple-attachments.png)

    メール アクションを作成する手順の詳細については、[Kusto クエリ結果をメールで送信する方法](flow.md#email-kusto-query-results)に関するページを参照してください。 

結果:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="next-steps"></a>次のステップ

[Microsoft Azure Explorer Logic App コネクタ](https://docs.microsoft.com/azure/kusto/tools/logicapps)について説明します。これは、スケジュールまたはトリガーされたタスクの一部として Kusto クエリとコマンドを自動的に実行するもう 1 つの方法です。
