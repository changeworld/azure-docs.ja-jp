---
title: チュートリアル:IoT 空間分析を実装する | Microsoft Azure Maps
description: IoT ハブを Microsoft Azure Maps サービスの API と統合します。
author: farah-alyasari
ms.author: v-faalya
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f0367a195ca0aa5f26ff0819b00c50fabae1d271
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505821"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>チュートリアル:Azure Maps を使用した IoT 空間分析の実装

IoT シナリオでは、空間と時間に生じる関連イベントをキャプチャして追跡するのが一般的です。 たとえば、フリート管理、資産の追跡、モビリティ、スマート シティ アプリケーションのシナリオです。 このチュートリアルでは、Azure Maps API シリーズを使用したソリューション パターンについて説明します。 関連イベントは、Event Grid によって提供されるイベント サブスクリプション モデルを使用して、IoT Hub によってキャプチャされます。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * IoT Hub を作成します。
> * Data Upload API を使用して Azure Maps のデータ サービスにジオフェンス領域をアップロードする。
> * Azure Functions で関数を作成し、Azure Maps 空間分析に基づいてビジネス ロジックを実装します。
> * Event Grid 経由で Azure 関数から IoT デバイス テレメトリ イベントをサブスクライブします。
> * IoT Hub のメッセージ ルーティングを使用して、テレメトリ イベントをフィルター処理します。
> * 関連イベント データを格納するストレージ アカウントを作成します。
> * 車載 IoT デバイスをシミュレートします。
    

## <a name="use-case"></a>使用事例

このソリューションは、レンタカー会社が、貸し出した車両に関するイベントの監視と記録を計画するシナリオを例示します。 レンタカー会社は、通常、特定の地域に車両を貸し出します。 貸し出し中は、車両の所在を追跡する必要があります。 選択された地域を車両が離れる事例をログに記録する必要があります。 データをログに記録すれば、ポリシーや料金などのビジネス要素が適切に処理されることにつながるでしょう。

