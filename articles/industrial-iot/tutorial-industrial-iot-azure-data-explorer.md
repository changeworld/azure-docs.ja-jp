---
title: Azure Industrial IoT データを ADX にプルする
description: このチュートリアルでは、IIoT データを ADX にプルする方法について説明します。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4c344dc09ad6c8aa4b2aa431952fc271d946b60d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787312"
---
# <a name="tutorial-pull-azure-industrial-iot-data-into-adx"></a>チュートリアル: Azure Industrial IoT データを ADX にプルする

Azure Industrial IoT (IIoT) プラットフォームは、エッジ モジュールおよびクラウド マイクロサービスを複数の Azure PaaS サービスと組み合わせて、産業用資産検出の機能を提供し、OPC UA を使用してこれらの資産からデータを収集します。 [Azure Data Explorer (ADX)](https://docs.microsoft.com/azure/data-explorer) は、Data Analytics の機能を備えた、IIoT データの本来の宛先です。これを使用すると、IoT Hub に接続されている OPC UA サーバーから OPC Publisher を介して、取り込まれたデータに対して柔軟なクエリを実行できます。 ADX クラスターは IoT Hub から直接データを取り込むことができますが、マイクロサービスの完全なデプロイを使用する場合に提供されるイベント ハブにデータを配置する前に、IIoT プラットフォームはそれをさらに処理して、より役立つものにします (IIoT プラットフォーム アーキテクチャを参照してください)。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ADX にテーブルを作成する
> * イベント ハブを ADX クラスターに接続する
> * ADX でデータを分析する

## <a name="how-to-make-the-data-available-in-the-adx-cluster-to-query-it-effectively"></a>データを ADX クラスターで使用可能にして効率的にクエリを実行する方法 

イベント ハブからのメッセージ形式 (Microsoft.Azure.IIoT.OpcUa.Subscriber.Models.MonitoredItemMessageModel クラスで定義されている) を調べると、ADX テーブル スキーマに必要な構造に関するヒントを確認できます。

![構造体](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-1.png)

データを ADX クラスターで使用できるようにして、データのクエリを効率的に実行するために必要な手順を以下に示します。  
1. ADX クラスターを作成します。 IIoT プラットフォームで既にプロビジョニングされている ADX クラスターがない場合、または別のクラスターを使用する場合は、[こちら](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-cluster)の手順に従ってください。 
2. [こちら](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming#enable-streaming-ingestion-on-your-cluster)の説明に従って、ADX クラスターでストリーミング インジェストを有効にします。 
3. [こちら](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-database)の手順に従って、ADX データベースを作成します。

次の手順では、[ADX Web インターフェイス](https://docs.microsoft.com/azure/data-explorer/web-query-data)を使用して、必要なクエリを実行します。 リンクの説明に従って、必ずクラスターを Web インターフェイスに追加してください。  
 
4. 取り込まれたデータを格納するテーブルを ADX に作成します。  MonitoredItemMessageModel クラスを使用して ADX テーブルのスキーマを定義できますが、最初に、[動的](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic)な型の列を 1 つ含むステージング テーブルにデータを取り込むことをお勧めします。 これにより、データを処理して複数のユース ケースのニーズに対応する他のテーブルに格納する (他のデータ ソースと結合する可能性があります) 際の柔軟性が向上します。 次の ADX クエリでは、1 つの列 'payload' を含むステージング テーブル 'iiot_stage' が作成されます。

```
.create table ['iiot_stage']  (['payload']:dynamic)
```

また、イベント ハブから Json メッセージ全体をステージング テーブルに配置するようにクラスターに指示するために、json インジェスト マッピングを追加する必要もあります。

```
.create table ['iiot_stage'] ingestion json mapping 'iiot_stage_mapping' '[{"column":"payload","path":"$","datatype":"dynamic"}]'
```

5. これで、テーブルでイベント ハブからのデータを受信する準備ができました。 
6. [こちら](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub)の手順を使用して、イベント ハブを ADX クラスターに接続し、ステージング テーブルへのデータの取り込みを開始します。 IIoT プラットフォームによってプロビジョニングされたイベントハブが既にあるため、接続の作成のみ行う必要があります。  
7. 接続が検証されると、テーブルへのデータの送信が開始されます。少し時間が経過した後に、テーブル内のデータの調査を開始できます。 ADX Web インターフェイスで次のクエリを使用して、10 行のデータ サンプルを確認します。 ここで、ペイロード内のデータが、前述の MonitoredItemMessageModel クラスにどのくらい似ているかを確認できます。

![クエリ](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-2.png)

8. 'payload' 列の動的データを直接解析することで、このデータに対していくつかの分析を実行してみましょう。 この例では、(変数 min_t によって定義された) 特定の時点以降に取り込まれたすべてのレコードに対して 10 分間の時間枠で "DisplayName": "PositiveTrendData" によって識別されるテレメトリの平均を計算します。let min_t = datetime(2020-10-23); iiot_stage | where todatetime(payload.Timestamp) > min_t | where tostring(payload.DisplayName)== 'PositiveTrendData' | summarize event_avg = avg(todouble(payload.Value)) by bin(todatetime(payload.Timestamp), 10 m)
 
'payload' 列には動的なデータ型が含まれているため、クエリ時にデータ変換を実行して、計算が正しいデータ型で実行されるようにする必要があります。

![ペイロード タイムスタンプ](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-3.png)

前述したように、OPC UA データを 1 つの "動的" 列を含むステージング テーブルに取り込むと、柔軟性が高まります。 ただし、クエリ時にデータ型変換を実行する必要があると、特にデータ量が多い場合や同時実行クエリが多数ある場合に、クエリの実行で遅延が発生することがあります。 この段階で、クエリ時のデータ型変換を回避するために、既に判別されたデータ型を含む別のテーブルを作成できます。
 
9. ステージング テーブル内の動的な 'payload' の内容からの限定的な選択で構成される、解析済みデータ用の新しいテーブルを作成します。 テレメトリに予期されるデータ型ごとに値列を作成していることに注意してください。

```
.create table ['iiot_parsed']  
    (['Tag_Timestamp']: datetime ,  
    ['Tag_PublisherId']:string ,  
    ['Tag']:string ,
    ['Tag_Datatype']:string ,  
    ['Tag_NodeId']:string,  
    ['Tag_value_long']:long ,  
    ['Tag_value_double']:double,  
    ['Tag_value_boolean']:bool)
```

10. (データベース レベルで) 関数を作成して、ステージング テーブルから必要なデータを射影します。 ここでは、'payload' 列から 'Timestamp'、'PublisherId'、'DisplayName'、'Datatype'、'NodeId' の各項目を選択し、これらを 'Tag_Timestamp'、'Tag_PublisherId'、''Tag'、'Tag_Datatype'、'Tag_NodeId' として射影します。 'Value' 項目は、'DataType' に基づいて 3 つの異なる部分として射影されます。

```
.create-or-alter function fn_InflightParseIIoTEvent()
{
iiot_stage
| extend Tag_Timestamp =  todatetime(payload.Timestamp)
| extend Tag_PublisherId = tostring(payload.PublisherId)
| extend Tag = tostring(payload.DisplayName)
| extend Tag_Datatype = tostring(payload.DataType)
| extend Tag_NodeId = tostring(payload.NodeId)
| extend Tag_value_long = case(Tag_Datatype == "Int64", tolong(payload.Value), long(null))
| extend Tag_value_double = case(Tag_Datatype == "Double", todouble(payload.Value), double(null))
| extend Tag_value_boolean = case(Tag_Datatype == "Boolean", tobool(payload.Value), bool(null))
| project Tag_Timestamp, Tag_PublisherId, Tag, Tag_Datatype, Tag_NodeId, Tag_value_long, Tag_value_double, Tag_value_boolean
}
```

ADX でのデータ型のマッピングの詳細については、[こちら](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic)を参照してください。ADX の関数については、[こちら](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/stored-functions)から開始できます。
 
11. 更新ポリシーを使用して、前の手順の関数を解析済みのテーブルに適用します。 更新[ポリシー](https://docs.microsoft.com/azure/data-explorer/kusto/management/updatepolicy)は、ソース テーブルに新しいデータが挿入されるたびに、ソース テーブルに挿入されたデータに対して実行される変換クエリに基づいて、ターゲット テーブルにデータを自動的に追加するように ADX に指示します。 次のクエリを使用して、前の手順で作成した関数によって定義された更新ポリシーの宛先として解析済みのテーブルを、ソースとしてステージ テーブルを割り当てることができます。

```
.alter table iiot_parsed policy update
@'[{"IsEnabled": true, "Source": "iiot_stage", "Query": "fn_InflightParseIIoTEvent()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
```

上記のクエリが実行されるとすぐに、データの送信が開始され、宛先テーブル 'iiot_parsed' にデータが設定されます。 次のような 'iiot_parsed' のデータを確認できます。

![解析済みのテーブル](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-4.png)

12. 前の手順で行った分析 (特定の時点 (変数 min_t によって定義) 以降に取り込まれたすべてのレコードに対して 10 分間の時間枠で "DisplayName": "PositiveTrendData" によって識別されるテレメトリの平均を計算する) を繰り返し実行する方法について確認します。 ここでは、'PositveTrendData' タグの値が double データ型の列に格納されているので、クエリのパフォーマンスが向上することが期待されます。

![分析の繰り返し](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-5.png)

13. 最後に、両方のケースでクエリのパフォーマンスを比較してみましょう。 ADX UI の [統計] (これはクエリ結果の上に配置できます) を使用して、クエリの実行にかかった時間を確認できます。  

![クエリ パフォーマンス 1](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-6.png)

![クエリ パフォーマンス 2](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-7.png)

解析済みのテーブルを使用するクエリは、ステージング テーブルの場合のほぼ 2 倍速いことがわかります。 この例では、データセットが小さく、同時実行クエリがないため、クエリ実行時間への影響は大きくありませんが、実際のワークロードでは、パフォーマンスに大きな影響があります。 このため、異なるデータ型を別々の列に分けることを検討することが重要です。

> [!NOTE] 
> 更新ポリシーは、ポリシーが設定された後にステージング テーブルに取り込まれたデータに対してのみ機能し、それ以前から存在するデータには適用されません。 たとえば、更新ポリシーを変更する必要がある場合は、この点を考慮する必要があります。 詳細は、ADX のドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ
構成の既定値を変更する方法を学んだので、次のことができます。 

> [!div class="nextstepaction"]
> [Industrial IoT コンポーネントを構成する](tutorial-configure-industrial-iot-components.md)

> [!div class="nextstepaction"]
> [Time Series Insights を使用してデータを視覚化および分析する](tutorial-visualize-data-time-series-insights.md)