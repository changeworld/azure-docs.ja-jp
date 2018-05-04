---
title: Azure IoT Hub のハウツー | Microsoft Docs
description: 開発者による IoT Hub のさまざまな機能の使用方法
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: dobett
ms.openlocfilehash: 9b112d2d7fc1756b74e98335831175f5d4c13320
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-use-azure-iot-hub"></a>Azure IoT Hub の使用方法

IoT Hub サービス向けの開発方法を学ぶ手段については、さまざまな選択肢があります。

* IoT Hub の機能を詳しく説明した概念記事を読みます。
* IoT Hub の各種機能について説明したいずれかのチュートリアルに従います。

## <a name="developer-guide"></a>開発者ガイド

開発者の場合は、[開発者ガイド][lnk-devguide]に含まれている IoT Hub に関する概念的な詳しいガイダンスを参照してください。 このガイドの内容は次のとおりです。

* IoT Hub の全機能についての詳しい説明。各機能の使い方がわかりやすく説明されています。
* 複数の選択肢があるときの選び方の指針。

## <a name="tutorials"></a>チュートリアル

実践的な演習を通じて IoT Hub の特定の機能について学習する場合は、複数あるチュートリアルから選択できます。 これらのチュートリアルの多くは、複数のプログラミング言語に対応しています。 以下のチュートリアルがあります。

- [ルートを使用した IoT Hub のデバイスからクラウドへのメッセージの処理][lnk-routes-tutorial]。 このチュートリアルでは、IoT Hub のルーティング規則を使用して、簡単な構成ベースの方法でデバイスからクラウドへのメッセージをディスパッチする方法について説明します。

- [IoT Hub を使用したクラウドからデバイスへのメッセージの送信][lnk-c2d-tutorial]。 このチュートリアルでは、IoT Hub を使用してクラウドからデバイスにメッセージを送信し、クラウドからデバイスへのメッセージをデバイスで受信する方法について説明します。

- [IoT Hub でデバイスからクラウドにファイルをアップロードする][lnk-upload-tutorial]。 このチュートリアルでは、IoT Hub のファイル アップロード機能を使用する方法について説明します。

- [デバイス ツインの使用][lnk-twin-tutorial]。 このチュートリアルでは、デバイス ツイン、報告されるプロパティ、必要なプロパティ、およびタグについて説明します。 デバイス ツインを使用して、デバイスと値を同期します。

- [ダイレクト メソッドの使用][lnk-methods-tutorial]。 このチュートリアルでは、ダイレクト メソッドの使用方法について説明します。 シミュレートされたデバイスでダイレクト メソッドのハンドラーを追加し、IoT Hub からダイレクト メソッドを呼び出します。

- [デバイス管理の開始][lnk-dm-tutorial]。 このチュートリアルでは、ツインやダイレクト メソッドなどの主要なデバイス管理機能を使用する方法について説明します。 これらの機能を使用して、シミュレートされたデバイスをリモートから再起動します。

- [必要なプロパティを使用してデバイスを構成する][lnk-properties-tutorial]。 このチュートリアルでは、デバイス ツインの必要なプロパティと報告されるプロパティを使用して、デバイスをリモートで構成する方法について説明します。

- [デバイス管理を使用してデバイスのファームウェアを更新する][lnk-jobs-tutorial]。 このチュートリアルでは、ツインやダイレクト メソッドなどの主要なデバイス管理機能を使用する方法について説明します。 これらの機能を使用して、デバイスのファームウェアをリモートから更新する方法を紹介します。

- [ジョブのスケジュールとブロードキャスト][lnk-schedule-tutorial]。 このチュートリアルでは、必要なプロパティとダイレクト メソッドを使用して、スケジュールされた時刻に複数のデバイスとやり取りする方法について説明します。

## <a name="next-steps"></a>次の手順

IoT Hub サービスの詳細については、[開発者ガイド][lnk-devguide]を参照してください。

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-routes-tutorial]: ./iot-hub-csharp-csharp-process-d2c.md
[lnk-c2d-tutorial]: ./iot-hub-csharp-csharp-c2d.md
[lnk-upload-tutorial]: ./iot-hub-csharp-csharp-file-upload.md
[lnk-twin-tutorial]: ./iot-hub-node-node-twin-getstarted.md
[lnk-methods-tutorial]: ./iot-hub-node-node-direct-methods.md
[lnk-dm-tutorial]: ./iot-hub-node-node-device-management-get-started.md
[lnk-properties-tutorial]: ./iot-hub-node-node-twin-how-to-configure.md
[lnk-jobs-tutorial]: ./iot-hub-node-node-firmware-update.md
[lnk-schedule-tutorial]: ./iot-hub-node-node-schedule-jobs.md