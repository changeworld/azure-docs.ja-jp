---
title: Azure Video Analyzer を Azure Stack Edge にデプロイする
description: この記事では、Azure Video Analyzer を Azure Stack Edge にデプロイするときに役立つ手順を示します。
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: da14368846cd87d5d4e231933cec0068a4e558f9
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546625"
---
# <a name="deploy-azure-video-analyzer-on-azure-stack-edge"></a>Azure Video Analyzer を Azure Stack Edge にデプロイする

この記事では、Video Analyzer を Azure Stack Edge にデプロイするときに役立つ手順を示します。 デバイスの設定とアクティブ化が済むと、Video Analyzer をデプロイできる状態になります。 

Video Analyzer では、IoT Hub を介してデプロイを行います。しかし、Azure Stack Edge リソースについては Kubernetes API が公開されており、これを使用して、Video Analyzer とやり取りできる IoT Hub 非対応ソリューションを追加でデプロイできます。 

> [!TIP]
> カスタム デプロイに Kubernetes (K8s) API を使用するのは、高度なユースケースです。 エッジ モジュールを作成して各 Azure Stack Edge リソースにデプロイする際には IoT Hub を使用するようにし、Kubernetes API は使用しないことをお客様にお勧めします。 この記事では、IoT Hub を使用して Video Analyzer モジュールをデプロイする手順について説明します。

## <a name="prerequisites"></a>前提条件

* Video Analyzer アカウント

    この[クラウド サービス](./overview.md)は、Video Analyzer エッジ モジュールの登録、録画されたビデオの再生、およびビデオ分析に使用されます
* マネージド ID

    これは、上記のストレージ アカウントへのアクセスを管理するために使用されるユーザー割り当ての[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) です。
* [Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) リソース
* [IoT ハブ](../../iot-hub/iot-hub-create-through-portal.md)
* ストレージ アカウント

    General Purpose v2 (GPv2) ストレージ アカウントを使用することをお勧めします。  
    [汎用 v2 ストレージ アカウント](../../storage/common/storage-account-upgrade.md?tabs=azure-portal)についてご確認ください。
