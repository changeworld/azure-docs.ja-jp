---
title: Live Video Analytics on IoT Edge と Azure Custom Vision でライブ ビデオを分析する
description: Custom Vision を使用して、おもちゃのトラックを検出できるコンテナー化されたモデルを構築し、Live Video Analytics on IoT Edge (LVA) の AI 拡張機能を使用して、ライブ ビデオ ストリームからおもちゃのトラックを検出するためにそのモデルをエッジにデプロイする方法について説明します。
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 0e980ac73d77b6fbbfdb8178f285904d3bf29920
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929458"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>チュートリアル:Live Video Analytics on IoT Edge と Azure Custom Vision でライブ ビデオを分析する

このチュートリアルでは、[Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) を使用して、おもちゃのトラックを検出できるコンテナー化されたモデルを構築し、Live Video Analytics on IoT Edge の [AI 拡張機能](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model)を使用して、ライブ ビデオ ストリームからおもちゃのトラックを検出するためにそのモデルをエッジにデプロイする方法について説明します。

ここでは、Custom Vision の機能 (これにより、データ サイエンス、ML、AI の知識がないユーザーでも数点の画像を単純にアップロードしてラベル付けすることでコンピューター ビジョン モデルを構築、トレーニングすることが可能) と Live Video Analytics の機能を組み合わせて、カスタム モデルをコンテナーとしてエッジに簡単にデプロイし、シミュレートされたライブ ビデオ フィードを分析する方法について説明します。 このチュートリアルは Azure VM を IoT Edge デバイスとして使用します。C# で記述されたサンプル コードに基づいており、クイックスタート「[モーションの検出とイベントの生成](detect-motion-emit-events-quickstart.md)」を基に構築されています。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * 関連リソースを設定する。
> * おもちゃのトラックを検出するための Custom Vision モデルをクラウドに構築してエッジにデプロイする
> * HTTP 拡張機能を持つメディア グラフを作成してカスタム ビジョン モデルにデプロイする
> * サンプル コードを実行する。
> * 結果を確認して解釈する。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>推奨される事前読み取り  

開始する前に、次の記事に目を通すことをお勧めします。 

