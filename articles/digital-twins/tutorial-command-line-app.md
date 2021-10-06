---
title: 'チュートリアル: Azure Digital Twins でグラフを作成する (クライアント アプリ)'
titleSuffix: Azure Digital Twins
description: サンプル コマンド ライン アプリケーションを使用して Azure Digital Twins シナリオを作成するチュートリアル
author: baanders
ms.author: baanders
ms.date: 9/1/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 4d75685ce62258c18d3c501ae08acd2678e177a6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658376"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-a-sample-client-app"></a>チュートリアル: サンプル クライアント アプリを使用して Azure Digital Twins グラフを作成する

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

このチュートリアルでは、モデル、ツイン、およびリレーションシップを使用して、Azure Digital Twins でグラフを作成します。 このチュートリアルのツールは、Azure Digital Twins インスタンスを操作するための<bpt id="p1">**</bpt>サンプル コマンドライン クライアント アプリケーション<ept id="p1">**</ept>です。 このクライアント アプリは、<bpt id="p1">[</bpt>クライアント アプリのコーディング<ept id="p1">](tutorial-code.md)</ept>に関する記事で作成したアプリに似ています。

このサンプルを使用して、モデルのアップロード、ツインの作成と変更、リレーションシップの作成など、基本的な Azure Digital Twins アクションを実行することができます。 <bpt id="p1">[</bpt>サンプルのコード<ept id="p1">](https://github.com/Azure-Samples/digital-twins-samples/tree/master/)</ept>を見て、Azure Digital Twins の API について学んだり、サンプル プロジェクトに自由に変更を加えながら、独自のコマンドを実装する練習をしたりすることもできます。

このチュートリアルでは次のことを行います。
> [!div class="checklist"]
> * 環境をモデル化する
> * デジタル ツインの作成
> * リレーションシップを追加して、グラフを形成する
> * グラフのクエリを実行して、質問に回答する

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

### <a name="run-the-sample-project"></a>サンプル プロジェクトを実行する

アプリと認証が設定されたので、ツール バーの次のボタンを使用してプロジェクトを実行します。

<bpt id="p1">:::image type="content" source="media/tutorial-command-line/app/start-button-sample.png" alt-text="</bpt>Visual Studio のスタート ボタン (SampleClientApp プロジェクト) のスクリーンショット。<ept id=&quot;p1&quot;>" lightbox="media/tutorial-command-line/app/start-button-sample.png":::</ept>

コンソール ウィンドウが表示されて認証が行われ、コマンドを待つ状態となります。 

次に示したのは、プロジェクト コンソールの画面例を示すスクリーンショットです。

<bpt id="p1">:::image type="content" source="media/tutorial-command-line/app/command-line-app.png" alt-text="</bpt>コマンドライン アプリからのウェルカム メッセージのスクリーンショット。<ept id=&quot;p1&quot;>" lightbox="media/tutorial-command-line/app/command-line-app.png":::</ept>

> [!TIP]
> このプロジェクトで使用できる全コマンドの一覧を確認するには、プロジェクト コンソールで「<ph id="ph1">`help`</ph>」と入力し、Return キーを押します。

アプリが正常に実行されていることを確認したら、コンソール ウィンドウを閉じて、アプリの実行をひとまず停止します。 この記事の後の方で、再び実行します。

## <a name="model-a-physical-environment-with-dtdl"></a>DTDL を使用して物理環境をモデル化する

Azure Digital Twins インスタンスとサンプル アプリが設定されたので、シナリオのグラフの作成を開始できます。 

Azure Digital Twins ソリューションを作成するにあたり最初にすべきことは、対象となる環境のツイン <bpt id="p1">[</bpt>モデル<ept id="p1">](concepts-models.md)</ept>の定義です。 

モデルは、オブジェクト指向プログラミング言語におけるクラスに似ています。つまりモデルは、<bpt id="p1">[</bpt>デジタル ツイン<ept id="p1">](concepts-twins-graph.md)</ept>のユーザー定義のテンプレートとなります。デジタル ツインは、そのテンプレートに従ってインスタンス化されることになります。 モデルは、**Digital Twins Definition Language (DTDL)** という JSON に似た言語で記述され、ツインの "*プロパティ*"、"*テレメトリ*"、"*リレーションシップ*"、"*コンポーネント*" を定義することができます。

> [!NOTE]
> デジタル ツインに対する "<bpt id="p1">*</bpt>コマンド<ept id="p1">*</ept>" も DTDL で定義できます。 ただし Azure Digital Twins サービスでは現在、コマンドがサポートされていません。

<bpt id="p1">_</bpt><bpt id="p2">**</bpt>AdtE2ESample<ept id="p2">**</ept><ept id="p1">_</ept> プロジェクトを開いた Visual Studio ウィンドウで、 <bpt id="p3">*</bpt>[ソリューション エクスプローラー]<ept id="p3">*</ept> ペインを使用して "<bpt id="p4">*</bpt>AdtSampleApp\SampleClientApp\Models フォルダー<ept id="p4">*</ept>" に移動します。 このフォルダーにサンプル モデルが格納されています。

<bpt id="p1">*</bpt>[Room.json]<ept id="p1">*</ept> を選択して編集ウィンドウで開き、次のように変更を加えます。

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Azure Digital Twins にモデルをアップロードする

モデルを設計したら、Azure Digital Twins インスタンスにアップロードする必要があります。 そうすることで、カスタム ドメインの独自のボキャブラリを使用して、Azure Digital Twins サービス インスタンスが構成されます。 モデルをアップロードしたら、そのモデルを使用するツイン インスタンスを作成できます。

1. 前のセクションで Room.json ファイルを編集した後、コンソール アプリの実行をもう一度開始します。

1. プロジェクト コンソール ウィンドウで、次のコマンドを実行して、更新済みの Room モデルと、次のセクションで異なる種類のツインを作成するために使用する Floor モデルをアップロードします。

    ```cmd/sh
    CreateModels Room Floor
    ```
    
    出力結果を見ると、モデルが正常に作成されたことがわかります。

1. <ph id="ph1">`GetModels true`</ph> コマンドを実行して、モデルが作成されたことを確認します。 このコマンドによって、Azure Digital Twins インスタンスにアップロードされたすべてのモデルの完全な情報が出力されます。 この結果から、編集済みの Room モデルを探してみましょう。

    <bpt id="p1">:::image type="content" source="media/tutorial-command-line/app/output-get-models.png" alt-text="</bpt>GetModels の結果 (更新済みの Room モデルを表示) のスクリーンショット。<ept id=&quot;p1&quot;>" lightbox="media/tutorial-command-line/app/output-get-models.png":::</ept>

### <a name="errors"></a>エラー

このサンプル アプリケーションは、サービスからのエラーも処理します。 

`CreateModels` コマンドを再実行して、もう一度、アップロードしたものと同じモデルをどれか再アップロードしてみてください。

```cmd/sh
CreateModels Room
```

モデルを上書きすることはできないので、このコマンドにより、今度はサービス エラーが返されます。
既存のモデルを削除する方法について詳しくは、<bpt id="p1">[</bpt>DTDL モデルの管理<ept id="p1">](how-to-manage-model.md)</ept>に関する記事を参照してください。
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

## <a name="create-digital-twins"></a>デジタル ツインを作成する

Azure Digital Twins インスタンスにいくつかのモデルをアップロードしたら、そのモデルの定義に基づいて<bpt id="p1">[</bpt>デジタル ツイン<ept id="p1">](concepts-twins-graph.md)</ept>を作成できます。 デジタル ツインは、農場のセンサー、建物内の部屋、車内の照明など、対象となるビジネス環境内のエンティティを表します。 

デジタル ツインを作成するには、<ph id="ph1">`CreateDigitalTwin`</ph> コマンドを使用します。 ツインのベースとなるモデルを参照する必要があります。モデルのプロパティには、必要に応じて初期値を定義することができます。 この段階では、リレーションシップ情報を渡す必要はありません。

1. 実行中のプロジェクト コンソールでこのコードを実行すると、いくつかのツインが作成されます。先ほど更新した Room モデルに基づくツインと、もう 1 つのモデル (Floor) に基づくツインです。 Room には 3 つのプロパティがあったことを思い出してください。これらのプロパティの初期値を引数で指定することができます。 (プロパティ値の初期化は一般に省略可能ですが、このチュートリアルでは必要です。)

    ```cmd/sh
    CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
    CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
    CreateDigitalTwin dtmi:example:Floor;1 floor0
    CreateDigitalTwin dtmi:example:Floor;1 floor1
    ```

    コマンドから返される出力を見ると、ツインが正常に作成されたことがわかります。 
    
    <bpt id="p1">:::image type="content" source="media/tutorial-command-line/app/output-create-digital-twin.png" alt-text="</bpt>CreateDigitalTwin コマンドの結果からの抜粋 (floor0、floor1、room0、room1 を含む) を示すスクリーンショット。<ept id=&quot;p1&quot;>" lightbox="media/tutorial-command-line/app/output-create-digital-twin.png":::</ept>

1. <ph id="ph1">`Query`</ph> コマンドを実行すると、ツインが作成されたことを確認できます。 Azure Digital Twins インスタンスに対し、そこに含まれるすべてのデジタル ツインがこのコマンドによって照会されます。 その結果から、room0、room1、floor0、floor1 のツインを探します。

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

### <a name="modify-a-digital-twin"></a>デジタル ツインに変更を加える

作成したツインのプロパティを変更することもできます。 

> [!NOTE]
> 基になる REST API では、ツインに対する更新を定義するために、<bpt id="p1">[</bpt>JSON Patch<ept id="p1">](http://jsonpatch.com/)</ept> 形式が使用されます。 また、コマンドライン アプリでは、この形式を使用して、基になる API が予期する内容を含む真のエクスペリエンスを提供します。

1. このコマンドを実行して、room0 の RoomName を "Room0" から "PresidentialSuite" に変更します。
    
    ```cmd/sh
    UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
    ```
    
    出力結果を見ると、ツインが正常に更新されたことがわかります。

1. 次のコマンドを実行して <bpt id="p1">*</bpt>room0<ept id="p1">*</ept> の情報を表示すると、更新が成功したことを確認できます。

    ```cmd/sh
    GetDigitalTwin room0
    ```
    
    更新後の名前が出力結果に反映されているはずです。


## <a name="create-a-graph-by-adding-relationships"></a>リレーションシップを追加してグラフを作成する

次に、ツイン間にいくつかの<bpt id="p1">**</bpt>リレーションシップ<ept id="p1">**</ept>を作成することで、それらのツインを接続し、<bpt id="p2">[</bpt>ツイン グラフ<ept id="p2">](concepts-twins-graph.md)</ept>を形成することができます。 ツイン グラフは、環境全体を表すために使用されます。 

あるツインから別のものへと作成できるリレーションシップの種類は、前にアップロードした<bpt id="p1">[</bpt>モデル<ept id="p1">](#model-a-physical-environment-with-dtdl)</ept>内に定義されています。 [Floor のモデル定義](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)には、フロアが *contains* という型のリレーションシップを持てることが指定されています。そのため、各 Floor ツインから、それに含まれる対応する部屋に対して *contains* 型のリレーションシップを作成できます。

リレーションシップを追加するには、<ph id="ph1">`CreateRelationship`</ph> コマンドを使用します。 リレーションシップの接続元となるツインと、リレーションシップの種類、リレーションシップの接続先のツインを指定します。 最後に、リレーションシップに一意の ID を指定します。

1. 次のコードを実行すると、先ほど作成した各 Floor ツインから対応する Room ツインへの "contains" リレーションシップが追加されます。 リレーションシップには、relationship0 と relationship1 という名前が付けられます。

    ```cmd/sh
    CreateRelationship floor0 contains room0 relationship0
    CreateRelationship floor1 contains room1 relationship1
    ```

    >[!TIP]
    >また、<bpt id="p2">[</bpt>Floor モデル<ept id="p2">](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)</ept>内の <bpt id="p1">*</bpt>contains<ept id="p1">*</ept> リレーションシップは、<ph id="ph1">`ownershipUser`</ph> と <ph id="ph2">`ownershipDepartment`</ph> の 2 つの文字列プロパティを指定して定義されていたため、リレーションシップを作成するときに、これらの初期値を引数として指定することもできます。
    > relationship0 を作成する上記のコマンドの代替バージョンを次に示します。これは、これらのプロパティの初期値も指定しています。
    > ```cmd/sh
    > CreateRelationship floor0 contains room0 relationship0 ownershipUser string MyUser ownershipDepartment string myDepartment
    > ``` 
    
    リレーションシップが正常に作成されたことが、コマンドから返される出力で確認できます。
    
    <bpt id="p1">:::image type="content" source="media/tutorial-command-line/app/output-create-relationship.png" alt-text="</bpt>CreateRelationship コマンドの結果からの抜粋 (relationship0 と relationship1 を含む) のスクリーンショット。<ept id=&quot;p1&quot;>" lightbox="media/tutorial-command-line/app/output-create-relationship.png":::</ept>

1. リレーションシップは、ご自分の Azure Digital Twins インスタンスのリレーションシップを出力する、次の任意のコマンドを使用して確認できます。
    * 各フロアを接続元とするすべてのリレーションシップを確認するには (一方の側からリレーションシップを表示):
        ```cmd/sh
        GetRelationships floor0
        GetRelationships floor1
        ```
    * 各部屋を接続先とするすべてのリレーションシップを表示するには ("もう一方の" 側からリレーションシップを表示):
        ```cmd/sh
        GetIncomingRelationships room0
        GetIncomingRelationships room1
        ```
    * これらのリレーションシップを ID で個別に検索するには:
        ```cmd/sh
        GetRelationship floor0 relationship0
        GetRelationship floor1 relationship1
        ```

このチュートリアルで設定したツインとリレーションシップによって、次の概念グラフが形成されます。

<bpt id="p1">:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="</bpt>概念グラフを示す図 (floor0 は relationship0 によって room0 に接続され、floor1 は relationship1 によって room1 に接続されている)。<ept id=&quot;p1&quot;>" border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::</ept>

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>環境についての質問をツイン グラフに照会する

Azure Digital Twins の主な機能は、環境についての質問に答える<bpt id="p1">[</bpt>クエリ<ept id="p1">](concepts-query-language.md)</ept>をツイン グラフに対して容易に、かつ効率よく実行できることです。 

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

サンプル環境に関するいくつかの質問に回答するには、実行中のプロジェクト コンソールで次のコマンドを実行します。

1. <bpt id="p1">**</bpt>Azure Digital Twins で表されている自分の環境内のすべてのエンティティを知りたい<ept id="p1">**</ept> (すべて照会)

    ```cmd/sh
    Query
    ```

    このコマンドを使用すると、環境の概要を調べ、必要な事柄がすべて Azure Digital Twins 内で表現されていることを確認できます。 このコマンドの結果は、個々のデジタル ツインとその詳細を含んだ出力です。 その一部を次に示します。

    <bpt id="p1">:::image type="content" source="media/tutorial-command-line/app/output-query-all.png" alt-text="</bpt>ツイン クエリの部分的な結果 (room0 と floor1 を含む) を示すスクリーンショット。<ept id=&quot;p1&quot;>":::</ept>

    >[!TIP]
    >サンプル プロジェクトでは、引数が追加されていない <ph id="ph1">`Query`</ph> コマンドは、<ph id="ph2">`Query SELECT * FROM DIGITALTWINS`</ph> に相当します。 <bpt id="p1">[</bpt>クエリ API<ept id="p1">](/rest/api/digital-twins/dataplane/query)</ept> または <bpt id="p2">[</bpt>CLI コマンド<ept id="p2">](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)</ept>を使用して、インスタンス内のすべてのツインに対してクエリを実行するには、より長い (完全な) クエリを使用します。

1. <bpt id="p1">**</bpt>環境内に存在する部屋をすべて知りたい<ept id="p1">**</ept> (モデルで照会)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    クエリを特定のタイプのツインに制限することで、表現されている内容についての、より具体的な情報を取得することができます。 このコマンドを実行すると、room0 と room1 は表示されますが、floor0 と floor1 は (room ではなく floor であるため) 表示 **されません**。
    
    <bpt id="p1">:::image type="content" source="media/tutorial-command-line/app/output-query-model.png" alt-text="</bpt>モデル クエリの結果 (room0 と room1 のみ表示) のスクリーンショット。<ept id=&quot;p1&quot;>":::</ept>

1. <bpt id="p1">**</bpt><bpt id="p2">*</bpt>floor0<ept id="p2">*</ept> に存在するすべての部屋を知りたい<ept id="p1">**</ept> (リレーションシップで照会)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    グラフ内のリレーションシップに基づいてクエリを実行すると、ツインの関係性についての情報を入手したり、クエリを特定のエリアに制限したりすることができます。 floor0 に存在するのは room0 のみです。したがって、結果に含まれる部屋は room0 のみとなります。

    <bpt id="p1">:::image type="content" source="media/tutorial-command-line/app/output-query-relationship.png" alt-text="</bpt>リレーションシップ クエリの結果 (room0 を表示) のスクリーンショット。<ept id=&quot;p1&quot;>":::</ept>

1. <bpt id="p1">**</bpt>自分の環境に存在するツインのうち、温度が 75 度を超えるツインをすべて知りたい<ept id="p1">**</ept> (プロパティで照会)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    プロパティに基づいてグラフを照会することにより、さまざまな質問への答えを得ることができます。たとえば、環境内で注意すべき外れ値を見つけることもできます。 その他の比較演算子 ( <bpt id="p1">*</bpt><ph id="ph1"><</ph><ept id="p1">*</ept> , <bpt id="p2">*</bpt><ph id="ph2">></ph><ept id="p2">*</ept> 、 <bpt id="p3">*</bpt><ph id="ph3">=</ph><ept id="p3">*</ept> 、 <bpt id="p4">*</bpt>!=<ept id="p4">*</ept> ) もサポートされます。 ここでは、温度が 80 である room1 が結果として表示されます。

    <bpt id="p1">:::image type="content" source="media/tutorial-command-line/app/output-query-property.png" alt-text="</bpt>プロパティ クエリの結果 (room1 のみ表示) のスクリーンショット。<ept id=&quot;p1&quot;>":::</ept>

1. <bpt id="p1">**</bpt><bpt id="p2">*</bpt>floor0<ept id="p2">*</ept> 上で温度が 75 度を超えるすべての部屋を知りたい<ept id="p1">**</ept> (複合クエリ)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    SQL と同様、結合演算子 (<ph id="ph1">`AND`</ph>、<ph id="ph2">`OR`</ph>、<ph id="ph3">`NOT`</ph> など) を使用して、先行するクエリを結合することもできます。 このクエリは、<ph id="ph1">`AND`</ph> を使用して、ツインの温度に関する先行するクエリを絞り込んでいます。 結果には、floor0 上の部屋のうち、温度が 75 度を超える部屋のみが表示されます。このケースでは、該当する部屋はありません。 結果セットは空になります。

    <bpt id="p1">:::image type="content" source="media/tutorial-command-line/app/output-query-compound.png" alt-text="</bpt>複合クエリの結果 (結果なし) のスクリーンショット。<ept id=&quot;p1&quot;>" lightbox="media/tutorial-command-line/app/output-query-compound.png":::</ept>

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを終えたら、次に行う作業に応じて、削除するリソースを選択できます。

* <bpt id="p1">**</bpt>次のチュートリアルに進む場合<ept id="p1">**</ept>は、ここで設定したリソースを残しておいてください。この Azure Digital Twins インスタンスと構成済みのサンプル アプリを引き続き次のチュートリアルで使用します。

* <bpt id="p1">**</bpt>Azure Digital Twins インスタンスは引き続き使用するものの、そのモデル、ツイン、関係をすべてクリアする場合<ept id="p1">**</ept>は、サンプル アプリの <ph id="ph1">`DeleteAllTwins`</ph> コマンドと <ph id="ph2">`DeleteAllModels`</ph> コマンドをそれぞれ使用して、インスタンスからツインとモデルをクリアすることができます。

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

さらに、プロジェクト フォルダーもローカル コンピューターから削除してください。

## <a name="next-steps"></a>次のステップ 

このチュートリアルでは、サンプル クライアント アプリケーションを使用してインスタンスにグラフを作成することで、Azure Digital Twins の使用を開始しました。 モデル、デジタル ツイン、およびリレーションシップを作成して、グラフを形成しました。 また、Azure Digital Twins で回答できる環境についての質問の種類を理解するために、グラフに対していくつかのクエリを実行しました。

次のチュートリアルに進み、Azure Digital Twins を他の Azure サービスと組み合わせて、データ ドリブンのエンド ツー エンドのシナリオを完了してください。
> [!div class="nextstepaction"]
> <bpt id="p1">[</bpt>エンドツーエンドのソリューションを接続する<ept id="p1">](tutorial-end-to-end.md)</ept>