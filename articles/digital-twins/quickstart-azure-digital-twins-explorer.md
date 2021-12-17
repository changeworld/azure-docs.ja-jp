---
title: クイックスタート - Azure Digital Twins Explorer での作業を始める
titleSuffix: Azure Digital Twins
description: モデルを使用してツインのインスタンスを作成し、ツイン グラフを操作するこちらのデモに従って、Azure Digital Twins Explorer の使用方法について説明します。
author: baanders
ms.author: baanders
ms.date: 10/4/2021
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 174efb47f8770496fbac8fa781b26b93c1b6b601
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133027"
---
# <a name="quickstart---get-started-with-a-sample-scenario-in-azure-digital-twins-explorer"></a>クイックスタート - Azure Digital Twins Explorer でサンプル シナリオを使用して作業を始める

このクイックスタートでは、[Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) を使用して、事前構築済みの Azure Digital Twins グラフを調べます。 このツールを使用すると、Azure portal 内で Azure Digital Twins のデータを視覚化して操作できます。

Azure Digital Twins を使用すると、より広範な IoT ソリューションの一部にできる、実際の環境のライブ モデルを作成して対話的に操作することができます。 最初に、個々の要素を "**デジタル ツイン**" としてモデル化します。 次に、ライブ イベントに応答して情報を照会できるナレッジ "**グラフ**" にそれらを接続します。

次の手順で行います。

1. Azure Digital Twins のインスタンスを作成し、Azure Digital Twins Explorer でそれに接続する。
1. 事前構築済みのモデルとグラフ データをアップロードしてサンプル シナリオを構築する。
1. 作成されたシナリオ グラフを精査する。
1. グラフに変更を加える。
1. 経験から学習したことを確認する。

ここで使用する Azure Digital Twins のサンプル グラフは、2 つのフロアと 2 つの部屋から成る建物を表します。 Floor0 には Room0 が含まれ、Floor1 には Room1 が含まれます。 グラフは次の画像のようになります。

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="Azure Digital Twins Explorer での、矢印で接続された 4 つの円形ノードで構成されたグラフのスクリーンショット。":::

>[!NOTE]
>このクイックスタートでは、事前構築済みのグラフをいろいろ試し、Azure Digital Twins でのデータの表現方法を理解します。 わかりやすくするために、このクイックスタートでは IoT Hub デバイスとそのグラフ表現の間の接続設定については取り上げません。 グラフで端から端までフローをつなげるには、[エンド ツー エンドのソリューションを接続する](tutorial-end-to-end.md)方法に関するチュートリアルまで進んでください。

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、Azure サブスクリプションが必要です。 まだお持ちでない場合は、すぐに [無料で作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) できます。

