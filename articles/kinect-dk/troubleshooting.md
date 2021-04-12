---
title: Azure Kinect の既知の問題とトラブルシューティング
description: Azure Kinect DK で Sensor SDK を使用する際の既知の問題とトラブルシューティングのヒントについて説明します。
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: conceptual
keywords: トラブルシューティング、更新プログラム、バグ、Kinect、フィードバック、回復、ログ記録、ヒント
ms.openlocfilehash: da5242a09934a756093a9e02b6d474e6c75fecda
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108742"
---
# <a name="azure-kinect-known-issues-and-troubleshooting"></a>Azure Kinect の既知の問題とトラブルシューティング

このページには、Azure Kinect DK で Sensor SDK を使用する際の既知の問題とトラブルシューティングのヒントが含まれます。 製品のハードウェア固有の問題については、[製品のサポート ページ](./index.yml)も参照してください。

## <a name="known-issues"></a>既知の問題

- ASMedia USB ホスト コントローラー (ASM1142 チップセットなど) との互換性の問題
  - 一部のケースでは Microsoft USB ドライバーを使用するとブロックが解除される場合がある
  - 多くの PC には代替ホスト コントローラーもあり、USB3 ポートを変更することが解決に役立つ場合がある

その他 Sensor SDK 関連の問題については、[GitHub の「Issues」](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/issues)を確認してください。

## <a name="collecting-logs"></a>ログの収集

K4A.dll のログ記録は、環境変数によって有効になります。 既定では、ログは stdout に送信され、エラーと重大なメッセージのみが生成されます。 これらの設定は、ログがファイルに記録されるように変更できます。 また、必要に応じて詳細を調整することもできます。 以下に、k4a.log という名前のファイルへのログ記録を有効にし、警告以上のレベルのメッセージをキャプチャする、Windows の例を示します。

1. `set K4A_ENABLE_LOG_TO_A_FILE=k4a.log`
2. `set K4A_LOG_LEVEL=w`
3. コマンド プロンプトからシナリオを実行する (例: ビューアーの起動)
4. k4a.log と共有ファイルに移動します。

詳細については、以下のヘッダー ファイルからのクリップを参照してください。

