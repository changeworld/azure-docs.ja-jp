---
title: Redash で Azure Data Explorer を視覚化する
description: この記事では、Redash ネイティブ コネクタを使用して Azure Data Explorer のデータを視覚化する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0380689ae6ca81e3f31a07f1e205c7773fdea8c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773950"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Azure Data Explorer からのデータを Redash で視覚化する

[Redash](https://redash.io/) を使用して、データ ソースに接続してクエリを実行し、ダッシュボードを構築してデータを視覚化し、それを同僚と共有します。 この記事では、Azure Data Explorer を Redash のデータ ソースとして設定し、データを視覚化する方法について説明します。

## <a name="prerequisites"></a>前提条件

1. [クラスターとデータベースを作成する](create-cluster-database-portal.md)。
1. 「[Azure Data Explorer にサンプル データを取り込む](ingest-sample-data.md)」で説明されているように、 データを取り込む。 取り込みオプションの詳細については、[取り込みの概要](ingest-data-overview.md)に関する記事を参照してください。

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Redash で Azure Data Explorer コネクタを作成する 

1. [Redash](https://www.redash.io/) にサインインします。 **[Get Started]\(開始\)** を選択してアカウントを作成します。
1. **[Let's get started]\(はじめに\)** で **[Connect a Data Source]\(データ ソースへの接続\)** を選択します。

    ![データ ソースに接続する](media/redash/connect-data-source.png)

1. **[Create a New Data Source]\(新しいデータ ソースの作成\)** ウィンドウで、 **[Azure Data Explorer (Kusto)]** を選択し、 **[Create]\(作成\)** を選択します。 

    ![Azure Data Explorer のデータ ソースを選択する](media/redash/select-adx-data-source.png)

1. **[Azure Data Explorer (Kusto)]** ウィンドウで、次のフィールドに入力して **[Create]\(作成\)** を選択します。

    ![Azure Data Explorer (Kusto) の設定ウィンドウ](media/redash/adx-settings-window.png)

1. **[Settings]\(設定\)** ウィンドウで、 **[Save]\(保存\)** および **[Test Connection]\(テスト接続\)** を選択して **Azure Data Explorer (Kusto)** データ ソース接続をテストします。

## <a name="create-queries-in-redash"></a>Redash でクエリを作成する

1. Redash の左上で、 **[Create]\(作成\)**  >  **[Query]\(クエリ\)** を選択します。 **[New Query]\(新しいクエリ\)** をクリックし、クエリの名前を変更します。

    ![クエリを作成する](media/redash/create-query.png)

1. 上部の編集ペインでクエリを入力し、 **[Save]\(保存\)** および **[Execute]\(実行\)** を選択 します。 今後使用するためにクエリをパブリッシュするには、 **[Publish]\(パブリッシュ\)** を選択します。

    ![クエリを保存して実行する](media/redash/save-and-execute-query.png)

    左側のペインで、ドロップダウン メニューにデータ ソース接続名 (このフローでは **Github connector**) が表示され、選択されたデータベース内のテーブルが表示されます。 

1. 下部中央のペインにクエリ結果が表示されます。 **[New Visualization]\(新しい視覚化\)** ボタンを選択して、クエリで使用する視覚化を作成します。

    ![新しい視覚化](media/redash/new-visualization.png)

1. 視覚化画面で、 **[Visualization Type]\(視覚化タイプ\)** と関連フィールド ( **[X Column]\(X 列\)** や **[Y Column]\(Y 列\)** など) を選択します。 視覚化を**保存**します。

    ![視覚化を構成して保存する](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>パラメーターを使用してクエリを作成する

1. **[Create]\(作成\)**  >  **[Query]\(クエリ\)** で新しいクエリを作成します。 {{}} 中かっこを使用してそれにパラメーターを追加します。 **{{}}** を選択して **[Add Parameter]\(パラメーターの追加\)** ウィンドウを開きます。 また、*設定アイコン* を選択して既存のパラメーターの属性を変更し、 **<parameter_name>** ウィンドウを開くこともできます。 

    ![パラメーターを挿入する](media/redash/insert-parameter.png)

1. パラメーターに名前を指定します。 ドロップダウン メニューから **[Type]\(タイプ\)** : **[Query Based Dropdown List]\(クエリベースのドロップダウン リスト\)** を選択します。 **[OK]** を選択します。

    ![クエリベースのドロップダウン リスト](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > このクエリでは複数の値を使用するため、構文 `| where Type in ((split('{{Type}}', ',')))` を含める必要があります。 詳細については、「[in 演算子](/azure/kusto/query/inoperator)」を参照してください。 これにより、[複数のクエリ パラメーター オプションが redash アプリに含まれます](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)。

## <a name="create-a-dashboard-in-redash"></a>Redash でダッシュボードを作成する

1. ダッシュボードを作成するには、 **[Create]\(作成\)**  >  **[Dashboard]\(ダッシュボード\)** を選択します。 または、既存のダッシュボードを選択し、 **[Dashboards]\(ダッシュボード\)** で一覧からダッシュボードを選択します。

    ![ダッシュボードの作成](media/redash/create-dashboard.png)

1. **[New Dashboard]\(新しいダッシュボード\)** ウィンドウで、ダッシュボードに名前を付け、 **[Save]\(保存\)** を選択します。 **<Dashboard_name>** ウィンドウで、 **[Add Widget]\(ウィジェットの追加\)** を選択して新しいウィジェットを作成します。 

1. **[Add Widget]\(ウィジェットの追加\)** ウィンドウで、クエリ名を選択し、 **[Choose Visualization]\(視覚化の選択\)** および **[Parameters]\(パラメーター\)** を選択します。 **[Add to Dashboard]\(ダッシュボードに追加\)** を選択します

   ![視覚化を選択してダッシュボードに追加する](media/redash/add-widget-window.png)

1. **[Done Editing]\(編集完了\)** を選択してダッシュボードの作成を完了します。

1.  ダッシュボード編集モードで、 **[Use Dashboard Level Filters]\(ダッシュボード レベル フィルターの使用\)** を選択し、以前に定義した **[Type]\(タイプ\)** パラメーターを使用します。

    ![ダッシュボードの作成を完了する](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer のクエリを記述する](write-queries.md)


