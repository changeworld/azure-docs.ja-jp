---
title: Azure Video Analyzer を IoT Edge デバイスにデプロイする - Azure
description: この記事では、IoT Edge デバイスに Azure Video Analyzer をデプロイする際に役立つ手順を示します。 たとえば、ローカルの Linux マシンにアクセスできる場合にこれを行います。
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6bd045a6cbc1d85790119564a00783b74f759b97
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556599"
---
# <a name="deploy-azure-video-analyzer-to-an-iot-edge-device"></a>Azure Video Analyzer を IoT Edge デバイスにデプロイする

[!INCLUDE [header](includes/edge-env.md)]

この記事では、Azure Video Analyzer エッジ モジュールを、これまでに他のモジュールがインストールされていない IoT Edge デバイスにデプロイする方法について説明します。 この記事の手順を完了すると、Video Analyzer アカウントが作成され、IoT Edge デバイスには、RTSP 対応の IP カメラをシミュレートするモジュールと共に Video Analyzer モジュールがデプロイされます。 このプロセスは、Video Analyzer のクイックスタートやチュートリアルで使用することを目的としています。 運用環境で使用するために Video Analyzer モジュールをデプロイする場合は、「[運用環境の準備状況とベスト プラクティス](production-readiness.md)」を確認する必要があります。

> [!NOTE]
> この記事で説明されているプロセスでは、自分の IoT Edge デバイスにインストールされているエッジ モジュールがある場合にアンインストールされます。

## <a name="prerequisites"></a>前提条件

* [サポートされている Linux オペレーティング システム](../../../iot-edge/support.md#operating-systems)のいずれかを実行している x86-64 または ARM64 デバイス
* アクティブなサブスクリプションが含まれる Azure アカウント
* [IoT Hub を作成してセットアップします](../../../iot-hub/iot-hub-create-through-portal.md)
* [IoT Edge デバイスを登録します](../../../iot-edge/how-to-register-device.md)
* [Debian ベースの Linux システムに Azure IoT Edge ランタイムをインストールする](../../../iot-edge/how-to-install-iot-edge.md)


## <a name="create-resources-on-iot-edge-device"></a>IoT Edge デバイス上にリソースを作成する

Azure Video Analyzer モジュールは、特権のないローカル ユーザー アカウントを使用して IoT Edge デバイス上で実行されるよう構成する必要があります。 このモジュールには、アプリケーションの構成データを格納するための特定のローカル フォルダーが必要となります。 この使用法ガイドでは、分析のためにビデオ フィードをリアルタイムで AVA モジュールに中継する [RTSP シミュレーター](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)を利用しています。 このシミュレーターは入力として、事前に録画されたビデオ ファイルを input ディレクトリから受け取ります。 次のスクリプトでは、Microsoft が提供するクイックスタートやチュートリアルで使用できるようにデバイスを準備します。

https://aka.ms/ava/prepare-device

`bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`

上で使用した prep-device スクリプトにより、入力および構成フォルダーの作成、ビデオ入力ファイルのダウンロード、および適切な特権を設定したユーザー アカウントの作成というタスクが自動化されます。 コマンドが正常に完了すると、次のフォルダーがエッジ デバイス上に作成されます。 

* `/home/localedgeuser/samples`
* `/home/localedgeuser/samples/input`
* `/var/lib/videoanalyzer`
* `/var/media`

    ライブ ビデオのシミュレートに使用されるビデオ ファイル ("*.mkv") は、/home/localedgeuser/samples/input フォルダーにあります。 

## <a name="creating-azure-resources-and-deploying-edge-modules"></a>Azure リソースの作成とエッジ モジュールのデプロイ
次の手順では、必要な Azure リソース (Video Analyzer アカウント、ストレージ アカウント、ユーザー割り当てマネージド ID) を作成し、Video Analyzer エッジ モジュールを Video Analyzer アカウントに登録して、Video Analyzer エッジ モジュールと RTSP シミュレーター モジュールを IoT Edge デバイスにデプロイします。

**[Azure へのデプロイ]** をクリックします

> [!WARNING]
> これは、Percept DK など、エッジ モジュールが既にインストールされている IoT Edge デバイスでは使用しないでください。 また、Azure Stack Edge でもサポートされていません。

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava/click-to-deploy/form)

