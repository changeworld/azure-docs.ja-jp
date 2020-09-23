---
title: Live Video Analytics を Azure Stack Edge にデプロイする
description: この記事では、Live Video Analytics を Azure Stack Edge にデプロイするときに役立つ手順を示します。
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: b13bb779a5a780b21f2d5d96ed8831ef5c26564d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90930944"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>Live Video Analytics を Azure Stack Edge にデプロイする

この記事では、Live Video Analytics を Azure Stack Edge にデプロイするときに役立つ手順を示します。 設定とアクティブ化が済んだら、デバイスで Live Video Analytics をデプロイする準備が整います。 

Live Video Analytics では、IoT Hub を介してデプロイを行います。しかし、Azure Stack Edge リソースについては Kubernetes API が公開されており、お客様はこれを使用して、Live Video Analytics とやり取りできる IoT Hub 非対応ソリューションを追加でデプロイできます。 

> [!TIP]
> カスタム デプロイに Kubernetes (K8s) API を使用するのは、高度なユースケースです。 エッジ モジュールを作成して各 Azure Stack Edge リソースにデプロイする際には IoT Hub を使用するようにし、Kubernetes API は使用しないことをお客様にお勧めします。 この記事では、IoT Hub を使用して Live Video Analytics モジュールをデプロイする手順について説明します。

## <a name="prerequisites"></a>前提条件

* [所有者特権](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)がある Azure サブスクリプション。
* [Azure Stack Edge](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep) リソース
   
