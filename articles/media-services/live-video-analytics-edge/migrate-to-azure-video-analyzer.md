---
title: Live Video Analytics から Azure Video Analyzer への移行
description: Live Video Analytics から Azure Video Analyzer に移行する方法について説明します。
ms.topic: how-to
ms.date: 08/16/2021
ms.openlocfilehash: a2225e25685bb9177851379fd86b59c986ff28f0
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326440"
---
# <a name="how-to-migrate-from-live-video-analytics-to-azure-video-analyzer"></a>Live Video Analytics から Azure Video Analyzer に移行する方法

この記事は、Live Video Analytics Edge モジュールを IoT Edge デバイスに配置しており、現在メディア グラフを実行してリアルタイム ストリーミング プロトコル (RTSP) カメラからのフィードを処理している場合に使用してください。

RTSP カメラを使用しておらず、[Live Video Analytics のクイックスタートやチュートリアル](../live-video-analytics-edge/overview.md)のいずれかを使用している場合は、[Azure Video Analyzer の対応するクイックスタートやチュートリアル](../../azure-video-analyzer/video-analyzer-docs/overview.md)に切り替えてください。

以下の手順は、1 つの IoT Edge デバイスの Live Video Analytics を Azure Video Analyzer に移行する場合に適用されます。 これらの手順は、個別の IoT Edge デバイスごとに繰り返す必要があります。

## <a name="prerequisites"></a>前提条件

* 有効な Azure サブスクリプション

   > [!NOTE]
   > 新しいリソースを作成するリソース グループ (ユーザー割り当てマネージド ID、ストレージ アカウント、Video Analyzer アカウント) に対する [共同作成者](../../role-based-access-control/built-in-roles.md) ロールと [ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md) ロールの両方にアクセスできる Azure サブスクリプションが必要です。 適切なアクセス許可がない場合は、これらのアクセス許可を付与してもらうようアカウント管理者に依頼してください。 

* Live Video Analytics がインストールされている IoT Edge デバイスに対する管理者特権が必要です。

## <a name="create-a-backup-of-current-settings"></a>現在の設定のバックアップを作成する

移行作業を開始する前に、Edge の配置マニフェストと Live Video Analytics メディア グラフの構成を保存することを強くお勧めします。

> [!NOTE]
> 以下の手順では、カメラの RTSP 認証情報とストリーム URL の詳細を取得することはできません。  これらの情報は、カメラの管理アプリケーションから直接取得する必要があります。

### <a name="save-the-current-deployment-manifest"></a>現在の配置マニフェストを保存する

1. Azure portal にサインインします。

1. **[IoT Hub]** に移動し、 **[自動デバイス管理]** の **[IoT Edge]** を選択します。

1. **[IoT Edge]** で、Azure Video Analyzer に移行する IoT Edge デバイスを選択します。

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/iot-edge.png" alt-text="IoT Edge デバイスを示すスクリーンショット。":::

1. **[モジュールの設定]** を選択し、次に **[確認および作成]** を選択します。

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/set-modules.png" alt-text="配置マニフェストを示すスクリーンショット。":::

1. ウィンドウの **[配置]** セクションをコピーし、安全な場所に保存します。

1. ポータルの右上隅にある **X** を選択します。  

    > [!NOTE]
    > **[作成]** は選択 "*しないでください*"。  配置マニフェストを確認し、`modules` の下にある `lvaEdge` ノードを見つけます。  `lvaEdge` ノードの下に `env` ノードがある場合は、LOCAL_EDGE_USER と lOCAL_GROUP_ID の値に注意してください。これらは、ドキュメントの後半で必要になります。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/deployment-manifest-env-node.png" alt-text="ローカルの Edge ユーザーとグループの配置マニフェストと env セクションを示すスクリーンショット。":::

### <a name="save-the-live-video-analytics-topologies"></a>Live Video Analytics トポロジを保存する

1. Live Video Analytics メディア グラフを保存するには、IoT Edge デバイスの下にある Live Video Analytics モジュールを選択します。  次の例では、コンテナ名は *lvaEdge* です。

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/live-video-analytics-edge.png" alt-text="Live Video Analytics の IoT Edge モジュールのコンテナー名を示すスクリーンショット。":::

