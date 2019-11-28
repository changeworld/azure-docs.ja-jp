---
title: マップの生成
description: FarmBeats でマップを生成する方法について説明します。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 635431fb87e5f164f92ab4b7a1027ee96e9d801a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891029"
---
# <a name="generate-maps"></a>マップの生成

Azure FarmBeats では、衛星画像とセンサー データを入力として使用して以下のマップを生成できます。 マップは、ファームの地理的な位置を表示して、デバイスの適切な位置を特定するための助けとなります。

  -  **センサー配置マップ** - 使用するセンサーの数やファームへの配置方法についての推奨情報が得られます。
  - **衛星指数マップ** - ファームの植生指数と水指数を表示します。
  - **土壌水分マップ** - 衛星データとセンサー データを合成して土壌水分分布を表示します。

## <a name="sensor-placement-maps"></a>センサー配置マップ

FarmBeats のセンサー配置マップは、土壌水分センサーの配置を支援します。 マップ出力は、センサーの配置に使用する一連の座標から成ります。 これらのセンサーからの入力を衛星画像と共に使用して土壌水分ヒートマップが生成されます。  

このマップは、1 年を通して複数日にわたって見られたキャノピーをセグメント化することで得られ、露出土壌や建物もキャノピーの構成要素となります。 その場所に不要なセンサーは除外することができます。 このマップは目安であり、位置や数は独自の知識に基づいて多少変更することができます (センサーを追加しても土壌水分ヒート マップの結果が悪化することはありませんが、センサーの数を減らした場合はヒート マップの正確性が低下する可能性があります)。  

## <a name="before-you-begin"></a>開始する前に  

センサー配置マップの生成を試みる前に次のことを確認してください。

- ファームのサイズが 1 エーカーより大きいこと。
- 選択した期間について、雲が映り込んでいない Sentinel シーンの数が 6 枚を超えること。  
- 雲が映り込んでいない少なくとも 6 枚の Sentinel シーンの NDVI が 0.3 以上であること。

    > [!NOTE]
    > Sentinel で許容されるコンカレント スレッド数は、1 ユーザーにつき 2 スレッドまでです。 結果的にジョブはキューに格納されるので、完了までに時間がかかる場合があります。

### <a name="dependencies-on-sentinel"></a>Sentinel への依存  

Sentinel に依存する事柄を次に示します。

- 衛星画像のダウンロードは、Sentinel のパフォーマンスに依存します。 Sentinel のパフォーマンスの状態とメンテナンス [アクティビティ](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)を確認してください。
- Sentinel で許容されるコンカレント [ダウンロード スレッド数](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services)は、1 ユーザーにつき 2 スレッドまでです。
- プレシジョン マップの生成は、[Sentinel のカバレッジと再通過頻度]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)によって左右されます。

## <a name="create-sensor-placement-map"></a>センサー配置マップを作成する
このセクションでは、センサー配置マップの作成手順を詳しく説明します。

> [!NOTE]
> センサーの配置は、 **[Maps]\(マップ\)** ページから、または **[Farm Details]\(ファームの詳細\)** ページの **[Generate Precision Maps]\(プレシジョン マップの生成\)** ドロップダウン メニューから開始できます。

次の手順に従います。

1. ホーム ページの左側のナビゲーション メニューから **[Maps]\(マップ\)** に移動します。
2. **[Create Maps]\(マップの作成\)** を選択し、ドロップダウン メニューから **[Sensor Placement]\(センサー配置\)** を選択します。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. **[Sensor Placement]\(センサー配置\)** を選択します。
  [Sensor Placement]\(センサー配置\) ウィンドウが表示されます。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. **[Farm]\(ファーム\)** ドロップダウン メニューからファームを選択します。  
   自分のファームを検索して選択するには、ボックスの一覧をスクロールするか、該当するファームの名前をテキスト ボックスに入力します。
