---
title: Azure Media Services を使用してアップロード、エンコード、ストリーム配信する | Microsoft Docs
description: Azure Media Services で REST を使用してファイルのアップロード、ビデオのエンコード、コンテンツのストリーム配信を行うには、このチュートリアルの手順のようにします。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 07/16/2018
ms.author: juliako
ms.openlocfilehash: 5cc109467f9affa9cf5f43342203e8d4298269e0
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115208"
---
# <a name="tutorial-upload-encode-and-stream-videos-with-rest"></a>チュートリアル: REST を使用してビデオのアップロード、エンコード、ストリーム配信を行う

このチュートリアルでは、Azure Media Services を使用してビデオのアップロード、エンコード、ストリーム配信を行う方法を示します。

Media Services では、メディア ファイルをさまざまなブラウザーおよびデバイスで再生できる形式にエンコードすることができます。 たとえば、Apple の HLS または MPEG DASH 形式のコンテンツをストリーム配信することが必要な場合があります。 ストリーム配信する前に、高品質のデジタル メディア ファイルをエンコードする必要があります。 エンコードのガイダンスについては、[エンコードの概念](encoding-concept.md)に関する記事をご覧ください。

![ビデオを再生する](./media/stream-files-tutorial-with-api/final-video.png)

このチュートリアルでは、次の操作方法について説明します。    