1. **[ダイレクト メソッド]** を選択します。

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/direct-method.png" alt-text="Live Video Analytics のダイレクト メソッド呼び出しを示すスクリーンショット。":::

1. [メソッド名] フィールドに「**GraphTopologyList**」と入力し、ペイロードに次のコードを入力します。

    ```JSON
    {
        "@apiVersion" : "2.0"
    }
    ```

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/method-name.png" alt-text="IoT Edge のダイレクト メソッド呼び出しを示すスクリーンショット。":::

1. **[メソッドの呼び出し]** を選択します。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/invoke-method.png" alt-text="[メソッドの呼び出し] を示すスクリーンショット。":::

1. 戻り値は、[結果] セクションに表示されます。  これが、ユーザーの Live Video Analytics のトポロジです。  JSON をコピーし、安全な場所に保存しておきます。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/topology-result.png" alt-text="GraphTopologyList メソッドの結果を示すスクリーンショット。":::

1. `GraphTopologyList` を `GraphInstanceList` に置き換えた上で、このプロセスを繰り返し、結果セクションを保存します。  これが、ユーザーのグラフ インスタンスの一覧です。 すべてのアクティブなインスタンスの名前を確認します。

1.  Live Video Analytics の設定のバックアップ コピーを作成したら、 **[メソッド名]** に「**GraphInstanceDeactivate**」と入力し、以下のコードを入力して、"*アクティブなメディア グラフをすべて無効*" にします。

    ```json
    {
        "@apiVersion" : "2.0",
        "name" : "<Graph_Name>"
    }
    ```

    > [!NOTE]
    > Graph_Name を、新しい結果のアクティブなインスタンスの名前に置き換えます。  すべてのアクティブなグラフが非アクティブ化されるまで、このプロセスを繰り返します。 

## <a name="azure-video-analyzer-device-preparation"></a>Azure Video Analyzer: デバイスの準備 

移行を開始するには、Azure Video Analyzer モジュールを実行するように IoT Edge デバイスを構成する必要があります。 Azure Video Analyzer モジュールは、特権のないローカル ユーザー アカウントを使用して IoT Edge デバイス上で実行できる必要があります。 このモジュールには、アプリケーションの構成データを格納するための特定のローカル フォルダーが必要となります。 RTSP カメラ シミュレーター モジュールには、ライブ ビデオ フィードの合成に使用するビデオ ファイルが必要です。 

1. SSH 経由で IoT Edge デバイスにサインインします。

    > [!Note]
    > IoT Edge デバイスのマニフェスト ファイルのレビュー (「現在の設定のバックアップを作成する」セクションの手順 6) に基づき、IoT Edge デバイスで新しいユーザーまたはグループを作成するか、または Live Video Analytics で作成したものを再利用する必要があります。

    IoT Edge デバイスが Live Video Analytics 用のローカル ユーザーとグループ ID (配置マニフェストにあります) を使用して構成されている場合、手順 1 は省略できますが、このドキュメントで後に使用されるユーザーとグループの名前と値をメモしておいてください。  Live Video Analytics の配置 (配置マニフェストにあります) に一意のフォルダ構造を使用している場合は、*videoanalyzer* をこれらの値に置き換え、手順 2 の最初の 2 行を省略します。  

    次の例は、Live Video Analytics の配置の一意のフォルダー構造を示したものです。  *applicationDataDirectory* が /var/lib/videofiles になっていることに注意してください。 この例では、Live Video Analytics によって、IoT Edge デバイス上に *localedgeuser* と *localedgegroup* をが作成されています。

    ```
    "lvaEdge": {
                "properties.desired": {
                    "applicationDataDirectory": "/var/lib/videofiles",
                    "azureMediaServicesArmId": "/subscriptions/resourceGroups/lvamigration/providers/microsoft.media/mediaservices/lvasamplejbbvaomtycjas",
                    "aadTenantId": "<guid>",
                    "aadServicePrincipalAppId": "<guid>",
                    "aadServicePrincipalSecret": "<guid>",
                    "aadEndpoint": "https://login.microsoftonline.com",
                    "aadResourceId": "https://management.core.windows.net/",
                    "armEndpoint": "https://management.azure.com/",
                    "diagnosticsEventsOutputName": "AmsDiagnostics",
                    "operationalEventsOutputName": "AmsOperational",
                    "logLevel": "Information",
                    "logCategories": "Application,Events",
                    "allowUnsecuredEndpoints": true,
                    "telemetryOptOut": false
                }
            },
    ```

    次に、下のコードを実行します。

     ```
       sudo mkdir -p /var/lib/videofiles/tmp/ 
       sudo chown -R localedgeuser:localedgegroup /var/lib/videofiles/tmp/
       sudo mkdir -p /var/lib/videofiles/logs
       sudo chown -R localedgeuser:localedgegroup /var/lib/videofiles/logs
     ```

