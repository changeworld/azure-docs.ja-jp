---
title: 産業用 IoT を使用して Azure Data Explorer にデータをプルする
description: このチュートリアルでは、IIoT データを ADX に移動し、ダッシュボードを作成して表示する方法について説明します。
author: eross-msft
ms.author: lizross
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 6/16/2021
ms.openlocfilehash: 4ddd2e14d3be90c211d0e278a11e45bd51154fa1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550023"
---
# <a name="tutorial-using-industrial-iot-to-pull-data-into-azure-data-explorer"></a>チュートリアル: 産業用 IoT を使用して Azure Data Explorer にデータをプルする

Azure Industrial IoT (IIoT) プラットフォームは、エッジ モジュールおよびクラウド マイクロサービスを Azure platform-as-a-service (PaaS) サービスと組み合わせて、産業用資産検出と資産データ取得の機能を提供するものです。 産業環境において、このタスク用の事実上の標準は OPC UA です。 このチュートリアルでは、クラウドベースの条件監視という単純なユース ケースを例として使用して、Azure を OPC UA で使用するために構成する方法について説明します。

このチュートリアルでは、次の内容を学習します。
- Azure アカウントを取得し、IoT ハブをデプロイする。
- IoT Edge をデプロイする
- OPC Publisher をインストールして構成する
- 時系列データベースを設定する。
- ダッシュボードを作成する。
- 受信データのグラフを作成する。

## <a name="requirements"></a>必要条件

