---
title: チュートリアル - Azure IoT ビデオ分析 - オブジェクトとモーションの検出 | Microsoft Docs
description: このチュートリアルでは、IoT Central のビデオ分析 - オブジェクトとモーションの検出アプリケーション テンプレートをデプロイして使用する方法について説明します。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 09/02/2021
ms.openlocfilehash: 026c1794f678c5d194b0e5174986f9f962508647
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473750"
---
# <a name="tutorial-deploy-and-walk-through-the-video-analytics---object-and-motion-detection-application-template"></a>チュートリアル: ビデオ分析 - オブジェクトとモーションの検出アプリケーション テンプレートをデプロイして調べる

"*ビデオ分析 - オブジェクトとモーション検出*" アプリケーションの主な概要として、**ビデオ分析 - オブジェクトとモーション検出** アプリケーション テンプレートにより、ライブ ビデオ分析機能を含む IoT ソリューションを構築できます。

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="ビデオ分析オブジェクトとモーション検出コンポーネントの概要の図。":::

ビデオ分析ソリューションの主要なコンポーネントは次のとおりです。

### <a name="live-video-analytics-lva"></a>ライブ ビデオ分析 (LVA)

LVA では、エッジとクラウドにまたがるインテリジェントなビデオ アプリケーションを構築するためのプラットフォームを提供します。 このプラットフォームにより、エッジとクラウドにまたがるインテリジェントなビデオ アプリケーションを構築できます。 このプラットフォームは、ライブ ビデオをキャプチャ、録画、分析し、その結果 (ビデオやビデオ分析など) を Azure サービスに発行する機能を備えています。 Azure サービスは、クラウドやエッジで実行されている場合があります。 このプラットフォームは、ビデオ分析を使用して IoT ソリューションを強化するために使用できます。

詳細については、GitHub の「[Live Video Analytics](https://github.com/Azure/live-video-analytics)」を参照してください。

### <a name="iot-edge-lva-gateway-module"></a>IoT Edge LVA ゲートウェイ モジュール

IoT Edge LVA ゲートウェイ モジュールでは、カメラを新しいデバイスとしてインスタンス化し、IoT デバイス クライアント SDK を使用して、それらを直接 IoT Central に接続します。

この参照実装では、デバイスはエッジからの対称キーを使用してソリューションに接続します。 デバイスの接続の詳細については、「[Azure IoT Central に接続する](../core/concepts-get-connected.md)」を参照してください

### <a name="media-graph"></a>メディア グラフ

メディア グラフでは、メディアのキャプチャ元、処理方法、および結果の配信場所を定義できます。 メディア グラフを構成するには、必要な方法でコンポーネントまたはノードを接続します。 詳細については、GitHub の「[メディア グラフ](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph)」を参照してください。

次のビデオでは、"_ビデオ分析 - 物体とモーションの検出アプリケーション テンプレート_" を使用して IoT Central ソリューションをデプロイする方法を段階的に説明しています。

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

この一連のチュートリアルでは、次の方法を学習します。

> [!div class="checklist"]
> * アプリケーションの配置
> * アプリケーションに接続する IoT Edge インスタンスのデプロイ
> * アプリケーションの監視と管理

## <a name="prerequisites"></a>前提条件

* このアプリをデプロイするために必要な前提条件は特にありません。
* 無料価格プランを使用するか、Azure サブスクリプションを使用することができます。

## <a name="deploy-the-application"></a>アプリケーションをデプロイする

ビデオ分析アプリケーション テンプレートを使用して IoT Central アプリケーションをデプロイするには、次の手順を実行します。

1. [Azure IoT Central でのビデオ分析アプリケーションの作成 (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) または [Azure IoT Central でのビデオ分析の作成 (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) に関するいずれかのチュートリアルを完了して、以下を行います。
    - Azure Media Services アカウントを作成する
    - ビデオ分析 - 物体とモーションの検出アプリケーション テンプレートから、IoT Central アプリケーションを作成します。
    - IoT Central アプリケーションでゲートウェイ デバイスを構成します。 ゲートウェイにより、カメラ デバイスがアプリケーションに接続できます。

1. 「[ビデオ分析用の IoT Edge インスタンスを作成する (Linux VM)](tutorial-video-analytics-iot-edge-vm.md)」または「[チュートリアル: ビデオ分析用の IoT Edge インスタンスを作成する (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md)」のいずれかのチュートリアルを完了して、以下を行います。
    - Azure IoT Edge ランタイムがインストールされた Azure VM を作成します。ビデオ分析モジュールをホストするように IoT Edge インストールを準備します。
    - IoT Edge デバイスを IoT Central アプリケーションに接続します。

1. [ビデオ分析アプリケーションの監視と管理](tutorial-video-analytics-manage.md)チュートリアルを実行して、
    - 物体とモーションの検出カメラを IoT Central アプリケーションのゲートウェイに追加します。
    - カメラの処理を開始します。
    - AMS で、キャプチャされたビデオを表示するために、ローカル メディア プレーヤーをインストールします。
    - 検出された物体を示すキャプチャされたビデオを表示します。
    - 整理します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーションを使い終えたら、次の手順で、作成したリソースをすべて削除することができます。

1. IoT Central アプリケーションの **[管理]** セクションの **[お客様のアプリケーション]** ページに移動します。 次に、 **[削除]** を選択します。
1. Azure portal で、**lva-rg** リソース グループを削除します。
1. ローカル コンピューターで、**amp-viewer** Docker コンテナーを停止します。

## <a name="next-steps"></a>次のステップ

ビデオ分析アプリケーション テンプレートをデプロイして使用する手順の概要を把握したら、

> [!div class="nextstepaction"]
> [Azure IoT Central (YOLO v3) でビデオ分析アプリケーションを作成する](tutorial-video-analytics-create-app-yolo-v3.md)方法に関するページか、

> [!div class="nextstepaction"]
> [Azure IoT Central (OpenVINO&trade;) でビデオ分析アプリケーションを作成する](tutorial-video-analytics-create-app-openvino.md)方法に関するページに進んでください。