このユース ケースでは、レンタカーには、利用統計情報を Azure IoT Hub に定期的に送信する IoT デバイスが搭載されています。 テレメトリには現在の場所が含まれ、車両のエンジンが作動中であるかどうかが示されます。 デバイスの場所スキーマは、[地理空間データ用の IoT プラグ アンド プレイ スキーマ](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md)に準拠しています。 レンタカーのデバイス テレメトリ スキーマは次のようになります。

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": { 
            "location": { 
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            } 
        } 
    }
}
```

目的を達成するために、車載デバイスのテレメトリを使用しましょう。 この場合、ジオフェンシング ルールを実行します。 さらに、車両が移動したことを示すイベントを受信するたびに応答を返す必要があります。 そのために、IoT Hub から Event Grid を介してデバイス テレメトリ イベントをサブスクライブします。 

Event Grid をサブスクライブするにはいくつかの方法があります。このチュートリアルでは、Azure Functions を使用します。 Azure Functions は、Event Grid で発行されたイベントに反応します。 また、Azure Maps の空間分析に基づくレンタカー ビジネス ロジックも実装します。 

車両がジオフェンスを離れたかどうかは、Azure 関数内のコードで確認します。 車両がジオフェンスを離れた場合、Azure 関数は、現在の場所に関連付けられている住所などの追加情報を収集します。 また、この関数では、イベントの状況を説明するのに役立つ、意味のあるイベント データをデータ BLOB ストレージに格納するロジックも実装します。 

レンタカー会社やレンタル利用者にとって、イベントの状況は有益な情報となります。 次の図は、システムの大まかな概要を示しています。

 
  <center>

  ![システムの概要](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

次の図では、ジオフェンス領域が青で強調表示されています。 レンタル車両のルートは、緑色の線で示されます。

  ![ジオフェンス ルート](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>前提条件 

### <a name="create-a-resource-group"></a>リソース グループを作成する

このチュートリアルの手順を完了するには、最初に Azure portal でリソース グループを作成する必要があります。 リソース グループを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. **[リソース グループ]** を選択します。
    
   ![リソース グループ](./media/tutorial-iot-hub-maps/resource-group.png)

3. **[リソース グループ]** から **[追加]** を選択します。
    
   ![リソース グループの追加](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. 次のプロパティ値を入力します。
    * **サブスクリプション:** Azure サブスクリプションを選択します。
    * **[リソース グループ]:** リソース グループ名として「ContosoRental」と入力します。
    * **[リージョン]:** リソース グループのリージョンを選択します。  

    ![リソース グループの詳細](./media/tutorial-iot-hub-maps/resource-details.png)

    **[確認および作成]** を選択し、次のページで **[作成]** を選択します。

### <a name="create-an-azure-maps-account"></a>Azure Maps アカウントを作成する 

Azure Maps 空間分析に基づいてビジネス ロジックを実装するには、作成したリソース グループに Azure Maps アカウントを作成する必要があります。 [アカウントの作成](quick-demo-map-app.md#create-an-account-with-azure-maps)手順に従い、S1 価格レベルで Azure Maps アカウントのサブスクリプションを作成します。 [プライマリ キーの取得](quick-demo-map-app.md#get-the-primary-key-for-your-account)に関するセクションの手順に従って、お使いのアカウントのプライマリ キーを取得します。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](how-to-manage-authentication.md)」を参照してください。



### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

イベント データをログに記録するには、すべての Azure Storage サービス (BLOB、ファイル、キュー、テーブル、ディスク) へのアクセスを提供する汎用 **v2storage** を作成します。  データを BLOB として格納するには、このストレージ アカウントを "ContosoRental" リソース グループに配置する必要があります。 ストレージ アカウントを作成するには、「[ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)」の手順に従います。 次に、BLOB を格納するためのコンテナーを作成する必要があります。 これを行うには、以下の手順に従います。

1. 自分の "ストレージ アカウント - BLOB、ファイル、テーブル、キュー" で、[コンテナー] に移動します。

    ![BLOB](./media/tutorial-iot-hub-maps/blobs.png)

2. 左上にある [コンテナー] ボタンをクリックします。コンテナーに「contoso-rental-logs」という名前を付け、[OK] をクリックします。

    ![blob-container](./media/tutorial-iot-hub-maps/blob-container.png)

3. 自分のストレージ アカウントの **[アクセス キー]** ブレードに移動して、"ストレージ アカウント名" と "アクセス キー" をコピーします。 これらは後続の手順で必要になります。

    ![access-keys](./media/tutorial-iot-hub-maps/access-keys.png)


これで、ストレージ アカウントと、イベント データをログに記録するためのコンテナーが準備できました。 次は IoT ハブを作成します。

### <a name="create-an-iot-hub"></a>IoT Hub の作成

IoT Hub は、クラウド内のマネージド サービスです。 IoT Hub は、IoT アプリケーションとそれが管理するデバイスの間の双方向通信のための中央メッセージ ハブとして機能します。 デバイス テレメトリ メッセージを Event Grid にルーティングするために、"ContosoRental" リソース グループ内に IoT ハブを作成します。 メッセージ ルート統合を設定し、車両のエンジン状態に基づいてメッセージをフィルター処理します。 また、車両が移動しているときは常にデバイス テレメトリ メッセージを Event Grid に送信します。

> [!Note] 
> Event Grid でデバイス テレメトリ イベントを発行する IoT Hub の機能は、パブリック プレビュー段階です。 パブリック プレビュー機能は、**米国東部、米国西部、西ヨーロッパ、Azure Government、Azure China 21Vianet**、および **Azure Germany** を除くすべてのリージョンで利用できます。 

[「IoT ハブの作成」セクション](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)の手順に従って、IoT ハブを作成します。


### <a name="register-a-device"></a>デバイスの登録 

IoT ハブに接続するには、デバイスを登録する必要があります。 IoT ハブにデバイスを登録するには、以下の手順に従います。

1. 自分の IoT ハブで、[IoT デバイス] ブレードをクリックし、[新規] をクリックします。

    ![add-device](./media/tutorial-iot-hub-maps/add-device.png)

2. [デバイスの作成] ページで、自分の IoT デバイスに名前を付けて、[保存] をクリックします。
    
    ![register-device](./media/tutorial-iot-hub-maps/register-device.png)

3. デバイスの **[プライマリ接続文字列]** を後の手順で使用するために保存します。後の手順では、プレースホルダーをこの接続文字列に変更する必要があります。

    ![add-device](./media/tutorial-iot-hub-maps/connection-string.png)

## <a name="upload-geofence"></a>ジオフェンスをアップロードする

[Postman アプリケーション](https://www.getpostman.com)を使用して、Azure Maps Data Upload API を使用して Azure Maps サービスに[ジオフェンスをアップロード](https://docs.microsoft.com/azure/azure-maps/geofence-geojson)します。 車両がこのジオフェンスの外側にあるときのイベントは、すべてログに記録されます。

Azure Maps の Data Upload API を使用してジオフェンスをアップロードするには、Postman アプリを開いて以下の手順に従います。  

1. Postman アプリで、[new]\(新規\)、[Create new]\(新規作成\) の順にクリックし、[Request]\(\要求\) を選択します。 ジオフェンス データのアップロードに対する要求名を入力して、これを保存するコレクションまたはフォルダーを選択し、[Save]\(保存\) をクリックします。

    ![Postman を使用してジオフェンスをアップロードする](./media/tutorial-iot-hub-maps/postman-new.png)

2. [builder]\(ビルダー\) タブで POST HTTP メソッドを選択し、POST 要求を行うための次の URL を入力します。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    URL パス内の `dataFormat` パラメーターに対する "geojson" 値は、アップロードされるデータの形式を表します。

3. **[Params]\(パラメーター\)** をクリックして、POST 要求の URL に使用する次のキーと値のペアを入力します。 subscription-key 値を自分の Azure Maps キーに置き換えます。
   
    ![Postman のキーと値のペアから成るパラメーター](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. **[Body]\(本文\)** をクリックしてから、 **[raw]\(未加工\)** 入力形式を選択し、ドロップダウン リストから **[JSON (application/text)]\(JSON (アプリケーション/テキスト)\)** を入力形式として選択します。 [こちら](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)の JSON データ ファイルを開き、アップロードすべきデータとしてこの JSON を本文セクションにコピーし、 **[Send]\(送信\)** をクリックします。
    
    ![データの投稿](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. 応答ヘッダーを確認します。 要求が成功すると、**Location** ヘッダーには、アップロード要求の現在の状態を確認するための状態 URI が格納されます。 状態 URI は次の形式になります。 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. 状態 URI をコピーし、それに `subscription-key` を追加します。 Azure Maps アカウントのサブスクリプション キーの値を `subscription-key` パラメーターに割り当てます。 ステータス URI の形式は次のようになります。`{Subscription-key}` は実際のサブスクリプション キーに置き換えられます。

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. `udId` を取得するには、Postman アプリで新しいタブを開き、[builder]\(ビルダー\) タブで GET HTTP メソッドを選択し、状態 URI で GET 要求を行います。 データのアップロードが成功した場合は、応答本文で udId が返されます。 後で使用するために udId をコピーします。

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


次に、"ContosoRental" リソース グループ内に Azure 関数を作成し、デバイス テレメトリ メッセージをフィルター処理するためのメッセージ ルートを IoT Hub で設定します。


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Azure 関数を作成して Event Grid サブスクリプションを追加する

Azure Functions は、コンピューティング インフラストラクチャを明示的にプロビジョニングまたは管理することなく、オンデマンドでコードを実行できるサーバーレス コンピューティング サービスです。 Azure Functions の詳細については、[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) のドキュメントを参照してください。 

関数で実装するロジックでは、車載デバイスのテレメトリから取得した位置情報データを使用して、ジオフェンスの状態を評価します。 特定の車両がジオフェンスの外に出た場合、この関数は [Get Search Address Reverse API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) を介して、その場所の住所などの詳細情報を収集します。 この API は、特定の位置座標を人間が理解できる住所に変換します。 

その後、すべての関連イベント情報が BLOB ストアに保持されます。 下の手順 5 は、そのようなロジックを実装する実行可能コードを示しています。 以下の手順に従って、BLOB ストレージ アカウントの BLOB コンテナーにデータ ログを送信する Azure 関数を作成し、それに Event Grid サブスクリプションを追加します。

1. Azure portal のダッシュボードで、[リソースの作成] を選択します。 使用可能なリソースの種類の一覧から **[Compute]** を選択し、 **[関数アプリ]** を選択します。

    ![create-resource](./media/tutorial-iot-hub-maps/create-resource.png)

2. **[関数アプリ]** 作成ページで、関数アプリに名前を付けます。 **[リソース グループ]** で **[既存のものを使用]** を選択し、ドロップダウン リストから "ContosoRental" を選択します。 [ランタイム スタック] には ".NET Core" を選択します。 **[ホスティング]** の **[ストレージ アカウント]** で、前の手順のストレージ アカウント名を選択します。 前の手順では、ストレージ アカウントに **v2storage** という名前を付けました。  その後、 **[確認および作成]** を選択します。
    
    ![create-app](./media/tutorial-iot-hub-maps/rental-app.png)

2. 関数アプリの詳細を確認し、[作成] を選択します。

3. アプリが作成されたら、それに関数を追加する必要があります。 Function App に移動します。 **[新しい関数]** をクリックして関数を追加し、開発環境として **[ポータル内]** を選択します。 その後、 **[続行]** を選択します。

    ![create-function](./media/tutorial-iot-hub-maps/function.png)

4. **[その他のテンプレート]** を選択し、 **[テンプレートの完了と表示]** をクリックします。 

5. **Azure Event Grid トリガー**が含まれているテンプレートを選択します。 プロンプトが表示されたら拡張機能をインストールし、関数に名前を付け、 **[作成]** を選択します。

    ![function-template](./media/tutorial-iot-hub-maps/eventgrid-funct.png)
    
    **Azure Event Hub トリガー**と **Azure Event Grid トリガー**のアイコンは似ています。 **Azure Event Grid トリガー**を選択していることを確認します。

6. [C# コード](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)を自分の関数にコピーします。
 
7. C# スクリプト内で、次のパラメーターを置き換えます。 **[保存]** をクリックします。 まだ **[実行]** をクリックしないでください。
    * **SUBSCRIPTION_KEY** を、自分の Azure Maps アカウントのプライマリ サブスクリプション キーに置き換えます。
    * **UDID** を、自分がアップロードしたジオフェンスの udId に置き換えます。 
    * スクリプト内の **CreateBlobAsync** 関数では、データ ストレージ アカウントでイベントごとに BLOB を作成します。 **ACCESS_KEY**、**ACCOUNT_NAME**、**STORAGE_CONTAINER_NAME** を、自分のストレージ アカウントのアクセス キー、アカウント名、データ ストレージ コンテナーに置き換えます。

10. **[Event Grid サブスクリプションの追加]** をクリックします。
    
    ![add-event-grid](./media/tutorial-iot-hub-maps/add-egs.png)

11. サブスクリプションの詳細を入力します。 **[イベント サブスクリプションの詳細]** で自分のサブスクリプションに名前を付けます。 [イベント スキーマ] では、[イベント グリッド スキーマ] を選択します。 **[トピックの詳細]** で [トピックの種類] として [Azure IoT Hub Accounts]\(Azure IoT Hub アカウント\) を選択します。 リソース グループの作成に使用したのと同じサブスクリプションを選択し、[リソース グループ] として "ContosoRental" を選択します。 "リソース" として作成した IoT ハブを選択します。 [イベントの種類] として **[Device Telemetry]\(デバイス テレメトリ\)** を選択します。 これらのオプションを選択すると、[トピックの種類] が [IoT Hub] に自動的に変更されます。

    ![event-grid-subscription](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>IoT Hub のメッセージ ルーティングを使用してイベントをフィルター処理する

Event Grid サブスクリプションを Azure 関数に追加すると、IoT Hub の **[メッセージ ルーティング]** ブレードに Event Grid への既定のメッセージ ルートが表示されます。 メッセージ ルーティングを使用すると、各種データ型をさまざまなエンドポイントにルーティングできます。 たとえば、デバイス テレメトリのメッセージ、デバイスのライフサイクル イベント、デバイス ツインの変更イベントをルーティングできます。 IoT ハブのメッセージ ルーティングの詳細については、[IoT Hub メッセージ ルーティングの使用](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)に関するページを参照してください。

![hub-EG-route](./media/tutorial-iot-hub-maps/hub-route.png)

この例のシナリオでは、レンタル車両が移動している場合のすべてのメッセージをフィルターで除外します。 このようなデバイス テレメトリ イベントを Event Grid に発行するために、ルーティング クエリを使用して、`Engine` プロパティが **"ON"** であるイベントをフィルター処理します。 デバイスからクラウドへの IoT メッセージに対してクエリを実行するには、さまざまな方法があります。メッセージ ルーティング構文の詳細については、[IoT Hub メッセージ ルーティング](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)に関するページを参照してください。 ルーティング クエリを作成するには、**RouteToEventGrid** ルートをクリックし、**ルーティング クエリ**を **"Engine='ON'"** に置き換えて、 **[保存]** をクリックします。 これで、IoT ハブにより、エンジンがオンになっているデバイス テレメトリのみが発行されます。

![hub-EG-filter](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>テレメトリ データを IoT Hub に送信する

Azure 関数が起動したら、テレメトリ データを IoT Hub に送信します。それにより、テレメトリ データは Event Grid にルーティングされます。 C# アプリケーションを使用して、レンタカーの車載デバイスの位置情報データをシミュレートしましょう。 アプリケーションを実行するには、自分の開発用マシン上に .NET Core SDK 2.1.0 以上が必要です。 以下の手順に従って、シミュレートされたテレメトリ データを IoT Hub に送信します。

1. [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# プロジェクトをダウンロードします。 

2. 任意のテキスト エディターで simulatedCar.cs ファイルを開き、`connectionString` の値を、デバイスを登録したときに保存したものに置き換えて、ファイルへの変更を保存します。
 
3. マシンに .NET Core がインストールされていることを確認します。 ローカル ターミナル ウィンドウで、C# プロジェクトのルート フォルダーに移動し、次のコマンドを実行して、シミュレートされたデバイス アプリケーションに必要なパッケージをインストールします。
    
    ```cmd/sh
    dotnet restore
    ```

4. 同じターミナルで次のコマンドを実行して、レンタカー シミュレーション アプリケーションをビルドして実行します。

    ```cmd/sh
    dotnet run
    ```

  ローカル ターミナルは次のようになります。

  ![ターミナルの出力](./media/tutorial-iot-hub-maps/terminal.png)

ここで BLOB ストレージ コンテナーを開くと、車両がジオフェンスの外側にあった場所の 4 つの BLOB を確認できます。

![BLOB の入力](./media/tutorial-iot-hub-maps/blob.png)

下のマップは、車両がジオフェンスの外側にあり、定期的にログに記録された 4 つの地点を示しています。

![違反マップ](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルで使用した Azure Maps API シリーズの詳細については、以下を参照してください。

* [Get Search Address Reverse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Get Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Azure Maps REST API シリーズの完全な一覧については、次を参照してください。

* [Azure Maps REST API シリーズ](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

IoT プラグ アンド プレイの詳細については、次を参照してください。

* [IoT プラグ アンド プレイ](https://docs.microsoft.com/azure/iot-pnp)

Azure で IoT の認定を受けたデバイスの一覧を取得するには、次のページにアクセスします。

* [Azure 認定デバイス](https://catalog.azureiotsolutions.com/)

デバイスからクラウドへのテレメトリを送信する方法、およびその逆の方法の詳細については、次を参照してください。

* [デバイスから利用統計情報を送信する](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