このチュートリアルを開始するには、Azure アカウントが必要です。 お持ちでない場合は、[ここをクリック](https://azure.microsoft.com/free/)して無料で入手できます。

## <a name="deploy-an-s1-iot-hub"></a>S1 IoT ハブをデプロイする

Azure サブスクリプションで最初に行う必要があるのは、[ポータル](../azure-portal/azure-portal-quickstart-center.md)にアクセスして [S1 の IoT Hub をデプロイ](../iot-hub/iot-hub-create-through-portal.md)することです。 無料の Azure アカウントで、1 日あたり 40 万個の 4K メッセージを使用できます。 1 つの 4K メッセージに約 15 個の OPC UA PubSub データ タグを適用できるので、データ タグは 1 日あたり 600 万個となり、このユース ケースには十分です。 IoT ハブがデプロイされたら、*iothubowner* プライマリ接続文字列をコピーします ([共有アクセス ポリシー] の [iothubowner])。 これは IoT Edge のインストールに必要です。

## <a name="deploy-iot-edge"></a>IoT Edge をデプロイする

Windows 10 PC または VM で [IoT Edge インストーラー](https://lnkd.in/ga6Ew4X)を使用して IoT Edge をデプロイします。 この PC または VM は、インターネットに接続できる必要があります。また、使用するマシンまたは産業用接続アダプター ソフトウェア (dataFEED、Zenon、Kepware など) にアクセスできる必要もあります。 施設で OT ネットワーク (マシンが配置されている) からインターネットへの接続に複数のネットワーク レイヤーを使用している場合は、IoT Edge デバイスを入れ子 (つまり "デイジーチェーン") にすることができます。 また、[GitHub 上](https://github.com/Azure/Industrial-IoT-Gateway-Installer/blob/master/Releases/Linux.zip)のインストーラーを使用して、Linux でネイティブに IoT Edge を実行することもできます。

IoT Edge インストーラーを実行するときに、 **[Use Docker Desktop]\(Docker Desktop を使用する\)** チェックボックスをオンにし、 **[Configure IoT Edge for Azure Industrial Cloud Platform (PaaS)]\(Azure Industrial Cloud Platform (PaaS) 用に IoT Edge を構成する\)** ラジオ ボタンを選択したままにして、表示されるテキスト ボックスに *iothubowner* 接続文字列を貼り付けます。 インストーラーには、実行している PC の名前が Edge インスタンス名として自動的に事前設定されていますが、これは必要に応じて変更できます。 **[インストール]** をクリックします。 Docker Desktop が検出されなかった場合、インストーラーで自動的にダウンロードされます。 それをインストールし、最適なパフォーマンスを実現するために必ず Linux 用 Windows サブシステム V2 (WSL2) を使用して構成します。 Docker Desktop のインストールが完了したら、IoT Edge インストーラーを再起動し、もう一度構成してから、再度 [インストール] をクリックします。 次に、Azure portal で **[IoT Hub]** ページの **[IoT Edge]** タブに移動し、新しくインストールした IoT Edge デバイスが表示されるまで待ちます ([最新の情報に更新] を数回クリックすることが必要な場合があります)。

## <a name="install-and-configure-opc-publisher"></a>OPC Publisher をインストールして構成する

次に、OPC Publisher をインストールして構成します。これは、Edge で OPC UA PubSub を使用してすべてのテレメトリ データを標準化および正規化するために使用されます。 OPC UA JSON エンコードを使用します。最初に別のクラウド サービスを使用して、クラウド データベースで認識できるようにバイナリ形式を変換し直す必要がなく、クラウド データベースに直接取り込むことができるためです。 この業界標準形式を使用すると、クラウドでの管理のオーバーヘッドとコストが減ります。 これは重要なポイントです。多くの人が、より効率的なテレメトリ エンコードを見つけたと言いますが、テレメトリをクラウドで大規模にクラウド データベース対応の形式に変換するコストと管理の側面は常に過小評価されています。 

次に、OPC Publisher の **[はじめに]** ページに移動し、指示に従います。 **[コンテナーの作成オプション]** として、次のように指定します。

```
{
    "Hostname": "OPCPublisher",
    "Cmd": [
        "PkiRootPath=/appdata/pki", 
        "--lf=/appdata/publisher.log", 
        "--pf=/appdata/publishednodes.json",
        "--aa",
        "--di=60",
        "--me=Json",
        "--mm=PubSub"
    ],
    "HostConfig": {
        "Binds": [
            "/c/IoTEdgeMapping:/appdata"
        ]
    }
}
```

次に、マシン (または接続アダプター ソフトウェア) からのクラウド時系列テレメトリ メッセージの生成元となる OPC UA データ ノードを構成します。 これを行うには、**publishednodes.json** テンプレート ファイルをここから _*_C:\IoTEdgeMapping_*_ にコピーし、OPC UA サーバー *EndpointURL* と、クラウドに送信するデータの OPC UA ノード ID を入力します。 何を送信すべきかわからない場合、ID が ns=0;i=2258 のデータ ノードはサーバーの現在時刻であり、1 秒ごとに変更されます。 OPC UA では、データが変更された場合にのみデータの変更内容が公開されます。 ただし、OPC Publisher の場合、変更されていなくてもデータを特定の間隔で読み取りおよび送信することがサポートされています。

ファイルの編集が完了したら、コマンドライン コマンド "**iotedge restart OPCPublisher**" を使用して OPC Publisher を再起動します。OPC Publisher によって、指定したすべての OPC UA サーバーへの接続と、リストにあるすべてのノードの公開が試みられます。 まず IoT Edge を実行している PC から UA Expert などの OPC UA クライアントを使用して OPC UA サーバーに接続できることを確認し、DNS が動作しない場合に備えて、hostname ではなく EndpointURL で OPC UA サーバーの IP アドレスを試す必要があることが、経験からわかっています。 次に、Azure portal の **[IoT Hub]** ページに戻り、**デバイスからクラウド** へのメッセージが受信されていることが **[概要]** タブに表示されることを再確認します。

## <a name="set-up-the-time-series-database"></a>時系列データベースを設定する

次に、OPC UA テレメトリを受け取るクラウドの時系列データベースを設定します。 このためには Azure Data Explorer を使用することをお勧めします。最適なパフォーマンスを実現するために複数のデータベース レイヤーを使用し、豊富な分析とダッシュボードの機能を備え、予測と異常検出もサポートしています。 Azure portal からインスタンスをデプロイし、開発またはテストのワークロード SKU を選択します。構成でストリーミング インジェストを必ず有効にしてください。

OPC UA では、OPC UA サーバーの **DatasetWriterID** と **ExpandedNodeID** (名前空間とノード ID の両方を含む) によって、テレメトリ データ タグを一意に識別します。 さらに、データが接続されたクライアントで使用できるようになる前に、OPC UA サーバーによって読み取られたときのタイムスタンプは、*SourceTimeStamp* と呼ばれます。 そのため、OPC UA テレメトリの時系列を作成するには、**DatasetWriterID**、**ExpandedNodeID**、読み取られたデータ値、**SourceTimeStamp** をデータベース テーブルに指定します。 これを Azure Data Explorer を使用してここで作成します。 データ値の型 (float、int など) も OPC UA PubSub メッセージに含めることができる点にご注意ください。これは、OPC UA 仕様で "*可逆エンコード*" と呼ばれます。

まず、10 年間という既定のデータ保持期間を使用してデータベースを作成します。このユース ケースにはこれで十分です。 次に、 **[データ接続を作成する]** をクリックして **[IoT Hub]** を選択し、接続に名前を付け、ドロップダウンから IoT ハブを選択して、IoT ハブからの直接ストリーミング インジェストを構成します。 **[共有アクセス ポリシー]** で *[iothubowner]* を、 **[コンシューマー グループ]** として *[$Default]* を選択します。 ここでは **[テーブル名]** を空白のままにし、 **[データ形式]** で *[MULTILINE JSON]\(複数行 JSON\)* を選択し、 **[マッピング名]** に「*opcua_mapping*」を入力します。 **[作成]** を選択します。

## <a name="create-the-database-tables"></a>データベース テーブルを作成する

新しいデータベースに必要なテーブルを作成します。 まず、未加工の OPC UA PubSub JSON テレメトリ メッセージを受信するための 1 つの列を含むステージング テーブルを作成します。 Azure Data Explorer に組み込まれた Kusto クエリ言語 (KQL) を使用します。これについてここで説明しています。 **[クエリ]** タブを選択し、次のように入力します。

```
.create table opcua_raw(payload: dynamic)

```

次のコマンドを使用して、前に設定した IoT ハブを使用した *opcua_mapping* テレメトリ インジェストとのマッピングを作成します。

```
.create table opcua_raw ingestion json mapping "opcua_mapping" @'[{"column":"payload","path":"$","datatype":"dynamic"}]'

```

作成したばかりのテーブルのテーブル名を使用してテレメトリ インジェストを更新します。それには、 **[データ インジェスト]** タブを選択し、データ インジェストの名前を選択します。次に **[編集]** を選択し、 *[テーブル名]* に「**opcua_raw**」を入力して、 **[更新]** をクリックします。 **[クエリ]** タブに戻り、最初のテレメトリ メッセージがテーブルに入力されるまで数分待ちます。 これを確認するには、テーブル内の 10 行に対してクエリを実行します。

```
opcua_raw
| take 10
```

**DataSetWriterID** によって識別されるデータセットを使用して、テレメトリを 1 つのメッセージにバッチ化することが OPC UA PubSub でサポートされています。 これらのデータセットは、データベース内の個別の行に "バッチ解除" される必要があります。 これを行うには、*mv-expand* 演算子を使用して、最初に作成する必要がある新しい中間テーブルに JSON インジェストを展開します。

```
.create table opcua_intermediate(DataSetWriterID: string, payload: dynamic)
```

ここで、展開を行うために次の関数を作成します。

```
.create-or-alter function OPCUARawExpand() {
    opcua_raw
    |mv-expand records = payload.Messages
    |project 
        DataSetWriterID = tostring(records["DataSetWriterId"]),
        Payload = todynamic(records["Payload"])
}
```

次に、新しい関数を適用して中間テーブルを更新します。

```
.alter table opcua_intermediate policy update @'[{"Source": "opcua_raw", "Query": "OPCUARawExpand()", "IsEnabled": "True"}]'
```

新しいテーブルが正しく事前設定されることを確認します。

```
opcua_intermediate
| take 10
```

 OPC UA データセットの各エントリをさらに展開して、最終的な OPC UA テレメトリ テーブルを作成して事前設定します。

```
.create table opcua_telemetry (DataSetWriterID: string, ExpandedNodeID: string, Value: dynamic, SourceTimestamp: datetime)
```

次に示す関数を作成します。

```
.create-or-alter function OPCUADatasetExpand() {
    opcua_intermediate
    | mv-apply payload on (
        extend key = tostring(bag_keys(payload)[0])
        | extend p = payload[key]
        | project ExpandedNodeId = key, Value = todynamic(p.Value), SourceTimestamp = todatetime(p.SourceTimestamp)
    )
}
```

新しい関数を適用して、3 番目と最後のテーブルを更新します。

```
.alter table opcua_telemetry policy update @'[{"Source": "opcua_intermediate", "Query": "OPCUADatasetExpand()", "IsEnabled": "True"}]'
```

OPC UA テレメトリ テーブルを確認するには、次のコマンドを使用します。

```
opcua_telemetry
| take 10
```

## <a name="create-line-graph-of-the-data"></a>データの折れ線グラフを作成する

では、データをすべて浮動小数点数にキャストして折れ線グラフを作成しましょう。 キャストが失敗した場合、データは無視されます。 このためには、ホストされている Azure Data Explorer ダッシュボードを使用します。 **[Web UI で開く]** をクリックし、新しいウィンドウで **[ダッシュボード]** をクリックして、 **[新しいダッシュボードの作成]** を選択し、 **[タイルの追加]** を選択します。 次に、 **[データ ソース]** を選択し、Azure Data Explorer インスタンスの名前を `https://<YourInstanceName>.<Your RegionName>.kusto.windows.net` の形式で入力します。 データベースを選択し、 **[適用]** をクリックします。 次のクエリを入力します。

```
opcua_telemetry
| project SourceTimestamp, yaxis = todouble(Value), DataSetWriterID,  ExpandedNodeID
```

**[ビジュアル]** タブをクリックし、 *[Y 列]* に **[yaxis]** 、 *[X 列]* に **[SourceTimeStamp]** 、 *[Series columns]\(系列の列\)* に **[DataSetWriterID]** を選択します。

これで、クラウド内のユース ケース データを監視するための便利なテレメトリ ダッシュボードが作成されました。