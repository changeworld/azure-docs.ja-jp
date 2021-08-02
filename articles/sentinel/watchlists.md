---
title: Azure Sentinel ウォッチリストを使用する
description: この記事では、Azure Sentinel ウォッチリストを使用して、脅威の調査、ビジネス データのインポート、許可リストの作成、およびイベント データの強化を行う方法について説明します。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 493f04883aa49c8658b7b4a1996bf010992a2f55
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "110792798"
---
# <a name="use-azure-sentinel-watchlists"></a>Azure Sentinel ウォッチリストを使用する

> [!IMPORTANT]
> ウォッチリスト機能は現在 **プレビュー** 段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Azure Sentinel ウォッチリストを使用すると、Azure Sentinel 環境内のイベントと関連付けるために、外部データ ソースからのデータ収集が可能になります。 ウォッチリストを作成すると、検索、検出規則、脅威ハンティング、および応答プレイブックで使用できます。 ウォッチリストは、名前と値のペアとして Azure Sentinel ワークスペースに格納され、最適なクエリ パフォーマンスと待ち時間の短縮のためにキャッシュされます。

ウォッチリストを使用する一般的なシナリオは次のとおりです。

- IP アドレス、ファイル ハッシュ、およびその他のデータを CSV ファイルから短時間でインポートすることで、迅速に **脅威の調査** とインシデントへの対応を行います。 インポート後は、アラート ルール、脅威ハンティング、ブック、ノートブック、および一般的なクエリの結合とフィルターに、ウォッチリストの名前と値のペアを使用できます。

- ウォッチリストとして **ビジネス データをインポート** します。 たとえば、システムの特権アクセスが割り当てられているユーザーリスト、または退職した従業員をインポートした後、ウォッチリストを使用して、これらのユーザーによるネットワークへのログインを検出または防止するために使用される許可リストや拒否リストを作成します。

- **アラート疲れを軽減** します。 通常はアラートをトリガーするタスクを実行する承認済み IP アドレスのユーザーなど、ユーザー グループからのアラートが表示されないように許可リストを作成し、問題のないイベントがアラートにならないようにします。

- **イベント データを強化** します。 ウォッチリストを使用して、外部データ ソースから派生した名前と値の組み合わせでイベント データを強化します。

## <a name="create-a-new-watchlist"></a>新しいウォッチリストを作成する

1. Azure portal で **[Azure Sentinel]**  >  **[構成]**  >  **[ウォッチリスト]** の順に移動し、 **[+新規追加]** を選択します。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new.png" alt-text="新しいウォッチリスト" lightbox="./media/watchlists/sentinel-watchlist-new.png":::

1. **[全般]** ページで、そのウォッチリストの名前、説明、および別名を指定し、 **[次へ: リソース]** を選択します。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-general.png" alt-text="ウォッチリストの [全般] ページ":::

1. **[ソース]** ページで、データセットの種類を選択し (現在は CSV のみを使用可能)、データ ファイルの **ヘッダー行の前の** 行数を入力し、次の 2 つの方法のいずれかを使用してアップロードするファイルを選択します。
    1. **[ファイルのアップロード]** ボックスの **[ファイルの参照]** リンク をクリック し、アップロードするデータ ファイルを選択します。
    1. データ ファイルを **[ファイルのアップロード]** ボックスに ドラッグ アンド ドロップします。

    ウィザード画面に結果の最初の 50 行のプレビューが表示されます。

1. **[SearchKey]** フィールドに、他のデータとの結合または頻繁な検索オブジェクトとして使用するウォッチリスト内の列の名前を入力します。 たとえば、サーバー ウォッチリストにサーバー名とそれぞれの IP アドレスが含まれている場合に、検索または結合に IP アドレスを頻繁に使用する場合は、SearchKey として **[IP アドレス]** 列を使用します。

1. **[次へ: 確認と作成]** を選択します。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="ウォッチリストの [ソース] ページ" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > 現在、ファイルのアップロードは、最大 3.8 MB のファイルに制限されています。

1. 情報を確認し、正しいと確認し、 *[検証に成功しました]* メッセージ が 表示されるのを待って、 **[作成]** を選択します。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-review.png" alt-text="ウォッチリストの確認ページ":::

    ウォッチリストが作成されると、通知が表示されます。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="ウォッチリストの作成成功の通知" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::

## <a name="use-watchlists-in-queries"></a>クエリでウォッチリストを使用する

> [!TIP]
> クエリのパフォーマンスを最適化するには、クエリ内の結合のキーとして **SearchKey** (ウォッチリストの作成で定義したフィールドを表す) を使用します。 次の例を見てください。

1. Azure portal で **[Azure Sentinel]**  >  **[構成]**  >  **[ウォッチリスト]** の順に移動し、使用するウォッチリストを選択して、 **[ログ分析で表示]** を選択します。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="クエリでのウォッチリストの使用" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::

1. ウォッチリスト内の項目はクエリに対して自動的に抽出され、 **[結果]** タブに表示されます。次の例は、 **[名前]** フィールドと **[IP アドレス]** フィールドの抽出結果を示しています。 **SearchKey は**、独自の列として表示されます。

    > [!NOTE]
    > クエリのタイムスタンプは、クエリ UI とスケジュールされたアラートの両方で無視されます。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="ウォッチリストのフィールドを使用したクエリ" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
1. ウォッチリストを結合または検索のテーブルとして扱うことで、あるテーブルに含まれるデータがウォッチリストのデータにないか照合できます。 結合のキーとして **SearchKey** を使用します。

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('IPlist') 
     on $left.ComputerIP == $right.SearchKey
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="検索としてウォッチリストに対して実行するクエリ" lightbox="./media/watchlists/sentinel-watchlist-queries-join.png":::

## <a name="use-watchlists-in-analytics-rules"></a>分析ルールでウォッチリストを使用する

> [!TIP]
> クエリのパフォーマンスを最適化するには、クエリ内の結合のキーとして **SearchKey** (ウォッチリストの作成で定義したフィールドを表す) を使用します。 次の例を見てください。

分析ルールでウォッチリストを使用するには、Azure portal で **[Azure Sentinel]**  >  **[構成]**  >  **[分析]** の順に移動し、クエリで `_GetWatchlist('<watchlist>')` 関数を使用してルールを作成します。

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

ウォッチリスト エイリアスの一覧を取得するには、Azure portal で **[Azure Sentinel]**  >  **[全般]**  >  **[ログ]** の順に移動し、`_GetWatchlistAlias` というクエリを実行します。 別名の一覧が **[結果]** タブに表示されます。

   :::image type="content" source="./media/watchlists/sentinel-watchlist-alias.png" alt-text="ウォッチリストの一覧表示" lightbox="./media/watchlists/sentinel-watchlist-alias.png":::

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure Sentinel でウォッチリストを使用して、データを強化し、調査を向上させる方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](./tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
