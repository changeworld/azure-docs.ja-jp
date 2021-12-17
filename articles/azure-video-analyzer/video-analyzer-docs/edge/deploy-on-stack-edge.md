---
title: Azure Video Analyzer を Azure Stack Edge にデプロイする
description: この記事では、Azure Video Analyzer を Azure Stack Edge に配置する方法について説明します。
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 023d6500a16d5b808bbb2e249076eae9b53741f8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487625"
---
# <a name="deploy-azure-video-analyzer-on-azure-stack-edge"></a>Azure Video Analyzer を Azure Stack Edge にデプロイする

この記事では、Azure Video Analyzer を Azure Stack Edge デバイスに配置するための詳細な手順について説明します。 デバイスを設定してアクティブ化すると、Video Analyzer を配置する準備が整います。 

この記事では、Azure IoT Hub を使用して Video Analyzer を配置しますが、Azure Stack Edge リソースについては Kubernetes API が公開されており、これを使用して、Video Analyzer とやり取りできる IoT Hub 非対応ソリューションを追加で配置できます。 

> [!TIP]
> カスタムの配置に Kubernetes API を使用するのは、高度なケースです。 Edge モジュールを作成して各 Azure Stack Edge リソースにデプロイする際には IoT Hub を使用するようにし、Kubernetes API は使用しないことをお勧めします。 この記事では、IoT Hub を使用して Video Analyzer モジュールを配置する方法について説明します。

## <a name="prerequisites"></a>前提条件

* Azure Video Analyzer アカウント

    この[クラウド サービス](../overview.md)は、Video Analyzer Edge モジュールの登録、録画されたビデオの再生、およびビデオ分析に使用されます
* マネージド ID

    これは、ストレージ アカウントへのアクセスを管理するために使用するユーザー割り当ての[マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) です。
* [Azure Stack Edge](../../../databox-online/azure-stack-edge-gpu-deploy-prep.md) リソース
* [IoT ハブ](../../../iot-hub/iot-hub-create-through-portal.md)
* ストレージ アカウント

    [汎用 v2 ストレージ アカウント](../../../storage/common/storage-account-upgrade.md?tabs=azure-portal)を使用することをお勧めします。  