> [!div class="checklist"]
> * Media Services アカウントを作成する
> * Media Services API にアクセスする
> * Postman ファイルをダウンロードする
> * Postman を構成する
> * Postman を使用して要求を送信する
> * ストリーミング URL をテストする
> * リソースのクリーンアップ

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- [Postman](https://www.getpostman.com/) REST クライアントをインストールして、AMS REST チュートリアルの一部で示されている REST API を実行します。 

    ここでは **Postman** を使用しますが、任意の REST ツールを使用できます。 その他の選択肢は、REST プラグインを使用した **Visual Studio Code** や **Telerik Fiddler** です。 

## <a name="download-postman-files"></a>Postman ファイルをダウンロードする

Postman コレクションと環境ファイルを含む GitHub リポジトリを複製します。

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="configure-postman"></a>Postman を構成する

このセクションでは、Postman を構成します。

### <a name="configure-the-environment"></a>環境の構成 

1. **Postman** を開きます。
2. 画面の右側で、**[Manage environment]/(環境の管理/)** オプションを選択します。

    ![環境を管理する](./media/develop-with-postman/postman-import-env.png)
4. **[Manage environment]/(環境の管理/)** ダイアログで、**[インポート]** をクリックします。
2. `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` を複製したときにダウンロードされた `Azure Media Service v3 Environment.postman_environment.json` ファイルを参照します。
6. **[Azure Media Service v3 Environment]\(Azure Media Service v3 環境\)** 環境が追加されています。

    > [!Note]
    > 前述の **[Access the Media Services API]\(Media Services API にアクセスする\)** セクションから取得した値で、アクセス変数を更新します。

7. 選択されたファイルをダブルクリックして、[API へのアクセス](#access-the-media-services-api)に関する手順に従って取得した値を入力します。
8. ダイアログを閉じます。
9. ドロップ ダウンから **[Azure Media Service v3 Environment]\(Azure Media Service v3 環境\)** 環境を選択します。

    ![環境を選択する](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>コレクションの構成

1. **[インポート]** をクリックしてコレクション ファイルをインポートします。
1. `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` を複製したときにダウンロードされた `Media Services v3.postman_collection.json` ファイルを参照します。
3. **Media Services v3.postman_collection.json** ファイルを選択します。

    ![ファイルをインポートする](./media/develop-with-postman/postman-import-collection.png)

## <a name="send-requests-using-postman"></a>Postman を使用して要求を送信する

このセクションでは、ファイルをストリーム配信できるように、URL のエンコードと作成に関連する要求を送信します。 具体的には、次の要求が送信されます。

1. サービス プリンシパルの認証のために Azure AD トークンを取得する
2. 出力資産を作成する
3. 変換を作成する
4. ジョブを作成する 
5. ストリーミング ロケーターを作成する
6. ストリーミング ロケーターのパスを一覧表示する

> [!Note]
>  このチュートリアルでは、すべてのリソースを一意の名前で作成していることを前提としています。  

### <a name="get-azure-ad-token"></a>Azure AD トークンを取得する 

1. Postman の左側のウィンドウで、[Step 1: Get AAD Auth token]\(手順 1: AAD 認証トークンを取得する\) を選択します。
2. 次に、[Get Azure AD Token for Service Principal Authentication]\(\サービス プリンシパル認証のために Azure AD トークンを取得する) を選択します。
3. **[送信]** をクリックします。

    次の **POST** 操作が送信されます。

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. 応答がトークンと共に返され、"AccessToken" 環境変数がトークン値に設定されます。 "AccessToken" を設定するコードを確認するには、**[Tests]\(テスト\)** タブをクリックします。 

    ![AAD トークンを取得する](./media/develop-with-postman/postman-get-aad-auth-token.png)

### <a name="create-an-output-asset"></a>出力資産を作成する

出力[アセット](https://docs.microsoft.com/rest/api/media/assets)には、対象のエンコード ジョブの結果が格納されます。 

1. Postman の左側のウィンドウで、[Assets]\(資産\) を選択します。
2. 次に、[Create or update an Asset]\(資産を作成または更新する\) を選択します。
3. **[送信]** をクリックします。

    * 次の **PUT** 操作が送信されます。

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
        ```
    * 操作の本文は次のとおりです。

        ```json
        {
        "properties": {
            "description": "My Asset",
            "alternateId" : "some GUID"
         }
        }
        ```

### <a name="create-a-transform"></a>変換を作成する

Media Services でコンテンツをエンコードまたは処理するときは、レシピとしてエンコード設定をセットアップするのが一般的なパターンです。 その後、**ジョブ**を送信してビデオにレシピを適用します。 新しいビデオごとに新しいジョブを送信することで、ライブラリ内のすべてのビデオにレシピを適用します。 Media Services でのレシピは**変換**と呼ばれます。 詳しくは、「[Transforms and jobs](transform-concept.md)」(変換とジョブ) をご覧ください。 このチュートリアルで説明するサンプルでは、さまざまな iOS および Android デバイスにストリーム配信するために、ビデオをエンコードするレシピが定義されています。 

新しい [Transform](https://docs.microsoft.com/rest/api/media/transforms) インスタンスを作成するときは、出力として生成するものを指定する必要があります。 必須のパラメーターは、**TransformOutput** オブジェクトです。 各 **TransformOutput** には **Preset** が含まれます。 **Preset** では、目的の **TransformOutput** の生成に使用されるビデオやオーディオの処理操作の詳細な手順が記述されています。 この記事で説明されているサンプルでは、**AdaptiveStreaming** という名前の組み込みプリセットを使っています。 プリセットは、入力ビデオを入力の解像度とビットレートに基づいて自動生成されるビットレート ラダー (ビットレートと解像度のペア) にエンコードし、ビットレートと解像度の各ペアに対応する、H.264 ビデオと AAC オーディオを含む ISO MP4 ファイルを生成します。 このプリセットについては、[ビットレート ラダーの自動生成](autogen-bitrate-ladder.md)に関するページをご覧ください。

組み込み EncoderNamedPreset またはカスタム プリセットを使用できます。 

> [!Note]
> [Transform](https://docs.microsoft.com/rest/api/media/transforms) を作成するときは、最初に **Get** メソッドを使って変換が既に存在するかどうかを確認する必要があります。 このチュートリアルでは、変換を一意の名前で作成していることを前提としています。

1. Postman の左側のウィンドウで、[Encoding and Analysis]\(エンコードと分析\) を選択します。
2. [Create Transform]\(変換の作成\) を選択します。
3. **[送信]** をクリックします。

    * 次の **PUT** 操作が送信されます。

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
        ```
    * 操作の本文は次のとおりです。

        ```json
        {
            "properties": {
                "description": "Basic Transform using an Adaptive Streaming encoding preset from the libray of built-in Standard Encoder presets",
                "outputs": [
                    {
                    "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                    "preset": {
                        "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                        "presetName": "AdaptiveStreaming"
                    }
                    }
                ]
            }
        }
        ```

### <a name="create-a-job"></a>ジョブを作成する

[ジョブ](https://docs.microsoft.com/rest/api/media/jobs)は、作成された**変換**を特定の入力ビデオまたはオーディオ コンテンツに適用する Media Services への実際の要求です。 **Job** は、入力ビデオの場所や出力先などの情報を指定します。

この例では、ジョブの入力は HTTPS URL ("https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/") に基づいています。

1. Postman の左側のウィンドウで、[Encoding and Analysis]\(エンコードと分析\) を選択します。
2. 次に、[Create or Update Job]\(ジョブを作成または更新する\) を選択します。
3. **[送信]** をクリックします。

    * 次の **PUT** 操作が送信されます。

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
        ```
    * 操作の本文は次のとおりです。

        ```json
        {
        "properties": {
            "input": {
            "@odata.type": "#Microsoft.Media.JobInputHttp",
            "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
            "files": [
                    "Ignite-short.mp4"
                ]
            },
            "outputs": [
            {
                "@odata.type": "#Microsoft.Media.JobOutputAsset",
                "assetName": "testAsset1"
            }
            ]
        }
        }
        ```

ジョブの完了には時間がかかり、完了したら通知を受け取る必要があります。 ジョブの進行状況を確認するには、Event Grid を使用することをお勧めします。 Event Grid は、高可用性、一貫したパフォーマンス、および動的スケーリングを目的に設計されています。 Event Grid では、アプリはほぼすべての Azure サービスやカスタム ソースのイベントをリッスンし、対応できます。 単純な HTTP ベースのリアクティブ イベント ハンドリングでは、インテリジェントなイベント フィルタリングやイベント ルーティングを使用して、効率的なソリューションを構築できます。  [カスタム Web エンドポイントへのイベントのルーティング](job-state-events-cli-how-to.md)に関するページをご覧ください。

**Job** には通常、**Scheduled**、**Queued**、**Processing**、**Finished** (最終状態) という状態があります。 ジョブでエラーが発生すると、**Error** 状態を取得します。 ジョブがキャンセル処理中の場合は **Canceling** を受け取り、完了すると **Canceled** を受け取ります。

### <a name="create-a-streaming-locator"></a>ストリーミング ロケーターを作成する

エンコード ジョブが完了したら、次に、出力アセット内のビデオをクライアントが再生に使用できるようにします。 これを実現するには 2 つのステップがあります。最初に [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) を作成し、次にクライアントが使用できるストリーミング URL を作成します。 

**StreamingLocator** を作成するプロセスは発行と呼ばれます。 既定では、**StreamingLocator** は API 呼び出しを行うとすぐに有効になり、省略可能な開始時刻と終了時刻を構成しない限り、削除されるまで存続します。 

[StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) を作成するときは、使用する **StreamingPolicyName** を指定する必要があります。 この例では、クリアな (暗号化されていない) コンテンツをストリーム配信するので、定義済みのクリア ストリーミング ポリシー **PredefinedStreamingPolicy.ClearStreamingOnly** が使用されます。

> [!IMPORTANT]
> カスタム [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) を使うときは、Media Service アカウントに対してこのようなポリシーの限られたセットを設計し、同じ暗号化オプションとプロトコルが必要なときは常に、お使いの StreamingLocator に対してそのセットを再利用する必要があります。 

Media Service アカウントには、StreamingPolicy エントリの数に対するクォータがあります。 StreamingLocator ごとに新しい StreamingPolicy を作成しないでください。

1. Postman の左側のウィンドウで、[Streaming Policies]\(ストリーミング ポリシー\) を選択します。
2. 次に、[Create a Streaming Locator]\(ストリーミング ロケーターの作成\) を選択します。
3. **[送信]** をクリックします。

    * 次の **PUT** 操作が送信されます。

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingPolicies/:streamingPolicyName?api-version={{api-version}}
        ```
    * 操作の本文は次のとおりです。

        ```json
        {
            "properties":{
            "assetName": "{{assetName}}",
            "streamingPolicyName": "{{streamingPolicyName}}"
            }
        }
        ```

### <a name="list-paths-and-build-streaming-urls"></a>パスを一覧を取得し、ストリーミング URL を構築する

#### <a name="list-paths"></a>パスの一覧を取得する

[StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) が作成されたので、ストリーミング URL を取得できます。

1. Postman の左側のウィンドウで、[Streaming Policies]\(ストリーミング ポリシー\) を選択します。
2. [List Paths]\(パスの一覧表示\) を選択します。
3. **[送信]** をクリックします。

    * 次の **POST** 操作が送信されます。

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName/listPaths?api-version={{api-version}}
        ```
        
    * 操作に本文はありません。
        
4. ストリーミングに使用するパスの 1 つをメモします。次のセクションで使用します。 この例では、次のパスが返されました。
    
    ```
    "streamingPaths": [
        {
            "streamingProtocol": "Hls",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)"
            ]
        },
        {
            "streamingProtocol": "Dash",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=mpd-time-csf)"
            ]
        },
        {
            "streamingProtocol": "SmoothStreaming",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest"
            ]
        }
    ]
    ```

#### <a name="build-the-streaming-urls"></a>ストリーミング URL を構築する

このセクションでは、HLS ストリーミング URL を構築しましょう。 URL は次の値で構成されます。

1. データが送信されるプロトコル。 この例では "https" です。

    > [!NOTE]
    > プレーヤーが HTTPS サイトでホストされている場合は、忘れずに URL を "https" に更新してください。

2. StreamingEndpoint のホスト名。 この例では、名前は "amsaccount-usw22.streaming.media.azure.net" です。

    ホスト名を取得するには、次の GET 操作を使用できます。
    
    ```
    https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingEndpoints/default?api-version={{api-version}}
    ```
    
3. 前のセクション「パスの一覧を取得する」で取得したパス。  

結果として、次の HLS URL が構築されました。

```
https://amsaccount-usw22.streaming.media.azure.net/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>ストリーミング URL をテストする


