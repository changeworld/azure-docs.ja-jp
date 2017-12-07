---
title: "Azure Stream Analytics on IoT Edge (プレビュー)"
description: "Azure Stream Analytics でエッジ ジョブを作成し、Azure IoT Edge で実行中のデバイスに展開します。"
keywords: "データ ストリーム, iot, エッジ"
services: stream-analytics
documentationcenter: 
author: jseb225
manager: jhubbard
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 11/16/2017
ms.author: jeanb
ms.openlocfilehash: 916aefb7916ab374c882efb95417babfc5b06a50
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2017
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>Azure Stream Analytics on IoT Edge (プレビュー)

> [!IMPORTANT]
> この機能はプレビュー状態です。 運用環境での使用はお勧めできません。
 
Azure Stream Analytics (ASA) on IoT Edge を使用することで、開発者は、ほぼリアルタイムの分析インテリジェンスをより IoT デバイスに近い場所に展開し、デバイス生成データの価値を最大限に活用できるようになります。 低待機時間、回復性、帯域幅の効率的な使用、およびコンプライアンスを意図して設計されているため、企業は制御ロジックを生産工程に近い場所に展開し、クラウドで実行されるビッグ データ分析を補完できるようになりました。  
Azure Stream Analytics on IoT Edge は [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) フレームワークの内部で実行されます。 ASA でジョブが作成されたら、IoT Hub を使用して ASA ジョブを配置し、管理します。
この機能はプレビュー段階にあります。質問やフィードバックは、[こちらのアンケート](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u)から製品チームにお寄せください。 

## <a name="scenarios"></a>シナリオ
![概要図](media/stream-analytics-edge/ASAedge_highlevel.png)

* **コマンドと制御の待機時間が短い**: たとえば、製造安全システムは、非常に短い待機時間で運用データに応答する必要があります。 ASA on IoT Edge を使用することで、センサー データをほぼリアルタイムで分析し、異常を検出した際はコマンドを発行してマシンを停止したり、アラートをトリガーしたりできます。
*   **クラウドへの接続が制限されている**: リモート採掘装置、接続された船舶、海洋掘削など、ミッション クリティカルなシステムでは、クラウド接続が断続的なときでも、データを分析し、そのデータに対応する必要があります。 ASA では、ネットワーク接続とは関係なくストリーミング ロジックが実行され、さらなる処理や保存のためにクラウドに送信するデータを選択できます。
* **帯域幅が制限されている**: ジェット エンジンやコネクテッド カーによって生成されるデータ量は膨大になる可能性があるため、クラウドに送信する前に、データをフィルター処理または前処理する必要があります。 ASA を使用すると、クラウドに送信する必要があるデータをフィルター処理したり集計したりできます。
* **コンプライアンス**: 法令順守では、一部のデータについてはクラウドに送信する前に、ローカルでの匿名化または集計が必要になる可能性があります。

## <a name="edge-jobs-in-azure-stream-analytics"></a>Azure Stream Analytics のエッジ ジョブ
### <a name="what-is-an-edge-job"></a>"エッジ" ジョブとは

ASA Edge ジョブは、[Azure IoT Edge ランタイム](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)内でモジュールとして実行されます。 これは 2 つの部分で構成されます。
1.  ジョブ定義の役割を担うクラウド部分: ユーザーが、クラウド内の入力、出力、クエリ、およびその他の設定 (順不同のイベントなど) を定義します。
2.  ローカルで実行される ASA on IoT Edge モジュール。 ASA 複合イベント処理エンジンが含まれ、クラウドからジョブ定義を受け取ります。 

ASA では、IoT ハブを使用してエッジ ジョブをデバイスに展開します。 IoT Edge の展開の詳細については、[こちら](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)をご覧ください。

![エッジ ジョブ](media/stream-analytics-edge/ASAedge_job.png)


### <a name="installation-instructions"></a>インストール手順
手順の概要を次の表に示します。 詳細については、以降のセクションを参照してください。
|      |手順   | 場所     | メモ   |
| ---   | ---   | ---       |  ---      |
| 1   | **ASA エッジ ジョブを作成する**   | Azure Portal      |  新しいジョブを作成し、**ホスティング環境**として **Edge** を選択します。 <br> このジョブはクラウドから作成および管理され、お使いの IoT Edge デバイスで実行されます。     |
| 2   | **ストレージ コンテナーを作成する**   | Azure ポータル       | ストレージ コンテナーを使用してジョブ定義を保存します。コンテナーには、IoT デバイスからアクセスできます。 <br>  既存のストレージ コンテナーを再利用できます。     |
| 3   | **デバイスで IoT Edge 環境を設定する**   | デバイス      | [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) または [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) 用の手順。          |
| 4   | **ASA を IoT Edge デバイスに展開する**   | Azure ポータル      |  ASA ジョブ定義は、先ほど作成したストレージ コンテナーにエクスポートされます。       |
最初の ASA ジョブを IoT Edge に展開するには、[順を追って解説したこちらのチュートリアル](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)に従ってください。

