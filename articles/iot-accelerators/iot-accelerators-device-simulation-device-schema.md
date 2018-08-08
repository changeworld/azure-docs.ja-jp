---
title: デバイス シミュレーション ソリューションのデバイス スキーマ - Azure | Microsoft Docs
description: この記事では、デバイス シミュレーション ソリューションのシミュレートされたデバイスを定義する JSON スキーマについて説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: a27624d4c7a0bde4b33aefe8d05881b743ce397d
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295286"
---
# <a name="understand-the-device-model-schema"></a>デバイス モデル スキーマについて

デバイス シミュレーション ソリューション アクセラレータを使用して、IoT Hub を使用するソリューション向けのテスト負荷を生成できます。 デバイス シミュレーション ソリューションをデプロイすると、シミュレートされたデバイスのコレクションが自動的にプロビジョニングされます。 既存のシミュレートされたデバイスをカスタマイズすることも、独自のデバイスを作成することもできます。

この記事では、シミュレートされたデバイスの機能と動作を指定するデバイス モデル スキーマについて説明します。 デバイス モデルは JSON ファイルに格納されます。

次の記事は、現在の記事に関連しています。

* 「[デバイス モデルの動作の実装](iot-accelerators-device-simulation-device-behavior.md)」では、シミュレートされたデバイスの動作を実装するために使用する JavaScript ファイルについて説明します。
* 「[新しいシミュレートされたデバイスの作成](iot-accelerators-remote-monitoring-test.md)」では、すべてをうまくまとめて、新しいシミュレートされたデバイスの種類をソリューションにデプロイする方法を示しています。

この記事では、次のことについて説明します:

>[!div class="checklist"]
> * JSON ファイルを使用して、シミュレートされたデバイス モデルを定義する
> * シミュレートされたデバイスのプロパティを指定する
> * シミュレートされたデバイスが送信するテレメトリを指定する
> * デバイスが応答する cloud-to-device メソッドを指定する

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>次の手順

この記事では、独自のシミュレートされたカスタム デバイス モデルを作成する方法について説明しました。 この記事では、次の方法について説明しました。

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * JSON ファイルを使用して、シミュレートされたデバイス モデルを定義する
> * シミュレートされたデバイスのプロパティを指定する
> * シミュレートされたデバイスが送信するテレメトリを指定する
> * デバイスが応答する cloud-to-device メソッドを指定する

JSON スキーマについて理解できたら、次のステップとして、[シミュレートされたデバイスの動作を実装する](iot-accelerators-device-simulation-device-behavior.md)方法について学習することをお勧めします。

デバイス シミュレーション ソリューションについての開発者情報については、[開発者向けリファレンス ガイド](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide)を参照してください。
