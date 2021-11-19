---
title: Azure Video Analyzer ライブ パイプラインの概要 - Azure portal
description: このクイックスタートでは、Azure Video Analyzer サービスのライブ パイプラインを使用して RTSP カメラからビデオをキャプチャおよび録画する手順について説明します。
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3f7c134174560d3222e0ba936378c37b931abee7
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132489929"
---
# <a name="quickstart-get-started-with-video-analyzer-live-pipelines-in-the-azure-portal"></a>クイックスタート: Azure portal の Video Analyzer ライブ パイプラインの概要

![クラウドのアイコン](media/env-icon/cloud.png)  
または、[Edge を使用して Video Analyzer の使用を開始する](../edge/get-started-detect-motion-emit-events-portal.md)方法に関するページを参照してください。

---

このクイックスタートでは、Azure Video Analyzer サービスのライブ パイプラインを使用して、リアル タイム ストリーミング プロトコル (RTSP) カメラからビデオをキャプチャおよび録画する手順について説明します。
Azure portal を使用して、Video Analyzer アカウントとそれに付随するリソースを作成します。 実際の RTSP カメラにアクセスできない場合は、RTSP カメラ シミュレーターをデプロイします (インターネット経由でアクセス可能にできます)。 次に、関連する Video Analyzer リソースをデプロイして、Video Analyzer アカウントにビデオを録画します。

このドキュメントで説明する手順は、ファイアウォールで保護されていない、インターネット経由でアクセス可能なカメラに該当します。 次の図は、Video Analyzer アカウントにデプロイするライブ [パイプライン](../pipeline.md)をグラフィカルに表したものです。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/live-pipeline.svg" alt-text="クラウド上のライブ パイプラインの表現":::

## <a name="prerequisites"></a>前提条件

- 有効な Azure サブスクリプション アカウントがない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

  [!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](../includes/note-account-storage-same-subscription.md)]
- インターネット経由でアクセスできる RTSP カメラ、または RTSP カメラ シミュレーターをホストする Azure Linux VM (管理者特権を持つ)

## <a name="sample-architecture---recording-video-from-a-camera-over-the-internet"></a>サンプル アーキテクチャ - インターネットを経由したカメラからのビデオの録画
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/public-camera-to-cloud-live-pipeline-arch.png" alt-text="クラウド上でビデオをキャプチャする Video Analyzer のライブ パイプラインと統合されたパブリック カメラ ビデオ フィードのサンプル アーキテクチャの図。":::

## <a name="rtsp-camera"></a>RTSP カメラ

RTSP 対応カメラにアクセスする必要があります[サポートされるカメラを確認します](../quotas-limitations.md)。 カメラは、最大ビットレートが 3 Mbps より少ないビデオをエンコードするように構成する必要があります。 この最大ビットレート設定をメモします。 さらに、このカメラの RTSP サーバーには、パブリック インターネットを経由してアクセスできる必要があります。 このようなカメラを使用できる場合は、「Azure リソースを作成する」セクションにスキップできます。 または、以下のセクションで説明するように、RTSP カメラ シミュレーターをデプロイできます。

## <a name="deploy-rtsp-camera-simulator"></a>RTSP カメラ シミュレーターをデプロイする

