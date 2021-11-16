---
title: Azure Digital Twins Explorer を使用する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins Explorer の機能を使用する方法について理解します。
author: baanders
ms.author: baanders
ms.date: 10/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f77efc7ab6880db771ea7f53fe9cfb2858c6ecc0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132328167"
---
# <a name="use-azure-digital-twins-explorer-preview"></a>Azure Digital Twins Explorer を使用する (プレビュー)

[Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) は、Azure Digital Twins を視覚化して操作するためのツールです。 この記事では、Azure Digital Twins Explorer の機能と、それらを使用して Azure Digital Twins インスタンス内のデータを管理する方法について説明します。 Azure Digital Twins Explorer は、クリックするか、[キーボード ショートカット](#accessibility-and-advanced-settings)を使用して操作することができます。

>[!NOTE]
>このツールは現在、**パブリック プレビュー** 段階にあります。

[!INCLUDE [digital-twins-access-explorer.md](../../includes/digital-twins-access-explorer.md)]

### <a name="switch-contexts-within-the-app"></a>アプリ内のコンテキストを切り替える

アプリケーションを起動したら、エクスプローラーに現在接続されているインスタンスを変更することもできます。そのためには、上部のツール バーからインスタンス名を選択します。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/instance-url-1.png" alt-text="Azure Digital Twins Explorer のスクリーンショット。上部のツール バーのインスタンス名が強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/instance-url-1.png":::

これにより、**Azure Digital Twins URL モーダル** が表示されます。ここでは、別の Azure digital Twins インスタンスのホスト名を "*https://* " の後に入力すると、そのインスタンスに接続できます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/instance-url-2.png" alt-text="Azure Digital Twins Explorer のスクリーンショット。Azure Digital Twins URL モーダルには、&quot;https://&quot; とホスト名が記述されている編集可能なボックスが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/instance-url-2.png":::

>[!NOTE]
>現時点では、アプリ内のコンテキストを切り替える機能は、個人の Microsoft アカウント (MSA) では使用 **できません**。 MSA ユーザーは、Azure portal の正しいインスタンスからエクスプローラーにアクセスする必要があります。または、[環境への直接リンク](#link-to-your-environment)を介して特定のインスタンスに接続できます。


## <a name="query-your-digital-twin-graph"></a>デジタル ツイン グラフでクエリを実行する

**[クエリ エクスプローラー]** パネルを使用すると、グラフに対して [クエリ](concepts-query-language.md)を実行できます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel.png" alt-text="Azure Digital Twins Explorer のスクリーンショット。[クエリ エクスプローラー] パネルが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel.png":::

実行するクエリを入力して、 **[クエリの実行]** ボタンを選択します。 これにより、 **[ツイン グラフ]** パネルにクエリ結果が読み込まれます。

>[!NOTE]
> リレーションシップを含むクエリ結果は、結果に少なくとも 1 つのツインが含まれている場合にのみ、 **[ツイン グラフ]** パネルにレンダリングされます。 Azure Digital Twins では、リレーションシップのみを返すクエリを実行できますが、それらを Azure Digital Twins Explorer で表示できるのは、[[出力] パネル](#accessibility-and-advanced-settings)を使用した場合のみです。

### <a name="overlay-query-results"></a>クエリ結果にオーバーレイを適用する

パネルの内容を新しいクエリ結果に完全に置き換えるのではなく、現在 **[ツイン グラフ]** パネルに表示されている内容から結果を強調表示したい場合は、クエリを実行する前に **[結果にオーバーレイを適用する]** ボックスをオンにすることができます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-overlay-results.png" alt-text="Azure Digital Twins Explorer の [クエリ エクスプローラー] パネルのスクリーンショット。[結果にオーバーレイを適用する] ボックスがオンにされ、[ツイン グラフ] パネルに表示されている大きいグラフで 2 つのツインが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-overlay-results.png":::

現在 **[ツイン グラフ]** パネルに表示されていないものがクエリ結果に含まれている場合は、既存のビューにその要素が追加されます。

### <a name="save-and-rerun-queries"></a>クエリの保存と再実行

クエリは、ブラウザーのローカル ストレージに保存できるため、簡単に選択して再実行することができます。

>[!NOTE]
>ローカルのブラウザー ストレージは、クエリを保存したものとは別のブラウザーでは保存したクエリを使用できないことを意味します。ローカル ストレージが消去されるまで、クエリはブラウザー ストレージに無期限に残ります。

クエリを保存するには、クエリ ボックスにクエリを入力して、パネルの右側にある **[保存]** アイコンを選択します。 入力を求められたら、保存するクエリの名前を入力します。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-save.png" alt-text="Azure Digital Twins Explorer の [クエリ エクスプローラー] パネルのスクリーンショット。[保存] アイコンが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-save.png":::

クエリが保存されたら、 **[保存されたクエリ]** ドロップダウン メニューから選択して、簡単に再実行できます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-saved-queries.png" alt-text="Azure Digital Twins Explorer の [クエリ エクスプローラー] パネルのスクリーンショット。[保存されたクエリ] ドロップダウン メニューが強調表示され、2 つのサンプル クエリが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-saved-queries.png":::

保存したクエリを削除するには、 **[保存されたクエリ]** ドロップダウン メニューが開いているときに、クエリ名の横にある **[X]** アイコンを選択します。

>[!TIP]
>大きなグラフの場合は、限定されたサブセットに対してのみクエリを実行し、必要に応じてグラフの残りの部分を読み込むことをお勧めします。 **[ツイン グラフ]** パネルでツインをダブルクリックすると、追加の関連ノードを取得できます。

## <a name="explore-the-twin-graph"></a>ツイン グラフを確認する

**[ツイン グラフ]** パネルでは、インスタンス内のツインとリレーションシップを調べることができます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel.png" alt-text="Azure Digital Twins Explorer のスクリーンショット。[ツイン グラフ] パネルが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel.png":::

このパネルを使用すると、[ツインとリレーションシップを表示できます](#view-twins-and-relationships)。

[ツイン グラフ] パネルには、グラフの表示エクスペリエンスをカスタマイズするための機能もいくつか用意されています。
* [ツイン グラフのレイアウトを編集する](#edit-twin-graph-layout)
* [ツイン グラフの展開を制御する](#control-twin-graph-expansion)
* [ツイン グラフの要素を表示または非表示にする](#show-and-hide-twin-graph-elements)
* [ツイン グラフの要素をフィルター処理および強調表示する](#filter-and-highlight-twin-graph-elements)

### <a name="view-twins-and-relationships"></a>ツインとリレーションシップを表示する

[[クエリ エクスプローラー]](#query-your-digital-twin-graph) を使用してクエリを実行すると、 **[ツイン グラフ]** パネルに表示されるクエリ結果にツインとリレーションシップが表示されます。

>[!TIP]
>すべてのツインおよびリレーションシップを表示するクエリは `SELECT * FROM digitaltwins` です。

#### <a name="view-twin-and-relationship-properties"></a>ツインとリレーションシップのプロパティを表示する

ツインまたはリレーションシップのプロパティ値を表示するには、 **[ツイン グラフ]** でツインまたはリレーションシップを選択し、 **[プロパティ インスペクターの切り替え]** ボタンを使用して **[ツインのプロパティ]** または **[リレーションシップのプロパティ]** パネルをそれぞれ展開します。 このパネルには、要素に関連付けられているすべてのプロパティとその値が表示されます。 また、まだ設定されていないプロパティの既定値も表示されます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。FactoryA ツインが選択され、[ツインのプロパティ] パネルは展開されてツインのプロパティが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png":::

##### <a name="data-type-icons"></a>データ型のアイコン

**[ツインのプロパティ]** および **[リレーションシップのプロパティ]** パネルに表示される各プロパティには、DTDL モデルのフィールドの型を示すアイコンが表示されます。 アイコンの上にマウス ポインターを置くと、関連付けられている型を表示できます。

次の表は、使用可能なデータ型とそれに対応するアイコンを示しています。 また、この表には、各データ型から [DTDL 仕様](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas)のスキーマの説明へのリンクも記載されています。

| アイコン | データの種類 |
| --- | --- |
| ![ブール型のアイコン](./media/how-to-use-azure-digital-twins-explorer/data-icons/boolean.svg) | [boolean](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![コンポーネントのアイコン](./media/how-to-use-azure-digital-twins-explorer/data-icons/component.svg) | [コンポーネント](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component) |
| ![日付アイコン](./media/how-to-use-azure-digital-twins-explorer/data-icons/date.svg) | [date](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![dateTime アイコン](./media/how-to-use-azure-digital-twins-explorer/data-icons/datetime.svg) | [dateTime](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![継続時間のアイコン](./media/how-to-use-azure-digital-twins-explorer/data-icons/duration.svg) | [duration](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![列挙型のアイコン](./media/how-to-use-azure-digital-twins-explorer/data-icons/enum.svg) | [enum](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#enum) |
| ![マップのアイコン](./media/how-to-use-azure-digital-twins-explorer/data-icons/map.svg) | [map](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#map) |
| ![数値のアイコン](./media/how-to-use-azure-digital-twins-explorer/data-icons/numeric.svg) | [Double、float、integer、long](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) を含む数値型 |
| ![オブジェクトのアイコン](./media/how-to-use-azure-digital-twins-explorer/data-icons/object.svg) | [object](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#object) |
| ![文字列のアイコン](./media/how-to-use-azure-digital-twins-explorer/data-icons/string.svg) | [string](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![時刻のアイコン](./media/how-to-use-azure-digital-twins-explorer/data-icons/time.svg) | [time](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |

##### <a name="errors"></a>エラー

ツインまたはそのプロパティの一部がモデルと一致しなくなった場合、[ツインのプロパティ] パネルにエラー メッセージが表示されます。 

エラーには 2 つのシナリオがあり、それぞれに独自のエラー メッセージが表示されます。
* **ツインで使用されている 1 つまたは複数のモデルがみつかりません**。 この場合、ツインのプロパティ パネルで、そのモデルに関連付けられているすべてのプロパティに "missing" というフラグが設定されます。 これは、ツインの作成後にモデルが削除された場合に発生する可能性があります。
* **ツインの一部のプロパティが、ツインのモデルの一部ではありません**。 ツインのプロパティ パネルでは、これらのプロパティにのみ "missing" というフラグが設定されます。 これは、プロパティの設定後にツインのモデルが置換または変更され、そのプロパティが最新バージョンのモデルに存在しなくなった場合に発生する可能性があります。

両方のエラー メッセージは、次のスクリーンショットに示されています。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/properties-errors.png" alt-text="2 つのエラー メッセージが表示されている、Azure Digital Twins Explorer のツインのプロパティ パネルのスクリーンショット。1 つのエラーはモデルがないことを示し、もう 1 つはプロパティにモデルがないことを示しています。" lightbox="media/how-to-use-azure-digital-twins-explorer/properties-errors.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="view-a-twins-relationships"></a>ツインのリレーションシップを表示する

特定のツインに関係するすべてのリレーションシップのコードをすばやく表示することもできます (受信と発信のリレーションシップを含む)。

これを行うには、グラフ内のツインを右クリックして、 **[リレーションシップの取得]** を選択します。 これにより、受信および発信のすべてのリレーションシップの [JSON 表現](concepts-twins-graph.md#relationship-json-format)を表示する **[リレーションシップ情報]** モーダルが表示されます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-get-relationships.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。画面の中央には、受信と発信のリレーションシップを示す [リレーションシップ情報] モーダルが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-get-relationships.png":::

### <a name="edit-twin-graph-layout"></a>ツイン グラフのレイアウトを編集する

[ツイン グラフ] 画面でツインをクリックしてドラッグすると、さまざまな構成にツインを再配置することができます。

また、 **[レイアウトの選択]** メニューのオプションから、いくつかのレイアウト アルゴリズムのうちのいずれかをグラフに適用することもできます。 

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-layout.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。[レイアウトの選択] ボタンが強調表示され、レイアウト オプション (Cola、Dagre、fCoSE、Klay) を含むメニューが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-layout.png":::

### <a name="control-twin-graph-expansion"></a>ツイン グラフの展開を制御する

**[ツイン グラフ]** パネルにクエリ結果が表示されているときにツインをダブルクリックすると、そのリレーションシップと関連するツインをグラフに取り込んで、それらを表示することができます (ビューにまだ存在していない場合)。 この展開をカスタマイズするには、サイズと方向を設定して、取り込むツインの数を決定します。

展開するレイヤーの数を設定するには、 **[展開レベル]** オプションを使用します。 この数値は、選択したツインから取り込むリレーションシップのレイヤーの数を示します。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-level.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。[拡張レベル] ボタンが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-level.png":::

展開時に対象とするリレーションシップの種類を指定するには、 **[Expansion Direction]\(展開方向\)** ボタンを使用します。 これにより、受信のみ、発信のみ、または受信と発信のリレーションシップから選択できます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-direction.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。[Expansion Direction]\(展開方向\) ボタンが強調表示され、[受信]、[発信]、[受信/発信] の各オプションを含むメニューが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-direction.png":::

### <a name="show-and-hide-twin-graph-elements"></a>ツイン グラフの要素を表示または非表示にする

グラフ ビューでツインまたはリレーションシップを非表示にするには、このオプションを切り替えます。 

ツインまたはリレーションシップを非表示にするには、 **[ツイン グラフ]** ウィンドウでそれを右クリックします。 これにより、要素またはその他の関連する要素を非表示にするためのオプションを含むメニューが表示されます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-hide.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。FactoryA ツインが選択されていて、[選択した項目を非表示]、[選択した項目と子を非表示]、[他のすべてを非表示]、[子以外を非表示] のオプションを含むメニューがあります。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-hide.png":::

また、複数のツインまたはリレーションシップを一度に非表示にするには、CTRL/CMD キーまたは SHIFT キーを使用して、グラフ内の同じ種類のいくつかの要素を複数選択します。 ここから、同じ右クリックのプロセスに従って、非表示のオプションを参照します。

一部のツインを非表示にした後にすべてのツインが表示されるように戻すには、 **[すべて表示]** ボタンを使用します。 すべてのリレーションシップが表示されるように戻すには、 **[すべてのリレーションシップの表示]** ボタンを使用します。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-show.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。[すべて表示] と [すべてのリレーションシップの表示] ボタンが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-show.png":::

### <a name="filter-and-highlight-twin-graph-elements"></a>ツイン グラフの要素をフィルター処理および強調表示する

グラフに表示されるツインおよびリレーションシップをテキストでフィルター処理するには、この **[フィルター]** アイコンを選択します。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-filter-text.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。テキスト フィルター アイコンが選択され、検索用語を入力できる [フィルター] タブが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-filter-text.png":::

また、グラフに表示されるツインとリレーションシップをテキストで強調表示するには、この **[強調表示]** アイコンを選択します。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-text.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。テキスト フィルター アイコンが選択され、検索用語を入力できる [強調表示] タブが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-text.png":::

## <a name="manage-twins-and-graph"></a>ツインとグラフを管理する

Azure Digital Twins Explorer には、インスタンス内の[ツイン](concepts-twins-graph.md#digital-twins)と[リレーションシップ](concepts-twins-graph.md#relationships-a-graph-of-digital-twins)を管理するためのいくつかの方法が用意されています。

このセクションでは、次の管理アクティビティを実行する方法について説明します。
* [ツインとリレーションシップの単純なリストを表示する](#view-flat-list-of-twins-and-relationships)
* [ツインを作成する](#create-twins) (初期プロパティありまたはなしで)
* [リレーションシップの作成](#create-relationships) (ツイン間)
* [ツインとリレーションシップのプロパティを編集する](#edit-twin-and-relationship-properties)
* [ツインとリレーションシップを削除する](#delete-twins-and-relationships)

ツインとリレーションシップの表示エクスペリエンスの詳細については、[ツインとツイン グラフの確認](#explore-the-twin-graph)に関するセクションを参照してください。

### <a name="view-flat-list-of-twins-and-relationships"></a>ツインとリレーションシップの単純なリストを表示する

**[ツイン]** パネルには、ツインとそれに関連付けられているリレーションシップの単純なリストが表示されます。 ツインを名前で検索したり、展開して受信と発信のリレーションシップの詳細を確認することができます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twins-panel.png" alt-text="Azure Digital Twins Explorer の [ツイン] パネルのスクリーンショット。ツインが強調表示され、そのリレーションシップが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twins-panel.png":::

### <a name="create-twins"></a>ツインを作成する

新しいデジタル ツインは、 **[モデル]** パネルのモデルの定義から作成できます。

モデルからツインを作成するには、一覧でそのモデルを見つけて、モデル名の横にあるメニュー ドットを選択します。 その後、 **[Create a Twin]\(ツインの作成\)** を選択します。 新しいツインの **名前** を入力するように求められます。この名前は一意である必要があります。 次にツインを保存します。これにより、グラフに追加されます。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-create-a-twin.png" alt-text="Azure Digital Twins Explorer の [モデル] パネルのスクリーンショット。1 つのモデルのメニュー ドットが強調表示され、ツインを作成するメニュー オプションも強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-create-a-twin.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ツインにプロパティ値を追加するには、「[ツインとリレーションシップのプロパティを編集する](#edit-twin-and-relationship-properties)」を参照してください。

### <a name="create-relationships"></a>リレーションシップの作成

2 つのツイン間のリレーションシップを作成するには、まず **[ツイン グラフ]** ウィンドウでリレーションシップのソース ツインを選択します。 次に、CTRL/CMD キーまたは SHIFT キーを押しながら、リレーションシップのターゲットとなる 2 つ目のツインを選択します。

2 つのツインを同時に選択したら、ターゲットのツインを右クリックします。 これにより、それらの間に **[リレーションシップの追加]** を行うためのオプションを含むメニューが表示されます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-add-relationship.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。FactoryA と Consumer ツインが選択され、メニューに [リレーションシップの追加] のオプションが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-add-relationship.png":::

これにより、 **[リレーションシップの作成]** ダイアログが表示されます。このダイアログには、リレーションシップのソース ツインとターゲット ツインが表示され、その後に、ソース ツインが持つことができるリレーションシップの種類 (DTDL モデルで定義されます) を含む **[リレーションシップ]** ドロップダウン メニューが表示されます。 リレーションシップの種類のオプションを選択し、新しいリレーションシップを **[保存]** します。

### <a name="edit-twin-and-relationship-properties"></a>ツインとリレーションシップのプロパティを編集する

ツインまたはリレーションシップのプロパティ値を表示するには、 **[ツイン グラフ]** で要素を選択し、 **[プロパティ インスペクターの切り替え]** ボタンを使用して **[ツイン プロパティ]** または **[リレーションシップのプロパティ]** パネルをそれぞれ展開します。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。FactoryA ツインが選択され、[ツインのプロパティ] パネルは展開されてツインのプロパティが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png":::

このパネルを使用すると、書き込み可能なプロパティを直接編集できます。 値をインラインで更新し、パネルの上部にある **[変更を保存する]** ボタンを選択して保存します。 更新が保存されると、[更新 API](/rest/api/azure-digitaltwins/) によって適用された JSON パッチ操作を示すモーダル ウィンドウが画面に表示されます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties-save.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。画面の中央には、JSON パッチのコードを示す [パス情報] モーダルが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties-save.png":::

>[!TIP]
> **[ツインのプロパティ]** および **[リレーションシップのプロパティ]** パネルに表示される各プロパティには、DTDL モデルのフィールドの型を示すアイコンが表示されます。 型のアイコンの詳細については、「[データ型のアイコン](#data-type-icons)」を参照してください。

### <a name="delete-twins-and-relationships"></a>ツインとリレーションシップを削除する

ツインまたはリレーションシップを削除するには、 **[ツイン グラフ]** ウィンドウでそれを右クリックします。 これにより、要素を削除するためのオプションを含むメニューが表示されます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-delete.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。FactoryA ツインが選択され、[ツインの削除] のオプションを含むメニューがあります。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-delete.png":::

複数のツインまたはリレーションシップを一度に削除するには、CTRL/CMD キーまたは SHIFT キーを使用して、グラフ内の同じ種類のいくつかの要素を複数選択します。 ここから、同じ右クリックのプロセスに従って、要素を削除します。

また、インスタンス内のすべてのツインを同時に削除するには、上部のツール バーにある **[すべてのツインの削除]** ボタンを使用します。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/delete-all-twins.png" alt-text="Azure Digital Twins Explorer のスクリーンショット。[すべてのツインの削除] アイコンが選択されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/delete-all-twins.png":::

## <a name="explore-models-and-the-model-graph"></a>モデルとモデル グラフを確認する

モデルは、Azure Digital Twins Explorer 画面の左側にある **[モデル]** パネルと画面の中央にある **[モデル グラフ]** パネルの両方に表示できます。

**[モデル]** パネル: :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel.png" alt-text="Azure Digital Twins Explorer のスクリーンショット。[モデル] パネルが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel.png":::

**[モデル グラフ]** パネル: :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png" alt-text="Azure Digital Twins Explorer のスクリーンショット。[モデル グラフ] パネルが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png":::

これらのパネルを使用すると、[モデルを表示](#view-models)できます。

[モデル グラフ] パネルには、グラフの表示エクスペリエンスをカスタマイズするための機能もいくつか用意されています。
* [モデル グラフのレイアウトを編集する](#edit-model-graph-layout)
* [モデル グラフの要素をフィルター処理および強調表示する](#filter-and-highlight-model-graph-elements)
* [モデルの画像をアップロードする](#upload-model-images) (グラフにモデルを表します)

### <a name="view-models"></a>モデルの表示

インスタンス内のモデルの単純な一覧を **[モデル]** パネルに表示できます。 この一覧は、パネルの **検索** バーを使用して検索できます。

**[モデル グラフ]** パネルを使用すると、インスタンス内のモデルと、それらを相互に接続するリレーションシップ、継承、コンポーネントをグラフィカルに表示できます。

#### <a name="view-model-definition"></a>モデルの定義を表示する

モデルの完全な定義を表示するには、 **[モデル]** ペインでそのモデルを見つけて、モデル名の横にあるメニュー ドットを選択します。 その後、 **[モデルの表示]** を選択します。 これにより、モデルの生の DTDL 定義を示す **[モデル情報]** モーダルが表示されます。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-view.png" alt-text="Azure Digital Twins Explorer の [モデル] パネルのスクリーンショット。1 つのモデルのメニュー ドットが強調表示され、モデルを表示するメニュー オプションも強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-view.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

**[モデル グラフ]** でモデルを選択し、 **[モデルの詳細の切り替え]** ボタンを使用して **[モデルの詳細]** パネルを展開することによって、モデルの完全な定義を表示することもできます。 このパネルには、モデルのすべての DTDL コードも表示されます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-graph-details.png" alt-text="Azure Digital Twins Explorer の [モデル グラフ] パネルのスクリーンショット。Floor モデルが選択されていて、[モデルの詳細] パネルが展開され、モデルの DTDL コードが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-graph-details.png":::

### <a name="edit-model-graph-layout"></a>モデル グラフのレイアウトを編集する

[モデル グラフ] 画面でモデルをクリックしてドラッグすると、さまざまな構成にモデルを再配置することができます。

また、 **[レイアウトの選択]** メニューのオプションのいくつかのレイアウト アルゴリズムのいずれかを、モデル グラフに適用することもできます。 

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-layout.png" alt-text="Azure Digital Twins Explorer の [モデル グラフ] パネルのスクリーンショット。[レイアウトの選択] ボタンが強調表示され、レイアウト オプション (Cola、Dagre、fCoSE、Klay、d3Force) を含むメニューが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-layout.png":::

### <a name="filter-and-highlight-model-graph-elements"></a>モデル グラフの要素をフィルター処理および強調表示する

[モデル グラフ] に表示される接続の種類はフィルター処理できます。 このメニューのスイッチを使用していずれかの接続の種類をオフにすると、その接続の種類がグラフに表示されなくなります。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-connections.png" alt-text="Azure Digital Twins Explorer の [モデル グラフ] パネルのスクリーンショット。[リレーションシップ]、[継承]、[コンポーネント] のフィルター メニューが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-connections.png":::

この **[フィルター]** アイコンを選択することによって、グラフに表示されるモデルと接続をテキストでフィルター処理することもできます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-text.png" alt-text="Azure Digital Twins Explorer の [モデル グラフ] パネルのスクリーンショット。テキスト フィルター アイコンが選択され、検索用語を入力できる [フィルター] タブが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-text.png":::

この **[強調表示]** アイコンを選択することによって、グラフに表示されるモデルと接続をテキストで強調表示することもできます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-text.png" alt-text="Azure Digital Twins Explorer の [モデル グラフ] パネルのスクリーンショット。テキスト フィルター アイコンが選択され、検索用語を入力できる [強調表示] タブが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-text.png":::

### <a name="upload-model-images"></a>モデルの画像をアップロードする

カスタム画像をアップロードして、[モデル グラフ] および [[ツイン グラフ]](#explore-the-twin-graph) ビューでさまざまなモデルを表すことができます。 個々のモデルまたは複数のモデルの画像を一度にアップロードできます。

>[!NOTE]
>これらの画像は、ローカルのブラウザー ストレージに格納されます。 これは、画像を保存したものとは別のブラウザーでは画像を使用できないことを意味します。ローカル ストレージが消去されるまで、画像はブラウザー ストレージに無期限に残ります。

1 つのモデルの画像をアップロードするには、 **[モデル]** パネルでモデルを見つけて、モデル名の横にあるメニュー ドットを選択します。 次に、 **[Upload Model Image]\(モデル画像のアップロード\)** を選択します｡ 表示されたファイル セレクター ボックスで、マシン上のそのモデルにアップロードする画像ファイルに移動します。 **[開く]** を選択してアップロードします。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-one-image.png" alt-text="Azure Digital Twins Explorer の [モデル] パネルのスクリーンショット。1 つのモデルのメニュー ドットが強調表示され、モデル画像をアップロードするメニュー オプションも強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-one-image.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

また、モデルの画像を一括でアップロードすることもできます。

まず、次の手順を使用して、アップロードする前に画像ファイル名を設定します。 これにより、アップロード後に Azure Digital Twins Explorer で画像を適切なモデルに自動的に割り当てることができます。 
1. モデル ID 値で開始します (`dtmi:example:Floor;1` など)
1. ":" のインスタンスを "_" に置き換えます (この例では `dtmi_example_Floor;1` になります)
1. ";" のインスタンスを "-" に置き換えます (この例では `dtmi_example_Floor-1` になります)
1. ファイルに画像の拡張子が付いていることを確認します (この例では `dtmi_example_Floor-1.png` のようになります)

> [!NOTE]
> 上記の条件を使用して既存のモデルにマップされていない画像をアップロードしようとすると、画像はアップロードまたは保存されません。

次に、同時に複数の画像をアップロードするには、[モデル] パネルの上部にある **[モデルの画像のアップロード]** アイコンを使用します。 ファイル セレクター ボックスで、アップロードする画像ファイルを選択します。 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-images.png" alt-text="Azure Digital Twins Explorer の [モデル] パネルのスクリーンショット。モデルの画像のアップロード アイコンが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-images.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-models"></a>モデルを管理する

Azure Digital Twins Explorer 画面の左側にある **[モデル]** パネルを使用すると、一連のすべてのモデルまたは個々のモデルに対する管理アクティビティを実行できます。 

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel.png" alt-text="Azure Digital Twins Explorer のスクリーンショット。[モデル] パネルが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel.png":::

このパネルでは、次のモデル管理アクティビティを実行できます。
* [モデルをアップロードする](#upload-models) (Azure Digital Twins インスタンスに)
* [モデルを削除する](#delete-models) (インスタンスから)
* [モデルを最新の情報に更新する](#refresh-models) (すべてのモデルの一覧をこのパネルに再度読み込みます)

モデルの表示エクスペリエンスの詳細については、「[モデルとモデル グラフを確認する](#explore-models-and-the-model-graph)」を参照してください。

### <a name="upload-models"></a>モデルのアップロード

モデルを個別に選択するか、モデルのフォルダー全体を一度にアップロードすることで、マシンからモデルをアップロードできます。

個別に選択した 1 つ以上のモデルをアップロードするには、上向きの矢印が示されている **モデルのアップロード** アイコンを選択します。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload.png" alt-text="Azure Digital Twins Explorer の [モデル] パネルのスクリーンショット。モデルのアップロード アイコンが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

表示されたファイル セレクター ボックスで、マシン上のアップロードするモデルに移動します。 1 つまたは複数の JSON モデル ファイルを選択し、 **[開く]** を選択してアップロードします。

モデルのフォルダー (フォルダー内のすべてのものを含む) をアップロードするには、ファイル フォルダーが示されている **モデルのディレクトリのアップロード** アイコンを選択します。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-directory.png" alt-text="Azure Digital Twins Explorer の [モデル] パネルのスクリーンショット。モデルのディレクトリのアップロード アイコンが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-directory.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

表示されたファイル セレクター ボックスで、マシン上の JSON モデル ファイルを含むフォルダーに移動します。 **[開く]** を選択して、その最上位のフォルダーとそのすべての内容をアップロードします。

>[!IMPORTANT]
>モデルの定義内で別のモデルが参照されている場合 (リレーションシップやコンポーネントを定義する場合など)、それを使用するモデルをアップロードするには、参照されているモデルがインスタンス内に存在する必要があります。 モデルを 1 つずつアップロードする場合は、使用するモデルをアップロードする **前** に参照されているモデルをアップロードする必要があります。 モデルを一括でアップロードする場合は、同じインポートにそれらの両方を選択できます。Azure Digital Twins によって、それらをアップロードする順序が推論されます。

### <a name="delete-models"></a>モデルを削除する

[モデル] パネルを使用すると、個々のモデル、またはインスタンス内のすべてのモデルを一度に削除できます。

1 つのモデルを削除するには、そのモデルを一覧から見つけて、モデル名の横にあるメニュー ドットを選択します。 次に、 **[モデルの削除]** を選択します。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-one.png" alt-text="Azure Digital Twins Explorer の [モデル] パネルのスクリーンショット。1 つのモデルのメニュー ドットが強調表示され、モデルを削除するメニュー オプションも強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-one.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

インスタンス内のすべてのモデルを一度に削除するには、[モデル] パネルの上部にある **[すべてのモデルの削除]** アイコンを選択します。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-all.png" alt-text="Azure Digital Twins Explorer の [モデル] パネルのスクリーンショット。すべてのモデルの削除アイコンが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-all.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="refresh-models"></a>モデルを最新の情報に更新する

Azure Digital Twins Explorer を開くと、環境で使用可能なすべてのモデルが [モデル] パネルに自動的に表示されます。 

ただし、いつでもパネルを手動で更新して、Azure Digital Twins インスタンス内のすべてのモデルの一覧を再度読み込むことができます。 これを行うには、**モデルの更新** アイコンを選択します。 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-refresh.png" alt-text="Azure Digital Twins Explorer の [モデル] パネルのスクリーンショット。モデルの更新アイコンが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-refresh.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="importexport-graph"></a>グラフのインポート/エクスポート

**[ツイン グラフ]** パネルには、グラフ機能を [インポート](#import-graph)および [エクスポート](#export-graph-and-models)するオプションがあります。

### <a name="import-graph"></a>グラフのインポート

インポート機能を使用すると、ツイン、リレーションシップ、モデルをインスタンスに追加できます。 これは、多くのツイン、リレーションシップ、モデルを一度に作成する場合に便利です。

#### <a name="create-import-file"></a>インポート ファイルを作成する

グラフをインポートする最初の手順は、追加するツインとリレーションシップを表すファイルの作成です。

インポート ファイルは、次の 2 つの形式のいずれかにすることができます。
* このセクションの残りの部分で説明する **Excel ベースのカスタム形式**。 これにより、ツインとリレーションシップをアップロードできます。
* [グラフのエクスポート](#export-graph-and-models)で生成される **JSON ベースの形式**。 これには、ツイン、リレーションシップ、モデルを含めることができます。

Excel でカスタム グラフを作成するには、次の形式を使用します。

各行は作成する要素を表します (ツイン、リレーションシップ、またはツインと対応するリレーションシップの組み合わせ)。
ツインまたはリレーションシップのデータを構造化するには、次の列を使用します。 列名はカスタマイズできますが、この順序のままである必要があります。

| ModelID | id | リレーションシップ (ソース) | リレーションシップ名 | 初期データ |
| --- | --- | --- | --- | --- |
| *Optional*<br>作成する必要があるツインの DTMI モデル ID。<br><br>行でリレーションシップのみを作成する (ツインなし) 場合は、行のこの列を空白のままにすることができます。 | *必須*<br>ツインの一意の ID。<br><br>この行で新しいツインが作成される場合、これは新しいツインの ID です。<br>行にリレーションシップ情報がある場合、この ID はリレーションシップの **ターゲット** として使用されます。 | *Optional*<br>新しいリレーションシップの **ソース** ツインである必要があるツインの ID。<br><br>行でツインのみを作成する (リレーションシップなし) 場合は、行のこの列を空白のままにすることができます。 | *Optional*<br>作成する新しいリレーションシップの名前。 リレーションシップの方向は、列 C のツイン **から** 列 B のツイン **に** なります。 | *Optional*<br>作成されるツインのプロパティ設定を含む JSON 文字列。 プロパティは、列 A のモデルで定義されているプロパティと一致している必要があります。 |

2 つのフロアと 2 つのルームの小さなグラフを作成する .xlsx ファイルの例を次に示します。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/import-example.png" alt-text="Excel のグラフ データのスクリーンショット。列ヘッダーは上記のフィールドの順で対応しており、行には対応するデータ値が含まれています。" lightbox="media/how-to-use-azure-digital-twins-explorer/import-example.png":::

このファイルと追加の .xlsx グラフの例は、GitHub の [Azure Digital Twins Explorer リポジトリ](https://github.com/Azure-Samples/digital-twins-explorer/tree/main/client/examples)で確認できます。

>[!NOTE]
>.xlsx に記述されているプロパティとリレーションシップは、関連するツインのモデル定義に定義されている内容と一致する必要があります。

#### <a name="import-file-to-azure-digital-twins-explorer"></a>Azure Digital Twins Explorer にファイルをインポートする

インポートする準備ができたファイルをマシンに配置したら、[ツイン グラフ]パネルの **[グラフのインポート]** アイコンを選択します。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。[グラフのインポート] ボタンが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import.png":::

表示されたファイル セレクター ボックスで、マシン上のアップロードするグラフ ファイル (.xlsx または .json) に移動し、 **[開く]** を選択してアップロードします。

Azure Digital Twins で、インポートするグラフのプレビューが表示された **[インポート]** パネルが開きます。 確定するには、パネルの右上隅にある **[保存]** アイコンを選択します。

インポートが成功すると、アップロードされたモデル、ツイン、リレーションシップの数がモーダル ウィンドウに表示されます。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import-successful.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。画面の中央には、[4 つのツインがインポートされました] および [2 つのリレーションシップがインポートされました] というメッセージが示されている [インポートに成功しました] というモーダルが表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import-successful.png":::

### <a name="export-graph-and-models"></a>グラフとモデルのエクスポート

エクスポート機能を使用すると、モデル、ツイン、リレーションシップを含む部分的なグラフまたは完全なグラフをエクスポートできます。 エクスポートでは、最新のクエリ結果とインスタンス内のすべてのモデルのツインとリレーションシップが、お使いのマシンにダウンロードできる JSON ベースの形式にシリアル化されます。

まず、[[クエリ エクスプローラー]](#query-your-digital-twin-graph) パネルを使用して、ダウンロードするツインとリレーションシップを選択するクエリを実行します。 これにより、それらが [ツイン グラフ] パネルに読み込まれます。

>[!TIP]
>すべてのツインおよびリレーションシップを表示するクエリは `SELECT * FROM digitaltwins` です。

ダウンロードするグラフの一部が [ツイン グラフ] パネルに表示されたら、 **[グラフのエクスポート]** アイコンを選択します。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-export.png" alt-text="Azure Digital Twins Explorer の [ツイン グラフ] パネルのスクリーンショット。[グラフのエクスポート] ボタンが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-export.png":::

このアクションにより、[ツイン グラフ] ボックスで **[ダウンロード]** リンクが有効になります。 それを選択して、クエリ結果の JSON ベースの表現とインスタンス内のすべてのモデルをマシンにダウンロードします。

>[!TIP]
>このファイルは、[インポート](#import-graph)機能を使用して、編集または Azure Digital Twins への再アップロード (あるいはその両方) を行うことができます。

## <a name="link-to-your-environment"></a>環境へのリンク

他のユーザーと Azure Digital Twins Explorer 環境を共有して、共同作業を行うことができます。 このセクションでは、他のユーザーに Azure Digital Twins Explorer 環境を送信する方法と、ユーザーがアクセス許可を持っていることを検証する方法について説明します。

環境を共有するには、インスタンスに接続されている Azure Digital Twins Explorer ウィンドウを開くリンクを受信者に送信します。 次のリンクを使用し、プレースホルダーをお使いの Azure Digital Twins インスタンスの **テナント ID** と **ホスト名** に置き換えます。 

`https://explorer.digitaltwins.azure.net/?tid=<tenant-ID>&eid=<Azure-Digital-Twins-host-name>`

>[!NOTE]
> ここでは、ホスト名のプレースホルダーの値の前に、"*https://* " を **付けません**。

プレースホルダーの値を設定した URL の例を次に示します。

`https://explorer.digitaltwins.azure.net/?tid=00a000aa-00a0-00aa-0a0aa000aa00&eid=ADT-instance.api.wcus.digitaltwins.azure.net`

受信者が結果の Azure Digital Twins Explorer ウィンドウでインスタンスを表示するには、自分の Azure アカウントにログインし、そのインスタンスへの **Azure Digital Twins データ リーダー** アクセス権を持っている必要があります (Azure Digital Twins ロールの詳細については、[セキュリティ](concepts-security.md)に関するページを参照)。 受信者がグラフとデータに変更を加えるには、インスタンスに対する **Azure Digital Twins データ所有者** ロールを持っている必要があります。

### <a name="link-with-a-query"></a>クエリを含むリンク

環境を共有して、ランディング時に実行するクエリを指定し、チームメイトにサブグラフまたはカスタム ビューを強調表示したい場合があります。 これを行うには、まず環境の URL で、クエリ文字列パラメーターとしてクエリ テキストを URL に追加します。

`https://explorer.digitaltwins.azure.net/?tid=<tenant-ID>&eid=<Azure-Digital-Twins-host-name>&query=<query-text>`

クエリ テキストは URL エンコードする必要があります。 

>[!TIP]
>**[クエリ エクスプローラー]** ウィンドウからクエリ テキストを コピーし、URL ウィンドウの URL の末尾の正しい位置に貼り付けます。 この URL を送信すると、クエリ テキストが変換されて、適切な URL エンコードが使用されます。
>
> また、独立した URL エンコーダーを使用して、クエリ テキストを変換することもできます。

`SELECT * FROM digitaltwins` を実行するクエリのパラメーターの例は以下のとおりです:

`...&query=SELECT%20*%20FROM%20digitaltwins`

その後、完全な URL を共有できます。

## <a name="accessibility-and-advanced-settings"></a>アクセシビリティと詳細設定

Azure Digital Twins Explorer のいくつかの詳細設定を有効にして、エクスペリエンスをカスタマイズしたり、よりアクセスしやすくすることができます。

右上のツール バーにある **キーボード ショートカット** アイコンを使用すると、Azure Digital Twins Explorer の操作に使用できるキーボード ショートカットの一覧を表示することができます。

 :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/keyboard-shortcuts.png" alt-text="Azure Digital Twins Explorer のスクリーンショット。上部のツール バーにあるキーボード ショートカット アイコンが強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/keyboard-shortcuts.png":::

右上のツール バーにある設定の歯車アイコンからは、いくつかの高度な機能にアクセスすることができます。
* **一括読み込み**: クエリ結果に含まれて **いない** 他のツインとのリレーションシップを持つツインがクエリから返された場合、この機能によって、グラフをレンダリングする前に "含まれていない" ツインが読み込まれます。
* **キャッシュ**: この機能を有効にすると、クエリのパフォーマンスを向上させるために、Azure Digital Twins Explorer によって、リレーションシップとモデルのローカル キャッシュがメモリ内に保持されます。 これらのキャッシュは、関連する要素に対する書き込み操作や、ブラウザーの更新時に消去されます。 この機能は既定では無効です。
* **コンソール**: この機能を使用すると、コンソール ウィンドウが表示されます。このウィンドウでは、グラフを操作するための単純なシェル関数を使用できます。
* **出力**: この機能を使用すると、操作の診断トレースを示す出力ウィンドウが表示されます。
* **ハイ コントラスト**: この機能を使用すると、Azure Digital Twins Explorer の色を変更し、コントラストを高めて表示することができます。

Azure Digital Twins Explorer を構成するパネル ([クエリ エクスプローラー]、[ツイン]、[モデル]、[ツイン グラフ]、[モデル グラフ]) の位置を編集して、**パネル レイアウトをカスタマイズ** することができます。 パネルを別の位置に移動するには、パネル名をクリックしたままにして、新しい目的の位置にドラッグします。

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/panels.png" alt-text="Azure Digital Twins Explorer のスクリーンショット。[クエリ エクスプローラー]、[モデル]、[ツイン グラフ]、[モデル グラフ] パネルの名前が強調表示されています。" lightbox="media/how-to-use-azure-digital-twins-explorer/panels.png":::

パネルの位置は、ブラウザー ウィンドウの更新時にリセットされます。

## <a name="next-steps"></a>次の手順 

Azure Digital Twins のツイングラフ用のクエリの作成について学習します。 
* [クエリ言語](concepts-query-language.md)
* [ツイン グラフにクエリを実行する](how-to-query-graph.md)