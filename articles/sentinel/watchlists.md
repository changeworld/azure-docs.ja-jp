---
title: Microsoft Sentinel ウォッチリストを使用する
description: この記事では、Microsoft Sentinel ウォッチリストを使用して、許可リストおよびブロックリストを作成し、イベント データをエンリッチメント処理し、脅威の調査を支援する方法について説明します。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: fc1d361c3e46cfed012637e9717d2a12391bd2b3
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711345"
---
# <a name="use-microsoft-sentinel-watchlists"></a>Microsoft Sentinel ウォッチリストを使用する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel ウォッチリストを使用すると、Microsoft Sentinel 環境内のイベントと関連付けるために、外部データ ソースからのデータ収集が可能になります。 ウォッチリストを作成すると、検索、検出規則、脅威ハンティング、および応答プレイブックで使用できます。 ウォッチリストは、名前と値のペアとして Microsoft Sentinel ワークスペースに格納され、最適なクエリ パフォーマンスと待ち時間の短縮のためにキャッシュされます。

> [!IMPORTANT]
> 記載されている機能は、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

ウォッチリストを使用する一般的なシナリオは次のとおりです。

- IP アドレス、ファイル ハッシュ、およびその他のデータを CSV ファイルから短時間でインポートすることで、迅速に **脅威の調査** とインシデントへの対応を行います。 インポート後は、アラート ルール、脅威ハンティング、ブック、ノートブック、および一般的なクエリの結合とフィルターに、ウォッチリストの名前と値のペアを使用できます。

- ウォッチリストとして **ビジネス データをインポート** します。 たとえば、システムの特権アクセスが割り当てられているユーザーのリスト、または退職した従業員をインポートした後、ウォッチリストを使用して、これらのユーザーによるネットワークへのログインを検出または防止するために使用される許可リストとブロックリストを作成します。

- **アラート疲れを軽減** します。 通常はアラートをトリガーするタスクを実行する承認済み IP アドレスのユーザーなど、ユーザー グループからのアラートが表示されないように許可リストを作成し、問題のないイベントがアラートにならないようにします。

- **イベント データを強化** します。 ウォッチリストを使用して、外部データ ソースから派生した名前と値の組み合わせでイベント データを強化します。

> [!NOTE]
> - ウォッチリストは大規模なデータ ボリューム用に設計されていないため、その使用は参照データに限定する必要があります。
>
> - 1 つのワークスペース内のすべてのウォッチリストにわたる **アクティブなウォッチリスト項目の合計数** は、現在 **1,000 万** に制限されています。 削除されたウォッチリスト項目は、この合計にはカウントされません。 大規模なデータ ボリュームを参照する機能が必要な場合、代わりに[カスタム ログ](../azure-monitor/agents/data-sources-custom-logs.md)を使用してそれらを取り込むことを検討してください。
>
> - ウォッチリストは、同じワークスペース内からのみ参照できます。 クロスワークスペースや Lighthouse のシナリオは現在サポートされていません。

## <a name="create-a-new-watchlist"></a>新しいウォッチリストを作成する

1. Azure portal で **[Microsoft Sentinel]**  >  **[構成]**  >  **[ウォッチリスト]** の順に移動し、 **[+ 新規追加]** を選択します。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new.png" alt-text="新しいウォッチリスト" lightbox="./media/watchlists/sentinel-watchlist-new.png":::

1. **[全般]** ページで、そのウォッチリストの名前、説明、および別名を指定し、 **[次へ: リソース]** を選択します。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-general.png" alt-text="ウォッチリストの [全般] ページ":::

1. **[ソース]** ページで、データセットの種類を選択し (現在は CSV のみを使用可能)、データ ファイルの **ヘッダー行の前の** 行数を入力し、次の 2 つの方法のいずれかを使用してアップロードするファイルを選択します。
    1. **[ファイルのアップロード]** ボックスの **[ファイルの参照]** リンク をクリック し、アップロードするデータ ファイルを選択します。
    1. データ ファイルを **[ファイルのアップロード]** ボックスに ドラッグ アンド ドロップします。

    ウィザード画面に結果の最初の 50 行のプレビューが表示されます。