1. **サブスクリプション** を選択します
2. 希望する **リージョン** を選択します
3. IoT ハブと IoT Edge デバイスが属している **リソース グループ** を選択します
4. **[Do you need an edge device?]\(エッジ デバイスが必要ですか?\)** のドロップダウン メニューで、**_[Use an existing edge device]\(既存のエッジ デバイスを使用する\)_** オプションを選択します
5. **[次へ]** をクリックします
![最初のデプロイ フォームのスクリーンショット](./media/deploy-iot-edge-device/project-details.png)

1. 自分の IoT Edge デバイスが接続されている **既存の IoT ハブ名** を選択します
1. **[次へ]** をクリックします
![2 番目のデプロイ フォームのスクリーンショット](./media/deploy-iot-edge-device/iot-hub-name.png)

1. 最後のページで **[作成]** をクリックします

Azure リソースが作成され、エッジ モジュールがデプロイされるまで、しばらく時間がかかる場合があります。

### <a name="verify-your-deployment"></a>デプロイを確認する

デプロイを作成したら、Azure portal で IoT ハブの [IoT Edge デバイス] ページに移動します。

1. デプロイで対象にした Azure IoT Edge デバイスを選択して、その詳細を開きます。
2. デバイスの詳細で、モジュールが **[デプロイで指定] および [デバイス別に報告]** の両方として表示されていることを確認します。

モジュールがデバイス上で開始され、IoT Hub にレポートされるまで、しばらく時間がかかる場合があります。 ページを更新して、最新の状態を表示します。
状態コード:200 –OK は、[IoT Edge ランタイム](../../../iot-edge/iot-edge-runtime.md)が正常であり、正常に動作していることを意味します。

![IoT Edge ランタイムの状態値を示しているスクリーンショット。](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>ダイレクト メソッドを呼び出す

次に、ダイレクト メソッドを呼び出してサンプルをテストします。 avaEdge モジュールによって提供されているすべてのダイレクト メソッドについて把握するには、[Azure Video Analyzer 用のダイレクト メソッド](direct-methods.md)に関するページを参照してください。

1. 作成したエッジ モジュールをクリックすると、その構成ページが表示されます。  

    ![エッジ モジュールの構成ページを示しているスクリーンショット。](./media/deploy-iot-edge-device/modules.png)
1. **[ダイレクト メソッド]** メニュー オプションをクリックします。

    > [!NOTE] 
    > 現在のページで確認できるように、[接続文字列] セクションに値を追加する必要があります。 **[設定名]** セクションの値を非表示にしたり変更したりする必要はありません。 パブリックにしておいてかまいません。

    ![ダイレクト メソッド](./media/deploy-iot-edge-device/module-details.png)
1. 次に、`Method Name` ボックスに「pipelineTopologyList」と入力します。
1. さらに、次の JSON ペイロードをコピーしてペイロード ボックスに貼り付けます。
    
   ```
   {
       "@apiVersion": "1.1"
   }
   ```
1. ページの上部にある **[メソッドの呼び出し]** オプションをクリックします
1. `Result` ボックスに状態 200 メッセージが表示されます

    ![200 状態メッセージ](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>次のステップ

[概要 - Azure Video Analyzer のクイックスタート](get-started-detect-motion-emit-events.md)を試してみる

> [!TIP]
> 上記のクイックスタートに進む場合は、Visual Studio Code を使用してダイレクト メソッドを呼び出すときに、既定の `avasample-iot-edge-device` ではなく、この記事で IoT Hub に追加したデバイスを使用します。
