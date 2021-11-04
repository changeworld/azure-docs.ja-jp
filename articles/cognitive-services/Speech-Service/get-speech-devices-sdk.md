---
title: Speech Devices SDK を取得する
titleSuffix: Azure Cognitive Services
description: 音声サービスは、さまざまなデバイスやオーディオ ソースと連携します。 適合するハードウェアやソフトウェアで音声アプリケーションを次のレベルに引き上げることができます。 この記事では、Speech Devices SDK を利用して開発を始める方法について説明します。
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: eur
ms.openlocfilehash: 76ef359ea125ccd17de1ff0670390eb7e89d4290
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509300"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Cognitive Services Speech Devices SDK を取得する

Speech Devices SDK は、専用の開発キットやさまざまなマイク アレイの構成に対応するように設計された調整済みのライブラリです。

## <a name="choose-a-development-kit"></a>開発キットを選択する

|デバイス|仕様|説明|シナリオ|
|--|--|--|--|
|[Azure Percept Audio DK](../../azure-percept/overview-azure-percept-audio.md)<br>[設定](../../azure-percept/quickstart-percept-dk-unboxing.md) / [クイックスタート](../../azure-percept/quickstart-percept-audio-setup.md)![Azure Percept Audio DK](./media/speech-devices-sdk/azure-percept-audio.png)|XMOS コーデックを使用した 4 つのマイクの線形配列。 <br> Linux| ご使用のエッジ デバイスに音声人工知能 (AI) 機能を追加するアクセサリ デバイス。 これには、事前に構成された音声プロセッサと 4 つのマイクの線形配列が含まれています。これにより、Azure Cognitive Services の支援を受けて、音声コマンド、キーワード認識、遠距離音声を使用できます。 このデバイスには Azure Percept DK、Azure Percept Studio、その他の Azure エッジ管理サービスが付属し、Microsoft の最も強力でコンパクトなオールインワンの音声デバイス SDK とのスムーズな統合を実現します。|会話の文字起こし、ロボット工学、スマート ビルディング、製造、農業|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[設定](../../kinect-dk/set-up-azure-kinect-dk.md) / [クイック スタート](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 マイク アレイ RGB および深度カメラ。 <br>[Windows](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows)/[Linux](./speech-devices-sdk-quickstart.md?pivots=platform-linux%253fpivots%253dplatform-linux)|高度な人工知能 (AI) センサーを採用した、先進的なコンピューター ビジョンと音声モデルを構築するための空間コンピューティング開発者キット。 クラス最高の空間マイク アレイ、ビデオ カメラ付きの深度カメラ、方位センサーが 1 つにまとめられています。Azure Cognitive Services とのシームレスな統合を実現する、さまざまな計算の種類に対応した複数のモード、オプション、SDK を備えたオールインワンの小型デバイスです。|会話の文字起こし、ロボット工学、スマート ビルディング|
|[Urbetter Dev Kit](http://www.urbetter.com/products_56/278.html)![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 マイク アレイ、ARM SOC、WIFI、イーサネット、HDMI、USB カメラ。 <br>Linux|Microsoft マイク アレイを採用し、HDMI/Ethernet やその他の USB 周辺機器などの拡張 I/O をサポートする業界レベルの Speech Devices SDK <br> [Urbetter に問い合わせる](http://www.urbetter.com/products_56/278.html)|会話の文字起こし、教育、病院、ロボット、OTT Box、音声エージェント、Drive Thru|
|[Roobo Smart Audio Dev Kit](http://ddk.roobo.com)<br>[設定](speech-devices-sdk-roobo-v1.md) / [クイックスタート](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)![Roobo Smart Audio Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 マイク アレイ、ARM SOC、WIFI、オーディオ出力、IO。 <br>[Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)|Microsoft Mic Array およびフロント プロセス SDK を採用する、高品質の文字起こしとスピーチ シナリオ開発用の初の Speech Devices SDK|会話の文字起こし、スマート スピーカー、音声エージェント、ウェアラブル|
|Roobo Smart Audio Dev Kit 2<br>[セットアップ](speech-devices-sdk-roobo-v2.md)<br>![Roobo Smart Audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 マイク アレイ、ARM SOC、WIFI、Bluetooth、IO。 <br>Linux|コスト効率の高いリファレンス設計で代替 OS などの機能を提供する第 2 世代の Speech Devices SDK。|会話の文字起こし、スマート スピーカー、音声エージェント、ウェアラブル|


## <a name="download-the-speech-devices-sdk"></a>Speech Devices SDK をダウンロードする

[Speech Devices SDK](./speech-devices-sdk.md) をダウンロードします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Speech Devices SDK を使ってみる](./speech-devices-sdk-quickstart.md?pivots=platform-android)