1. **[SearchKey]** フィールドに、他のデータとの結合または頻繁な検索オブジェクトとして使用するウォッチリスト内の列の名前を入力します。 たとえば、サーバー ウォッチリストに国名とそれぞれの 2 文字の国番号が含まれていて、検索または結合に国番号を頻繁に使用することが想定される場合は、SearchKey として **[コード]** 列を使用します。

1. <a name="review-and-create"></a> **[次へ: 確認と作成]** を選択します。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="ウォッチリストの [ソース] ページ" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > 現在、ファイルのアップロードは、最大 3.8 MB のファイルに制限されています。

1. 情報を確認し、正しいと確認し、 *[検証に成功しました]* メッセージ が 表示されるのを待って、 **[作成]** を選択します。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-review.png" alt-text="ウォッチリストの確認ページ":::

    ウォッチリストが作成されると、通知が表示されます。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="ウォッチリストの作成成功の通知" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::


## <a name="create-a-new-watchlist-using-a-template-public-preview"></a>テンプレートを使用して新しいウォッチリストを作成する (パブリック プレビュー)

1. Azure portal から、 **[Microsoft Sentinel]**  >  **[構成]**  >  **[ウォッチリスト]**  >  **[Templates (Preview)]\(テンプレート (プレビュー)\)** の順に移動します。

1. 一覧からテンプレートを選択して右側に詳細を表示し、 **[テンプレートから作成]** を選択してウォッチリストを作成します。

    :::image type="content" source="./media/watchlists/create-watchlist-from-template.png" alt-text="組み込みのテンプレートからウォッチリストを作成します。" lightbox="./media/watchlists/create-watchlist-from-template.png":::

1. **ウォッチリスト ウィザード** で作業を続けます。

    - ウォッチリスト テンプレートを使用する場合、ウォッチリストの **[名前]** 、 **[説明]** 、 **[ウォッチリスト エイリアス]** の値はすべて読み取り専用です。

    - **[スキーマをダウンロード]** を選択し、選択したウォッチリスト テンプレートに必要な関連スキーマを含む CSV ファイルをダウンロードします。

    各組み込みウォッチリスト テンプレートには、テンプレートにアタッチされた CSV ファイルに一覧表示されている、独自のデータのセットがあります。 詳細については、[組み込みウォッチリスト スキーマ](watchlist-schemas.md)に関するページを参照してください

1.  ローカル バージョンの CSV ファイルにデータを設定し、ウィザードにアップロードして戻します。