1. 次のコードを実行して、IoT Edge デバイスにローカル ユーザー アカウントを作成します。

   ```bash
   sudo groupadd -g 1010 localedgegroup
   sudo useradd --home-dir /home/localedgeuser --uid 1010 --gid 1010 localedgeuser
   sudo mkdir -p /home/localedgeuser
   sudo chown -R localedgeuser:localedgegroup /home/localedgeuser/
   ```

1. 次のコードを使用して、*avaedge* フォルダー構造を作成します。

   ```bash
   sudo mkdir -p /var/lib/videoanalyzer 
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/
   sudo mkdir -p /var/lib/videoanalyzer/tmp/ 
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/tmp/
   sudo mkdir -p /var/lib/videoanalyzer/logs
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/logs
   ```

## <a name="create-azure-resources"></a>Azure リソースを作成します

次の手順では、必要な Azure リソースを作成します。 次の手順は、[Azure リソースの作成](../../azure-video-analyzer/video-analyzer-docs/get-started-detect-motion-emit-events-portal.md)に関するセクションに記載されており、便宜上、ここでも説明しています。 

ここでは、必要な Azure リソース (Video Analyzer アカウント、ストレージ アカウント、ユーザーに割り当てられたマネージド ID) を作成します。 

Azure Video Analyzer アカウントを作成する場合は、Azure ストレージ アカウントを関連付ける必要があります。 Video Analyzer を使用してカメラからライブ ビデオを記録する場合、そのデータはストレージ アカウント内のコンテナーに BLOB として格納されます。 マネージド ID を使用して、Video Analyzer アカウントにストレージ アカウントへの適切なアクセス権を付与する必要があります。 

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Azure Portal で Video Analyzer アカウントを作成する