* 開発用マシン上の [Visual Studio Code](https://code.visualstudio.com/)。 [Azure IoT Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)があることを確認します。
* 開発用マシンが接続されているネットワークで、ポート 5671 経由の Advanced Message Queuing Protocol が許可されていることを確認します。 このセットアップにより、Azure IoT Tools が Azure IoT Hub と通信できるようになります。

## <a name="configuring-azure-stack-edge-for-using-video-analyzer"></a>Video Analyzer を使用するための Azure Stack Edge の構成

Azure Stack Edge は、サービスとしてのハードウェア ソリューションであり、ネットワーク データ転送機能を備えた AI 対応のエッジ コンピューティング デバイスです。 詳細については、[Azure Stack Edge と詳細な設定手順](../../databox-online/azure-stack-edge-gpu-deploy-prep.md)を参照してください。 最初に、以下のリンクの指示に従います。

* [Azure Stack Edge および Data Box Gateway リソースの作成](../../databox-online/azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)
* [インストールと設定](../../databox-online/azure-stack-edge-gpu-deploy-install.md)
* 接続とアクティブ化

    1. [のインスタンスに接続するときには、](../../databox-online/azure-stack-edge-gpu-deploy-connect.md)
    2. [ネットワークを構成する](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    3. [デバイスを構成する](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
    4. [証明書の構成](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)
    5. [アクティブ化](../../databox-online/azure-stack-edge-gpu-deploy-activate.md)
* [Azure Stack Edge への IoT ハブのアタッチ](../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute)
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Azure Stack Edge ローカル UI でのコンピューティングに関する前提条件の有効化

続行する前に以下を確認します。

* 自分の Azure Stack Edge リソースをアクティブにしていること。
* Azure Stack Edge リソースにアクセスするために、PowerShell 5.0 以降を実行している Windows クライアント システムにアクセスできること。
* Kubernetes クラスターをデプロイするには、[ローカル Web UI](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup) を使用して自分の Azure Stack Edge リソースを構成する必要があります。 

    * 接続と構成:
    
        1. [のインスタンスに接続するときには、](../../databox-online/azure-stack-edge-gpu-deploy-connect.md)
        2. [ネットワークを構成する](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
        3. [デバイスを構成する](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
        4. [証明書の構成](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)
        5. [アクティブ化](../../databox-online/azure-stack-edge-gpu-deploy-activate.md)
    * コンピューティングを有効にするには、自分のデバイスのローカル Web UI で [コンピューティング] ページに移動します。
    
        * コンピューティングを有効にしたいネットワーク インターフェイスを選択します。 [有効化] を選択します。 コンピューティングを有効にすると、そのネットワーク インターフェイスでデバイスの仮想スイッチが作成されます。
        * Kubernetes テスト ノードの IP と Kubernetes 外部サービスの IP は空白のままにします。
        * [適用] を選択します (この操作には約 2 分かかります)。
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="../../databox-online/media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png" alt-text="Azure Stack Edge ローカル UI でのコンピューティングに関する前提条件":::

        * Kubernetes API および Azure Stack Edge リソース用に DNS を更新していない場合は、Windows のホスト ファイルを更新できます。
        
            * 管理者としてテキスト エディターを開きます
            * "C:\Windows\System32\drivers\etc\hosts" のファイルを開きます
            * Kubernetes API デバイス名の IPv4 とホスト名をファイルに追加します。 (この情報は、Azure Stack Edge ポータルの [デバイス] セクションで確認できます。)
            * 保存して閉じる

### <a name="deploy-video-analyzer-edge-modules-using-azure-portal"></a>Azure portal を使用して Video Analyzer Edge モジュールをデプロイする

Azure portal では、配置マニフェストの作成から、IoT Edge デバイスへのデプロイのプッシュまでをガイドします。  
#### <a name="select-your-device-and-set-modules"></a>デバイスを選択してモジュールを設定する

1. [Azure Portal](https://ms.portal.azure.com/) にサインインし、IoT Hub に移動します。
1. メニューから **[IoT Edge]** を選択します。
1. デバイスの一覧でターゲット デバイスの ID をクリックします。
1. **[Set Modules] \(モジュールの設定)** を選択します。

#### <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 Azure portal には、配置マニフェストを作成する手順を示すウィザードがあります。 タブには次の3つの手順が構成されています:**モジュール**、**ルート**、および **レ表示と作成** を行います。

#### <a name="add-modules"></a>モジュールを追加する

1. ページの **[IoT Edge モジュール]** セクションで、 **[追加]** ドロップダウンをクリックし、**IoT Edge モジュール** を選択して、 **[IoT Edge モジュールの追加]** ページを表示します。
1. **モジュールの設定** タブで、モジュール名を入力し、コンテナー イメージ URI を指定します:   
    例 :
    
    * **IoT Edge モジュールの名前**: avaedge
    * **イメージ URI**: mcr.microsoft.com/media/video-analyzer:1    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-module.png" alt-text="[モジュール設定] タブを示しているスクリーンショット":::
    
    > [!TIP]
    > この手順の説明に従って、 **[モジュール設定]** タブ、 **[コンテナ作成オプション]** タブ、および **[モジュールツイン設定]** タブで値を指定するまで、 **[追加]** を選択しないでください。
    
    > [!WARNING]
    > Azure IoT Edge モジュールの呼び出しでは、大文字と小文字が区別されます。 モジュール名として使用する文字列を正確に記録しておきます。

1. **[環境変数]** タブを開きます。
   
   表示される入力ボックスに、次の値を追加します

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/environment-variables.png" alt-text="環境変数":::

1. **[コンテナーの作成オプション]** タブを開きます。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/container-create-options.png" alt-text="コンテナーの作成オプション":::
 
    次の JSON をコピーしてボックスに貼り付け、モジュールによって生成されるログ ファイルのサイズを制限します。
    
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
   1. "/var/lib/videoanalyzer:/var/lib/videoanalyzer": これは、永続的なアプリケーション構成データをコンテナーからバインドし、エッジ デバイスに格納するために使用されます。
   1. "/var/media:/var/media": これにより、エッジ デバイスとコンテナーの間でメディア フォルダーがバインドされます。 これは、エッジ デバイスへのビデオ クリップの格納をサポートする pipelineTopology を実行するときに、ビデオ記録を格納するために使用されます。
   
1. **[モジュール ツインの設定]** タブで、以降の JSON をコピーし、ボックスに貼り付けます。
 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/twin-settings.png" alt-text="ツインの設定":::

    [モジュール ツインの構成スキーマ](module-twin-configuration-schema.md)に関するページで説明されているように、Azure Video Analyzer では、実行するために一連の必須ツイン プロパティが必要です。  

    [モジュール ツインの設定] 編集ボックスには、次の JSON を入力する必要があります。    
    ```
    {
        "applicationDataDirectory": "/var/lib/videoanalyzer",
        "ProvisioningToken": "{provisioning-token}",
    }
    ```
    次に示すのは、JSON に追加してモジュールを監視するのに役立つ、**推奨される** いくつかの追加プロパティです。 詳細については、「[監視とログ記録](monitor-log-edge.md)」を参照してください。
    
    ```
    "diagnosticsEventsOutputName": "diagnostics",
    "OperationalEventsOutputName": "operational",
    "logLevel": "Information",
    "logCategories": "Application,Events",
    "allowUnsecuredEndpoints": true,
    "telemetryOptOut": false
    ```
1. **[追加]** を選択します。  

RTSP シミュレーター エッジ モジュールを追加する

1. ページの **[IoT Edge モジュール]** セクションで、 **[追加]** ドロップダウンをクリックし、**IoT Edge モジュール** を選択して、 **[IoT Edge モジュールの追加]** ページを表示します。
1. **モジュールの設定** タブで、モジュール名を入力し、コンテナー イメージ URI を指定します:   
    例 :
    
    * **IoT Edge モジュールの名前**: rtspsim
    * **イメージ URI**: mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2  


1. **[コンテナーの作成オプション]** タブを開きます。
 
    次の JSON をコピーし、ボックスに貼り付けます。
    
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

1. **Next:ルート** 　でルート のセクションに進みます。 ルートを指定します。

    [名前] に「**AVAToHub**」と入力し、[値] に「**FROM /messages/modules/avaedge/outputs/ INTO $upstream**」と入力します
1. **[次: 確認および作成]** を選択して、レビュー セクションに進みます。
1. デプロイ情報を確認してから、 **[作成]** を選択してモジュールをデプロイします。

    > [!TIP]
    > プロビジョニング トークンを生成するには、次の手順のようにします。
1. Azure portal を開き、Video Analyzer に移動します
1. 左側のナビゲーション ウィンドウで、 **[Edge モジュール]** をクリックします。
1. エッジ モジュールを選択して、 **[トークンの生成]** ボタンをクリックします。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/generate-provisioning-token.png" alt-text="トークンを生成します" lightbox="./media/deploy-on-stack-edge/generate-provisioning-token.png":::
1. プロビジョニング トークンをコピーします。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/copy-provisioning-token.png" alt-text="トークンをコピーします":::



#### <a name="optional-setup-docker-volume-mounts"></a>(省略可能) Docker ボリューム マウントの設定

作業ディレクトリ内のデータを表示したい場合は、これらの手順に従ってデプロイ前に Docker ボリューム マウントを設定します。 

これらの手順では、ゲートウェイ ユーザーの作成とファイル共有の設定を行って、Video Analyzer の作業ディレクトリと Video Analyzer のメディア フォルダーのコンテンツを表示できるようにする方法について説明します。

> [!NOTE]
> バインド マウントもサポートされていますが、ボリューム マウントでは、データを表示できるほか、必要に応じてリモートでコピーできます。 バインドとボリューム、両方のマウントを使用することが可能です。ただし、それらで同じコンテナー パスを指定することはできません。

1. Azure portal を開いて、Azure Stack Edge リソースに移動します。
1. 共有にアクセスできる **ゲートウェイ ユーザー** を作成します。
    
    1. 左側のナビゲーション ペインで、 **[クラウド ストレージ ゲートウェイ]** をクリックします。
    1. 左側のナビゲーション ペインで **[ユーザー]** をクリックします。
    1. **[+ ユーザーの追加]** をクリックし、ユーザー名とパスワードを設定します。 (推奨: `avauser`)。
    1. **[追加]** をクリックします。

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/add-user.png" alt-text="ユーザーの追加":::
1. Video Analyzer で保持するための **ローカル共有** を作成します。

    1. **[クラウド ストレージ ゲートウェイ] -> [共有]** をクリックします。
    1. **[+ Add Shares]\(+ 共有の追加)\** をクリックします。
    1. 共有名を設定します。 (推奨: `ava`)。
    1. 共有の種類は SMB のままにします。
    1. **[Edge コンピューティングで共有を使用する]** チェック ボックスがオンであることを確かめます。
    1. **[Edge ローカル共有として構成]** チェック ボックスがオンであることを確かめます。
    1. [ユーザーの詳細] で、先ほど作成したユーザーに共有へのアクセス権を付与します。
    1. **[作成]** をクリックします。
            
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/local-share.png" alt-text="ローカル共有":::  
    
        > [!TIP]
        > 自分の Azure Stack Edge に接続された Windows クライアントを使用し、[このドキュメントで説明されている](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share)次の手順に従って SMB 共有に接続します。    
1. ファイル同期ストレージ用にリモート共有を作成します。

    1. 最初に、同じリージョンに BLOB ストレージ アカウントを作成します。そのためには、 **[クラウド ストレージ ゲートウェイ] -> [ストレージ アカウント]** をクリックします。
    1. **[クラウド ストレージ ゲートウェイ] -> [共有]** をクリックします。
    1. **[+ Add Shares]\(+ 共有の追加)\** をクリックします。
    1. 共有名を設定します。 (推奨: media)。
    1. 共有の種類は SMB のままにします。
    1. **[Edge コンピューティングで共有を使用する]** チェック ボックスがオンであることを確かめます。
    1. **[Edge ローカル共有として構成]** チェック ボックスがオフであることを確かめます。
    1. 先ほど作成したストレージ アカウントを選択します。
    1. ストレージの種類をブロック BLOB に設定します。
    1. コンテナー名を設定します。
    1. [ユーザーの詳細] で、先ほど作成したユーザーに共有へのアクセス権を付与します。
    1. **[作成]** をクリックします。    
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/remote-share.png" alt-text="リモート共有":::
1. ボリューム マウントを使用するように、RTSP シミュレーター モジュールのコンテナー作成オプションを更新します。
    1. **[モジュールの設定]** ボタンをクリックします。

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/set-modules.png" alt-text="モジュールを設定します" lightbox="./media/deploy-on-stack-edge/set-modules.png":::
    1. **rtspsim** モジュールをクリックします。

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/select-module.png" alt-text="モジュールを選択します":::
    1. **[コンテナーの作成オプション]** タブを選択し、次に示すようにマウントを追加します。
    
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/update-module.png" alt-text="モジュールを更新します":::

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
    1. **[更新]** ボタンをクリックします。
    1. **[確認と作成]** ボタンをクリックし、最後に **[作成]** ボタンをクリックしてモジュールを更新します。
    
### <a name="verify-that-the-module-is-running"></a>モジュールが動作していることの検証

最後の手順では、モジュールが接続され、想定どおりに実行されていることを確認します。 モジュールのランタイムの状態は、IoT Hub リソース内のお客様の IoT Edge デバイスに対して実行中である必要があります。

モジュールが実行中であることを確認するには、以下を実行します。

1. Azure portal で、Azure Stack Edge リソースに戻ります。
1. [モジュール] タイルを選択します。 これにより、[モジュール] ブレードが開きます。 モジュールの一覧で、自分がデプロイしたモジュールを特定します。 自分が追加したモジュールのランタイムの状態は、[実行中] になっているはずです。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/running-module.png" alt-text="カスタム モジュール" lightbox="./media/deploy-on-stack-edge/running-module.png":::

### <a name="configure-the-azure-iot-tools-extension"></a>Azure IoT Tools 拡張機能を構成する

Azure IoT Tools 拡張機能を使用して IoT ハブに接続するには、次の手順に従います。

1. Visual Studio Code で [表示]、[エクスプローラー] の順に選択します。 または、Ctrl + Shift + E キーを押します。
1. [エクスプローラー] タブの左下隅で、 [Azure IoT Hub] を選択します。
1. [その他のオプション] アイコンを選択して、コンテキスト メニューを表示します。 次に、 [Set IoT Hub Connection String]\(IoT Hub 接続文字列を設定する\) を選択します。
1. 入力ボックスが表示されたら、IoT Hub 接続文字列を入力します。 

   * 接続文字列を取得するには、Azure portal で自分の IoT ハブに移動し、左側のナビゲーション ペインで共有アクセス ポリシーをクリックします。
   * iothubowner をクリックし、共有アクセス キーを取得します。
   * [接続文字列] のプライマリ キーをコピーし、それを VS Code の入力ボックスに貼り付けます。

   接続文字列は次のようになります。<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   接続に成功した場合、エッジ デバイスの一覧が表示されます。 自分の Azure Stack Edge が表示されます。 これでコンテキスト メニューから IoT Edge デバイスを管理し、Azure IoT Hub を操作できるようになりました。 エッジ デバイスにデプロイされたモジュールを表示するには、Azure Stack デバイスの [モジュール] ノードを展開します。
    
## <a name="troubleshooting"></a>トラブルシューティング

* **Kubernetes API アクセス (kubectl)**

    * ドキュメントに従って、[Kubernetes クラスターにアクセス](../../databox-online/azure-stack-edge-gpu-create-kubernetes-cluster.md)できるように自分のマシンを構成します。
    * デプロイしたすべての IoT Edge モジュールでは、`iotedge` 名前空間が使用されます。 kubectl の使用時にそれを含めるようにしてください。  
* **モジュール ログ**

    `iotedge` ツールは、ログを取得するためにアクセスすることができません。 ログを表示したり、ファイルにパイプしたりするには、[kubectl logs](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) を使用する必要があります。 例: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`  
* **ポッドとノードのメトリック**

    ポッドとノードのメトリックを確認するには、[kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top) を使用します。
    <br/>`kubectl top pods -n iotedge` 
* **モジュールのネットワーク**   

    Azure Stack Edge でモジュールを検出するには、モジュールに createOptions のホスト ポート バインドがなければなりません。 このモジュールは、`moduleName:hostport` を介してアドレス指定できるようになります。
    
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
* **gRPC を使用する際の共有メモリ**

    Azure Stack Edge リソース上の共有メモリは、ホスト IPC を使用して、任意の名前空間のポッド全体でサポートされます。
    IoT Hub を介してデプロイを行うために、エッジ モジュール上の共有メモリを構成します。
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    //(Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
* **(高度) ポッドのコロケーション**

    K8s を使用し、gRPC 経由で Video Analyzer と通信するカスタム推論ソリューションをデプロイしている場合は、必ず Video Analyzer モジュールと同じノードにポッドがデプロイされている必要があります。

    * **オプション 1** - コロケーションにノード アフィニティと組み込みノード ラベルを使用する。

    ノードにラベルを設定するためのアクセス権をユーザーが持っていないため、現在は NodeSelector カスタム構成を利用できないように思えるかもしれません。 しかし、お客様のトポロジと名前付け規則によっては、[組み込みノード ラベル](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels)を使用できる場合もあります。 Azure Stack Edge リソースと Video Analyzer を参照する nodeAffinity セクションは、コロケーションを実現するために推論ポッド マニフェストに追加できます。
    * **オプション 2** - コロケーションにポッド アフィニティを使用する (推奨)。

        Kubernetes では、同じノードでポッドをスケジュールできる[ポッド アフィニティ](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)がサポートされています。 コロケーションを実現するために、Video Analyzer モジュールを参照する podAffinity セクションを推論ポッド マニフェストに追加できます。

         ```json   
        // Example Video Analyzer module deployment match labels
        selector:
          matchLabels:
            net.azure-devices.edge.deviceid: dev-ase-1-edge
            net.azure-devices.edge.module: mediaedge
        
        // Example Inference deployment manifest pod affinity
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
* **`rtspsim` モジュールの使用中に発生する 404 エラー コード**  
    
    このコンテナーは、そこに含まれる 1 つのフォルダーからのみビデオを読み取ります。 コンテナー イメージ内の既存のフォルダーに外部のフォルダーをマッピング (バインド) した場合、そのコンテナー イメージに存在するファイルが、Docker によって非表示にされます。  
 
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

[モーションの検出とイベントの生成](detect-motion-emit-events-quickstart.md)
