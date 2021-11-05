---
title: EFLOW を使用して Windows デバイスにデプロイする
description: この記事では、IoT Edge for Linux on Windows デバイスにデプロイする方法に関するガイダンスを取り上げます。
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3e2e045a72700b7e2c2e96cf63f344a84173b0f7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091982"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>IoT Edge for Linux on Windows (EFLOW) デバイスにデプロイする

[!INCLUDE [header](includes/edge-env.md)]

この記事では、[IoT Edge for Linux on Windows (EFLOW)](../../../iot-edge/iot-edge-for-linux-on-windows.md) がインストールされているエッジ デバイスに Azure Video Analyzer をデプロイする方法について説明します。 このドキュメントの手順を完了すると、ビデオでモーションを検出し、そのようなイベントを Azure IoT Hub に出力する[パイプライン](../pipeline.md)を実行できるようになります。 そのパイプラインを、高度なシナリオに対応するよう置き換えれば、Azure Video Analyzer の強力な機能を Windows ベースの IoT Edge デバイスで利用することができます。

## <a name="prerequisites"></a>前提条件 

* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 開発用マシン上の [Visual Studio Code](https://code.visualstudio.com/)。 [Azure IoT Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)があることを確認します。
* [EFLOW の概要](../../../iot-edge/iot-edge-for-linux-on-windows.md)に関するページを参照してください。

## <a name="deployment-steps"></a>デプロイメントの手順

このドキュメントの全体的な流れを次に示します。EFLOW がインストールされている Windows デバイスで Azure Video Analyzer を実行する準備が、簡単な 5 つの手順ですべて整います。

![IoT Edge for Linux on Windows (E FLOW) の図。](./media/deploy-iot-edge-linux-on-windows/eflow.png)

1. PowerShell を使用して、Windows デバイスに [EFLOW をインストール](../../../iot-edge/how-to-install-iot-edge-on-windows.md)します。

    > [!NOTE]
    > EFLOW をデプロイする方法が 2 つあり (PowerShell と Windows Admin Center)、仮想マシンをプロビジョニングする方法が 2 つあります (接続文字列を使用した手動プロビジョニングと X.509 証明書を使用した手動プロビジョニング)。 [PowerShell デプロイ](../../../iot-edge/how-to-install-iot-edge-on-windows.md#create-a-new-deployment)に従い、[IoT Hub からの接続文字列を使用してコンピューターをプロビジョニング](../../../iot-edge/how-to-install-iot-edge-on-windows.md#manual-provisioning-using-the-connection-string)してください。

1. EFLOW を設定したら、コマンド `Connect-EflowVm` を PowerShell (管理者特権) に入力して接続します。 これで、PowerShell 内に EFLOW VM を制御する bash ターミナルが表示されます。ここでは、Top や Nano などのユーティリティを含む Linux コマンドを実行できます。 

    > [!TIP] 
    > EFLOW VM を終了するには、ターミナル内に「`exit`」と入力します。

1. PowerShell を使用して EFLOW VM にログインし、次のコマンドを入力します。

    `bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`
    
    `sudo iptables -I INPUT -p udp -j ACCEPT`

    Video Analyzer では、アプリケーション構成データを格納するために特定のローカル フォルダーが必要です。 このハウツー ガイドでは、ビデオ フィードを分析のために Video Analyzer モジュールにリアルタイムでリレーする [RTSP シミュレーター](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)を利用しています。 このシミュレーターは入力として、事前に録画されたビデオ ファイルを input ディレクトリから受け取ります。 

    上で使用した prep-device スクリプトにより、これらのタスクが自動化されます。そのため、1 つのコマンドを実行すれば、関連するすべての入力と構成フォルダー、ビデオ入力ファイル、特権付きのユーザー アカウントがシームレスに作成されます。 コマンドが正常に完了すると、次のフォルダーがエッジ デバイス上に作成されます。 

    * `/home/localedgeuser/samples`
    * `/home/localedgeuser/samples/input`
    * `/var/lib/videoanalyzer`
    * `/var/media`

    /home/localedgeuser/samples/input フォルダーにあるビデオ ファイル (*.mkv) に注目してください。これらが分析対象の入力ファイルとなります。 
    
1. エッジ デバイスを設定してハブに登録し、適切なフォルダー構造を作成して正常に実行したので、次の手順では、次の追加の Azure リソースを設定し、Video Analyzer モジュールをデプロイします。 次のデプロイ テンプレートで、リソースの作成が行われます。

    [![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
    
    デプロイには約 20 分かかります。 完了すると、次のような特定の Azure リソースが Azure サブスクリプションにデプロイされます。

    * Video Analyzer アカウント - このクラウド サービスは、Video Analyzer エッジ モジュールの登録、録画されたビデオの再生、およびビデオ分析に使用されます。
    * ストレージ アカウント - 録画されたビデオとビデオ分析の格納用です。
    * マネージド ID - これは、上記のストレージ アカウントへのアクセスを管理するために使用されるユーザー割り当てマネージド ID です。
    * IoT Hub - IoT アプリケーションと IoT Edge モジュール、さらにそれが管理するデバイスの間の双方向通信に対する中央メッセージ ハブとして機能します。

    テンプレートで、エッジ デバイスが必要かどうかの確認を求められたら、前にデバイスと IoT ハブの両方を作成したので、[Use and existing edge device]\(既存のエッジ デバイスを使用する\) オプションを選択します。 さらに、その後の手順では、IoT ハブの名前と IoT Edge デバイスの ID の入力を求められます。  
    
    ![既存のデバイスを使用する](./media/deploy-iot-edge-linux-on-windows/use-existing-device.png) 

    完了したら、EFLOW VM に再度ログオンして、次のコマンドを実行します。

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
* [VS Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge)を使用して、その他のパイプラインを確認します。
* RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラは、[ONVIF 準拠製品](https://www.onvif.org/conformant-products/)のページで見つけることができます。 プロファイル G、S、または T に準拠しているデバイスを探します。
* [ライブ ビデオの AI](analyze-live-video-use-your-model-http.md#overview) を実行します (前提条件である設定は、上記で既に完了しているのでスキップしてかまいません)。

    > [!WARNING] 
    > YOLO など、メモリを集中的に使用する AI モデルの実行を希望する上級ユーザーの場合、EFLOW VM に割り当てるリソースを場合によっては増やす必要があります。 まず、「`exit`」と入力して EFLOW VM を終了し、Windows PowerShell ターミナルに戻ります。 次に、管理者特権の PowerShell でコマンド `Set-EflowVM` を実行します。 コマンドの実行後、PowerShell のプロンプトに従って目的の[パラメーター](../../../iot-edge/reference-iot-edge-for-linux-on-windows-functions.md#set-eflowvm)を入力します。たとえば、`cpuCount: 2`, `memoryInMB: 2048` にします。 数分後、Edge モジュールを再デプロイし、ライブ パイプラインを再アクティブ化し、推論を表示します。 接続の問題が発生した場合 (IoT Hub でエラー 137 または 255 が表示されるなど)、この手順を場合によっては再実行する必要があります。 