* [IoT ハブ](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)
* Live Video Analytics モジュール用の[サービス プリンシパル](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/create-custom-azure-resource-manager-role-how-to#create-service-principal)。

   IoT Hub が利用可能なこれらのリージョンのうち、いずれかを使用します。米国東部 2、米国中部、米国中北部、東日本、米国西部 2、米国中西部、カナダ東部、英国南部、フランス中部、フランス南部、スイス北部、スイス西部、西日本。
* ストレージ アカウント

    General Purpose v2 (GPv2) ストレージ アカウントを使用することをお勧めします。  
    [汎用 v2 ストレージ アカウント](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade?tabs=azure-portal)についてご確認ください。
* 開発用マシン上の [Visual Studio Code](https://code.visualstudio.com/)。 [Azure IoT Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)があることを確認します。
* 開発用マシンが接続されているネットワークで、ポート 5671 経由の Advanced Message Queuing Protocol が許可されていることを確認します。 このセットアップにより、Azure IoT Tools が Azure IoT Hub と通信できるようになります。

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>Live Video Analytics を使用するための Azure Stack Edge の構成

Azure Stack Edge は、サービスとしてのハードウェア ソリューションであり、ネットワーク データ転送機能を備えた AI 対応のエッジ コンピューティング デバイスです。 詳細については、[Azure Stack Edge と詳細な設定手順](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep)を参照してください。 最初に、以下のリンクの指示に従います。

* [Azure Stack Edge および Data Box Gateway リソースの作成](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep)
* [インストールと設定](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-install)
* [接続とアクティブ化](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate)

### <a name="attach-an-iot-hub-to-azure-stack-edge"></a>Azure Stack Edge への IoT ハブのアタッチ

1. [Azure portal](https://ms.portal.azure.com) で、自分の Azure Stack Edge リソースに移動して [概要] をクリックします。 右側のウィンドウの [コンピューティング] タイルで [開始] を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge.png" alt-text="Azure Stack Edge":::
1. [Edge コンピューティングの構成] タイルで、 [コンピューティングの構成] を選択します。
1. [Edge コンピューティングの構成] ブレードで、次を入力します。
    
    | フィールド|値|
    |---|---|
    |IoT Hub|[新規] または [既存] を選択します。<br/>既定では、IoT リソースの作成には Standard レベル (S1) が使用されます。 Free レベルの IoT リソースを使用するには、それを作成してから既存のリソースを選択します。<br/>いずれの場合も、IoT Hub リソースでは、Azure Stack Edge リソースによって使用されるのと同じサブスクリプションとリソース グループが使用されます。|
    |名前|自分の IoT Hub リソースの名前を入力します。|

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-get-started.png" alt-text="Azure Stack Edge の開始":::
1. **［作成］** を選択します IoT Hub リソースの作成には数分かかります。 IoT Hub リソースが作成された後、**[コンピューティングの構成]** タイルが更新され、コンピューティングの構成が表示されます。 Edge コンピューティング ロールが構成されたことを確認するには、**[コンピューティングの構成]** タイルの **[View Compute]\(コンピューティングの表示\)** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/edge-compute-config.png" alt-text="IoT Hub リソースの作成":::

    > [!NOTE]
    > Azure Stack Edge リソースに IoT ハブが関連付けられる前に [コンピューティングの構成] ダイアログを閉じた場合、IoT ハブは作成されますが、コンピューティングの構成には表示されません。 数分後にページを再読み込みすると、それが表示されるのがわかります。
    
    Edge デバイスで Edge コンピューティング ロールが設定されると、2 つのデバイスが作成されます (IoT デバイスと IoT Edge デバイス)。 IoT Hub リソースでは、両方のデバイスを表示できます。 IoT Edge ランタイムは、IoT Edge デバイス上でも動作しています。 現時点では、お客様の IoT Edge デバイスに対して使用できるのは Linux プラットフォームのみです。
    
    情報をすべて入力すると、次のような [Edge コンピューティングの構成] カードが表示されます。
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/configure-edge-compute.png" alt-text="[Edge コンピューティングの構成] カード":::
 
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Azure Stack Edge ローカル UI でのコンピューティングに関する前提条件の有効化

続行する前に以下を確認します。

* 自分の Azure Stack Edge リソースをアクティブにしていること。
* Azure Stack Edge リソースにアクセスするために、PowerShell 5.0 以降を実行している Windows クライアント システムにアクセスできること。
* Kubernetes クラスターをデプロイするには、[ローカル Web UI](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate#connect-to-the-local-web-ui-setup) を使用して自分の Azure Stack Edge リソースを構成する必要があります。 
    
    * コンピューティングを有効にするには、自分のデバイスのローカル Web UI で [コンピューティング] ページに移動します。
    
        * コンピューティングを有効にしたいネットワーク インターフェイスを選択します。 [有効化] を選択します。 コンピューティングを有効にすると、そのネットワーク インターフェイスでデバイスの仮想スイッチが作成されます。
        * Kubernetes テスト ノードの IP と Kubernetes 外部サービスの IP は空白のままにします。
        * [適用] を選択します (この操作には約 2 分かかります)。
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text="Azure Stack Edge ローカル UI でのコンピューティングに関する前提条件":::

        * Kubernetes API および Azure Stack Edge リソース用に DNS を更新していない場合は、Windows のホスト ファイルを更新できます。
        
            * 管理者としてテキスト エディターを開きます
            * "C:\Windows\System32\drivers\etc\hosts" のファイルを開きます
            * Kubernetes API デバイス名の IPv4 とホスト名をファイルに追加します。 (この情報は、Azure Stack Edge ポータルの [デバイス] セクションで確認できます。)
            * 保存して閉じる

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>Azure portal を使用した Live Video Analytics Edge モジュールのデプロイ

ここでは、[IoT Hub を使用した Live Video Analytics のデプロイ](deploy-iot-edge-device.md)に関するページの特定の手順だけを実行します。

1. ユーザーとグループの作成手順はスキップして、「[Live Video Analytics Edge モジュールをデプロイする](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module)」に移動します。 そこに記載されている手順に従ってください。
1. [コンテナーの作成オプション] では、環境変数を設定する必要はありません。 そのため、この手順はスキップします。
1. [コンテナーの作成オプション] タブを開きます。

   * 次の JSON をコピーしてボックスに貼り付け、モジュールによって生成されるログ ファイルのサイズを制限します。
    
      ```json
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
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > 共有メモリの転送に gRPC プロトコルを使用する場合は、Live Video Analytics と推論ソリューションの間の共有メモリ アクセスに、ホスト IPC モードを使用します。
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > JSON の "Binds" セクションには、2 つのエントリがあります。 エッジ デバイスのバインドは自由に更新してかまいませんが、これらのディレクトリは必ず用意してください。
    
    * "/var/lib/azuremediaservices:/var/lib/azuremediaservices": これは、永続的なアプリケーション構成データをコンテナーからバインドし、エッジ デバイスに格納するために使用されます。
    * "/var/media:/var/media": これにより、エッジ デバイスとコンテナーの間でメディア フォルダーがバインドされます。 これは、エッジ デバイスへのビデオ クリップの格納をサポートするメディア グラフ トポロジを実行するときに、ビデオ記録を格納するために使用されます。
        
1. ドキュメントの手順を続行して、[モジュール ツインの設定] に入力を行います。
1. **[次へ]** を選択します。[ルート] でルートのセクションに進みます。 ルートを指定します。

    既定のルートをそのまま保持して、[次へ] を選択します:[確認と作成] で、[レビュー] セクションに進みます。
1. [デプロイの確認と検証](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a>(省略可能) Docker ボリューム マウントの設定

作業ディレクトリ内のデータを表示したい場合は、これらの手順に従ってデプロイ前に Docker ボリューム マウントを設定します。 

これらの手順では、ゲートウェイ ユーザーの作成とファイル共有の設定を行って、Live Video Analytics の作業ディレクトリと Live Video Analytics のメディア フォルダーのコンテンツを表示できるようにする方法について説明します。

> [!NOTE]
> バインド マウントもサポートされていますが、ボリューム マウントでは、データを表示できるほか、必要に応じてリモートでコピーできます。 バインドとボリューム、両方のマウントを使用することが可能です。ただし、それらで同じコンテナー パスを指定することはできません。

1. Azure portal を開いて、Azure Stack Edge リソースに移動します。
1. 共有にアクセスできる**ゲートウェイ ユーザー**を作成します。
    
    1. 左側のナビゲーション ペインで、 **[ゲートウェイ]、[ユーザー]** の順にクリックします。
    1. **[+ ユーザーの追加]** をクリックし、ユーザー名とパスワードを設定します。 (推奨: `lvauser`)。
    1. **[追加]** をクリックします。
    
1. Live Video Analytics で保持するための**ローカル共有**を作成します。

    1. **[ゲートウェイ]、[共有]** の順にクリックします。
    1. **[+ Add Shares]\(+ 共有の追加)\** をクリックします。
    1. 共有名を設定します。 (推奨: `lva`)。
    1. 共有の種類は SMB のままにします。
    1. **[Edge コンピューティングで共有を使用する]** チェック ボックスがオンであることを確かめます。
    1. **[Edge ローカル共有として構成]** チェック ボックスがオンであることを確かめます。
    1. [ユーザーの詳細] で、先ほど作成したユーザーに共有へのアクセス権を付与します。
    1. **[作成]** をクリックします。
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="ローカル共有":::
    
1. ファイル同期ストレージ用にリモート共有を作成します。

    1. まず、同じリージョンに BLOB ストレージ アカウントを作成します。
    1. **[ゲートウェイ]、[共有]** の順にクリックします。
    1. **[+ Add Shares]\(+ 共有の追加)\** をクリックします。
    1. 共有名を設定します。 (推奨: media)。
    1. 共有の種類は SMB のままにします。
    1. **[Edge コンピューティングで共有を使用する]** チェック ボックスがオンであることを確かめます。
    1. **[Edge ローカル共有として構成]** チェック ボックスがオフであることを確かめます。
    1. 先ほど作成したストレージ アカウントを選択します。
    1. コンテナー名を設定します。
    1. ストレージの種類をブロック BLOB に設定します。
    1. [ユーザーの詳細] で、先ほど作成したユーザーに共有へのアクセス権を付与します。
    1. **[作成]** をクリックします。    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="リモート共有":::
    
    > [!TIP]
    > 自分の Azure Stack Edge に接続された Windows クライアントを使用し、[このドキュメントで説明されている](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-add-shares#connect-to-an-smb-share)次の手順に従って SMB 共有に接続します。
    
1. ボリューム マウントを使用するよう、Live Video Analytics Edge モジュールの [コンテナーの作成オプション] を更新します (「[モジュールを追加する](deploy-iot-edge-device.md#add-modules)」のポイント 4 を参照してください)。

   ```json
    // Original (Bind Mounts)
    "createOptions": {
        "HostConfig": {
            "Binds": [
                "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
                "/var/media:/var/media"
            ]
        }
    }
    // Updated (Volume Mounts)
    "createOptions": {
        "HostConfig": {
            "Mounts": [
            {
                "Target": "/var/lib/azuremediaservices",
                "Source": "lva",
                "Type": "volume"
            },
            {
                "Target": "/var/media",
                "Source": "media",
                "Type": "volume"
            }]
        }
    }
    ```

### <a name="verify-that-the-module-is-running"></a>モジュールが動作していることの検証

最後の手順では、モジュールが接続され、想定どおりに実行されていることを確認します。 モジュールのランタイムの状態は、IoT Hub リソース内のお客様の IoT Edge デバイスに対して実行中である必要があります。

モジュールが実行中であることを確認するには、以下を実行します。

1. Azure portal で、Azure Stack Edge リソースに戻ります。
1. [モジュール] タイルを選択します。 これにより、[モジュール] ブレードが開きます。 モジュールの一覧で、自分がデプロイしたモジュールを特定します。 自分が追加したモジュールのランタイムの状態は、[実行中] になっているはずです。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="カスタム モジュール":::

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

* Kubernetes API アクセス (kubectl)。

    * ドキュメントに従って、[Kubernetes クラスターにアクセス](https://review.docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-create-kubernetes-cluster?toc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Ftoc.json&bc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Fbreadcrumb%2Ftoc.json&branch=release-tzl#debug-kubernetes-issues)できるように自分のマシンを構成します。
    * デプロイしたすべての IoT Edge モジュールでは、`iotedge` 名前空間が使用されます。 kubectl の使用時にそれを含めるようにしてください。
* モジュール ログ

    `iotedge` ツールは、ログを取得するためにアクセスすることができません。 ログを表示したり、ファイルにパイプしたりするには、[kubectl logs](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) を使用する必要があります。 例: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`
* ポッドとノードのメトリック

    ポッドとノードのメトリックを確認するには、[kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top) を使用します。 (この機能は、次の Azure Stack Edge リリース (バージョン 2008 以上) で使用できるようになる予定です) &gt;v2007)<br/>`kubectl top pods -n iotedge`
* モジュールのネットワーク Azure Stack Edge でモジュールを検出するには、モジュールに createOptions のホスト ポート バインドがなければなりません。 このモジュールは、`moduleName:hostport` を介してアドレス指定できるようになります。
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* ボリュームのマウント

    空でない既存のディレクトリに対してコンテナーがボリュームのマウントを試行している場合、モジュールの起動は失敗します。
* 共有メモリ

    Azure Stack Edge リソース上の共有メモリは、ホスト IPC を使用して、任意の名前空間のポッド全体でサポートされます。
    IoT Hub を介してデプロイを行うために、エッジ モジュール上の共有メモリを構成します。
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    (Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API.
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
* (高度) ポッドのコロケーション

    K8s を使用し、gRPC 経由で Live Video Analytics と通信するカスタム推論ソリューションをデプロイしている場合は、必ず Live Video Analytics モジュールと同じノードにポッドがデプロイされている必要があります。

    * オプション 1 - コロケーションにノード アフィニティと組み込みノード ラベルを使用する。

    ノードにラベルを設定するためのアクセス権をユーザーが持っていないため、現在は NodeSelector カスタム構成を利用できないように思えるかもしれません。 しかし、お客様のトポロジと名前付け規則によっては、[組み込みノード ラベル](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels)を使用できる場合もあります。 Azure Stack Edge リソースと Live Video Analytics を参照する nodeAffinity セクションは、コロケーションを実現するために推論ポッド マニフェストに追加できます。
    * オプション 2 - コロケーションにポッド アフィニティを使用する (推奨)。
Kubernetes では、同じノードでポッドをスケジュールできる[ポッド アフィニティ](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)をサポートしています。 コロケーションを実現するために、Live Video Analytics モジュールを参照する podAffinity セクションを推論ポッド マニフェストに追加できます。

    ```json   
    // Example Live Video Analytics module deployment match labels
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

## <a name="next-steps"></a>次のステップ

ライブ ビデオ ストリームの分析は、モジュールを使用し、ダイレクト メソッドを呼び出すことによって行うことができます。 モジュールでの[ダイレクト メソッドの呼び出し](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls)。