また、クイックスタートで使用するサンプル グラフの素材をダウンロードする必要もあります。 以下のリンクと説明を使用して、[digital-twins-explorer GitHub リポジトリ](https://github.com/Azure-Samples/digital-twins-explorer)から 3 つの必要なファイルをダウンロードします。 後で、これらを Azure Digital Twins にアップロードするための追加の手順について説明します。
* [Room.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-explorer/main/client/examples/Room.json): これは、建物内の部屋を表すモデル ファイルです。 リンク先に移動して、画面の任意の場所を右クリックし、ブラウザーの右クリック メニューで **[名前を付けて保存]** を選択します。 次の [名前を付けて保存] ウィンドウを使用して、コンピューターのどこかに **Room.json** という名前でファイルを保存します。
* [Floor.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-explorer/main/client/examples/Floor.json): これは、建物内のフロアを表すモデル ファイルです。 リンク先に移動して、画面の任意の場所を右クリックし、ブラウザーの右クリック メニューで **[名前を付けて保存]** を選択します。 次の [名前を付けて保存] ウィンドウを使用して、**Room.json** と同じ場所に、**Floor.json** という名前でファイルを保存します。
* [buildingScenario.xlsx](https://github.com/Azure-Samples/digital-twins-explorer/blob/main/client/examples/buildingScenario.xlsx): このファイルには、部屋とフロアのツインのグラフと、それらの間のリレーションシップが含まれています。 リンク先に移動し、 **[ダウンロード]** ボタンを選択します。 これにより、既定のダウンロード場所にファイルがダウンロードされます。

    :::image type="content" source="media/quickstart-azure-digital-twins-explorer/download-building-scenario.png" alt-text="GitHub の digital-twins-explorer/client/examples/buildingScenario.xlsx ファイルのスクリーンショット。[ダウンロード] ボタンが強調されています。" lightbox="media/quickstart-azure-digital-twins-explorer/download-building-scenario.png":::

## <a name="set-up-azure-digital-twins"></a>Azure Digital Twins を設定する

Azure Digital Twins を操作する際の最初の手順は、Azure Digital Twins インスタンスを作成することです。 サービスのインスタンスを作成した後、Azure Digital Twins Explorer でそのインスタンスに接続できます。これは、クイックスタート全体でインスタンスを操作するために使用します。

このセクションの残りの部分では、これらの手順について説明します。

### <a name="create-an-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを作成する

[!INCLUDE [digital-twins-setup-portal.md](../../includes/digital-twins-setup-portal.md)]

3. セットアップの **[基本]** タブにある、新しいインスタンスのサブスクリプション、リソース グループ、場所、リソース名などのフィールドに入力します。 インスタンス内のデータを管理するためのアクセス許可を自分に付与するには、 **[Assign Azure Digital Twins Data Owner Role]\(Azure Digital Twins データ所有者ロールの割り当て\)** ボックスをオンにします。

    >[!NOTE]
    > [Assign Azure Digital Twins Data Owner Role]\(Azure Digital Twins データ所有者ロールの割り当て\) ボックスが淡色表示になっている場合、リソースへのユーザー アクセスを管理するためのアクセス許可がお使いの Azure サブスクリプションに含まれていません。 このセクションでインスタンスの作成を続行できます。その後、このクイックスタートの残りの部分を完了する前に、必要なアクセス許可を持つユーザーに依頼し、[インスタンスでこのロールを自分に割り当ててもらう](how-to-set-up-instance-portal.md#assign-the-role-using-azure-identity-management-iam)必要があります。
    >
    > この要件を満たす一般的なロールは、**所有者**、**アカウント管理者**、**ユーザー アクセス管理者** と **共同作成者** の組み合わせです。  

4. **[確認と作成]** を選択し、インスタンスの作成を完了します。

    :::image type="content" source= "media/quickstart-azure-digital-twins-explorer/create-azure-digital-twins-basics.png" alt-text="Azure portal の Azure Digital Twins の [リソースの作成] プロセスのスクリーンショット。記述された値が入力されます。":::
    
5. 入力した詳細を示す概要ページが表示されます。 **[作成]** を選択して確定し、インスタンスを作成します。

インスタンスのデプロイ状態を追跡する概要ページが表示されます。

### <a name="open-instance-in-azure-digital-twins-explorer"></a>Azure Digital Twins Explorer でインスタンスを開く

インスタンスのデプロイが完了したら、 **[リソースに移動]** ボタンを使用してポータルのインスタンスの [概要] ページに移動します。

:::image type="content" source= "media/quickstart-azure-digital-twins-explorer/deployment-complete.png" alt-text="Azure portal の Azure Digital Twins の [デプロイ] ページのスクリーンショット。デプロイが完了したことがページに示されています。":::

次に、 **[Azure Digital Twins Explorer を開く (プレビュー)]** ボタンを選択します。

:::image type="content" source="media/includes/azure-digital-twins-explorer-portal-access.png" alt-text="Azure Digital Twins インスタンスの [概要] ページが表示されている Azure portal のスクリーンショット。[Azure Digital Twins Explorer を開く (プレビュー)] ボタンが強調されています。" lightbox="media/includes/azure-digital-twins-explorer-portal-access.png":::

これにより、インスタンスに接続された Azure Digital Twins Explorer ウィンドウが開きます。

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="インターネット ブラウザーでの Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

## <a name="upload-the-sample-materials"></a>サンプル素材をアップロードする

次に、Azure Digital Twins Explorer にサンプル モデルとグラフをインポートします。 「[前提条件](#prerequisites)」セクションでマシンにダウンロードしたモデル ファイルとグラフ ファイルを使用します。

### <a name="models"></a>モデル

Azure Digital Twins ソリューションで実行する最初の手順は、対象となる環境のボキャブラリを定義することです。 カスタム [モデル](concepts-models.md)を作成し、実際の環境に存在するエンティティのタイプを記述します。

各モデルは、デジタル ツイン定義言語 (DTDL) と呼ばれる JSON-LD のような言語で記述します。 各モデルで、"**プロパティ**"、"**テレメトリ**"、"**リレーションシップ**"、"**コンポーネント**" の観点から、1 種類のエンティティを記述します。 これらのモデルは、これらのタイプの特定インスタンスを表すデジタル ツインの基礎として後で使用します。

通常、モデルを作成する場合、次の 3 つの手順を実行します。

1. モデルの定義を作成する。 このクイックスタートでは、この手順は、サンプル ソリューションの一環として既に完了しています。
1. それを検証して構文が正確であることを確認する。 このクイックスタートでは、この手順は、サンプル ソリューションの一環として既に完了しています。
1. それを自分の Azure Digital Twins インスタンスにアップロードする。
 
このクイックスタートでは、モデル ファイルが既に作成および検証されています。 これらは、ダウンロードしたソリューションと共に含まれています。 このセクションでは、事前に作成された 2 つのモデルを自分のインスタンスにアップロードして、建物の環境を構成するこちらの要素を定義します。

* 床
* ルーム

#### <a name="upload-the-models-json-files"></a>モデル (.json ファイル) をアップロードする

こちらの手順に従って、モデル (先ほどダウンロードした *.json* ファイル) をアップロードします。

1. **[モデル]** パネルで、クラウドを指す矢印が表示されている **[モデルのアップロード]** アイコンを選択します。

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/upload-model.png" alt-text="[モデル] パネルとその中の [モデルのアップロード] アイコンが強調されている Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/quickstart-azure-digital-twins-explorer/upload-model.png":::
 
1. 表示される [開く] ウィンドウで、前にダウンロードした **Room.json** と **Floor.json** ファイルが格納されているフォルダーに移動します。
1. **Room.json** と **Floor.json** を選択し、 **[開く]** を選択して両方をアップロードします。 

Azure Digital Twins Explorer によって、これらのモデル ファイルが Azure Digital Twins インスタンスにアップロードされます。 これらは、フレンドリ名および完全なモデル ID と共に **[モデル]** パネルに表示されます。 **[モデルの表示]** という情報アイコンを選択すると、それらの背後にある DTDL コードが表示されます。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/model-info.png" alt-text="2 つのモデル定義 Floor と Room が含まれる [モデル] パネルが表示されている Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/quickstart-azure-digital-twins-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>ツインとツイン グラフ

自分の Azure Digital Twins インスタンスにいくつかのモデルをアップロードしたら、そのモデルの定義に準拠した[デジタル ツイン](concepts-twins-graph.md)を追加できます。

デジタル ツインは、ビジネス環境内の実際のエンティティを表します。 たとえば、農場のセンサーや車内の照明などです。このクイックスタートでは、建物のフロアにある部屋です。 特定のモデル タイプのツインを複数作成できます (同じ Room モデルを使用する複数の部屋など)。 これらをリレーションシップで接続し、環境全体を表す **ツイン グラフ** に反映することができます。

このセクションでは、事前作成済みのグラフに接続されている事前作成済みのツインをアップロードします。 このグラフには、2 つのフロアと 2 つの部屋が含まれていて、次のレイアウトで接続されています。

* Floor0
    - Room0 を含む
* Floor1
    - Room1 を含む

#### <a name="import-the-graph-xlsx-file"></a>グラフ (.xlsx ファイル) をインポートする

こちらの手順に従って、グラフ (先ほどダウンロードした *.xlsx* ファイル) をインポートします。

1. **[Twin Graph]\(ツイン グラフ\)** パネルで、クラウドを指す矢印が表示されている **[Import Graph]\(グラフのインポート\)** アイコンを選択します。

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-graph.png" alt-text="[Import Graph]\(グラフのインポート\) アイコンが強調された [グラフのビュー] パネルが示されている Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/quickstart-azure-digital-twins-explorer/import-graph.png":::

2. [開く] ウィンドウで、前にダウンロードした **buildingScenario.xlsx** ファイルに移動します。 このファイルには、サンプル グラフの説明が含まれています。 **[Open (開く)]** を選択します。

   数秒後、Azure Digital Twins Explorer で **[インポート]** ビューが開き、読み込まれるグラフのプレビューが表示されます。

3. グラフのアップロードを確認するには、グラフ プレビュー パネルの右上隅にある **[保存]** アイコンを選択します。

    :::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png" alt-text="グラフ プレビュー ペインの [保存] アイコンが強調されている Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png":::

4. アップロードしたファイルを使用して、要求したツインとそれらの間のリレーションシップが Azure Digital Twins Explorer によって作成されます。 終了すると、ダイアログ ボックスが表示されます。 **[閉じる]** を選択します。

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-success.png" alt-text="グラフのインポートが成功したことを示すダイアログ ボックスが表示されている Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/quickstart-azure-digital-twins-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. これでグラフが Azure Digital Twins Explorer にアップロードされました。 **[Twin Graph]\(ツイン グラフ\)** パネルに戻ります。
 
   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/twin-graph-tab.png" alt-text="[Twin Graph]\(ツイン グラフ\) タブが強調されている Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/quickstart-azure-digital-twins-explorer/twin-graph-tab.png"::: 
 
6. グラフを表示するには、Azure Digital Twins Explorer ウィンドウの上部近くにある、 **[クエリ エクスプローラー]** パネルの **[クエリの実行]** ボタンを選択します。

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/run-query.png" alt-text="ウィンドウの右上隅にある [クエリの実行] ボタンが強調されている Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/quickstart-azure-digital-twins-explorer/run-query.png":::

このアクションにより、すべてのデジタル ツインを選択して表示する既定のクエリが実行されます。 Azure Digital Twins Explorer によって、サービスからすべてのツインとリレーションシップが取得されます。 これらによって定義されたグラフが **[Twin Graph]\(ツイン グラフ\)** パネルに描画されます。

## <a name="explore-the-graph"></a>グラフを精査する

次は、アップロードしたサンプル シナリオのグラフを確認しましょう。

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="[グラフのビュー] パネルにツイン グラフが表示されている Azure Digital Twins Explorer のスクリーンショット。":::

円 (グラフの "ノード") は、デジタル ツインを表します。 線はリレーションシップを表します。 Floor0 ツインには Room0 が含まれており、Floor1 ツインには Room1 が含まれています。

マウスを使用する場合、グラフの要素をドラッグしてあちこちに動かすことができます。

### <a name="view-twin-properties"></a>ツインのプロパティを表示する

ツインを選択すると、そのプロパティと値の一覧が **[プロパティ]** パネルに表示されます。

次に示すのは、Room0 のプロパティです。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room0.png" alt-text="[プロパティ] パネルが強調されている Azure Digital Twins Explorer のスクリーンショット。Room0 の $dtId、Temperature、Humidity の各プロパティが表示されています。" lightbox="media/quickstart-azure-digital-twins-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room0 の温度は 70 度です。

次に示すのは、Room1 のプロパティです。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room1.png" alt-text="[プロパティ] パネルが強調されている Azure Digital Twins Explorer のスクリーンショット。Room1 の $dtId、Temperature、Humidity の各プロパティが表示されています。" lightbox="media/quickstart-azure-digital-twins-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room1 の温度は 80 度です。

### <a name="query-the-graph"></a>グラフのクエリを行う

Azure Digital Twins の主な機能は、環境についての質問に答える[クエリ](concepts-query-language.md)をツイン グラフに対して容易に、かつ効率よく実行できることです。

グラフ内のツインに対してクエリを実行する方法の 1 つは、それらの "**プロパティ**" を条件にすることです。 プロパティに基づいてクエリを実行すると、さまざまな質問への回答を得やすくなります。 たとえば、注意が必要な場合がある環境内の外れ値を見つけることができます。

このセクションでは、クエリを実行して、温度が 75 度を上回っている環境内のツインの数はいくつかという質問への回答を得ます。

回答を得るには、 **[クエリ エクスプローラー]** パネルで次のクエリを実行します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="TemperatureQuery":::

先ほどツインのプロパティを確認したことを思い出してください。Room0 の温度は 70 度、Room1 の温度は 80 度です。 したがって、ここで結果として返されるのは Room1 のみです。
    
:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png" alt-text="プロパティのクエリの結果が表示されている Azure Digital Twins Explorer のスクリーンショット。Room1 だけが表示されています。" lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png":::

>[!TIP]
> 前述のクエリ内では、その他の比較演算子 (<、>、=、!=) もサポートされます。 これらの演算子、さまざまな値、さまざまなツイン プロパティをクエリに組み込んで、独自の質問の答えを導き出してみてください。

## <a name="edit-data-in-the-graph"></a>グラフのデータを編集する

グラフに表されたツインのプロパティは、Azure Digital Twins Explorer を使用して編集できます。 このセクションでは、Room0 の温度を 76 度に上げます。

まず、次のクエリを再実行して、すべてのデジタル ツインを選択します。 これにより、 **[Twin Graph]\(ツイン グラフ\)** パネルに完全なグラフがもう一度表示されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="GetAllTwins":::

**Room0** を選択して、 **[プロパティ]** パネルにプロパティの一覧を表示します。

このリストのプロパティは編集することができます。 温度の値 **70** を選択して、新しい値を入力できるようにします。 「**76**」と入力し、 **[保存]** アイコンを選択して、温度を **76** 度に更新します。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png" alt-text="Room0 で編集できるプロパティが表示されている [プロパティ] パネルが強調されている Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

これで、 **[パッチ情報]** ウィンドウが表示され、更新を実行するための Azure Digital Twins [API](concepts-apis-sdks.md) と共に、バックグラウンドで使用されたパッチ コードが表示されます。 **[閉じる]** を選択します。

### <a name="query-to-see-the-result"></a>クエリを実行して結果を確認する

グラフでの更新内容が Room0 の温度に正常に登録されたことを確認するには、先ほどのクエリを再実行して、温度が 75 度を超えるすべてのツインを環境から取得します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="TemperatureQuery":::

Room0 の温度が 70 度から 76 度に変更されたので、今度は、両方のツインが結果に表示されているはずです。

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png" alt-text="プロパティのクエリの結果が表示されている Azure Digital Twins Explorer のスクリーンショット。Room0 と Room1 の両方が表示されています。" lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>学習内容の復習とまとめ

このクイックスタートでは、Azure Digital Twins のインスタンスを作成し、Azure Digital Twins Explorer を使用してそれにサンプル シナリオを設定しました。

その後、次のようにしてグラフを精査しました。

* クエリを使用して、シナリオについての質問の回答を得る。
* デジタル ツインのプロパティを編集する。
* 更新の結果、どのように回答が変化したかを調べるために同じクエリを再実行しました。

この演習の意図は、Azure Digital Twins のグラフをどのように使用すれば、環境が絶えず変化する中で、対象となる環境についての質問への回答が得られるかを示すことです。

このクイックスタートでは、温度を手動で更新しました。 Azure Digital Twins では、デジタル ツインがテレメトリ データに基づいて自動的に更新を受信できるよう、それらを実際の IoT デバイスに接続するのが一般的です。 このようにすると、環境の実際の状態を常に反映するライブ グラフを作成できます。 クエリを使用すると、環境内で起きていることについての情報をリアルタイムで取得できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートの後でクリーンアップするには、次に行うことに基づいて、削除するリソースを選択します。

* **Azure Digital Twins のチュートリアルを続行する場合**、このクイックスタートのインスタンスをそれらの記事で再利用できるため、インスタンスを削除する必要はありません。

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

さらに、サンプル プロジェクト フォルダーもローカル コンピューターから削除できます。

## <a name="next-steps"></a>次のステップ

引き続き Azure Digital Twins のチュートリアルに進んで、独自の Azure Digital Twins シナリオとインタラクション ツールを作成しましょう。

> [!div class="nextstepaction"]
> [クライアント アプリをコーディングする](tutorial-code.md)