1. [新しいウォッチリストを最初から作成する](#review-and-create)場合と同様に作業を続け、[クエリ](#use-watchlists-in-queries)と[分析ルール](#use-watchlists-in-analytics-rules)でウォッチリストを使用します。

## <a name="use-watchlists-in-queries"></a>クエリでウォッチリストを使用する

> [!TIP]
> クエリのパフォーマンスを最適化するには、クエリ内の結合のキーとして **SearchKey** (ウォッチリストの作成で定義したフィールドを表す) を使用します。 次の例を見てください。

1. Azure portal で **[Microsoft Sentinel]**  >  **[構成]**  >  **[ウォッチリスト]** の順に移動し、使用するウォッチリストを選択して、 **[ログ分析で表示]** を選択します。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="クエリでのウォッチリストの使用" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png" :::

1. ウォッチリスト内の項目はクエリに対して自動的に抽出され、 **[結果]** タブに表示されます。次の例は、 **[名前]** フィールドと **[IP アドレス]** フィールドの抽出結果を示しています。 **SearchKey は**、独自の列として表示されます。

    > [!NOTE]
    > クエリのタイムスタンプは、クエリ UI とスケジュールされたアラートの両方で無視されます。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="ウォッチリストのフィールドを使用したクエリ" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::

1. ウォッチリストを結合または検索のテーブルとして扱うことで、あるテーブルに含まれるデータがウォッチリストのデータにないか照合できます。 結合のキーとして **SearchKey** を使用します。

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('mywatchlist') 
     on $left.RemoteIPCountry == $right.SearchKey
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="検索としてウォッチリストに対して実行するクエリ" lightbox="./media/watchlists/sentinel-watchlist-queries-join.png":::

## <a name="use-watchlists-in-analytics-rules"></a>分析ルールでウォッチリストを使用する

> [!TIP]
> クエリのパフォーマンスを最適化するには、クエリ内の結合のキーとして **SearchKey** (ウォッチリストの作成で定義したフィールドを表す) を使用します。 次の例を見てください。

分析ルールでウォッチリストを使用するには、Azure portal で **[Microsoft Sentinel]**  >  **[構成]**  >  **[分析]** の順に移動し、クエリで `_GetWatchlist('<watchlist>')` 関数を使用してルールを作成します。

1. この例では、"ipwatchlist" という名前のウォッチリストを次の値で作成します。

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="ウォッチリストの 4 項目の一覧":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-other.png" alt-text="項目が 4 つあるウォッチリストを作成する":::

1. 次に、分析ルールを作成します。  この例では、ウォッチリストに IP アドレスからのイベントのみを含めます。

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    //Use SearchKey for the best performance
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project SearchKey)
    )
    ```

    :::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule.png" alt-text="分析ルールでのウォッチリストの使用":::

## <a name="view-list-of-watchlists-aliases"></a>ウォッチリスト エイリアスの一覧を表示する

ウォッチリスト エイリアスの一覧を取得するには、Azure portal で **[Microsoft Sentinel]**  >  **[全般]**  >  **[ログ]** の順に移動し、`_GetWatchlistAlias` というクエリを実行します。 別名の一覧が **[結果]** タブに表示されます。

   :::image type="content" source="./media/watchlists/sentinel-watchlist-alias.png" alt-text="ウォッチリストの一覧表示" lightbox="./media/watchlists/sentinel-watchlist-alias.png":::

## <a name="manage-your-watchlist-in-the-microsoft-sentinel-portal"></a>Microsoft Sentinel ポータルでウォッチリストを管理する

Microsoft Sentinel ポータルの [ウォッチリスト] ブレードから直接、ウォッチリスト項目を表示、編集、および新規作成することもできます。

1. ウォッチリストを編集するには、 **[Microsoft Sentinel] > [構成] > [ウォッチリスト]** の順に移動し、編集するウォッチリストを選択し、詳細ペインで **[Edit watchlist items]\(ウォッチリスト項目を編集する\)** を選択します。

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit.png" alt-text="ウォッチリストを編集する方法を示すスクリーン ショット" lightbox="./media/watchlists/sentinel-watchlist-edit.png":::

1. 既存のウォッチリスト項目を編集するには、そのウォッチリスト項目のチェック ボックスをオンにし、項目を編集して、 **[保存]** を選択します。 確認を求められたら **[はい]** を選択します。

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-change.png" alt-text="ウォッチリスト項目をオンにして編集する方法を示すスクリーン ショット。":::

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-confirm.png" alt-text="変更を確認するスクリーン ショット。":::

1. ウォッチリストに新しい項目を追加するには、 **[Edit watchlist items]\(ウォッチリスト項目を編集する\)** 画面で **[新しく追加]** を選択し、 **[Add watchlist item]\(ウォッチリスト項目を追加する\)** パネルのフィールドに記入し、そのパネルの下部の **[追加]** を選択します。

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-add.png" alt-text="ウォッチリストに新しい項目を追加する方法を示すスクリーン ショット。":::

## <a name="next-steps"></a>次の手順
このドキュメントでは、Microsoft Sentinel でウォッチリストを使用して、データを強化し、調査を向上させる方法について説明しました。 Microsoft Sentinel の詳細については、次の記事を参照してください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](./detect-threats-built-in.md)の概要。
- [ブックを使用](monitor-your-data.md)してデータを監視する。