#### <a name="create-an-asa-edge-job"></a>ASA Edge ジョブを作成する
1. Azure Portal で、新しい "Stream Analytics ジョブ" を作成します。 新しい ASA ジョブを作成するには、[こちらの直接リンク](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob)を参照してください。

> [!Note]
> ASA でサポートされているすべてのリージョンでエッジ ジョブを作成できます。ただし、**"米国西部 2" リージョンを除きます**。
> この制限は、間もなく削除されます。

2. 作成画面で、**ホスティング環境**として **Edge** を選択します (次の図を参照) ![ジョブの作成](media/stream-analytics-edge/ASAEdge_create.png)
3. ジョブ定義
    1. **入力ストリームの定義**。 ジョブに対して 1 つ以上の入力ストリームを定義します。
    2. 参照データの定義 (省略可能)。
    3. **出力ストリームの定義**。 ジョブに対して 1 つ以上の出力ストリームを定義します。 
    4. **クエリの定義**。 インライン エディターを使用して、クラウドで ASA クエリを定義します。 ASA エッジに対して有効になっている構文が、コンパイラによって自動的にチェックされます。 サンプル データをアップロードして、クエリをテストすることもできます。 
4. オプション設定を設定します
    1. **イベントの順序付け**。 ポータルで順不同ポリシーを構成できます。 ドキュメントは[こちら](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396)で入手できます。
    2. **ロケール**。 内部化形式を設定します。


#### <a name="create-a-storage-container"></a>ストレージ コンテナーを作成する
ASA のコンパイルされたクエリとジョブ構成をエクスポートするには、ストレージ コンテナーが必要です。 これは、特定のクエリで ASA Docker イメージを構成するときに使用されます。 
1. Azure Portal からストレージ アカウントを作成するには、[こちらの手順](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)に従ってください。 ASA でアカウントを使用するために、オプションを変更する必要はありません。
2. 新しく作成したストレージ アカウントで、Blob Storage コンテナーを作成します。
    1. [BLOB]、[+ コンテナー] の順にクリックします。 
    2. 名前を入力し、コンテナーは [プライベート] のままにします


> [!Note]
> 展開が作成されると、ASA によってジョブ定義がストレージ コンテナーにエクスポートされます。 このジョブ定義は、展開中に変更されることはありません。 このため、エッジで実行されているジョブを更新するには、ASA でジョブを編集し、IoT ハブで新しい展開を作成する必要があります。


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>デバイスで IoT Edge 環境を設定する
エッジ ジョブは、Azure IoT Edge が実行されているデバイスに展開できます。
これを行うには、次の手順に従う必要があります。
- IoT ハブを作成します。
- Docker と IoT Edge ランタイムをエッジ デバイスにインストールします。
- IoT ハブで "IoT Edge デバイス" としてデバイスを設定します。

この手順の説明については、[Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) または [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) の IoT Edge ドキュメントを参照してください。  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>ASA を IoT Edge デバイスに展開する
##### <a name="add-asa-to-your-deployment"></a>ASA を展開に追加する
- Azure Portal で IoT ハブを開き、IoT Edge Explorer に移動して、デバイス ブレードを開きます。
- **[Set modules]\(モジュールを設定\)**、**[Import Azure Service IoT Edge Module]\(Azure Service IoT Edge モジュールのインポート\)** の順に選択します。
- サブスクリプションと、作成した ASA Edge ジョブを選択します。 次に、ストレージ アカウントを選択します。 [保存] をクリックします。
![ASA モジュールを展開に追加する](media/stream-analytics-edge/set_module.png)


> [!Note]
> この手順の間に、ASA は、選択したストレージ コンテナーへのアクセスを要求し、"EdgeJobs" という名前のフォルダーを作成します。 "EdgeJobs" フォルダーには、展開ごとに新しいサブフォルダーが作成されます。
> ジョブをエッジ デバイスに展開する目的で、ASA は、ジョブ定義ファイルに対して共有アクセス署名 (SAS) を作成します。 SAS キーは、デバイス ツインを使用して、IoT Edge デバイスに安全に送信されます。 このキーの有効期限は、作成日から 3 年間です。


IoT Edge の展開の詳細については、[こちらのページ](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)をご覧ください。


