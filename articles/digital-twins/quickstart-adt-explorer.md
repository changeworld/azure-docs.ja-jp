---
title: クイック スタート - サンプル シナリオを精査する
titleSuffix: Azure Digital Twins
description: クイック スタート - ADT Explorer サンプルを使用して、事前構築済みのシナリオを可視化し、精査します。
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 466129e8435ef694821b078592a100a111a43f3a
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242281"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>クイック スタート - ADT Explorer を使用して、Azure Digital Twins のサンプル シナリオを精査する

Azure Digital Twins を使用すると、現実の環境のライブ モデルを作成して対話的に操作することができます。 そのためには、個々の要素を **デジタル ツイン** としてモデル化し、それらを接続してナレッジ **グラフ** にまとめます。それにより、ライブ イベントに応答したり、照会された情報を返したりすることができます。

このクイックスタートでは、 [**Azure Digital Twins (ADT) エクスプローラー**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)というサンプル アプリケーションを利用して、事前構築済みの Azure Digital Twins グラフを精査します。 ADT Explorer を使用すると、環境のデジタル表現をアップロードしたり、Azure Digital Twins の環境を表すために作成されたツインやグラフを視覚化したり、ブラウザーベースの視覚的な操作を通じてその他の管理アクティビティを実行したりできます。

このクイックスタートには主に次の手順が含まれています。

1. Azure Digital Twins インスタンスと ADT エクスプローラーを設定する
1. 事前構築済みのモデルとグラフ データをアップロードしてサンプル シナリオを構築する
1. 作成されたシナリオ グラフを精査する
1. グラフに変更を加える

