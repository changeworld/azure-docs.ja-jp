---
title: Speech Devices SDK を取得する
titleSuffix: Azure Cognitive Services
description: Speech Services は、さまざまなデバイスやオーディオ ソースと連携します。 適合するハードウェアやソフトウェアで音声アプリケーションを次のレベルに引き上げることができます。 この記事では、Speech Devices SDK を利用して開発を始める方法について説明します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 4c2cff23f66ec704fe7e7c44136160313c10c9c2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020577"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Cognitive Services Speech Devices SDK を取得する

Speech Devices SDK は、専用の開発キットやさまざまなマイク アレイの構成に対応するように設計された調整済みのライブラリです。

## <a name="choose-a-development-kit"></a>開発キットを選択する

|デバイス|仕様|説明|シナリオ|
|--|--|--|--|
|[Roobo Smart Audio Dev Kit](http://ddk.roobo.com)</br>[設定](speech-devices-sdk-roobo-v1.md) / [クイックスタート](speech-devices-sdk-android-quickstart.md)![Roobo Smart Audio Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 マイク アレイ、ARM SOC、WIFI、オーディオ出力、IO。 </br>Android|Microsoft Mic Array およびフロント プロセス SDK を採用する、高品質の文字起こしとスピーチ シナリオ開発用の初の Speech Devices SDK|会話の文字起こし、スマート スピーカー、音声エージェント、ウェアラブル|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 マイク アレイ RGB および深度カメラ。 </br>Windows/Linux|高度な人工知能 (AI) センサーを採用した、先進的なコンピューター ビジョンと音声モデルを構築するための開発者キット。 クラス最高の空間マイク アレイ、ビデオ カメラ付きの深度カメラ、方位センサーが 1 つにまとめられています。さまざまな計算の種類に対応する複数のモード、オプション、SDK を備えたオールインワンの小型デバイスです。|会話の文字起こし、ロボット工学、スマート ビルディング|
|Roobo Smart Audio Dev Kit 2![Roobo Smart Audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 マイク アレイ、ARM SOC、WIFI、Bluetooth、IO。 </br>Linux|コスト効率の高いリファレンス設計で代替 OS などの機能を提供する第 2 世代の Speech Devices SDK。|会話の文字起こし、スマート スピーカー、音声エージェント、ウェアラブル|
|URbetter T11 Development Board![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 マイク アレイ、ARM SOC、WIFI、イーサネット、HDMI、USB カメラ。 </br>Linux|Microsoft マイク アレイを採用し、HDMI/Ethernet やその他の USB 周辺機器などの拡張 I/O をサポートする業界レベルの Speech Devices SDK|会話の文字起こし、教育、病院、ロボット、OTT Box、音声エージェント、Drive Thru|

## <a name="download-the-speech-devices-sdk"></a>Speech Devices SDK をダウンロードする

[Speech Devices SDK](https://aka.ms/sdsdk-download) をダウンロードします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Speech Devices SDK を使ってみる](https://aka.ms/sdsdk-quickstart)
