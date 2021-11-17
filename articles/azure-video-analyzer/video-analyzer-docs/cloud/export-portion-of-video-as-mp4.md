---
title: Azure Video Analyzer で録画されたビデオの一部を MP4 ファイルにエクスポートする
description: このチュートリアルでは、Video Analyzer で録画されたビデオの一部を MP4 としてエクスポートする方法について説明します。これは Video Analyzer ビデオとして保存され、Video Analyzer アカウント エコシステムの外部からダウンロードして使用できるようになります。
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7a947ecad80a1ec6a22458acc1b4b1cec636ad32
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332214"
---
# <a name="tutorial-export-portion-of-recorded-video-as-an-mp4-file"></a>チュートリアル: 録画されたビデオの一部を MP4 ファイルとしてエクスポートする

[!INCLUDE [header](includes/cloud-env.md)]

このチュートリアルでは、Azure Video Analyzer アカウントで録画されたビデオの一部をエクスポートする方法について説明します。  エクスポートされたビデオは MP4 ファイルとして保存され、Video Analyzer アカウントの外部にダウンロードして使用できるようになります。

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事  

始める前に、以下の記事をお読みください。

* [Azure Video Analyzer の概要](../overview.md)
* [Azure Video Analyzer の用語](../terminology.md)
* [Video Analyzer パイプラインの概念](../pipeline.md)
* [イベントベースのビデオ記録](../event-based-video-recording-concept.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルの前提条件は次のとおりです。

* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、無料の[アカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Video Analyzer アカウント](../create-video-analyzer-account.md)。
* 「[クイック スタート: 動きを検出し、Video Analyzer にビデオを録画する](../edge/detect-motion-record-video-clips-cloud.md)」か、ビデオ シンクに録画する Video Analyzer パイプラインを完了している。

## <a name="overview"></a>概要

Video Analyzer は、RTSP ソースからビデオを録画することができ、Video Analyzer アカウントではこれらのビデオはセグメント化されたアーカイブとして録画されます。  セグメント化されたアーカイブのおかげで無期限のビデオ録画が可能になりますが、状況によっては、ビデオの一部を MP4 として保存し、個別にアーカイブやダウンロードをしたり、Video Analyzer エコシステムの外部で再生したりできるようにする必要があります。

このチュートリアルでは、次の事項について説明します。

* バッチ パイプライン トポロジとバッチ パイプライン ジョブについて
* バッチ トポロジを作成する方法。
* Video Analyzer ビデオ アーカイブから、指定した時間 (最大 24 時間) の MP4 ファイルを作成するバッチ パイプライン ジョブを作成する方法を説明します。

## <a name="pipeline-topology-of-batch-kind"></a>**バッチ** タイプのパイプライン トポロジ

バッチ タイプのパイプライン トポロジでは、独自のニーズに基づいて、3 つの相互接続されたノードを使用して、録画されたビデオをエクスポートのために処理する方法を記述することができます。  バッチ タイプのパイプライン トポロジは、パイプライン ジョブが使用するベースとなります。  パイプライン ジョブは、バッチ タイプのパイプライン トポロジの個別のインスタンスです。  パイプライン ジョブは録画された Video Analyzer ビデオをインポートし、ダウンロード可能な MP4 ファイルとして Video Analyzer のストレージ アカウントに保存します。  バッチ タイプのパイプライン トポロジは、[ビデオ ソース ノード](../pipeline.md#video-source)を使用します。これは、[エンコーダー プロセッサ ノード](../pipeline.md#encoder-processor)に接続し、その後[ビデオ シンク ノード](../pipeline.md#video-sink)に接続します。

> [!NOTE]
> ソース、プロセッサ、シンクの詳細については、「[ソース、プロセッサ、およびシンク](../pipeline.md#sources-processors-and-sinks)」を参照してください。 パイプライン ジョブの詳細については、「[パイプライン ジョブ](../pipeline.md#batch-pipeline)」を参照してください

## <a name="create-a-pipeline-job-from-videos"></a>パイプライン ジョブの作成 (ビデオから)

1. Azure portal で、Video Analyzer アカウントに移動します。
1. `Video Analyzer` セクションにある **[ビデオ]** を選択して、一部をエクスポートするビデオ ストリームを選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/export-portion-of-video-as-mp4/video-analyzer-video.png" alt-text="選択されたビデオが強調表示されている Azure Video Analyzer の [Video Analyzer] メニュー セクションの画像。":::
1. ビデオ ウィジェット プレーヤー ブレードで、上部にある **[ジョブの作成]** をクリックします。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/export-portion-of-video-as-mp4/create-job.png" alt-text="選択された [ジョブの作成] が強調表示されている Azure Video Analyzer のビデオ ウィジェット ブレードの画像。":::
1. `Create Job` フライアウト ブレードで、次を選択します。

   1. `Batch topology` に対して、 **[Create from sample]** \(サンプルから作成\) を選択します。
   1. `Batch topology name` ドロップ ダウン リストで、 **[Video export]** \(ビデオのエクスポート\) サンプル トポロジを選択します。
   1. 保存するトポロジの名前を `Batch topology name` に入力します。

        > [!NOTE]
        > サンプル トポロジが、上で入力した名前で保存されます。  これは、次回ビデオをエクスポートするときに再利用できます。
1. `Name your job` セクションで、[ジョブ名] フィールドに **ジョブ名** を入力して、[説明] フィールドにジョブの **説明** を入力します (説明は省略可能です)
1. `Define parameters` セクションで、次の操作を行います。
    1. Video Analyzer で録画されたビデオの名前を、[`sourceVideoName`] フィールドに入力します。
    1. `videoSourceTimeSequenceParameter` フィールドでカレンダー アイコンをクリックして開始日と終了日を選択し、各値の日付を選択します。  [時間] フィールドに、パイプライン ジョブがビデオ クリップを作成する際に使用する開始時刻と終了時刻を入力します。
        > [!NOTE]
        > 選択した録画されたビデオの時刻値は、ビデオ ウィジェット プレーヤーの右上に表示されています。  下の画像では、この時刻値が赤いボックスで囲まれています。  画像には、緑のボックスで囲まれたカレンダー アイコンも示されています。

    1. [`exportedVideoName`] フィールドに、エクスポートする MP4 ファイルの名前を入力します。
  
      > [!div class="mx-imgBorder"]
      > :::image type="content" source="./media/export-portion-of-video-as-mp4/video-widget-job-creation.png" alt-text="ビデオのタイム スタンプが赤いボックス、カレンダー アイコンが緑のボックスで強調表示された、Azure Video Analyzer のビデオ プレーヤー ウィジェットとパイプライン ジョブ フライアウト ブレードの画像。":::
1. `Create a job` フライアウト ブレードの下部にある **[作成]** をクリックします。  
    
    パイプライン ジョブを監視するには、`Batch Jobs tab` に移動します。
1. [`Video Analyzer`] セクションで、 **[バッチ]** を選択します。
1. バッチ ブレードの上部にある [`Jobs`] タブをクリックします。
  
    バッチ ジョブは処理状態に入り、正常に完了すると状態が `Completed` に変わります。  関連する MP4 ビデオ ファイルを表示するには、次のようにします。
1. [`Video Analyzer`] セクションの **[ビデオ]** をクリックします。
1. 手順 5 ですでに使用したバッチ ジョブ名に一致するビデオ名をクリックします。  

ビデオ ウィジェット プレーヤーが MP4 ファイルを再生します。  MP4 ファイルをダウンロードするには、ブレード上部にある `Download video` をクリックします。  MP4 ファイルが新しいブラウザ タブで開きます。ビデオを右クリックして、 **[名前を付けて保存]** を選択します。

## <a name="cancel-a-pipeline-job"></a>パイプライン ジョブのキャンセル

パイプライン ジョブが処理状態に入ったら、パイプライン ジョブをキャンセルすることができます。  パイプライン ジョブをキャンセルするには、次のようにします。

1. Video Analyzer アカウントに移動し、 **[Video Analyzer]** セクションで **[バッチ]** を選択します。
1. バッチ ブレードで、上部にある **[ジョブ]** タブを選択します。
1. [ジョブ] タブには、さまざまな状態のジョブの一覧が表示されます。 キャンセルしたい処理状態にあるジョブを探し、[バッチ パイプライン ジョブ] タブの右側にある **[キャンセル]** を選択して、 **[はい]** をクリックします。

    > [!NOTE]
    > 失敗したパイプライン ジョブはキャンセルできません。

## <a name="delete-a-pipeline-job"></a>パイプライン ジョブの削除

パイプライン ジョブが完了または失敗の状態になったら、パイプライン ジョブを削除できます。  パイプライン ジョブを削除するには、次のようにします。

1. Video Analyzer アカウントに移動し、 **[Video Analyzer]** セクションで **[バッチ]** を選択します。
1. バッチ ブレードで、上部にある **[ジョブ]** タブを選択します。
1. [ジョブ] タブには、さまざまな状態のジョブの一覧が表示されます。  削除したいジョブ (キャンセル済み、完了、失敗の状態のジョブ) を探し、右側にある **[削除]** を選択して、 **[削除]** をクリックします。

## <a name="delete-a-pipeline-topology-of-batch-kind"></a>バッチ タイプのパイプライン トポロジの削除

バッチ タイプのパイプライン トポロジを削除するには、そのパイプライン トポロジに関連付けられているすべてのパイプライン ジョブを削除する必要があります。  バッチ タイプのパイプライン トポロジを削除するには、次のようにします。

1. Video Analyzer アカウントに移動します。
2. [`Video Analyzer`] セクションの **[バッチ]** をクリックします。
3. [トポロジ] タブで、削除するバッチ タイプのパイプライン トポロジを見つけ、バッチ タイプのパイプライン トポロジの右側にある ' **...** ' をクリックします。
4. **[トポロジの削除]** をクリックします。

    > [!NOTE]
    > バッチ タイプのパイプライン トポロジを削除する前に、バッチ タイプのパイプライン トポロジから、すべてのパイプライン ジョブを削除する必要があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他のクイックスタートまたはチュートリアルに取り組む場合は、作成したリソースをそのまま残しておきます。 それ以外の場合は、Azure portal にアクセスして、ご利用のリソース グループに移動し、このクイックスタートを実行したリソース グループを選択して、そのリソースをすべて削除してください。

## <a name="next-steps"></a>次のステップ

* [クラウド パイプライン](../pipeline.md)を使用してビデオをキャプチャおよび録画するために、[カメラをクラウドに直接接続します](./connect-cameras-to-cloud.md)。
* [RTSP プロトコルを使用してビデオ パケットの透過的なゲートウェイとして機能する Video Analyzer エッジ モジュールを介して、カメラを Video Analyzer のサービスに接続します](./use-remote-device-adapter.md)。