ここで使用するサンプル グラフは、2 つのフロアと 2 つの部屋から成る建物を表します。 グラフの外観は次のとおりです。

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="矢印で接続された 4 つの円形ノードから成るグラフの画面&quot;Floor1&quot; という円が &quot;contains&quot; という矢印によって &quot;Room1&quot; という円に接続されている。&quot;Floor0&quot; という円が &quot;contains&quot; という矢印によって &quot;Room0&quot; という円に接続されている。&quot;Floor1&quot; と &quot;Floor0&quot; は接続されていない。":::

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、Azure サブスクリプションが必要です。 まだお持ちでない場合は、すぐに **[無料で作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** できます。

お使いのコンピューターには **Node.js** も必要になります。 次のリンクで最新バージョンを入手できます。[Node.js](https://nodejs.org/)。

最後に、クイックスタートの間に使用するサンプル ( **ADT エクスプローラー** サンプル アプリケーション) をダウンロードする必要もあります。 このサンプルには、クイックスタートで Azure Digital Twins シナリオを読み込んで精査する際に使用するアプリのほか、サンプル シナリオ ファイルが含まれています。 サンプルを入手するには、こちらに移動してください: [Azure Digital Twins (ADT) エクスプローラー](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)。 *[Download ZIP]\(ZIP のダウンロード\)* ボタンをクリックして、このサンプル コードの *.ZIP* ファイルをご自分のマシンにダウンロードしてください。 これにより、ZIP フォルダーが _**Azure_Digital_Twins__ADT__explorer.zip**_ としてマシンにダウンロードされます。 フォルダーを解凍し、ファイルを抽出します。

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Azure Digital Twins と ADT エクスプローラーを設定する

Azure Digital Twins を操作する際の最初の手順は、 **Azure Digital Twins インスタンスを設定する** ことです。 サービスのインスタンスを作成し、ADT Explorer で認証するための **資格情報を設定** したら、 **ADT Explorer でインスタンスに接続** し、このクイックスタートの後半でサンプル データを設定することができます。

このセクションの残りの部分では、これらの手順について説明します。

### <a name="set-up-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを設定する

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>ローカルの Azure 資格情報を設定する

ADT Explorer アプリケーションは、ローカル コンピューターで実行された Azure Digital Twins インスタンスに対し、[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (`Azure.Identity` ライブラリの一部) を使用してユーザーの認証を行います。 Azure Digital Twins に対してクライアント アプリの認証を行う各種の方法について詳しくは、 [*アプリ認証コードを作成する方法*](how-to-authenticate-client.md)に関するページで確認します。

この種類の認証では、ADT Explorer によってローカル環境内で資格情報が検索されます。たとえば、ローカル [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) や Visual Studio または Visual Studio Code での Azure ログインなどです。 つまり、ADT Explorer アプリの資格情報を設定するには、これらのメカニズムのいずれかを使用して、 **ローカルで Azure にログイン** する必要があります。

これらの方法のいずれかを使用して既に Azure にログインしている場合は、[次のセクション](#run-and-configure-adt-explorer)に進むことができます。

それ以外の場合は、これらの手順を使用してローカル **Azure CLI** をインストールできます。
1. [**こちらのインストール リンク**](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)のプロセスに従って、お使いの OS に一致するインストールを完了します。
2. お使いのコンピューターでコンソール ウィンドウを開きます。
3. `az login` を実行し、認証プロンプトに従って Azure アカウントにログインします。

これを実行した後は、ADT Explorer は、次のセクションで 実行されたときに、Azure 資格情報を自動的に取得するはずです。

認証コンソール ウィンドウは、必要に応じて閉じることができます。または、次の手順で使用するために開いたままにしておくこともできます。

### <a name="run-and-configure-adt-explorer"></a>ADT エクスプローラーを実行して構成する

次に、ADT エクスプローラー アプリケーションを実行し、自分の Azure Digital Twins インスタンス用に構成します。

ダウンロードして解凍した、 _**Azure_Digital_Twins__ADT__explorer**_ フォルダーに移動します。 コンソール ウィンドウを開いて、 *Azure_Digital_Twins__ADT__explorer/client/src* フォルダーの場所に移動します。

`npm install` を実行して、必須の依存関係をすべてダウンロードします。

次に、`npm run start` を実行してアプリを起動します。

数秒後、ブラウザー ウィンドウが開き、ブラウザーにアプリが表示されます。

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="localhost:3000 で実行中のアプリがブラウザー ウィンドウに表示されている。このアプリは ADT エクスプローラーと呼ばれ、クエリ エクスプローラー、モデル ビュー、グラフ ビュー、プロパティ エクスプローラーのボックスが存在する。画面上にはまだデータが入力されていない。" lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

設定したインスタンスを操作できるように ADT Explorer を構成するため、ウィンドウ上部の *[サインイン]* ボタン (下のイメージに表示) をクリックします。 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="ADT エクスプローラーのウィンドウ上部付近にあるサインイン アイコンが強調表示されている。このアイコンには、人のシンプルなシルエットに鍵のシルエットが重なるように表示されている。" lightbox="media/quickstart-adt-explorer/sign-in.png":::

「 [前提条件](#prerequisites)」セクションで先ほど収集した " *Azure Digital Twins インスタンスの URL* " を *https://{インスタンス ホスト名}* の形式で入力します。

>[!NOTE]
> 同じアイコンを選択して [サインイン] ボックスを呼び出すことで、この情報はいつでも再表示して編集することができます。 指定した値は維持されます。

> [!TIP]
> 接続時に `SignalRService.subscribe` エラー メッセージが表示される場合は、Azure Digital Twins の URL が *https://* で始まることを確認してください。

Microsoft の *[要求されているアクセス許可]* ポップアップ ウィンドウが表示された場合は、このアプリケーションに同意し、承諾して続行します。

## <a name="add-the-sample-data"></a>サンプル データを追加する

次に、ADT エクスプローラーにサンプル シナリオとグラフをインポートします。 サンプル シナリオは、先ほどダウンロードした **Azure_Digital_Twins__ADT__explorer** フォルダーにもあります。

### <a name="models"></a>モデル

Azure Digital Twins ソリューションで実行する最初の手順は、対象となる環境のボキャブラリの定義です。 これは、カスタム [**モデル**](concepts-models.md)を作成して、実際の環境に存在するエンティティのタイプを記述することによって行います。 

それぞれのモデルは、 **Digital Twin Definition Language (DTDL)** という JSON-LD に似た言語で作成され、単一のエンティティ タイプがその " *プロパティ* "、" *テレメトリ* "、" *リレーションシップ* "、" *コンポーネント* " の観点から記述されます。 それらのモデルは後で、これらのタイプの特定のインスタンスを表すデジタル ツインの基礎として使用することになります。

通常、モデルを作成する際は、3 つの手順を実行します。
1. モデルの定義を作成する (このクイックスタートでは、サンプル ソリューションの一環として既に完了しています)
2. それを検証して構文が正確であることを確認する (このクイックスタートでは、サンプル ソリューションの一環として既に完了しています)
3. それを自分の Azure Digital Twins インスタンスにアップロードする
 
このクイックスタートでは、モデル ファイルはあらかじめ作成されており、検証も済んだ状態で、ダウンロードしたソリューションに同梱されています。 このセクションでは、事前に作成された 2 つのモデルを自分のインスタンスにアップロードして、建物の環境を構成するこれらの要素を定義します。
* 床
* ルーム

#### <a name="upload-models"></a>モデルのアップロード

*[モデル ビュー]* ボックスの *[Upload a Model]\(モデルのアップロード\)* アイコンをクリックします。

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="[モデル ビュー] ボックスの中央のアイコンが強調表示されている。クラウドに向かう矢印を示している。" lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. 表示されたファイル セレクター ボックスで、ダウンロードしたリポジトリの *Azure_Digital_Twins__ADT__explorer/client/examples* フォルダーに移動します。
2. *Room.json* と *Floor.json* を選択し、[OK] をクリックします。 (必要であればさらに別のモデルをアップロードすることもできますが、このクイックスタートでは使用しません。)
3. Azure アカウントへのサインインを求めるポップアップ ダイアログに従います。

>[!NOTE]
>次のようなエラー メッセージが表示されることがあります。:::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="&quot;Error: Error fetching models: ClientAuthError: Error opening popup window. This can happen if you are using IE or if popups are blocked in the browser. &quot; (エラー: モデルをフェッチしているときにエラーが発生しました: ClientAuthError: ポップアップ ウィンドウを開いているときにエラーが発生しました。これは、IE を使用しているか、ポップアップがブラウザーでブロックされている場合に発生します。) という、一番下に [閉じる] ボタンを備えたポップアップが表示される" border="false"::: 
> ポップアップ ブロッカーを無効にするか、別のブラウザーを使用してみてください。

ADT エクスプローラーによって、これらのモデル ファイルが Azure Digital Twins インスタンスにアップロードされます。 これらは、フレンドリ名と完全なモデル ID と共に *[モデル ビュー]* ボックスに表示されます。 *[モデルの表示]* という情報バブルをクリックすると、それらの背後にある DTDL コードが表示されます。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Floor (dtmi:example:Floor;1) と Room (dtmi:example:Room;1) という 2 つのモデルの定義が一覧表示されている [モデル ビュー] ボックス。各モデルの [モデルの表示] アイコン (円の中にアルファベットの &quot;i&quot;) が強調表示されている。" lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>ツインとツイン グラフ

自分の Azure Digital Twins インスタンスにいくつかのモデルをアップロードしたら、そのモデルの定義に準拠した [**デジタル ツイン**](concepts-twins-graph.md)を追加できます。 

デジタル ツインは、農場のセンサー、車内の照明、(このクイックスタートで取り上げる) 建物内の部屋など、対象となるビジネス環境内の実際のエンティティを表します。 特定のモデル タイプのツインを複数作成して (同じ *Room* モデルを使用する複数の部屋など)、それらをリレーションシップで接続し、環境全体を表す **ツイン グラフ** に反映することができます。

このセクションでは、接続された事前作成済みのツインをアップロードして、事前作成済みのグラフに反映します。 このグラフには、2 つのフロアと 2 つの部屋が含まれていて、次のレイアウトで接続されています。
* *Floor0*
    - *Room0* を含む
* *Floor1*
    - *Room1* を含む

#### <a name="import-the-graph"></a>グラフをインポートする

*[グラフ ビュー]* ボックスの *[Import Graph]\(グラフのインポート\)* アイコンをクリックします。

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="[グラフ ビュー] ボックスで強調表示されているアイコン。クラウドに向かう矢印を示している。" lightbox="media/quickstart-adt-explorer/import-graph.png":::

ファイル セレクター ボックスで *Azure_Digital_Twins__ADT__explorer/client/examples* フォルダーに移動し、 _**buildingScenario.xlsx**_ スプレッドシート ファイルを選択します。 このファイルには、サンプル グラフの説明が含まれています。 [OK] をタップします。

数秒後、ADT エクスプローラーで *[インポート]* ビューが開き、読み込みの対象となるグラフのプレビューが表示されます。

グラフのアップロードを確認するには、 *[グラフ ビュー]* の右上隅にある *[保存]* アイコンをクリックします。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="[Graph Preview]\(グラフのプレビュー\) ペインで強調表示されている [保存] アイコン" lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

これで、アップロードしたファイルを使用して、要求したツインとそれらの間のリレーションシップが ADT エクスプローラーによって作成されます。 完了したことを示すダイアログが表示されます。 *[閉じる]* をクリックします。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="グラフのインポート成功を示すダイアログ ボックス。インポートに成功し、4 つのツイン、2 つのリレーションシップがインポートされたことを示している。" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

これでグラフが ADT エクスプローラーにアップロードされました。 グラフを表示するには、ADT Explorer ウィンドウの上部付近にある *[Graph エクスプローラー]* ボックスの *[クエリの実行]* ボタンをクリックします。 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="ウィンドウ上部付近にある [クエリの実行] ボタンが強調表示されている" lightbox="media/quickstart-adt-explorer/run-query.png":::

すべてのデジタル ツインを選択して表示する既定のクエリが実行されます。 ADT エクスプローラーによって、すべてのツインとリレーションシップがサービスから取得され、それらによって定義されたグラフが *[グラフ ビュー]* ボックスに描画されます。

## <a name="explore-the-graph"></a>グラフを精査する

次は、アップロードしたサンプル シナリオのグラフを確認しましょう。

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="ツイン グラフが存在する [グラフ ビュー] ボックスのビュー。&quot;floor1&quot; という円が &quot;contains&quot; という矢印によって &quot;room1&quot; という円に接続されている。&quot;floor0&quot; という円が &quot;contains&quot; という矢印によって &quot;room0&quot; という円に接続されている。":::

円 (グラフの "ノード") はデジタル ツインを表し、線はリレーションシップを表しています。 *Floor0* ツインには *Room0* が、そして *Floor1* ツインには *Room1* が含まれていることがわかります。

マウスを使用している場合、グラフの要素をクリックしてドラッグすることにより、あちこちに動かすことができます。

### <a name="view-twin-properties"></a>ツインのプロパティを表示する 

ツインを選択すると、そのプロパティと値を *[PROPERTY EXPLORER]\(プロパティ エクスプローラー\)* ボックスに一覧表示できます。 

次に示すのは、 *Room0* のプロパティです。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="枠で強調表示された [Property Explorer]\(プロパティ エクスプローラー\) ボックスに Room0 のプロパティが表示されている。たとえば、$dtId フィールドは &quot;Room0&quot;、Temperature フィールドは 70、Humidity フィールドは 30 となっている。" lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

*Room0* の温度は **70** 度であることがわかります。

次に示すのは、 *Room1* のプロパティです。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="枠で強調表示された [Property Explorer]\(プロパティ エクスプローラー\) ボックスに Room1 のプロパティが表示されている。たとえば、$dtId フィールドは &quot;Room1&quot;、Temperature フィールドは 80、Humidity フィールドは 60 となっている。" lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

*Room1* の温度は **80** 度であることがわかります。

### <a name="query-the-graph"></a>グラフのクエリを行う

Azure Digital Twins の主な機能は、環境についての質問に答える[クエリ](concepts-query-language.md)をツイン グラフに対して容易に、かつ効率よく実行できることです。 

グラフ内のツインに対してクエリを実行する方法の 1 つは、それらの " *プロパティ* " を条件にすることです。 プロパティをクエリの条件とすることにより、環境内で注意すべき外れ値を見つけるなど、さまざまな質問への答えが得やすくなります。

このセクションでは、" _**自分の環境に存在するツインのうち、温度が 75 度を超えるツインをすべて知りたい**_ " という質問に回答するクエリを実行します。

回答を得るためには、 *[QUERY EXPLORER]\(クエリ エクスプローラー\)* ボックスで次のクエリを実行します。

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

先ほどツインのプロパティを確認したことを思い出してください。 *Room0* の温度は **70** 度、 *Room1* の温度は **80** 度です。 したがって、ここで結果として返されるのは _**Room1**_ だけです。
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="プロパティ クエリの結果 (Room1 のみが表示されている)" lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> 上記のクエリでは、その他の比較演算子 ( *<* 、 *>* 、 *=* 、 *!=* ) もサポートされます。 こういったさまざまな値やさまざまなツイン プロパティをクエリに当てはめながら、独自の質問の答えを導き出してみてください。

## <a name="edit-data-in-the-graph"></a>グラフのデータを編集する

グラフに表されたツインのプロパティは、ADT エクスプローラーを使用して編集できます。 このセクションでは、 **Room0 の " _温度_ " を 76 度に上げます** 。

そのためには、 *[PROPERTY EXPLORER]\(プロパティ エクスプローラー\)* ボックスで *Room0* を選択し、そのプロパティ リストを呼び出します。

このリストのプロパティは編集することができます。 温度の値 **70** を選択して、新しい値を入力できるようにします。 「 **76** 」と入力して *[保存]* アイコンをクリックすると、温度が **76** 度に更新されます。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Room0 のプロパティを表示する [Property Explorer]\(プロパティ エクスプローラー\) ボックス。温度の値が編集可能な状態で &quot;76&quot; と表示されている。また、[保存] アイコンが強調表示されている。" lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

保存に成功すると、 *[Patch Information]\(パッチ情報\)* ウィンドウが現れ、更新を実行するための Azure Digital Twins [API](how-to-use-apis-sdks.md) と共に、バックグラウンドで使用されたパッチ コードが表示されます。 *[閉じる]* をクリックします。

### <a name="query-to-see-the-result"></a>クエリを実行して結果を確認する

グラフでの更新内容が *Room0* の温度に正常に登録されたことを確認するには、先ほどのクエリを再実行して、 **温度が 75 度を超えるすべてのツインを環境から取得** します。

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

*Room0* の温度が **70** 度から **76** 度に変更されたので、今度は、両方のツインが結果に表示されているはずです。

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="プロパティ クエリの結果 (Room0 と Room1 の両方が表示されている)" lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>学習内容の復習とまとめ

このクイックスタートでは、Azure Digital Twins インスタンスを作成して ADT エクスプローラーに接続し、それにサンプル シナリオを設定しました。 

その後、次のようにしてグラフを精査しました。
1. クエリを使用して、シナリオについての質問の回答を得る。
2. デジタル ツインのプロパティを編集する。
3. 更新の結果、どのように回答が変化したかを調べるために同じクエリを再実行しました。

この演習の意図は、Azure Digital Twins のグラフをどのように使用すれば、環境が絶えず変化する中で、対象となる環境についての質問への回答が得られるかを示すことです。 

このクイックスタートでは、温度の更新を手動で行いましたが、Azure Digital Twins では、デジタル ツインがテレメトリ データに基づいて自動的に更新を受信できるよう、それらを実際の IoT デバイスに接続するのが一般的です。 そうすれば、実際の環境の状態を絶えず反映するライブ グラフを構築できるほか、環境内で起きていることについての情報をクエリを使用してリアルタイムに得ることができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートの作業を終了するために、まず、実行中のコンソール アプリを終了してください。 ブラウザーの ADT エクスプローラー アプリへの接続が切断され、ブラウザーでライブ データを表示できなくなります。 ブラウザー タブは閉じてかまいません。

引き続き Azure Digital Twins のチュートリアルに進む予定の場合は、それらの記事に、このクイックスタートで使用したインスタンスを再利用できるので、それを削除する必要はありません。
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

最後に、ローカル コンピューターにダウンロードしたプロジェクトのサンプル フォルダー ( _**Azure_Digital_Twins__ADT__explorer**_ ) を削除します。 zip されているバージョンと解凍されたバージョンの両方を削除する必要がある場合があります。

## <a name="next-steps"></a>次のステップ 

引き続き Azure Digital Twins のチュートリアルに進んで、独自の Azure Digital Twins シナリオとインタラクション ツールを作成しましょう。

> [!div class="nextstepaction"]
> [*チュートリアル:クライアント アプリをコーディングする*](tutorial-code.md)