* 開発マシンにインストールされている [Visual Studio Code](https://code.visualstudio.com/)
*  Visual Studio Code にインストールされている [Azure IoT Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
* 開発用マシンが接続されているネットワークで、ポート 5671 経由の Advanced Message Queuing Protocol が許可されていることを確認します。 このセットアップにより、Azure IoT Tools が Azure IoT ハブと通信できるようになります。

## <a name="configure-azure-stack-edge-to-use-video-analyzer"></a>Video Analyzer を使用するように Azure Stack Edge を構成する

Azure Stack Edge は、サービスとしてのハードウェア ソリューションであり、ネットワーク データ転送機能を備えた AI 対応の Edge コンピューティング デバイスです。 詳細については、[Azure Stack Edge と詳細な設定手順](../../../databox-online/azure-stack-edge-gpu-deploy-prep.md)に関する記事を参照してください。 

使用を開始するには、以下を実行します。

1. [Azure Stack Edge または Azure Data Box Gateway リソースを作成します](../../../databox-online/azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)。  
1. [Azure Stack Edge Pro with GPU をインストールして設定します](../../../databox-online/azure-stack-edge-gpu-deploy-install.md)。  
1. 次の手順でリソースを接続し、アクティブ化します。

    a. [ローカル Web UI 設定に接続します](../../../databox-online/azure-stack-edge-gpu-deploy-connect.md)。  
    b. [ネットワークを構成します](../../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。  
    c. [デバイスを構成します](../../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)。  
    d. [証明書を構成します](../../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)。  
    e. [デバイスをアクティブ化します](../../../databox-online/azure-stack-edge-gpu-deploy-activate.md)。  

1. [IoT ハブを Azure Stack Edge にアタッチします](../../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute)。

### <a name="meet-the-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Azure Stack Edge ローカル UI でコンピューティングに関する前提条件を満たす

次に進む前に、以下を完了していることを確認します。

* 自分の Azure Stack Edge リソースをアクティブにしていること。
* Azure Stack Edge リソースにアクセスするために、PowerShell 5.0 以降を実行している Windows クライアント システムにアクセスできること。
* Kubernetes クラスターを配置する場合は、Azure Stack Edge リソースをその [ローカル Web UI](../../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup) 上で構成済みであること。 

    1. リソースを Connect して構成するには、次の手順を実行します。a. [ローカル Web UI 設定に接続します](../../../databox-online/azure-stack-edge-gpu-deploy-connect.md)。  
        b. [ネットワークを構成します](../../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。  
        c. [デバイスを構成する](../../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)  
        d. [証明書を構成します](../../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)。  
        e. [デバイスをアクティブ化します](../../../databox-online/azure-stack-edge-gpu-deploy-activate.md)。

    1. コンピューティングを有効にするには、自分のデバイスのローカル Web UI で **[コンピューティング]** ページに移動します。
    
        a. コンピューティングに対して有効にするネットワーク インターフェイスを選択して、**[有効]** を選択します。 コンピューティングを有効にすると、そのネットワーク インターフェイス上のデバイスに仮想スイッチが作成されます。  
        b. Kubernetes テスト ノードの IP と Kubernetes 外部サービスの IP は空白のままにします。  
        c. **[適用]** を選択します。 この操作には約 2 分かかります。
        
        > [!div class="mx-imgBorder"]

        > :::image type="content" source="../../../databox-online/media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png" alt-text="Azure Stack Edge ローカル UI でのコンピューティングの前提条件のスクリーンショット。":::

        Azure DNS が Kubernetes API と Azure Stack Edge リソース用に構成されていない場合は、次の手順で Windows ホスト ファイルを更新できます。
        
        a. 管理者としてテキスト エディターを開きます。  
        b. *C:\Windows\System32\drivers\etc\\* にある *hosts* ファイルを開きます。  
        c. このファイルに Kubernetes API デバイス名の インターネット プロトコル バージョン 4 (IPv4) とホスト名を追加します。 この情報は、Azure Stack Edge ポータルの **[デバイス]** で確認できます。  
        d. ファイルを保存して閉じます。

### <a name="deploy-video-analyzer-edge-modules-by-using-the-azure-portal"></a>Azure portal を使用して Video Analyzer Edge モジュールを配置する

Azure portal で配置マニフェストを作成し、IoT Edge デバイスに配置をプッシュすることができます。  

#### <a name="select-your-device-and-set-modules"></a>デバイスを選択してモジュールを設定する

1. [Azure portal](https://ms.portal.azure.com/) にサインインし、お使いの IoT ハブに移動します。
1. 左側のペインで、 **[IoT Edge]** を選択します。
1. デバイスの一覧で、ターゲット デバイスの ID を選択します。
1. **[Set Modules] \(モジュールの設定)** を選択します。

#### <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、配置するモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 Azure portal には、配置マニフェストを作成する手順を示すウィザードがあります。 その 3 つの手順は、**[モジュール]**、**[ルート]**、**[確認と作成]** の各タブで構成されています。

#### <a name="add-modules"></a>モジュールを追加する

1. **[IoT Edge モジュール]** セクションで、**[追加]** ドロップダウン リストの **[IoT Edge モジュール]** を選択し、**[IoT Edge モジュールを追加する]** ページを表示します。
1. **[モジュールの設定]** タブを選択し、モジュール名を入力して、コンテナー イメージ URI を指定します。 次の画像に示されているのはサンプルの値です。     
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-module.png" alt-text="[IoT Edge モジュールを追加する] ページの [モジュールの設定] ペインのスクリーンショット。":::
    
    > [!TIP]
    > この手順の説明に従って、**[モジュール設定]** タブ、**[コンテナ作成オプション]** タブ、**[モジュールツイン設定]** タブで値を指定するまで、**[追加]** を選択しないでください。
    
    > [!IMPORTANT]
    > モジュールの呼び出し時に、Azure IoT Edge 値は大文字と小文字が区別されます。 モジュール名として使用する文字列を正確にメモしておきます。
1. **[環境変数]** タブを選択し、次の画像のように値を入力します。
   
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/environment-variables.png" alt-text="[IoT Edge モジュールを追加する] ページの [環境変数] ペインのスクリーンショット。":::
1. **[コンテナーの作成オプション]** タブを選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/container-create-options.png" alt-text="[IoT Edge モジュールを追加する] ページの [コンテナーの作成オプション] ペインのスクリーンショット。":::
 
    **[コンテナーの作成オプション]** ペインのボックスに、次の JSON コードを貼り付けます。 この操作により、モジュールから生成されるログ ファイルのサイズが制限されます。
    
    ```    
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
               "/var/lib/videoanalyzer/:/var/lib/videoanalyzer",
               "/var/media:/var/media"
            ],
            "IpcMode": "host",
            "ShmSize": 1536870912
        }
    }
    ````
   
   JSON の "Binds" セクションには、次の 2 つのエントリがあります。
   * **"/var/lib/videoanalyzer:/var/lib/videoanalyzer"** は、永続的なアプリケーション構成データをコンテナーからバインドし、エッジ デバイスに格納するために使用されます。
   * **"/var/media:/var/media"** により、エッジ デバイスとコンテナーの間でメディア フォルダーがバインドされます。 これは、エッジ デバイスへのビデオ クリップの格納をサポートする pipelineTopology を実行するときに、ビデオ記録を格納するために使用されます。
   
1. **[モジュール ツイン設定]** タブを選択します。
 
   実行するには、「[モジュール ツイン構成スキーマ](module-twin-configuration-schema.md)」に記載されているように、Video Analyzer edge モジュールに一連の必須ツイン プロパティが必要です。 
1. **[モジュール ツインの設定]** ペインのボックスに、次の JSON コードを貼り付けます。    
    ```
    {
        "applicationDataDirectory": "/var/lib/videoanalyzer",
        "ProvisioningToken": "{provisioning-token}",
        ...
    }
    ```
   
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/twin-settings.png" alt-text="[IoT Edge モジュールを追加する] ページの [モジュール ツインの設定] ペインのスクリーンショット。":::   

    モジュールの監視を支援するために、次の "*推奨される*" プロパティを JSON コードに追加することができます。 詳細については、「[監視とログ記録](monitor-log-edge.md)」を参照してください。
    
    ```
    "diagnosticsEventsOutputName": "diagnostics",
    "OperationalEventsOutputName": "operational",
    "logLevel": "Information",
    "logCategories": "Application,Events",
    "allowUnsecuredEndpoints": true,
    "telemetryOptOut": false
    ```
1. **[追加]** を選択します。  

#### <a name="add-the-real-time-streaming-protocol-rtsp-simulator-edge-module"></a>リアルタイム ストリーミング プロトコル (RTSP) シミュレーター Edge モジュールを追加する

1. **[IoT Edge モジュール]** セクションで、**[追加]** ドロップダウン リストの **[IoT Edge モジュール]** を選択し、**[IoT Edge モジュールを追加する]** ページを表示します。
1. **[モジュールの設定]** タブを選択し、モジュール名を入力して、コンテナー イメージ URI を指定します。 次に例を示します。   
    
    * **IoT Edge モジュールの名前**: rtspsim  
    * **イメージ URI**: mcr.microsoft.com/ava-utilities/rtspsim-live555:1.2 

1. **[コンテナーの作成オプション]** タブを選択し、ボックス内に次の JSON コードを貼り付けます。
    
    ```
    {
        "HostConfig": {
            "Binds": [
               "/home/localedgeuser/samples/input/:/live/mediaServer/media/"
            ],
            "PortBindings": {
                    "554/tcp": [
                        {
                        "HostPort": "554"
                        }
                    ]
            }
        }
    }
    ```
1. **[追加]** を選択します。  
1. **次へ:ルート** 　でルート のセクションに進みます。 
1. ルートを指定するには、**[名前]** に「**AVAToHub**」と入力し、**[値]** に「**FROM /messages/modules/avaedge/outputs/ INTO $upstream**」と入力します。
1. **[次へ: レビューと作成]** を選択し、レビュー セクションに進みます。
1. 配置情報を確認してから、**[作成]** を選択してモジュールを配置します。

#### <a name="generate-the-provisioning-token"></a>プロビジョニング トークンを生成する

1. Azure portal で Video Analyzer に移動します。
1. 左側のペインで **[Edge modules]\(Edge モジュール\)** を選択します。
1. Edge モジュールを選択し、**[トークンの生成]** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/generate-provisioning-token.png" alt-text="トークンを生成するための [Add edge modules]\(Edge モジュールの追加\) ペインのスクリーンショット。" lightbox="./media/deploy-on-stack-edge/generate-provisioning-token.png":::
1. 次の画像に示すように、プロビジョニング トークンをコピーします。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/copy-provisioning-token.png" alt-text="[Copy provisioning token]\(プロビジョニング トークンのコピー\) ページのスクリーンショット。":::



#### <a name="optional-set-up-docker-volume-mounts"></a>(省略可能) Docker ボリューム マウントを設定する

作業ディレクトリのデータを表示する場合は、配置する前に Docker ボリューム マウントを設定します。 

このセクションでは、ゲートウェイ ユーザーの作成とファイル共有の設定を行って、Video Analyzer の作業ディレクトリと Video Analyzer のメディア フォルダーのコンテンツを表示できるようにする方法について説明します。

> [!NOTE]
> バインド マウントもサポートされていますが、ボリューム マウントを使用すると、データを表示できるほか、選択した場合はリモートでコピーできます。 バインドとボリューム、両方のマウントを使用することはできますが、同じコンテナー パスを指定することはできません。

1. Azure portal で Azure Stack Edge リソースに移動します。
1. 次の手順で共有にアクセスできるゲートウェイ ユーザーを作成します。
    
    a. 左側のペインで **[Cloud storage gateway]\(クラウド ストレージ ゲートウェイ\)** を選択します。  
    b. 左側のペインで **[ユーザー]** を選択します。  
    c. **[ユーザーの追加]** を選択し、ユーザー名 (たとえば、「*avauser*」をお勧めします) とパスワードを設定します。  
    d. **[追加]** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-user.png" alt-text="Azure Stack Edge リソースの [ユーザーの追加] ページのスクリーンショット。":::
1. 次の手順で Video Analyzer の永続化のための "*ローカル共有*" を作成します。

    a. **[Cloud storage gateway]\(クラウド ストレージ ゲートウェイ\)** > **[共有]** を選択します。  
    b. **[共有の追加]** を選択します。  
    c. 共有名を設定します (たとえば、「*ava*」をお勧めします)。  
    d. 共有の種類は **[SMB]** のままにします。  
    e. **[Edge コンピューティングで共有を使用する]** チェックボックスがオンであることを確認します。  
    f. **[Edge ローカル共有として構成]** チェックボックスがオンであることを確認します。  
    g. **[ユーザーの詳細]** で **[既存のものを使用]** を選択し、最近作成したユーザーに共有へのアクセス権を付与します。  
    h. **［作成］** を選択します
            
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/local-share.png" alt-text="ローカル共有を作成するための [共有の追加] ページのスクリーンショット。":::  
    
    > [!TIP]
    > Windows クライアントが Azure Stack Edge デバイスに接続されている状態で、「[Azure Stack Edge Pro FPGA を使用してデータを転送する](../../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share)」の「SMB 共有に接続する」セクションの手順を実行します。    
1. 次の手順でファイル同期ストレージの "*リモート共有*" を作成します。

    a. **[Cloud storage gateway]\(クラウド ストレージ ゲートウェイ\)** > **[ストレージ アカウント]** を選択して、同じリージョン内に Azure Blob Storage アカウントを作成します。  
    b. **[Cloud storage gateway]\(クラウド ストレージ ゲートウェイ\)** > **[共有]** を選択します。  
    c. **[共有の追加]** を選択します。  
    d. **[名前]** ボックスに共有名を入力します (たとえば、「*media*」をお勧めします)。  
    e. **[種類]** の共有の種類は **[SMB]** のままにします。  
    f. **[Edge コンピューティングで共有を使用する]** チェックボックスがオンであることを確認します。  
    g. **[Edge ローカル共有として構成]** チェックボックスがオフであることを確認します。  
    h. **[ストレージ アカウント]** ドロップダウン リストで最近作成したストレージ アカウントを選択します。  
    i. **[ストレージ サービス]** ドロップダウン リストで **[ブロック BLOB]** を選択します。  
    j. **[BLOB コンテナーの選択**] ボックスにコンテナー名を入力します。  
    k. **[ユーザーの詳細]** で **[既存のものを使用]** を選択し、最近作成したユーザーに共有へのアクセス権を付与します。  
    l. **［作成］** を選択します    
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/remote-share.png" alt-text="リモート共有を作成するための [共有の追加] ページのスクリーンショット。":::

1. ボリューム マウントを使用するには、次の手順で RTSP シミュレーター モジュールの **[コンテナーの作成オプション]** ペインの設定を更新します。

    a. **[モジュールの設定]** ボタンを選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/set-modules.png" alt-text="エッジ デバイスの設定ペインの [モジュールの設定] ボタンを示すスクリーンショット。" lightbox="./media/deploy-on-stack-edge/set-modules.png":::  

    b. **[名前]** リストで **rtspsim** モジュールを選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/select-module.png" alt-text="エッジ デバイスの設定ペインの [IoT Edge モジュール] にある 'rtspsim' モジュールのスクリーンショット。":::  
    
    c. **[IoT Edge モジュールの更新]** ペインで **[コンテナーの作成オプション]** タブを選択し、次の JSON コードに示すようにマウントを追加します。
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/update-module.png" alt-text="[コンテナーの作成オプション] ペインの JSON マウント コードのスクリーンショット。":::

    ```json
        "createOptions": 
        {
            "HostConfig": 
            {
                "Mounts": 
                [
                    {
                        "Target": "/live/mediaServer/media",
                        "Source": "media",
                        "Type": "volume"
                    }
                ],
                "PortBindings": {
                    "554/tcp": [
                        {
                        "HostPort": "554"
                        }
                    ]
                }
            }
        }
    ```  
    d. **[更新]** を選択します。  
    e. モジュールを更新するには、**[確認と作成]** を選択し、**[作成]** を選択します。
    
### <a name="verify-that-the-module-is-running"></a>モジュールが動作していることの検証

最後に、期待したとおりに IoT Edge デバイス モジュールが接続され、動作していることを確認します。 モジュールのランタイムの状態を確認するには、次の手順を実行します。

1. Azure portal で、Azure Stack Edge リソースに戻ります。
1. 左側のペインで **[モジュール]** を選択します。 
1. **[モジュール]** ペインの **[名前]** リストで配置したモジュールを選択します。 **[ランタイムの状態]** 列のモジュールの状態は "*実行中*" になっているはずです。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/running-module.png" alt-text="[モジュール] ペインのスクリーンショット。選択したモジュールのランタイムの状態が &quot;実行中&quot; と表示されています。" lightbox="./media/deploy-on-stack-edge/running-module.png":::

### <a name="configure-the-azure-iot-tools-extension"></a>Azure IoT Tools 拡張機能を構成する

Azure IoT Tools 拡張機能を使用して IoT ハブに接続するには、次の手順を実行します。

1. Visual Studio Code で、 **[表示]**  >  **[エクスプローラー]** を選択します。
1. **[エクスプローラー]** ペインの左下にある **[Azure IoT Hub]** を選択します。
1. **[その他のオプション]** アイコンを選択してコンテキスト メニューを表示し、**[Set IoT Hub Connection String]\(IoT Hub の接続文字列の設定\)** を選択します。

   入力ボックスが表示されたら、IoT Hub の接続文字列を入力します。 接続文字列を取得するには、次の手順を実行します。 

   a. Azure portal で IoT Hub に移動します。  
   b. 左側のペインで、 **[共有アクセス ポリシー]** を選択します。  
   c. **iothubowner を選択し、共有アクセス キーを取得します**。  
   d. 接続文字列のプライマリ キーをコピーし、それを入力ボックスに貼り付けます。

   > [!NOTE]
   > 接続文字列は次の形式で出力されます。
   >
   > `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   接続が成功すると、Azure Stack Edge デバイスを含むエッジ デバイスの一覧が表示されます。 これでコンテキスト メニューから IoT Edge デバイスを管理し、Azure IoT ハブを操作できるようになりました。 
   
   エッジ デバイスに配置されたモジュールを表示するには、Azure Stack デバイスの **[モジュール]** ノードを展開します。
    
## <a name="troubleshooting"></a>トラブルシューティング

* **Kubernetes API アクセス (kubectl)**

    * [Azure Stack Edge Pro GPU デバイス上での Kubernetes クラスターの接続と管理](../../../databox-online/azure-stack-edge-gpu-create-kubernetes-cluster.md)に関する記事の手順を実行して、Kubernetes クラスターにアクセスできるようにマシンを構成します。
    * 配置されたすべての IoT Edge モジュールには *iotedge* 名前空間が使用されます。 kubectl を使用するときには、必ずその名前を含めてください。 
* **モジュール ログ**

    *iotedge* ツールでログを取得できない場合は、[kubectl logs](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) を使用してログを表示するか、ファイルにパイプします。 次に例を示します。 <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`  
* **ポッドとノードのメトリック**

    ポッドとノードのメトリックを表示するには、[kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top) を使用します。 例:   <br/>`kubectl top pods -n iotedge` 
* **モジュールのネットワーク**   

    Azure Stack Edge 上のモジュールを検出するには、モジュールに createOptions のホスト ポート バインドを設定する必要があります。 このモジュールは、`moduleName:hostport` を介してアドレス指定できるようになります。
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```    
* **ボリュームのマウント**

    空でない既存のディレクトリに対してコンテナーがボリュームのマウントを試行している場合、モジュールの起動は失敗します。
* **gRPC を使用する場合の共有メモリ**

    ホスト IPC を使用する場合、Azure Stack Edge リソース上の共有メモリは任意の名前空間のポッド全体でサポートされます。
    
    IoT Hub 経由で配置する Edge モジュールに共有メモリを構成するには、次のコードを使用します。
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    //(Advanced) Configuring shared memory on a Kubernetes pod or deployment manifest for deployment via the Kubernetes API spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
* **(高度) ポッドのコロケーション**

    Kubernetes を使用し、gRPC 経由で Video Analyzer と通信するカスタム推論ソリューションを配置している場合は、必ず Video Analyzer モジュールと同じノードにポッドを配置します。

    * **オプション 1** - コロケーションに "*ノード アフィニティ*" と組み込みノード ラベルを使用する。  

    ノードにラベルを設定するためのアクセス権をユーザーが持っていないため、現在は NodeSelector カスタム構成を利用できないように思えるかもしれません。 しかし、ユーザーのトポロジと名前付け規則によっては、[組み込みノード ラベル](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels)を使用できる場合もあります。 コロケーションを実現するには、Video Analyzer を使用して Azure Stack Edge リソースを参照する nodeAffinity セクションを推論ポッド マニフェストに追加します。    
    * **オプション 2** - (推奨) コロケーションに "*ポッド アフィニティ*" を使用する。  

        Kubernetes は、同じノードでポッドをスケジュールできる[ポッド アフィニティ](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)をサポートしています。 コロケーションを実現するには、Video Analyzer モジュールを参照する podAffinity セクションを推論ポッド マニフェストに追加します。

        ```yaml
        // Example Video Analyzer module deployment match labels
        selector:
          matchLabels:
            net.azure-devices.edge.deviceid: dev-ase-1-edge
            net.azure-devices.edge.module: mediaedge
        
        // Example inference deployment manifest pod affinity
        spec:
          affinity:
            podAntiAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
              - labelSelector:
                  matchExpressions:
                  - key: net.azure-devices.edge.module
                    operator: In
                    values:
                    - mediaedge
                topologyKey: "kubernetes.io/hostname"
        ```
* ***rtspsim* モジュールを使用すると 404 エラー コードを受け取る**  
    
    このコンテナーにより、コンテナー内の 1 つのフォルダーからのみビデオが読み取られます。 コンテナー イメージ内の既存のフォルダーに外部のフォルダーをマップまたはバインドした場合、そのコンテナー イメージに存在するファイルが、Docker によって非表示にされます。
 
    たとえば、バインディングがない状態で、コンテナーに次のファイルが存在するとします。  

    ```
    root@rtspsim# ls /live/mediaServer/media  
    /live/mediaServer/media/camera-300s.mkv  
    /live/mediaServer/media/win10.mkv  
    ```
     
    また、ホストには次のファイルが存在するとします。

    ```    
    C:\MyTestVideos> dir
    Test1.mkv
    Test2.mkv
    ```
     
    ところが、次のバインディングを配置マニフェスト ファイルに追加すると、/live/mediaServer/media の内容が、ホスト側と一致するように Docker によって上書きされます。

    `C:\MyTestVideos:/live/mediaServer/media`
    
    ```
    root@rtspsim# ls /live/mediaServer/media
    /live/mediaServer/media/Test1.mkv
    /live/mediaServer/media/Test2.mkv
    ```

## <a name="next-steps"></a>次のステップ

[Azure Stack Edge を使用した](computer-vision-for-spatial-analysis.md) Computer Vision と空間分析によるビデオの分析
