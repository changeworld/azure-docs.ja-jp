---
title: IoT Edge for Linux on Windows にデプロイする - Azure
description: この記事では、IoT Edge for Linux on Windows デバイスにデプロイする方法に関するガイダンスを取り上げます。
ms.topic: how-to
ms.date: 05/25/2021
ms.openlocfilehash: 2907318f7d1c49c4aea247880a9880e724b46ca6
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387463"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>IoT Edge for Linux on Windows (EFLOW) デバイスにデプロイする

この記事では、[IoT Edge for Linux on Windows (EFLOW)](../../iot-edge/iot-edge-for-linux-on-windows.md) がインストールされているエッジ デバイスに Azure Video Analyzer をデプロイする方法について説明します。 このドキュメントの手順を終えると、ビデオでの動きを検出してそのようなイベントをクラウド内の IoT ハブに出力する[パイプライン](pipeline.md)を実行できるようになります。 そのパイプラインを、高度なシナリオに対応するよう置き換えれば、Azure Video Analyzer の強力な機能を Windows ベースの IoT Edge デバイスで利用することができます。

## <a name="prerequisites"></a>前提条件 

* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 開発用マシン上の [Visual Studio Code](https://code.visualstudio.com/)。 [Azure IoT Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)があることを確認します。
* [EFLOW の概要](../../iot-edge/iot-edge-for-linux-on-windows.md)に関するページを参照してください。

## <a name="deployment-steps"></a>デプロイメントの手順

このドキュメントの全体的な流れを次に示します。EFLOW がインストールされている Windows デバイスで Azure Video Analyzer を実行する準備が、簡単な 5 つの手順ですべて整います。

![IoT Edge for Linux on Windows (EFLOW) の図](./media/deploy-iot-edge-linux-on-windows/eflow.png)

1. Windows デバイスに [EFLOW をインストール](../../iot-edge/how-to-install-iot-edge-on-windows.md)します。 

    1. Windows PC を使用している場合、[Windows Admin Center](/windows-server/manage/windows-admin-center/overview) 開始ページにある接続の一覧に、Windows Admin Center を実行している PC を表すローカル ホスト接続が表示されます。 
    1. その他の管理対象のサーバー、PC、またはクラスターもここに表示されます。
    1. Windows Admin Center を使用して、ローカル デバイスまたはリモート マネージド デバイス上に、Azure EFLOW をインストールして管理できます。 このガイドでは、ローカル ホスト接続が、Azure IoT Edge for Linux on Windows のデプロイにおけるターゲット デバイスとして使用されました。 そのため、localhost も IoT Edge デバイスとして表示されています。

    ![デプロイ手順 - Windows Admin Center](./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png) 
1. IoT Edge デバイスをクリックしてそれに接続すると、[概要] タブと [コマンド シェル] タブが表示されます。[コマンド シェル] タブでは、エッジ デバイスに対するコマンドを実行できます。

    ![デプロイ手順 - Azure IoT Edge Manager](./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png)
1. コマンド シェルに移動して次のコマンドを入力します。

    `bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`

    Azure Video Analyzer モジュールは、エッジ デバイス上で、特権のないローカル ユーザー アカウントを使用して実行されます。 また、アプリケーションの構成データを格納するための特定のローカル フォルダーも必要となります。 最後に、この使用法ガイドでは、分析のためにビデオ フィードをリアルタイムで AVA モジュールに中継する [RTSP シミュレーター](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)を活用しています。 このシミュレーターは入力として、事前に録画されたビデオ ファイルを input ディレクトリから受け取ります。 

    上で使用した prep-device スクリプトにより、これらのタスクが自動化されます。そのため、1 つのコマンドを実行すれば、関連するすべての入力と構成フォルダー、ビデオ入力ファイル、特権付きのユーザー アカウントがシームレスに作成されます。 コマンドが正常に完了すると、次のフォルダーがエッジ デバイス上に作成されます。 

    * `/home/localedgeuser/samples`
    * `/home/localedgeuser/samples/input`
    * `/var/lib/videoanalyzer`
    * `/var/media`

    /home/localedgeuser/samples/input フォルダーにあるビデオ ファイル (*.mkv) に注目してください。これらが分析対象の入力ファイルとなります。 
1. エッジ デバイスの設定とハブへの登録が完了し、適切なフォルダー構造が作成された状態で実行することに成功したら、次に、別途以下の Azure リソースを設定し、AVA モジュールをデプロイします。 

    * ストレージ アカウント
    * Azure Media Services アカウント

    [![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

    テンプレートで、エッジ デバイスが必要かどうかの確認を求められたら、前にデバイスと IoT ハブの両方を作成したので、[Use and existing edge device]\(既存のエッジ デバイスを使用する\) オプションを選択します。 さらに、その後の手順では、IoT ハブの名前と IoT Edge デバイスの ID の入力を求められます。  
    
    ![既存のデバイスを使用する](./media/deploy-iot-edge-linux-on-windows/use-existing-device.png) 

    完了したら、IoT Edge デバイスのコマンド シェルに再度ログオンして、次のコマンドを実行します。

    **`sudo iotedge list`**

    次の 4 つのモジュールがエッジ デバイスにデプロイされ、実行されていることがわかります。 リソース作成スクリプトによって、IoT Edge モジュール (edgeAgent と edgeHub)、RTSP ビデオ フィードのシミュレーションを提供する RTSP シミュレーター モジュールと共に、AVA モジュールがデプロイされることに注意してください。
    
    ![デプロイされたモジュール](./media/vscode-common-screenshots/avaedge-module.png)
1. モジュールのデプロイと設定を終えたら、初めての AVA パイプラインを EFLOW で実行する準備は完了です。 次の手順を実行すると、以下に示すような単純なモーション検出パイプラインを実行し、その結果を視覚化することができます。

    ![モーション検出に基づく Video Analyzer](./media/get-started-detect-motion-emit-events/motion-detection.svg)

    1. Azure IoT Tools 拡張機能を[構成](get-started-detect-motion-emit-events.md#prepare-to-monitor-the-modules)します。
    1. この[ダイレクト メソッド呼び出し](get-started-detect-motion-emit-events.md#use-direct-method-calls)を使用して、pipelineTopology を設定し、livePipeline をインスタンス化してアクティブ化します。
    1. ハブで[結果を確認](get-started-detect-motion-emit-events.md#observe-results)します。
    1. [クリーンアップ メソッド](get-started-detect-motion-emit-events.md#deactivate-the-live-pipeline)を呼び出します。
    1. 今後必要がない場合は、リソースを削除します。

        > [!IMPORTANT]
        > 削除しなかった場合、リソースは引き続きアクティブな状態となり、Azure のコストが発生します。 使用しないリソースは必ず削除してください。
        
## <a name="next-steps"></a>次のステップ

* 関連するビデオをクラウドで記録しながらモーション検出を試します。 [モーション検出とビデオ クリップの記録](detect-motion-record-video-edge-devices.md)に関するクイックスタートの手順に従ってください。
* [ライブ ビデオの AI](analyze-live-video-use-your-model-http.md#overview) を実行します (前提条件である設定は、上記で既に完了しているのでスキップしてかまいません)。
* [VS Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge)を使用して、その他のパイプラインを確認します。
* RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラは、[ONVIF 準拠製品](https://www.onvif.org/conformant-products/)のページで見つけることができます。 プロファイル G、S、または T に準拠しているデバイスを探します。
