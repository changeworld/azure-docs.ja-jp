---
title: AI コンポジションを使用した複数の AI モデルによるライブ ビデオ ストリームの分析
description: この記事では、Azure Video Analyzer の AI コンポジション機能を使用した、複数の AI モデルによるライブ ビデオ ストリーム分析の方法について説明します。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d4d54fba6f3b59c0e32286333694fa460cdb07b3
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487545"
---
# <a name="analyze-live-video-streams-with-multiple-ai-models-using-ai-composition"></a>AI コンポジションを使用した複数の AI モデルによるライブ ビデオ ストリームの分析

[!INCLUDE [header](includes/edge-env.md)]

特定の顧客シナリオでは、複数の AI モデルを使用してビデオを分析する必要があります。 それらのモデルが[相互に強化](../ai-composition-overview.md#sequential-ai-composition)し合って、または[個別に並行](../ai-composition-overview.md#parallel-ai-composition)して同じビデオ ストリームに作用することによって、またはそのような強化されるモデルと個別に並行するモデルの[組み合わせ](../ai-composition-overview.md#combined-ai-composition)が同じビデオ ストリームに作用することによって、アクション可能な分析情報を導き出すことができます。

Azure Video Analyzer を使用すると、[AI コンポジション](../ai-composition-overview.md)と呼ばれる機能を通してこのようなシナリオがサポートされます。 このガイドでは、同じビデオ ストリームに複数のモデルを強化される方式で適用する方法を示します。 Tiny (Light) YOLO と通常の YOLO モデルを並行して使用し、関心のあるオブジェクトを検出します。 Tiny YOLO モデルは、YOLO モデルよりも計算上は軽量ですが精度が低く、最初に呼び出されます。 検出されたオブジェクトが特定の信頼度のしきい値を超えた場合、順次ステージングされる通常の YOLO モデルは呼び出されないため、基になるリソースを効率的に利用できます。

このガイドの手順を完了すると、AI コンポジションが可能なパイプラインを介してシミュレートされたライブ ビデオ ストリームを実行し、ご自身の特定のシナリオに拡張することができます。 次の図は、そのパイプラインをグラフィカルに表したものです。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-ai-composition/motion-with-object-detection-using-ai-composition.svg" alt-text="AI コンポジションの概要":::
 
## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    > [!NOTE]
    > サービス プリンシパルを作成するためのアクセス許可を与えられた Azure サブスクリプションが必要です (owner role には、そのアクセス許可があります)。 適切なアクセス許可がない場合は、適切なアクセス許可をアカウント管理者に申請してください。
* 開発用マシン上の [Visual Studio Code](https://code.visualstudio.com/)。 [Azure IoT Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)があることを確認します。
* 開発用マシンが接続されているネットワークで、ポート 5671 のアウトバウンド トラフィックに Advanced Message Queuing Protocol (AMQP) が許可されていることを確認します。 このセットアップにより、Azure IoT Tools が Azure IoT Hub と通信できるようになります。
* 「[独自の gRPC モデルを使用してライブ ビデオを分析する](analyze-live-video-use-your-model-grpc.md)」クイックスタートを完了します。 これはこのハウツー ガイドの厳密な要件であるため、省略しないでください。

> [!TIP]
> Azure IoT Tools 拡張機能のインストール中に Docker のインストールを求められる場合があります。 このプロンプトは無視してかまいません。
>
> 作成された Azure リソースで問題が発生した場合は、 [トラブルシューティング ガイド](troubleshoot.md#common-error-resolutions) を参照して、よく発生する問題を解決してください。

## <a name="review-the-video-sample"></a>ビデオ サンプルを確認する

前提条件のセクションで指定されたクイックスタートは既に完了しているため、エッジ デバイスは既に作成されています。 このエッジ デバイスには、特定のビデオ ファイルを含む /home/localedgeuser/samples/input という入力フォルダーがあります。 IoT Edge デバイスにログインし、ディレクトリを /home/localedgeuser/samples/input/ に変更します。次のコマンドを実行して、このハウツー ガイドで使用する入力ファイルを取得します。

wget https://avamedia.blob.core.windows.net/public/co-final.mkv

さらに、必要に応じて、[VLC メディア プレーヤー](https://www.videolan.org/vlc/)がインストールされているマシンで Ctrl + N キーを押し、[サンプル ビデオ (.mkv)](https://avamedia.blob.core.windows.net/public/co-final.mkv) へのリンクを貼り付けて再生を開始します。 高速道路上の車の映像が表示されます。

## <a name="create-and-deploy-the-pipeline"></a>パイプラインを作成してデプロイする

前提条件で完了したクイックスタートの手順と同様に、ここでの手順に従うことができますが、少し調整してください。

1. 終了したばかりのクイックスタートの「[パイプラインを作成してデプロイする](analyze-live-video-use-your-model-grpc.md#create-and-deploy-the-pipeline)」セクションのガイドラインに従います。 手順を続行する場合は、次の調整を行ってください。 これらの手順は、ダイレクト メソッド呼び出しのための正しい本文を確実に使用するのに役立ちます。
    
    *operations.json* ファイルを編集します。
    
    * パイプライン トポロジへのリンクを変更します: `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/ai-composition/topology.json"`
    * `livePipelineSet` で、  
       1. `"topologyName" : "AIComposition"` を確認し、 
       2. `rtspUrl` パラメーター値を `"rtsp://rtspsim:554/media/co-final.mkv"` に変更します。
        
    * `pipelineTopologyDelete` で、名前を編集します: `"name" : "AIComposition"`
2. 「[IoT Edge の配置マニフェストを生成してデプロイする](analyze-live-video-use-your-model-grpc.md#generate-and-deploy-the-iot-edge-deployment-manifest)」セクションのガイドラインに従いますが、代わりに次の配置マニフェストを使用してください: src/edge/deployment.composite.template.json
3. 「[サンプル プログラムを実行する](analyze-live-video-use-your-model-grpc.md#run-the-sample-program)」セクションのガイドラインに従います。
4. 結果の詳細については、「[結果を解釈する](analyze-live-video-use-your-model-grpc.md#interpret-results)」セクションを参照してください。 ハブ上の分析イベントと診断イベントに加えて、使用したトポロジによって、関連するビデオ クリップもクラウド上に作成されます。これは AI シグナルをベースにしたシグナル ゲートのアクティブ化によってトリガーされます。 また、このクリップには、ダウンストリーム ワークフローで使用するハブ上の[操作イベント](record-event-based-live-video.md#operational-events)が伴います。 Azure portal にログインすると、ビデオ クリップを[調べて再生](record-event-based-live-video.md#playing-back-the-recording)できます。

## <a name="clean-up"></a>クリーンアップ

このアプリケーションを引き続き使用する予定がない場合は、このクイックスタートで作成したリソースを削除します。

## <a name="next-steps"></a>次のステップ

[診断メッセージ](monitor-log-edge.md)について詳しく学習します。