```console
/**
* environment variables
* K4A_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4A_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4A_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

Body Tracking SDK K4ABT.dll のログ記録は似ていますが、次のように、ユーザーが別セットの環境変数名を変更する必要がある点で異なります。

```console
/**
* environment variables
* K4ABT_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4ABT_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4ABT_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

## <a name="device-doesnt-enumerate-in-device-manager"></a>デバイス マネージャーでデバイスが列挙されない

- デバイスの背面にあるステータス LED を確認してください。黄色で点滅している場合は、USB の接続に問題があり、十分な電力が得られていません。 電源ケーブルは、所定の電源アダプターに接続されている必要があります。 電源ケーブルの USB タイプ A は接続されていますが、そのデバイスには PC の USB ポートが供給できるよりも多くの電力を必要とします。 そのため、それを PC ポートや USB ハブに接続しないでください。
- 電源ケーブルが接続されていて、データ接続に USB3 ポートを使用していることを確認します。
- データ接続を行う USB3 ポートを変更してみてください (PC の背面など、マザーボードの近くにある USB ポートを使用することをお勧めします)。
- ケーブルを確認します。破損したケーブルや低品質のケーブルにより、信頼性の低い列挙を引き起こすおそれがあります (デバイスがデバイス マネージャーで "点滅" し続けます)。
- バッテリ駆動のノート パソコンに接続されている場合は、ポートに供給される電力が抑えられている可能性があります。
- ホスト PC を再起動します。
- 問題が解決しない場合は、互換性の問題がある可能性があります。
- ファームウェアの更新中にエラーが発生し、デバイスが自己復旧しなかった場合は、[出荷時の設定にリセット](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)します。

## <a name="azure-kinect-viewer-fails-to-open"></a>Azure Kinect ビューアーを開くことができない

- まず、対象のデバイスが Windows デバイス マネージャーに列挙されていることを確認します。

    ![Windows デバイス マネージャー内の Azure Kinect カメラ](./media/resources/viewer-fails.png)

- デバイスを使用している他のアプリケーション (Windows カメラ アプリケーションなど) があるかどうかを確認します。 デバイスに一度にアクセスできるアプリケーションは 1 つだけです。
- k4aviewer.err のログでエラー メッセージを確認します。
- Windows カメラ アプリケーションを開き、その動作を確認します。
- 電源をオフにして再度オンにし、ストリーミング LED の電源がオフになるのを待ってからデバイスを使用します。
- ホスト PC を再起動します。
- PC で最新のグラフィックス ドライバーを使用していることを確認します。
- 独自の SDK のビルドを使用している場合は、正式にリリースされたバージョンを使用して問題が解決するかどうかを試してみてください。
- 問題が解決しない場合は、[ログを収集](troubleshooting.md#collecting-logs)してフィードバックを提出してください。

## <a name="cannot-find-microphone"></a>マイクが見つからない

- まず、デバイス マネージャーでマイク配列が列挙されていることを確認します。
- デバイスが列挙されており、その他の点では Windows で正しく機能している場合、問題はファームウェアの更新後に Windows によって別のコンテナー ID が深度カメラに割り当てられたことである可能性があります。
- デバイス マネージャーに移動し、[Azure Kinect Microphone Array]\(Azure Kinect マイク配列\) を右クリックして、[デバイスのアンインストール] を選択することで、これをリセットしてみてください。 完了したら、センサーを取り外して再接続します。

    ![Azure Kinect マイク配列](./media/resources/mic-not-found.png)

- その後、Azure Kinect ビューアーを再起動して、もう一度実行してください。

## <a name="device-firmware-update-issues"></a>デバイス ファームウェアの更新に関する問題

- 更新後に正しいバージョン番号が報告されない場合は、デバイスの電源をオフにすることが必要になる場合があります。
- ファームウェアの更新が中断されると、正常な状態でなくなり、列挙されなくなる場合があります。 デバイスを取り外して再接続し、60 秒間待って復旧できるかどうかを確認します。
できない場合は、[出荷時の設定にリセット](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)してください。

## <a name="image-quality-issues"></a>画質に関する問題

- [Azure Kinect ビューアー](azure-kinect-viewer.md)を開始し、デバイスの位置をチェックして干渉がないかどうか、センサーがブロックされていないか、レンズが汚れていないかを確認します。
- 特定のモードで問題が発生している場合は、異なる動作モードを試して絞り込んでみてください。
- 画質の問題をチームと共有するには、次のようにします。

1) [Azure Kinect ビューアー](azure-kinect-viewer.md)でビューを一時停止し、スクリーンショットを撮る
2) [Azure Kinect レコーダー](azure-kinect-recorder.md) (例: `k4arecorder.exe -l 5 -r 5 output.mkv`) を使用して録画する

## <a name="inconsistent-or-unexpected-device-timestamps"></a>一致しない、または予期しないデバイスのタイムスタンプ

```k4a_device_set_color_control``` を呼び出すと、一時的にタイミングの変更が発生し、安定するまでに数キャプチャかかる場合があります。 新しい各イメージで内部的なタイミング計算がリセットされないように、イメージ キャプチャ ループで API を呼び出さないでください。 代わりに、カメラを開始する前、またはイメージ キャプチャ ループ内の値を変更する必要がある場合にのみ、API を呼び出してください。 特に ```k4a_device_set_color_control(K4A_COLOR_CONTROL_AUTO_EXPOSURE_PRIORITY)``` は呼び出さないでください。

## <a name="usb3-host-controller-compatibility"></a>USB3 ホスト コントローラーの互換性

デバイス マネージャーでそのデバイスが列挙されていない場合、サポートされていない USB3 コントローラーに接続されていることが原因である可能性があります。 

Windows 上の Azure Kinect DK では、**Intel**、**Texas Instruments (TI)** 、および **Renesas** の "*ホスト コントローラーのみがサポートされています*"。 Windows プラットフォーム上の Azure Kinect SDK は、1 つの統合されたコンテナー ID に依存しており、同じデバイス上に物理的に配置されている深度、カラー、およびオーディオ デバイスを SDK が検出できるように、USB 2.0 および 3.0 デバイスにまたがる必要があります。 Linux では、そのプラットフォームはコンテナー ID よりもデバイスのシリアル番号により依存するため、より多くのホスト コントローラーがサポートされる可能性があります。 

USB ホスト コントローラーのトピックは、PC に複数のホスト コントローラーがインストールされている場合に、さらに複雑になります。 ホスト コントローラーが混在していると、一部のポートが正常に動作する一方で、他のポートがまったく動作しない問題が発生する可能性があります。 ポートがケースにどのように配線されているかによっては、前面のポートすべてで Azure Kinect との接続に問題が見られる場合があります。

**Windows:** どのホスト コントローラーがあるかを確認するには、デバイス マネージャーを開き、次のようにします。

1. [表示] -> [デバイス (種類別)] 
2. Azure Kinect が接続された状態で、[カメラ] -> [Azure Kinect 4K Camera]\(Azure Kinect 4K カメラ\) を選択する
3. [表示] -> [デバイス (接続別)]

![USB ポートに関するトラブルシューティング](./media/resources/usb-troubleshooting.png)

PC でどの USB ポートが接続されているかの理解を深めるには、その PC の異なる USB ポートに Azure Kinect DK を接続するときに、USB ポートごとにこれらの手順を繰り返します。

## <a name="depth-camera-auto-powers-down"></a>深度カメラの自動電源切断

イメージ深度データを計算するために深度カメラによって使用されるレーザーには、寿命に限りがあります。 レーザーの寿命を最長にするために、深度カメラは深度データが消費されていないタイミングを検出します。 深度カメラが数分間にわたってストリーミングされているが、ホスト PC がデータを読み取っていないとき、そのデバイスの電源が切断されます。 また、これは複数デバイスの同期にも影響します。そこでは、下位デバイスが、深度カメラがストリーミングされており、深度フレームがマスター デバイスによるキャプチャの同期の開始を積極的に支援するために待機している状態で起動します。 複数デバイスでのキャプチャのシナリオにおいてこの問題を回避するために、最初の下位デバイスが起動されてから 1 分以内にマスター デバイスが起動されるようにしてください。 

## <a name="using-body-tracking-sdk-with-unreal"></a>Unreal での Body Tracking SDK の使用

Unreal で Body Tracking SDK を使用するには、`<SDK Installation Path>\tools` を環境変数 `PATH` に追加しており、`dnn_model_2_0.onnx` と `cudnn64_7.dll` を `Program Files/Epic Games/UE_4.23/Engine/Binaries/Win64` にコピーしていることを確認します。

## <a name="using-azure-kinect-on-headless-linux-system"></a>ヘッドレス Linux システムで Azure Kinect を使用する

Linux 上の Azure Kinect 深度エンジンでは、OpenGL を使用します。 OpenGL には、ウィンドウ インスタンスが必要です。これには、モニターをシステムに接続することが要求されます。 この問題の回避策は次のようになります。

1. 使用する予定のユーザー アカウントに対して自動ログインを有効にします。 自動ログインを有効にする方法については、[この](https://vitux.com/how-to-enable-disable-automatic-login-in-ubuntu-18-04-lts/)記事を参照してください。
2. システムの電源を切り、モニターを切断し、システムの電源を入れます。 自動ログインでは、x サーバー セッションが強制的に作成されます。
3. ssh 経由で接続し、DISPLAY 環境変数 `export DISPLAY=:0` を設定します。
4. Azure Kinect アプリケーションを起動します。

[xtrlock](http://manpages.ubuntu.com/manpages/xenial/man1/xtrlock.1x.html) ユーティリティを使用すると、自動ログイン後すぐに画面をロックすることができます。 スタートアップ アプリケーションまたは systemd サービスに次のコマンドを追加します。

`bash -c “xtrlock -b”`

## <a name="missing-c-documentation"></a>C# ドキュメントがない

Sensor SDK C# ドキュメントは、[こちら](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/namespace_microsoft_1_1_azure_1_1_kinect_1_1_sensor.html)から入手できます。

Body Tracking SDK C# ドキュメントは、[こちら](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/namespace_microsoft_1_1_azure_1_1_kinect_1_1_body_tracking.html)から入手できます。

## <a name="specifying-onnx-runtime-execution-environment"></a>ONNX Runtime 実行環境の指定

Body Tracking SDK では、CPU、CUDA、DirectML (Windows のみ)、および TensorRT の実行環境がサポートされており、姿勢推定モデルを推論できます。 `K4ABT_TRACKER_PROCESSING_MODE_GPU` は、Linux では CUDA、Windows では DirectML が既定になります。 特定の実行環境を選択するために、`K4ABT_TRACKER_PROCESSING_MODE_GPU_CUDA`、`K4ABT_TRACKER_PROCESSING_MODE_GPU_DIRECTML`、`K4ABT_TRACKER_PROCESSING_MODE_GPU_TENSORRT` の 3 つの追加のモードが追加されました。

> [!NOTE]  
> ONNX Runtime は、アクセラレートされないオペコードの警告を表示します。 これらは無視しても問題ありません。

ONNX Runtime には、TensorRT モデルのキャッシュを制御するための環境変数が含まれています。 推奨値は次のとおりです。
- ORT_TENSORRT_ENGINE_CACHE_ENABLE=1 
- ORT_TENSORRT_CACHE_PATH="pathname"

本文の追跡を開始する前に、フォルダーを作成する必要があります。

> [!IMPORTANT]  
> TensorRT では、推論の前にモデルが事前に処理されるため、他の実行環境と比較すると、開始時間が長くかかります。 エンジン キャッシュはこれを最初の実行に限定しますが、これは実験的なものであり、モデル、ONNX Runtime バージョン、TensorRT バージョン、および GPU モデルに固有です。

TensorRT 実行環境では、FP32 (既定) と FP16 の両方がサポートされます。 FP16 は、精度の低下を最小限に抑えるために、最大 2 倍のパフォーマンスを引き換えにします。 FP16 を指定するには、次のようにします。
- ORT_TENSORRT_FP16_ENABLE=1

## <a name="required-dlls-for-onnx-runtime-execution-environments"></a>ONNX Runtime 実行環境に必要な DLL

|モード      | CUDA 11.1            | CUDNN 8.0.5          | TensorRT 7.2.1       |
|----------|----------------------|----------------------|----------------------|
| CPU      | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           |                      |                      |
|          | cublas64_11          |                      |                      |
|          | cublasLt64_11        |                      |                      |
| CUDA     | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           | cudnn_ops_infer64_8  |                      |
|          | cublas64_11          | cudnn_cnn_infer64_8  |                      |
|          | cublasLt64_11        |                      |                      |
| DirectML | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           |                      |                      |
|          | cublas64_11          |                      |                      |
|          | cublasLt64_11        |                      |                      |
| TensorRT | cudart64_110         | cudnn64_8            | nvinfer              |
|          | cufft64_10           | cudnn_ops_infer64_8  | nvinfer_plugin       |
|          | cublas64_11          | cudnn_cnn_infer64_8  | myelin64_1           |
|          | cublasLt64_11        |                      |                      |
|          | nvrtc64_111_0        |                      |                      |
|          | nvrtc-builtins64_111 |                      |                      |

## <a name="next-steps"></a>次のステップ

[その他のサポート情報](support.md)