##### <a name="configure-routes"></a>ルートを構成する
IoT Edge は、モジュール間およびモジュールと IoT ハブの間でメッセージを宣言的にルーティングするための方法を提供します。 完全な構文については、[こちら](https://docs.microsoft.com/azure/iot-edge/module-composition)をご覧ください。
ASA ジョブに作成された入力および出力の名前を、ルーティングのエンドポイントとして使用できます。  

###### <a name="example"></a>例
```
{
"routes": {                                              
    "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
    "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream", 
    "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")" 
}
}   

```
この例は、次の図で説明するシナリオのルートを示しています。 これには "**ASA**" というエッジ ジョブが含まれており、入力の名前は "**temperature**" で、出力の名前は "**alert**" です。
![ルーティングの例](media/stream-analytics-edge/RoutingExample.png)

この例は、次のルートを定義しています。
- **temperature** という名前の入力に対して、**tempSensor** からのすべてのメッセージが、**ASA** という名前のモジュールに送信されます。
- **ASA** モジュールのすべての出力が、このデバイス ($upstream) にリンクされた IoT ハブに送信されます。
- **ASA** モジュールのすべての出力が、**tempSensor** の **control** エンドポイントに送信されます。


## <a name="technical-information"></a>技術情報
### <a name="current-limitations-for-edge-jobs-compared-to-cloud-jobs"></a>クラウド ジョブと比較したエッジ ジョブの現在の制限事項
目的は、エッジ ジョブとクラウド ジョブの間の類似性を確認することです。 SQL クエリ言語の機能のほとんどが既にサポートされています。
ただし、次の機能はエッジ ジョブではまだサポートされていません。
* ユーザー定義関数 (UDF) とユーザー定義集計 (UDA)。
* Azure ML 関数。
* 1 つの手順での 14 を超える集計の使用。
* 入力/出力の AVRO 形式。 現時点では、CSV と JSON のみがサポートされます。
* JSON 入力の圧縮。
* 次の SQL 演算子:
    * AnomalyDetection
    * 地理空間演算子:
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * PARTITION BY
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>ランタイムとハードウェア要件
IoT Edge で ASA を実行するには、[Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) を実行できるデバイスが必要です。 

ASA と Azure IoT Edge は **Docker** コンテナーを使用して、複数のホスト OS (Windows、Linux) で実行されるポータブル ソリューションを提供します。

ASA on IoT Edge は、Windows および Linux イメージとして入手でき、x86-64 または ARM アーキテクチャの両方で実行されます。 


### <a name="input-and-output"></a>入力と出力
#### <a name="input-and-output-streams"></a>入力ストリームと出力ストリーム
ASA Edge ジョブは、IoT Edge デバイスで実行されている他のモジュールから入力と出力を取得できます。 特定のモジュールとの接続を確立するには、展開時にルーティング構成を設定します。 詳細については、[IoT Edge モジュールの構成に関するドキュメント](https://docs.microsoft.com/azure/iot-edge/module-composition)をご覧ください。

入力と出力の両方で、CSV 形式および JSON 形式がサポートされます。

ASA ジョブで作成した入力ストリームおよび出力ストリームごとに、対応するエンドポイントが、展開済みのモジュールに作成されます。 こうしたエンドポイントは、展開のルートで使用できます。



##### <a name="reference-data"></a>参照データ
参照データ (ルックアップ テーブルとも呼ばれます) は、有限のデータ セットで、本来は静的であるか、あまり変更されません。 これは、参照の実行やデータ ストリームとの関連付けに使用されます。 Azure Stream Analytics のジョブで参照データを使用するには、一般的にクエリで[参照データの結合](https://msdn.microsoft.com/library/azure/dn949258.aspx)を使用します。 詳細については、[参照データに関する ASA ドキュメント](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data)をご覧ください。

ASA on IoT Edge で参照データを使用するには、次の手順に従う必要があります。 
1. ジョブに対して新しい入力を作成します
2. **ソースの種類**として**参照データ**を選択します。
3. ファイル パスを設定します。 ファイル パスは、デバイス上の**絶対**ファイル パスにします![参照データの作成](media/stream-analytics-edge/ReferenceData.png)
4. Docker 構成で**共有ドライブ**を有効にして、展開を開始する前に、ドライブが有効になっていることを確認します。

詳細については、[こちらの Windows 用 Docker ドキュメント](https://docs.docker.com/docker-for-windows/#shared-drives)をご覧ください。

> [!Note]
> 現時点では、ローカルの参照データのみがサポートされます。




## <a name="license-and-third-party-notices"></a>ライセンスとサード パーティに関する通知
* [Azure Stream Analytics on IoT Edge プレビュー ライセンス](https://go.microsoft.com/fwlink/?linkid=862827)。 
* [Azure Stream Analytics on IoT Edge プレビューのサード パーティに関する通知](https://go.microsoft.com/fwlink/?linkid=862828)。

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)を参照してください。


## <a name="next-steps"></a>次のステップ

* [Azure IoT Edge の詳細](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA on IoT Edge チュートリアル](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [このアンケートを使用してフィードバックをチームに送信する](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