> [!NOTE]
> ストリーム配信元のストリーミング エンドポイントが実行されていることを確認してください。

ストリーム配信をテストするため、この記事では Azure Media Player を使います。 

1. Web ブラウザーを開いて、[https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) にアクセスします。
2. **[URL:]** ボックスに、構築した URL を貼り付けます。 
3. **[Update Player]\(プレーヤーの更新\)** をクリックします。

Azure Media Player はテストには使用できますが、運用環境では使わないでください。 

## <a name="clean-up-resources-in-your-media-services-account"></a>Media Services アカウント内のリソースをクリーンアップする

一般に、再利用を計画しているオブジェクトを除くすべてのものをクリーンアップする必要があります (通常、Transform は再利用し、StreamingLocator などは保持します)。 実験後にアカウントをクリーンアップする場合は、再利用する予定がないリソースを削除する必要があります。  

リソースを削除するには、削除するリソースの [削除] 操作を選択します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成した Media Services アカウントとストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、前に作成したリソース グループを削除します。 **CloudShell** ツールを使うことができます。

**CloudShell** で、次のコマンドを実行します。

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>次の手順

ビデオをアップロード、エンコード、ストリーム配信する方法がわかったので、次の記事を参照してください。 

> [!div class="nextstepaction"]
> [ビデオを分析する](analyze-videos-tutorial-with-api.md)