このセクションでは、'Ubuntu Server 18.04' オペレーティング システムを実行している Azure Linux VM に RTSP カメラ シミュレーターをデプロイする方法について説明します。 このシミュレーターでは [Live555 Media Server](http://www.live555.com/mediaServer/) を使用します。

> [!NOTE]
> サードパーティ製ソフトウェアについて言及していますが、これはあくまで情報提供および利便性を目的としたものです。 サードパーティ製ソフトウェアに関して、Microsoft は公式にサポートしているわけではなく、また権利を提供するものでもありません。 詳細については、[Live555 Media Server](http://www.live555.com/mediaServer/) に関するページを参照してください。

> [!WARNING]
> この RTSP カメラ シミュレーター エンドポイントはインターネット上で公開され、RTSP URL を知っているすべてのユーザーがアクセスできることに注意してください。

**デプロイメントの手順:**
1. 'Ubuntu Server 18.04' オペレーティング システムを実行している standard_D2s_v3 シリーズの Azure Linux VM をデプロイします。VM の作成手順については、[こちらを参照してください](../../../virtual-machines/linux/quick-create-portal.md)。リンクされた記事で言及した Web サーバーをインストールする必要はありません。 また、SSH 接続を使用して VM に接続するために、配置ウィザードで SSH ポートを許可します。
1. RTSP プロトコルの受信接続を有効にします。 Azure portal で、前の手順で作成した Linux VM の管理ウィンドウを開きます。

    1. [ネットワーク] をクリックすると、受信 SSH 接続をサポートするために作成されたネットワーク セキュリティ グループ (NSG) の受信ポート規則に対するブレードが開きます。
    1. [受信ポートの規則を追加する] をクリックして新しいポート規則を追加します。
    1. 表示されたウィンドウで、宛先ポート範囲を 554 に変更します。 "RTSP" など、規則の名前を選択します。 他のすべての値は既定値のままにします。 詳細については、[こちら](../../../virtual-machines/windows/nsg-quickstart-portal.md)を参照してください。
1. [こちら](https://docs.docker.com/engine/install/ubuntu/)の手順を使用して VM に Docker をインストールします。"hello-world" イメージを実行して Docker のインストールを確認するまでの手順のみに従ってください。
1. たとえば、SSH を使用して VM に接続します。 ターミナル ウィンドウから、メディア ファイルをホストするために "localmedia" などのローカル フォルダーを作成します。この VM ローカル フォルダーは、RTSP メディアサーバー コンテナーにマップするために使用されます。
1. カメラ フィードをシミュレートするために使用する MKV ファイルを次のようにコピーします。

    ```
    cd localmedia
    wget https://avamedia.blob.core.windows.net/public/camera-1800s.mkv
    ```
1. 次のように構築済みのコンテナー イメージを使用して、VM 上で RTSP サーバーを起動します

    ```    
    sudo docker run -d -p 554:554 -v ${PWD}:/live/mediaServer/media mcr.microsoft.com/ava-utilities/rtspsim-live555:1.2
    ```
1. RTSP サーバーが実行された後、クライアントは RTSP URL を使用して接続できるようになります。

    - Azure portal で、VM の [概要] ページに移動し [パブリック IP アドレス] の値をメモします
    
        - RTSP の URL は、rtsp://{パブリック IP アドレス}:554/media/camera-1800s.mkv です。VLC などのプレーヤーで、デスクトップからテストできます。

## <a name="create-azure-resources"></a>Azure リソースを作成する

次の手順では、必要な Azure リソース (Video Analyzer アカウント、ストレージ アカウント、ユーザーに割り当てられたマネージド ID) を作成します。

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Azure Portal で Video Analyzer アカウントを作成する

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. 上部にある検索バーで、「**Video Analyzer**」と入力します。
1. **[サービス]** で **[Video Analyzer]** を選択します。
1. **[追加]** を選択します。
1. **[Create Video Analyzer account]\(Video Analyzer アカウントを作成する\)** セクションで、これらの必要な値を入力します。

   - **サブスクリプション**: Video Analyzer アカウントを作成するために使用するサブスクリプションを選びます。
   - **リソース グループ**: Video Analyzer アカウントを作成するリソース グループを選ぶか、 **[新規作成]** を選択してリソース グループを作成します。
   - **Video Analyzer account name\(Video Analyzer アカウント名\)** : Video Analyzer アカウントの名前を入力します。 名前は、スペースを入れず、すべて小文字のアルファベットまたは数字を使用し、3 から 24 文字の長さにする必要があります。
   - **場所**: Video Analyzer アカウントをデプロイする場所を選びます (例: **米国西部 2**)。
   - **ストレージ アカウント**: ストレージ アカウントを作成します。 [Standard 汎用 v2](../../../storage/common/storage-account-overview.md#types-of-storage-accounts) ストレージ アカウントを選択することをお勧めします。
   - **[ユーザー ID]** : 新しいユーザー割り当てマネージド ID を作成し、名前を付けます。
1. フォームの下部にある **[確認と作成]** を選択します。

### <a name="deploy-a-live-pipeline"></a>ライブ パイプラインをデプロイする

Video Analyzer アカウントが作成されると、次の手順に進み、ライブ パイプライン トポロジとライブ パイプラインを作成できます。
1. Video Analyzer アカウントに移動し、左下にある **[ライブ]** メニュー項目を見つけて選択します。 
1. [トポロジ] プレーンで、上部の **[Create topology]\(トポロジの作成\)** オプションを選択してライブ トポロジを作成します。 ポータル ウィザードの手順に従ってライブ パイプライン トポロジを作成する

    - **パイプライン トポロジの作成** ウィザードがポータルに表示されます
    - **[Try sample toppology]\(サンプル トポロジを試す\)** 、 **[Live capture, record, and stream from RTSP camera topology]\(RTSP カメラ トポロジからのライブ キャプチャ、レコード、ストリーム\)** 、 **[Load sample topology]\(サンプル トポロジの読み込み\)** ダイアログ ボックスの [続行] の順に選択します。
    - ライブ パイプライン トポロジを作成するためのウィザードが表示され、ビデオ シンク ノードに接続されている RTSP ソース ノードが表示されます。
    - トポロジを作成するために必要なフィールドに入力します。 
    
        - **[トポロジ名]** : トポロジの名前を入力します 
        - **[説明]** (省略可能): トポロジに関する簡単な説明 
        - **[タイプ]** ([ライブ] と事前設定)
        - **[RTSP source]\(RTSP ソース\)** ノードを選択し、 **[転送]** プロパティの値を TCP として設定します
        - 残りのプロパティは、既定の構成で **[保存]** を選択します
1. 次の手順では、前の手順で作成したトポロジを使用してライブ パイプラインを作成します。 

    - **[Pipelines]** 、 **[パイプラインを作成]** の順に選択し、前の手順で作成したライブ パイプライン トポロジを選択してパイプラインを作成します。 トポロジを選択したら、 **[作成]** をクリックします。
    - **ライブ パイプラインの作成** ウィザードがポータルに表示されます。 必須フィールドに必要事項を入力します。 
    
        - **[Live pipeline name]\(ライブ パイプライン名\)** : 一意の名前を指定します。英数字とダッシュを使用できます
        - **[Bitrate]\(ビットレート\)** : ライブ パイプライン用に予約されている最大容量 (Kbps) です。許可される範囲は 500 kbps から 3,000 kbps です。 RTSP カメラ シミュレーター camera-1800s.mkv ファイルには、既定の 1000 を使用します (この値は、使用されているサンプル ビデオ ファイルと一致する必要があります)。 
        - **rtspUserNameParameter**、**rtspPasswordParameter**: RTSP カメラ シミュレーターを使用する場合は、これらのフィールドにダミー値を設定します。それ以外の場合は、実際の RTSP カメラ ストリームの認証資格情報を入力します
        - **rtspUrlParameter**: `rtsp://<VMpublicIP>:554/media/camera-1800s.mkv` (RTSP カメラ シミュレーターの場合) を使用します。それ以外の場合は、実際の RTSP カメラ ストリーム URL を使用します
        - **videoNameParameter**: 録画するターゲット ビデオ リソースの一意の名前です。 注: カメラ (または MKV ファイル) ごとに一意のビデオ リソースを使用します
    - **[作成]** を選択すると、ポータルのパイプライン グリッドにパイプラインが作成されます。
    - グリッドで作成されたライブ パイプラインを選択し、ウィンドウの右側にある **[アクティブにする]** オプションを選択してライブ パイプラインをアクティブにします。 これで、ライブ パイプラインが開始され、ビデオの録画が開始されます
1. これで、ポータルの Video Analyzer アカウントの下にある **[Videos]\(ビデオ\)** ペインにビデオ リソースが表示されます。 パイプラインがアクティブでライブのビデオ ストリームを録画中のため、その状態は、 **[Recording]\(録画中\)** と示されます。
1. 数秒後にビデオを選択すると、[待機時間の短いストリーム](../playback-recordings-how-to.md)が表示されます。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/camera-1800s-mkv.png" alt-text="クラウド上のライブ パイプラインによってキャプチャされたビデオ録画の図。":::

    > [!NOTE]
    > RTSP カメラ シミュレーターを使用している場合、エンドツーエンドの待ち時間を正確に判断できません。 さらに、RTSP カメラ シミュレーターは、MKV ファイルの末尾に到達すると停止します。 ライブ パイプラインは再接続を試み、しばらくすると、シミュレーターはファイルの先頭から再びストリーミングを開始します。 このライブ パイプラインを何時間も実行すると、シミュレーターが停止して再開されるたびに、ビデオの録画にギャップが生じます。
1. 必要に応じて、アクティビティ ログを参照して、デプロイ操作をすばやく確認します。 監視とイベント ログについては、[ここ](./monitor-log-cloud.md)を参照してください。
1. パイプラインの録画を非アクティブ化するには、Video Analyzer アカウントに移動し、左側のパネルで **[ライブ]** ->  **[Pipelines]** を選択し、非アクティブ化するパイプラインを選択して、パイプライン グリッドで **[非アクティブ化]** を選択すると録画が停止します。 
1. パイプラインとトポロジが必要ない場合は、引き続き削除することもできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他のクイックスタートまたはチュートリアルに取り組む場合は、作成したリソースをそのまま残しておきます。 それ以外の場合は、Azure portal にアクセスして、ご利用のリソース グループに移動し、このクイックスタートを実行したリソース グループを選択して、そのリソースをすべて削除してください。

## <a name="next-steps"></a>次のステップ

- ビデオの[保持ポリシー](../manage-retention-policy.md)の管理の詳細を確認します
- [ここ](https://github.com/Azure/video-analyzer/tree/main/media)からメディア シミュレーター用のさまざまな MKV サンプル ファイルを試してください。サンプル ファイルのビットレートはパイプラインのセットアップと一致する必要があります。
- [クラウド パイプラインの監視とログ記録](./monitor-log-cloud.md)に関するページで詳細を確認します。