5. 昨年のマップを生成したい場合は、 **[Recommended]\(推奨\)** オプションを選択します。
6. 期間を指定してマップを生成したい場合は、 **[Select Date Range]\(日付範囲の選択\)** を選択して、生成するセンサー配置マップの開始日と終了日を指定します。
7. **[Generate Maps]\(マップの生成\)** を選択します。
 ジョブの詳細を含んだ確認メッセージが表示されます。

  ジョブの状態については、 **[View Jobs]\(ジョブの表示\)** セクションを参照してください。 ジョブの状態が *[Failed]\(失敗\)* として表示された場合は、 *[Failed]\(失敗\)* 状態のヒントに詳しいエラー メッセージが表示されます。 その場合は、前述の手順を繰り返してからもう一度実行してください。

  問題が解決しない場合は、「[トラブルシューティング](troubleshoot-project-farmbeats.md)」セクションを参照するか、関連するログを添えて [Azure FarmBeats のサポート フォーラム](https://aka.ms/FarmBeatsMSDN)にお問い合わせください。

### <a name="view-and-download-sensor-placement-map"></a>センサー配置マップを表示してダウンロードする

次の手順に従います。

1. ホーム ページの左側のナビゲーション メニューから **[Maps]\(マップ\)** に移動します。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. ウィンドウの左側のナビゲーションから **[Filter]\(フィルター\)** を選択します。
  **[Filter]\(フィルター\)** ウィンドウに検索条件が表示されます。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. ドロップダウン メニューから **[Type]\(種類\)** 、 **[Date]\(日付\)** 、 **[Name]\(名前\)** を選択し、 **[Apply]\(適用\)** を選択して表示したいマップを検索します。
  ジョブの作成日が type_farmname_YYYY-MM-DD 形式で表示されます。
4. ページの最後にあるナビゲーション バーを使用して、利用可能なマップの一覧をスクロールします。
5. 表示するマップを選択します。 選択したマップのプレビューがポップアップ ウィンドウに表示されます。
6. **[Download]\(ダウンロード\)** を選択し、センサーの座標の GeoJSON ファイルをダウンロードします。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>衛星指数マップ

以降の各セクションでは、衛星指数マップの作成と表示に関係した手順を説明します。

> [!NOTE]
> 衛星指数マップは、 **[Maps]\(マップ\)** ページから、または **[Farm Details]\(ファームの詳細\)** ページの **[Generate Precision Maps]\(プレシジョン マップの生成\)** ドロップダウン メニューから開始できます。

FarmBeats には、ファームの正規化植生指数 (NDVI) マップ、拡張植生指数 (EVI) マップ、正規化水指数 (NDWI) マップを生成する機能が用意されています。 これらの指数は、農作物の現在の生育状況や過去の生育状況、地中の代表的水位を判断するうえで役立ちます。


> [!NOTE]
> マップの生成期間の Sentinel 画像が必要です。


## <a name="create-satellite-indices-map"></a>衛星指数マップを作成する

次の手順に従います。

1. ホーム ページの左側のナビゲーション メニューから **[Maps]\(マップ\)** に移動します。
2. **[Create Maps]\(マップの作成\)** を選択し、ドロップダウン メニューから **[Satellite Indices]\(衛星指数\)** を選択します。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. **[Satellite Indices]\(衛星指数\)** を選択します。
  [Satellite Indices]\(衛星指数\) ウィンドウが表示されます。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. ドロップダウン メニューからファームを選択します。  
   自分のファームを検索して選択するには、ボックスの一覧をスクロールするか、該当するファームの名前を入力します。   
5. 先週のマップを生成したい場合は、 **[This Week]\(今週\)** オプションを選択します。
6. 期間を指定してマップを生成したい場合は、 **[Select Date Range]\(日付範囲の選択\)** を選択して、生成する衛星指数マップの開始日と終了日を指定します。
7. **[Generate Maps]\(マップの生成\)** を選択します。
    ジョブの詳細を含んだ確認メッセージが表示されます。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    ジョブの状態については、 **[View Jobs]\(ジョブの表示\)** セクションを参照してください。 ジョブの状態が *[Failed]\(失敗\)* として表示された場合は、 *[Failed]\(失敗\)* 状態のヒントに詳しいエラー メッセージが表示されます。 その場合は、前述の手順を繰り返してからもう一度実行してください。

    問題が解決しない場合は、「[トラブルシューティング](troubleshoot-project-farmbeats.md)」セクションを参照するか、関連するログを添えて [Azure FarmBeats のサポート フォーラム](https://aka.ms/FarmBeatsMSDN)にお問い合わせください。

### <a name="view-and-download-map"></a>マップを表示してダウンロードする

次の手順に従います。

1. ホーム ページの左側のナビゲーション メニューから **[Maps]\(マップ\)** に移動します。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. ウィンドウの左側のナビゲーションから **[Filter]\(フィルター\)** を選択します。 **[Filter]\(フィルター\)** ウィンドウに検索条件が表示されます。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. ドロップダウン メニューから **[Type]\(種類\)** 、 **[Date]\(日付\)** 、 **[Name]\(名前\)** を選択し、 **[Apply]\(適用\)** を選択して表示したいマップを検索します。
  ジョブの作成日が type_farmname_YYYY-MM-DD 形式で表示されます。

4. ページの最後にあるナビゲーション バーを使用して、利用可能なマップの一覧をスクロールします。
5. **[Farm Name]\(ファーム名\)** と **[Date]\(日付\)** の組み合わせごとに、次の 3 つのマップが利用できます。
    - NDVI
    - EVI
    - NDWI
6. 表示するマップを選択します。 選択したマップのプレビューがポップアップ ウィンドウに表示されます。
7. **[Download]\(ダウンロード\)** ドロップダウン メニューを選択して、ダウンロード形式を選択すると、マップがダウンロードされてお使いのコンピューターのローカル フォルダーに格納されます。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="get-soil-moisture-heatmap"></a>土壌水分ヒートマップを取得する

土壌水分は、土壌の粒子間に保持される水分です。 土壌水分ヒートマップを使用すると、ファーム内のさまざまな深さの土壌水分データを高解像度で明瞭に把握することができます。 正確で実用的な土壌水分ヒートマップを生成するためには、センサーをすべて同じプロバイダーの製品で統一し、それらを一様に配置する必要があります。 プロバイダーが異なると、土壌水分の測定方法が異なるほか、キャリブレーションにも差があります。 ヒートマップは、特定の深さを対象としたものであり、その深さで配置されたセンサーを使用して生成されます。

### <a name="before-you-begin"></a>開始する前に  

土壌水分ヒートマップの生成を試みる前に次のことを確認してください。

- 少なくとも 3 つの土壌水分センサーが配置されていること。 センサーの配置とファームへの関連付けを行う前に土壌水分マップを作成しようとすることは推奨しません。  
- 土壌水分ヒートマップの生成は、Sentinel のパス カバレッジ、雲量、雲の影の影響を受けます。 土壌水分マップが要求された日から直近 120 日間の、雲が映り込んでいない Sentinel シーンが少なくとも 1 枚あること。
- ファームに配置されているセンサーの少なくとも半数がオンラインになっていて、データ ハブにデータがストリーム配信されていること。
- ヒートマップは、同じプロバイダーのセンサーのメジャーを使用して生成する必要があります。


## <a name="create-soil-moisture-heatmap"></a>土壌水分ヒートマップを作成する

次の手順に従います。

1. ホーム ページの左側のナビゲーション メニューから **[Maps]\(マップ\)** に移動してマップ ページを表示します。
2. **[Create Maps]\(マップの作成\)** を選択し、ドロップダウン メニューから **[Soil Moisture]\(土壌水分\)** を選択します。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. **[Soil Moisture]\(土壌水分\)** を選択します。
    [Soil Moisture]\(土壌水分\) ウィンドウが表示されます。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. **[Farm]\(ファーム\)** ドロップダウン メニューからファームを選択します。  
   自分のファームを検索して選択するには、ボックスの一覧をスクロールするか、該当するファームの名前を [Select Farm]\(ファームの選択\) ドロップダウン メニューに入力します。
5. マップの生成対象とする土壌水分センサーのメジャー (深さ) を **[Select Soil Moisture Sensor Measure]\(土壌水分センサーのメジャーの選択\)** ボックスの一覧から選択します。
センサーのメジャーを検索するには、 **[Sensors]\(センサー\)** に移動して、いずれかの土壌水分センサーを選択します。 その値を、 **[Sensor Properties]\(センサーのプロパティ\)** セクションの **[Measure Name]\(メジャー名\)** に使用します。
6. 生成対象として **[Today]\(今日\)** または **[This Week]\(今週\)** のいずれかのオプションを選択します。
7. 期間を指定して生成したい場合は、 **[Select Date Range]\(日付範囲の選択\)** を選択して、生成する土壌水分マップの開始日と終了日を指定します。
8. **[Generate Maps]\(マップの生成\)** を選択します。
 ジョブの詳細を含んだ確認メッセージが表示されます。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    ジョブの状態については、 **[View Jobs]\(ジョブの表示\)** セクションを参照してください。 ジョブの状態が *[Failed]\(失敗\)* として表示された場合は、 *[Failed]\(失敗\)* 状態のヒントに詳しいエラー メッセージが表示されます。 その場合は、前述の手順を繰り返してからもう一度実行してください。

    問題が解決しない場合は、「[トラブルシューティング](troubleshoot-project-farmbeats.md)」セクションを参照するか、関連するログを添えて [Azure FarmBeats のサポート フォーラム](https://aka.ms/FarmBeatsMSDN)にお問い合わせください。

### <a name="view-and-download-map"></a>マップを表示してダウンロードする

次の手順に従います。

1. ホーム ページの左側のナビゲーション メニューから **[Maps]\(マップ\)** に移動します。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. ウィンドウの左側のナビゲーションから **[Filter]\(フィルター\)** を選択します。 **[Filter]\(フィルター\)** ウィンドウが表示されてマップを検索することができます。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  ドロップダウン メニューから **[Type]\(種類\)** 、 **[Date]\(日付\)** 、 **[Name]\(名前\)** を選択し、 **[Apply]\(適用\)** を選択して表示したいマップを検索します。 ジョブの作成日が type_farmname_YYYY-MM-DD 形式で表示されます。
4. テーブルのヘッダーの横にある **[Sort]\(並べ替え\)** アイコンを選択して、ファーム、日付、作成日、ジョブ ID、ジョブ タイプを基準に並べ替えます。
5. ページの最後にあるナビゲーション ボタンを使用して、利用可能なマップの一覧をスクロールします。
6. 表示するマップを選択します。 選択したマップのプレビューがポップアップ ウィンドウに表示されます。
7. **[Download]\(ダウンロード\)** ドロップダウン メニューを選択して、ダウンロード形式を選択すると、マップがダウンロードされ、指定したフォルダーに格納されます。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