* [IoT Edge の Live Video Analytics (プレビュー)](overview.md)
* [Azure Custom Vision の概要](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)
* [Live Video Analytics on IoT Edge の用語](terminology.md)
* [メディア グラフの概念](media-graph-concept.md)
* [ビデオ記録を行わない Live Video Analytics](analyze-live-video-concept.md)
* [ご自分のモデルを使用して Live Video Analytics を実行する](use-your-model-quickstart.md)
* [チュートリアル:IoT Edge モジュールを開発する](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [deployment.*.template.json を編集する方法](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>前提条件

このチュートリアルの前提条件は次のとおりです。

* [Visual Studio Code](https://code.visualstudio.com/)。[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) および [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 拡張機能と共に、ご自分の開発マシンにインストールされている必要があります。

    > [!TIP]
    > Docker のインストールを求められる場合があります。 このメッセージは無視してください。
* 開発マシンにインストールされた [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer)。
* 次のことを行っておきます。
    
    * [Azure リソースを設定する](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [開発環境を設定する](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)

## <a name="review-the-sample-video"></a>サンプル ビデオを確認する


このチュートリアルでは、[おもちゃの車の推論ビデオ](https://lvamedia.blob.core.windows.net/public/t2.mkv/) ファイルを使用してライブ ストリームをシミュレートします。 このビデオは、[VLC media player](https://www.videolan.org/vlc/) などのアプリケーションを使用して確認できます。 Ctrl キーを押しながら N キーを押し、[おもちゃの車の推論ビデオ](https://lvamedia.blob.core.windows.net/public/t2.mkv)へのリンクを貼り付けて、再生を開始します。 ビデオを見ると、36 秒のマーカーで、おもちゃのトラックがビデオに出てくることがわかります。 このカスタム モデルは、この特定のおもちゃのトラックを検出するようにトレーニングされています。 このチュートリアルでは、Live Video Analytics on IoT Edge を使用して、このようなおもちゃのトラックを検出し、関連する推論イベントを IoT Edge ハブに発行します。

## <a name="overview"></a>概要

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Custom Vision の概要":::

この図は、このチュートリアルでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](media-graph-concept.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、[フレーム レート フィルター プロセッサ](media-graph-concept.md#frame-rate-filter-processor) ノードにビデオ フレームを送信します。 このプロセッサは、[HTTP 拡張プロセッサ](media-graph-concept.md#http-extension-processor) ノードに到達するビデオ ストリームのフレーム レートを制限します。
HTTP 拡張ノードは、プロキシの役割を果たします。 ビデオ フレームを、指定した画像の種類に変換します。 次に、その画像を、HTTP エンドポイントの背後で AI モデルを実行する別のエッジ モジュールに、REST 経由で転送します。 この例では、このエッジ モジュールは Custom Vision を使用して構築された、おもちゃのトラックの検出モデルです。 HTTP 拡張プロセッサ ノードは、検出結果を収集し、イベントを [IoT Hub シンク](media-graph-concept.md#iot-hub-message-sink) ノードに発行します。 次に、このノードはこれらのイベントを [IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub) に送信します。

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Custom Vision のおもちゃ検出モデルを構築してデプロイする 

Custom Vision という名前が示唆するように、この機能を活用して、独自のカスタム オブジェクト検出器または分類器をクラウドに構築できます。 シンプルで使いやすい、直感的なインターフェイスを使用してカスタム ビジョン モデルを構築し、コンテナーを介してクラウドまたはエッジにデプロイできます。 

おもちゃのトラックの検出器を構築するには、Web ポータルの[クイックスタートの記事](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector)で、この Custom Vision の「オブジェクト検出器の構築」に従うことをお勧めします。

その他のメモ:
 
* このチュートリアルでは、クイックスタートの記事の[前提条件セクション](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#prerequisites)にあるサンプル画像を使用しないでください。 特定の画像セットを利用しておもちゃ検出器のカスタム ビジョン モデルを構築する代わりに、クイックスタートで[トレーニング画像の選択](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#choose-training-images)を求められときに、[こちらの画像](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip)を使用することをお勧めします。
* クイックスタートの画像へのタグ付け セクションで、図に示されているおもちゃのトラックに、「デリバリー トラック」というタグを付けてください。

完了し、満足のいくモデルが準備できたら、[パフォーマンス] タブの [エクスポート] ボタンを使用して、Docker コンテナーにエクスポートできます。コンテナー プラットフォームの種類として Linux を選択してください。 これは、コンテナーが実行されるプラットフォームです。 コンテナーをダウンロードするコンピューターは、Windows でも Linux でもかまいません。 次の手順は、Windows コンピューターにダウンロードされたコンテナー ファイルに基づいています。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Dockerfile":::
 
1. `<projectname>.DockerFile.Linux.zip` という名前の zip ファイルがローカル コンピューターにダウンロードされている必要があります。 
1. Windows デスクトップ用の [Docker](https://docs.docker.com/get-docker/) をインストールしない場合は、docker がインストールされているかどうかを確認します。
1. ダウンロードしたファイルを任意の場所に解凍します。 コマンド ラインを使用して、解凍したフォルダー ディレクトリにアクセスします。
    
    次のコマンドを実行します 
    
    1. `docker build -t cvtruck` 
    
        このコマンドは、一連のパッケージをダウンロードし、docker イメージをビルドして、`cvtruck:latest` というタグを付けます。 
    
        > [!NOTE]
        > 成功した場合は、`- Successfully built <docker image id> and Successfully tagged cvtruck:latest.` のように表示されます。初回は一部の依存関係パッケージがダウンロードされないことがあるため、ビルド コマンドが失敗した場合はもう一度試してください。
    1. `docker  image ls`

        このコマンドは、新しいイメージがローカル レジストリにあるかどうかを確認します。
    1. `docker run -p 127.0.0.1:80:80 -d cvtruck`
    
        このコマンドは、docker の公開されたポート (80) をローカル コンピューターのポート (80) に公開します。
    1. `docker container ls`
    
        このコマンドは、ポート マッピングと、docker コンテナーがコンピューター上で正常に実行されているかどうかを確認します。 出力は次のようになります。

        ```
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
        8b7505398367        cvtruck             "/bin/sh -c 'python …"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            このコマンドは、ローカル コンピューター上のコンテナーをテストします。モデルをトレーニングしたときと同じデリバリー トラックが画像に含まれている場合、出力は次のようになり、これはデリバリー トラックが 90.12% の確率で検出されたことを示しています。
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>サンプル ファイルを詳しく調べる

1. VSCode で、"src/edge" を参照します。 作成した .env ファイルが、いくつかの展開テンプレート ファイルと共に確認できます。

    展開テンプレートでは、いくつかのプレースホルダー値で、エッジ デバイスの配置マニフェストが参照されています。 それらの変数の値が .env ファイルに格納されています。
1. 次に、"src/cloud-to-device-console-app" フォルダーに移動します。 ここには、作成した appsettings.json ファイルのほか、いくつかのファイルが確認できます。

    * c2d-console-app.csproj - これは VSCode のプロジェクト ファイルです。
    * operations.json - このファイルには、プログラムで実行するさまざまな操作がリストされます。
    * Program.cs - これは次の処理を実行するサンプル プログラムのコードです。

        * アプリ設定を読み込みます。
        * Live Video Analytics on IoT Edge モジュールのダイレクト メソッドを呼び出してトポロジを作成し、グラフのインスタンスを作成し、アクティブ化します。
        * 一時停止し、グラフ出力を [ターミナル] ウィンドウで調べたり、IoT ハブに送信されたイベントを [出力] ウィンドウで調べたりできるようにします。
        * グラフ インスタンスを非アクティブ化し、グラフ インスタンスを削除して、グラフ トポロジを削除します。
        
## <a name="generate-and-deploy-the-deployment-manifest"></a>配置マニフェストを生成してデプロイする

1. VSCode で、"src/cloud-to-device-console-app/operations.json" に移動します

1. GraphTopologySet で、以下に該当することを確認します。<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. GraphInstanceSet で、以下が設定されていることを確認します。 
    1. "topologyName" :"InferencingWithHttpExtension"
    1. パラメーター配列の先頭に `{"name": "inferencingUrl","value": "http://cv:80/image"},` を追加します。
    1. rtspUrl パラメーターの値を "rtsp://rtspsim:554/media/t2.mkv" に変更します。    
1. GraphTopologyDelete で、以下が設定されていることを確認します。"name":"InferencingWithHttpExtension"
1. "src/edge/ deployment.customvision.template.json" ファイルを右クリックし、 **[Generate IoT Edge Deployment Manifest]\(IoT Edge 配置マニフェストの生成\)** をクリックします。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="IoT Edge 配置マニフェストの生成":::
  
    これで、"deployment.customvision.amd64.json" という名前のマニフェスト ファイルが src/edge/config フォルダーに作成されます。
1. "src/edge/ deployment.customvision.template.json" ファイルを開き、registryCredentials json ブロックを見つけます。 このブロックには、Azure コンテナー レジストリのアドレスと、そのユーザー名とパスワードが記載されています。
1. コマンド ラインに従って、ローカルの Custom Vision コンテナーを Azure コンテナー レジストリにプッシュします。

    1. 次のコマンドを実行して、レジストリにログインします。
    
        `docker login <address>`
    
        認証を求められたら、ユーザー名とパスワードを入力します。 
        
        > [!NOTE]
        > パスワードはコマンド ラインに表示されません。
    1. 次を使用して画像にタグを付けます。<br/>`docker tag cvtruck   <address>/cvtruck`
    1. 次を使用して画像をプッシュします。<br/>`docker push <address>/cvtruck`

        成功した場合は、コマンドラインに画像の SHA と共に 'Pushed' と表示されます。 
    1. また、Azure portal で Azure コンテナー レジストリをチェックすることによって確認することもできます。 ここでは、リポジトリの名前がタグと共に表示されます。 
1. 左下隅の [Azure IoT Hub] ペインの横にある [その他のアクション] アイコンをクリックして、IoTHub 接続文字列を設定します。 この文字列は、appsettings.json ファイルからコピーできます (VSCode で適切な IoT Hub 構成を確実に行うためのもう 1 つの推奨される方法として、[IoT Hub の選択コマンド](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)を使用する方法があります)。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="接続文字列":::
1. 次に、"src/edge/config/ deployment.customvision.amd64.json" を右クリックし、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** をクリックします。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="単一デバイスのデプロイを作成する":::
1. その後、IoT Hub デバイスを選択するように求められます。 ドロップダウンから [lva-sample-device] を選択します。
1. 30 秒ほど経過したら、左下のセクションで Azure IoT Hub を最新の情報に更新すると、エッジ デバイスに次のモジュールがデプロイされています。

    * Live Video Analytics on IoT Edge (名前: "lvaEdge")。
    * `rtspsim` という名前のモジュール。RTSP サーバーをシミュレートし、ライブ ビデオ フィードのソースとして機能します。
    * `cv` という名前のモジュール。これは名前が示唆するように、カスタム ビジョンを画像に適用し、複数のタグの種類を返す、Custom Vision のおもちゃのトラックの検出モデルです。 (このモデルは "デリバリー トラック" という 1 つのタグのみでトレーニングされています)。

## <a name="prepare-for-monitoring-events"></a>イベントを監視するための準備をする

Live Video Analytics デバイスを右クリックし、 **[組み込みイベント エンドポイントの監視を開始する]** を選択します。 この手順は、Visual Studio Code の [出力] ウィンドウで、IoT Hub イベントを監視するために必要です。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="組み込みイベント エンドポイントの監視を開始する":::

## <a name="run-the-sample-program"></a>サンプル プログラムを実行する

このチュートリアルのグラフ トポロジをブラウザーで開くと、inferencingUrl の値が http://cv:80/image に設定されていることがわかります。これは、ライブ ビデオでおもちゃのトラックが検出された場合に、推論サーバーから結果が返されることを意味します。

1. デバッグ セッションを開始するには、F5 キーを押します。 [ターミナル] ウィンドウにメッセージが出力されるのを確認できます。
1. operations.json コードは、ダイレクト メソッド GraphTopologyList と GraphInstanceList の呼び出しで開始されます。 前回のクイックスタートを完了した後にリソースをクリーンアップしている場合は、このプロセスにより空のリストが返されてから、一時停止します。 続行するには、Enter キーを押します。
    
   [ターミナル] ウィンドウに、次の一連のダイレクト メソッド呼び出しが表示されます。
    
   * 前の topologyUrl を使用する GraphTopologySet の呼び出し。
   * 次の body を使用した GraphInstanceSet の呼び出し。
        
   ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
              },
              {
                "name": "rtspUserName",
                "value": "testuser"
              },
              {
                "name": "rtspPassword",
                "value": "testpassword"
              }
            ]
          }
        }
   ```
    
   * グラフ インスタンスを起動し、ビデオのフローを開始する GraphInstanceActivate の呼び出し。
   * グラフ インスタンスが実行状態であることを表示する GraphInstanceList の 2 回目の呼び出し。
    
1. [ターミナル] ウィンドウの出力が、[続行するには Enter キーを押してください] というプロンプトで一時停止されます。 Enter キーはまだ押さないでください。 上へスクロールして、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認します。
1. Visual Studio Code の [出力] ウィンドウに切り替えます。 Live Video Analytics on IoT Edge モジュールから IoT ハブに送信されているメッセージが表示されます。 このチュートリアルの次のセクションでは、これらのメッセージについて説明します。
1. メディア グラフは引き続き実行され、結果が出力されます。 RTSP シミュレーターによって、ソース ビデオがループ処理され続けます。 メディア グラフを停止するには、 [ターミナル] ウィンドウに戻り、Enter キーを押します。
次の一連の呼び出しによって、リソースがクリーンアップされます。
    
   * グラフ インスタンスを非アクティブ化する GraphInstanceDeactivate の呼び出し。
   * インスタンスを削除する GraphInstanceDelete の呼び出し。
   * トポロジを削除する GraphTopologyDelete の呼び出し。
   * リストが空であることを示す GraphTopologyList の最後の呼び出し。
    
## <a name="interpret-the-results"></a>結果を解釈する

メディア グラフを実行すると、HTTP 拡張プロセッサ ノードから IoT Hub シンク ノードを介して IoT ハブに結果が渡されます。 出力ウィンドウに表示されるメッセージには、body セクションと applicationProperties セクションが含まれています。 詳細については、「[IoT Hub メッセージを作成し、読み取る](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)」を参照してください。

次のメッセージ内のアプリケーションのプロパティと body の内容は、Live Video Analytics モジュールによって定義されています。

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished イベント

メディア グラフがインスタンス化されると、RTSP ソース ノードは、rtspsim-live555 コンテナーで実行されている RTSP サーバーへの接続を試みます。 接続に成功すると、次のイベントが出力されます。 イベントの種類は、Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished です。

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

このメッセージでは、以下の詳細に注目します。

* このメッセージは、診断イベントです。 MediaSessionEstablished は、RTSP ソース ノード (subject) が RTSP シミュレーターと接続され、(シミュレートされた) ライブ フィードの受信を開始したことを示します。
* applicationProperties の subject を見ると、メッセージの生成元がメディア グラフの RTSP ソース ノードであることがわかります。
* applicationProperties の eventType には、このイベントが診断イベントであることが示されています。
* eventTime にはイベントの発生時刻が示されています。
* body には、診断イベントに関するデータが含まれています。 このケースでは、データは[セッション記述プロトコル (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細で構成されています。

### <a name="inference-event"></a>推論イベント

HTTP 拡張プロセッサ ノードは、Custom Vision コンテナーから推論結果を受け取り、その結果を推論イベントとして IoT Hub シンク ノードを介して出力します。

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

上のメッセージに関して、次の点に注意してください。

* applicationProperties の subject が、メッセージの生成元となった MediaGraph 内のノードを参照しています。 このケースでは、HTTP 拡張プロセッサからメッセージが生成されています。
* applicationProperties の eventType を見ると、これは Analytics 推論イベントであることがわかります。
* eventTime にはイベントの発生時刻が示されています。
* "body" に、分析イベントに関するデータが含まれています。 このケースでは、イベントは推論イベントであるため、body には “predictions” という推論の配列が含まれています。
* "predictions" セクションには、おもちゃのデリバリー トラック (タグ = デリバリー トラック) がフレーム内で見つかった予測の一覧が含まれています。 ご存じのように、デリバリー トラックは、おもちゃのトラック用のトレーニング済みのカスタム モデルに指定したカスタム タグであり、このモデルは様々な確率の信頼度スコアで入力ビデオ内のおもちゃのトラックを推論して識別します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

これから他のチュートリアルやクイックスタートに取り組む場合は、作成したリソースをそのまま残しておいてください。 それ以外の場合は、Azure portal にアクセスして、ご利用のリソース グループに移動し、このチュートリアルで使用したリソース グループを選択して、すべてのリソースを削除してください。

## <a name="next-steps"></a>次のステップ

上級ユーザー向けのその他の課題を確認します。

* RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラは、[ONVIF 準拠製品](https://www.onvif.org/conformant-products/)のページで検索できます。 プロファイル G、S、または T に準拠しているデバイスを探します。
* Azure Linux VM ではなく、AMD64 または x64 Linux デバイスを使用してください。 このデバイスは、IP カメラと同じネットワーク内にある必要があります。 [Linux への Azure IoT Edge ランタイムのインストール](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)に関するページの手順を参照できます。 

次に、「[初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)」の手順に従って、デバイスを Azure IoT Hub に登録します。

