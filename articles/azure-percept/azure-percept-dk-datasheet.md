---
title: Azure Percept DK のデータシート
description: 詳細なデバイス仕様については、Azure Percept DK のデータシートを確認してください
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 1d94dea1424ad31dab5c5b0ea338aa07f27784ac
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660671"
---
# <a name="azure-percept-dk-datasheet"></a>Azure Percept DK のデータシート

|製品仕様           |値     |
|--------------------------------|--------|
|工業デザイン               |統合 80/20 1010 シリーズ マウント |
|パフォーマンス                     |0.7 TOPS (Azure Percept Vision)|
|対象業界               |小売 <br> 製造 <br> サービス業 <br> スマート ビルディング <br> 自動 |
|主要なシナリオ                  |最初の AI フレームを 10 分以内ですぐに使用できる <br> プロトタイプを 30 分以内ですぐに使用できる <br> 初期トレーニング データをキャプチャしてラベルを付ける <br> 基本的な AI モデルのカスタマイズ <br> 高度な AI モデルのカスタマイズ |
|ボックスに含まれているもの                 |1 x Azure Percept DK キャリア ボード  <br> 1 x [Azure Percept Vision](./azure-percept-vision-datasheet.md) <br> 1 x RGB センサー (カメラ) <br> 1 x USB 3.0 Type C ケーブル <br> 1 x DC 電源ケーブル <br> 1 x AC/DC コンバーター <br> 2 x Wi-Fi アンテナ  |
|OS                              |[CBL-Mariner](https://github.com/microsoft/CBL-Mariner)           |
|管理コントロール プレーン        |Azure Device Update (ADU) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)          |
|サポートされているソフトウェアとサービス |Azure Device Update <br> [Azure IoT](https://azure.microsoft.com/overview/iot/) <br> [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Central](https://azure.microsoft.com/services/iot-central/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) および [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1) <br> [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) <br> [Azure Mariner OS と接続性](https://github.com/microsoft/CBL-Mariner) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX Runtime](https://www.onnxruntime.ai/) <br> [TensorFlow](https://www.tensorflow.org/) <br> [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) <br> Azure PnP <br> [Azure Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/) <br> [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) <br> [Power BI](https://powerbi.microsoft.com/)      |
|汎用プロセッサ               |NXP iMX8m (Azure Percept DK キャリア ボード)        |
|AI アクセラレーション                 |1 x Intel Movidius Myriad X Integrated ISP (Azure Percept Vision) |
|センサーと視覚インジケーター   |Omni Vision 5670 カメラ <br> GSO GS8882AA レンズ (解像度: 30FPS で 5MP、距離: 50 cm - 無限大、FoV: 120 度、色: ワイド ダイナミック レンジ、固定フォーカス ローリング シャッター)          |
|セキュリティ                        |TPM 2.0 Nuvoton NCPT750 |
|接続                    |Realtek RTL882CE のシングルチップ コントローラーによる Wi-Fi と Bluetooth     |
|ストレージ                         |16GB     |
|メモリ                          |4 GB     |
|ポート                           |1 x イーサネット <br> 2 x USB-A 3.0 <br> 1 x USB-C <br> 1 x HDMI (非アクティブ)     |
|認定資格                   |CE <br> FCC     |
|動作温度           |-20 から 70°C     |
|相対湿度               |8% から 90%    |
|電源                    |3\.42 A の 19VDC (65 W) |
|振動                       |1 から 300 Hz で 2.0 Grms。     |