1.  [Azure portal](https://portal.azure.com/) にサインインします。 

1. 検索ボックスに「 **Video Analyzer**」と入力します。 

1.  **[サービス]** の下にある  **[Video Analyzer]**   を選択します。 

1.  **[追加]** を選択します。 

1.  **[Create Video Analyzer account]\(Video Analyzer アカウントを作成する\)**   セクションで、これらの必要な値を入力します。 

   - **サブスクリプション**: Video Analyzer アカウントを作成するために使用するサブスクリプションを選びます。 

   - **リソース グループ**: Video Analyzer アカウントを作成するリソース グループを選ぶか、 **[新規作成]**   を選択してリソース グループを作成します。 

   - **Video Analyzer account name\(Video Analyzer アカウント名\)** : Video Analyzer アカウントの名前を入力します。 その名前は、スペースを入れず、すべて小文字または数字を使用し、3 から 24 文字の長さにする必要があります。 

   - **場所**: Video Analyzer アカウントを展開する場所を選びます (例:  **米国西部 2**)。 

   - **ストレージ アカウント**: ストレージ アカウントを作成します。  [Standard 汎用 v2](../../storage/common/storage-account-overview.md#types-of-storage-accounts)  ストレージ アカウントを選択することをお勧めします。 

      > [!NOTE]
      > Azure Video Analyzer アカウントを展開するリージョンでストレージ アカウントをお持ちの場合は、新しいストレージ アカウントを作成する代わりにそのストレージ アカウントを使用することができます。 

   - **[ユーザー ID]** : 新しいユーザー割り当てマネージド ID を作成し、名前を付けます。 

1. フォームの下部にある、"I certify that I have obtained all necessary rights and consents under applicable law to process media content and agree to be bound by all applicable" (私は、メディア コンテンツを処理するために必要なすべての権利と同意を、適用される法の下に取得していること、および該当するすべての法を遵守することに同意します) の横のボックスに **チェック** を入れてください。 

1. フォームの下部にある  **[確認および作成]**   を選択します。 

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/video-analyzer-account.png" alt-text="Azure portal で Video Analyzer アカウントを作成する方法を示すスクリーンショット。":::

   > [!NOTE]
   > これらの Azure リソースは、ユーザーのリソース グループに追加されます。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/resources.png" alt-text="Azure リソース グループにも追加されるリソースを示すスクリーンショット。":::

## <a name="deploy-the-azure-video-analyzer-edge-module"></a>Azure Video Analyzer Edge モジュールを配置する

### <a name="generate-azure-video-analyzer-edge-module-token"></a>Azure Video Analyzer Edge モジュール トークンの生成

1. Video Analyzer アカウントに移動します。

1. **[エッジ]** ペインで **[Edge モジュール]** を選択します。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge-module.png" alt-text="Azure Video Analyzer アカウントの Edge モジュールの作成を示すスクリーンショット。":::

1. **[Add edge modules]\(Edge モジュールの追加\)** を選択し、新しいエッジ モジュールの名前として「**avaedge**」と入力し、 **[追加]** を選択します。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/add-edge-modules.png" alt-text="Edge モジュールを追加する方法を示すスクリーンショット。":::

1. 右側に **[Copy the provisioning token]\(プロビジョニング トークンのコピー\)** ペインが表示されます。 **[Recommended desired properties for IoT module deployment]\(IoT モジュールの配置に推奨される必要なプロパティ\)** の下に次のスニペットをコピーします。  これは後の手順で必要になります。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/provisioning-token.png" alt-text="[プロビジョニング トークン] ペインを示すスクリーンショット。":::

### <a name="deploy-the-azure-video-analyzer-edge-module"></a>Azure Video Analyzer Edge モジュールを配置する

1.  Azure IoT Hub に移動します。

1. **[デバイスの自動管理]** の下にある **[Select IoT Edge]\(IoT Edge を選択\)** を選択します。

1. IT Edge デバイスのデバイス ID の値を選択します。

1. [Set modules]\(モジュールの設定\) を選びます。

1. **[追加]** を選択し、ドロップダウン メニューから **[IoT Edge モジュール マーケットプレース]** を選びます。

1. 検索フィールドに Azure Video Analyzer と入力します。

1. Azure Video Analyzer を選択します。

1. **[モジュールの設定] ペイン** で、**AzureVideoAnalyzerEdge** を選択します。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge.png" alt-text="Azure Video Analyzer の IoT Edge の追加モジュールを示すスクリーンショット。":::

1. IoT Edge モジュール名のフィールドに「**avaedge**」と入力します。
1. **[環境変数]** を選択します。 

   > [!NOTE]
   > Live Video Analytics の展開で、「現在の設定のバックアップを作成する」セクションの手順 6 で見つけたローカル ユーザーとグループを使用していた場合は、次の手順で LOCAL_USER_ID、LOCAL_GROUP_ID の代わりにこれらの値を使用します。

1. 名前フィールドに「**LOCAL_USER_ID**」と入力し、値フィールドに「**1010**」と入力します。  次の名前フィールドに「**LOCAL_GROUP_ID**」と入力し、値フィールドに「**1010**」と入力します。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/environment-variables.png" alt-text="Azure Video Analyzer の環境変数を示すスクリーンショット。":::

1. **[コンテナーの作成オプション]** を選択し、次を貼り付けます。

      ```json
      {
         "HostConfig": {
            "LogConfig": {
                  "Type": "",
                  "Config": {
                     "max-size": "10m",
                     "max-file": "10"
                  }
            },
            "Binds": [
                  "/var/media/:/var/media/",
                  "/var/lib/videoanalyzer/:/var/lib/videoanalyzer"
            ],
            "IpcMode": "host",
            "ShmSize": 1536870912
         }
      }
      ```

1.  **[モジュール ツインの設定]**   を選択し、Video Analyzer アカウントの  **[Copy the provisioning token]\(プロビジョニング トークンのコピー\)** ページから先ほどコピーしたスニペットを貼り付けます。 

      ```json
      {
         "applicationDataDirectory": "/var/lib/videoanalyzer",
         "ProvisioningToken": "<Provisioning Token Value>",
         "diagnosticsEventsOutputName": "diagnostics",
         "operationalEventsOutputName": "operational",
         "logLevel": "information",
         "LogCategories": "Application,Events",
         "allowUnsecuredEndpoints": true,
         "telemetryOptOut": false
      }
      ```

      > [!NOTE]
      > `<Provisioning Token Value>` は、前の「Azure Video Analyzer Edge モジュール トークンの生成」セクションの手順 4 で取得したプロビジョニング トークンのプレースホルダーです。

1. **[更新]** を選択します。

1.  **[ルート]** を選択します。
1.  **[名前]** に「 **AVAToHub**」と入力します。  **[値]** に「 __FROM /messages/modules/avaedge/outputs/* INTO $upstream__」と入力します。 
1. ルートの右側にあるごみ箱アイコンを選択して、ルート **LVAToHub** を削除します。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/delete-route.png" alt-text="Live Video Analytics I O T Edge ルートを削除する方法を示すスクリーンショット。":::

1. **[モジュール]** の下にある *lvaEdge* モジュールを探し、右側にあるごみ箱アイコンを選択して削除します。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/delete-live-video-analytics-edge.png" alt-text="Live Video Analytics IoT Edge モジュールを削除する方法を示すスクリーンショット。":::

1.  **[確認および作成]** を選択してから、 **[作成]**   を選択して  **avaedge**  Edge モジュールをデプロイします。 

### <a name="verify-your-deployment"></a>デプロイを確認する

デバイスの詳細ページで、 **avaedge**   モジュールが  **[デプロイで指定]**   および  **[デバイス別に報告]** の両方として一覧表示されていることを確認します。 

モジュールがデバイス上で起動され、IoT Hub に報告されるまで、しばらく時間がかかる場合があります。 ページを更新して、最新の状態を表示します。 状態コード  **200 -- OK**  は、 [IoT Edge ランタイム](../../iot-edge/iot-edge-runtime.md) が正常であり、正しく動作していることを意味します。 

:::image type="content" source="./media/migrate-to-azure-video-analyzer/status-200.png" alt-text="IoT Edge ランタイムの状態が 200 であることが報告されているスクリーンショット。":::

:::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge-running.png" alt-text="すべてのモジュールが実行されていることを報告する IoT Edge ランタイムを示すスクリーンショット。":::

## <a name="convert-media-graph-topologies-to-azure-video-analyzer-pipelines"></a>メディア グラフ トポロジから Azure Video Analyzer パイプラインへの変換

Live Video Analytics で使用されるメディア グラフ トポロジと、Azure Video Analyzer で使用されるパイプライン トポロジには、いくつかの違いがあります。  次の表を使用して、Live Video Analytics と Azure Video Analyzer トポロジを比較してください。

| シナリオ                                                     | メディア グラフ (Live Video Analytics)                                            | パイプライン (Azure Video Analyzer)                                               |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 複数の AI モデルを使用して目的のオブジェクトを記録する         | [ai-composition](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/ai-composition/2.0/topology.json) | [ai-composition](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/ai-composition/topology.json) |
| ビデオとオーディオ                                              | [audio-video]()                                               | [audio-video]()                                               |
| 継続的なビデオ記録                                   | [cvr-asset](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset) | [cvr-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-video-sink) |
| gRPC 拡張機能を使用した継続的なビデオ記録と推論 | [cvr-with-grpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json) | [cvr-with-grpcExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-grpcExtension/topology.json) |
| HTTP 拡張機能を使用した継続的なビデオ記録と推論 | [cvr-with-httpExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-httpExtension/2.0/topology.json) | [cvr-with-httpExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-httpExtension/topology.json) |
| モーション検出を使用した継続的なビデオ記録             | [cvr-with-motion](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-motion/2.0/topology.json) | [cvr-with-motion](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-motion/topology.json) |
| 外部 AI からのイベントに基づく [Azure Video Analyzer Video/Assets] へのイベント ベースのビデオ記録 | [evr-grpcExtension-assets](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-grpcExtension-assets) | [evr-grpcExtension-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sinkhttps://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sink) |
| 外部 AI からのイベントに基づく [Azure Video Analyzer ビデオまたはアセット] へのイベント ベースのビデオ記録 | [evr-httpExtension-assets](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-httpExtension-assets) | [evr-httpExtension-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-httpExtension-video-sink) |
| 外部推論エンジンによって検出された特定のオブジェクトに基づいた [アセットまたはビデオ シンク] へのイベント ベースのビデオ記録 | [evr-hubMessages-assets](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/2.0/topology.json) | [evr-hubMessages-video-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json) |
| IoT Edge Hub を介して送信されたメッセージに基づいたファイルへのイベント ベースのビデオ記録 | [evr-hubMessage-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/2.0/topology.json) | [evr-hubMessage-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-hubMessage-file-sink/topology.json) |
| モーション イベントに基づいたローカル ファイルへのイベントベースのビデオ記録 | [evr-motion-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json) | [evr-motion-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-motion-file-sink/topology.json) |
| モーション イベントに基づいた [アセットまたはビデオ シンク] およびローカル ファイルへのイベントベースのビデオ記録 | [evr-motion-assets-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets-files/2.0/topology.json) | [evr-motion-video-sink-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-motion-video-sink-file-sink/topology.json) |
| OpenVINO(TM) DL Streamer にイメージを送るために gRPC 拡張機能を使ってライブ映像を解析する - インテルの Edge AI 拡張機能モジュール | [grpcExtensionOpenVINO](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) | [grpcExtensionOpenVINO](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json) |
| HTTP 拡張機能を使用して、外部の推論エンジンにイメージを送信するライブ ビデオを分析。 | [httpExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json) | [httpExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json) |
| OpenVINO™ Model Server にイメージを送るために HTTP 拡張機能を使ってライブ映像を解析する - インテルの Edge AI 拡張機能モジュール | [httpExtensionOpenVINO](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtensionOpenVINO/2.0/topology.json) | [httpExtensionOpenVINO](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtensionOpenVINO/topology.json) |
| 空間分析用の Computer Vision を使用したライブ ビデオの分析 | [lva-spatial-analysis](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/lva-spatial-analysis/2.0/topology.json) | [spatial-analysis](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/spatial-analysis) |

> [!NOTE]
> GitHub のサンプル セクションでは、[Live Video Analytics メディア グラフ トポロジ](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies)と [Azure Video Analyzer パイプライン トポロジ](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies)の両方を検索して比較することができます。 これらのサンプルを確認することで、既存のメディア グラフ トポロジを Azure Video Analyzer パイプライン トポロジに更新することができます。

### <a name="set-and-activate-the-pipeline-topology"></a>パイプライン トポロジを設定してアクティブ化する

メディア グラフ トポロジ (Live Video Analytics) をパイプライン トポロジ (Azure Video Analyzer) に変換したら、*avaedge* モジュールでパイプラインを設定する必要があります。

1. トポロジを設定するには、IoT Edge デバイスで Azure Video Analyzer Edge モジュールを選択します。  次の例では、コンテナー名は *avaedge* です。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-container.png" alt-text="Live Video Analytics の IoT Edge モジュールを示すスクリーンショット。":::

1. **[ダイレクト メソッド]** を選択します。

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/direct-method.png" alt-text="Live Video Analytics のダイレクト メソッド呼び出しを示すスクリーンショット。":::

1. **[メソッド名]** に「**pipelineTopologySet**」と入力し、ペイロードに変換したパイプライン JSON を入力します。

1. **[メソッドの呼び出し]** を選択します。

      戻り値フィールドには、"status": 201 の後に実際のトポロジが表示されているはずです。  この状態は、新しいトポロジが作成されたことを示しています。

1. 次に、トポロジを使用してライブ パイプラインを作成する必要があります。  [メソッド名] フィールドに「**livePipelineSet**」と入力し、[ペイロード] フィールドには以下を入力して、手順 1 から 3 を実行します。

   ```JSON
         {
   "@apiVersion": "1.0",
   "name": "<live pipeline name>",
   "properties": {
      "topologyName": "<Name of the set topology>",
      "description": "Sample pipeline description",
      "parameters": [
         {
         "name": "rtspUrl",
         "value": "<RTSP camera string>"
         },
         {
         "name": "rtspUserName",
         "value": "<Camera User>"
         },
         {
         "name": "rtspPassword",
         "value": "Camera User Password"
         }
      ]
   }
   }
   ```

   > [!NOTE]
   > `<live pipeline name>`、`<Name of the set topology>`、`<RTSP camera string>`、`<Camera User Password>` の値を実際の環境の値に置き換えます。 

1. **[メソッドの呼び出し]** を選択します。
   
      戻り値フィールドには、"status": 201 の後に実際のライブ パイプラインが表示されているはずです。  この状態は、新しいパイプラインが作成されたことを示しています。

2. 最後に、 **[メソッド名]** に「**livePipelineActivate**」と入力し、ペイロード フィールドに以下を入力して、手順 1 から 3 に従ってライブ パイプラインをアクティブにします。

      ```JSON
         {
            "@apiVersion": "1.0",
            "name": "<live pipeline name>"
         }
      ```

    > [!NOTE]
    > `<live pipeline name>` を、上記で設定したライブ パイプライン名に置き換えます。

4. **[メソッドの呼び出し]** を選択します。
   
   戻り値フィールドには、"Status": 200 と表示されているはずです。  この状態は、新しいパイプラインがアクティブ化されたことを示しています。

## <a name="direct-methods"></a>ダイレクト メソッド

Live Video Analytics と Azure Video Analyzer は、どちらもダイレクト メソッド呼び出しを使用しています。  移行の一環として、ダイレクト メソッドが更新され、Live Video Analytics ソリューションで使用する可能性があるワークフローについて確認が必要になりました。  次の表は、[Live Video Analytics のダイレクト メソッド呼び出し](direct-methods.md)と [Azure Video Analyzer のダイレクト メソッド呼び出し](../../azure-video-analyzer/video-analyzer-docs/direct-methods.md)の違いを示しています。

| ライブ ビデオ分析 | Azure Video Analyzer |
| ------------------------ | ------------------------ |
| GraphTopologyList        | pipelineTopologyList     |
| GraphTopologySet         | pipelineTopologySet      |
| GraphTopologyDelete      | pipelineTopologyDelete   |
| GraphInstanceList        | livePipelineList         |
| GraphInstanceSet         | livePipelineSet          |
| GraphInstanceActivate    | livePipelineActivate     |
| GraphInstanceDeactivate  | livePipelineDeactivate   |
| GraphInstanceDelete      | livePipelineDelete       |

## <a name="miscellaneous-changes"></a>その他の変更 

- Appsettings.js: appsettings.json ファイルを更新し、moduleId があなたのモジュールを参照するようにします。 つまり、*avaeEdge* に設定します。 

- [gRPC のコントラクト](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc)にマイナーな変更が加えられました。 既存の gRPC 拡張機能は引き続き機能しますが、プロトタイプ ファイルを更新することをお勧めします。 変更は、inferencing.proto : string sequence_id=14 の新しい行です。

## <a name="next-steps"></a>次のステップ

-  [モーションが検出されたらクラウドにビデオを記録するクイック スタート](../../azure-video-analyzer/video-analyzer-docs/detect-motion-record-video-clips-cloud.md)を試す。 
-  [ライブ ビデオを分析するためのクイック スタート](../../azure-video-analyzer/video-analyzer-docs/analyze-live-video-use-your-model-http.md)を試す。 
- [Azure Video Analyzer の用語](../../azure-video-analyzer/video-analyzer-docs/terminology.md)を確認する。 
- [Azure Video Analyzer パイプライン](../../azure-video-analyzer/video-analyzer-docs/pipeline.md)を確認する。 