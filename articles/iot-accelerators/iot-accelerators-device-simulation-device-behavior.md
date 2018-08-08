---
title: デバイス シミュレーション ソリューションでのシミュレートされたデバイスの動作 - Azure | Microsoft Docs
description: この記事では、JavaScript を使用して、デバイス シミュレーション ソリューションでのシミュレートされたデバイスの動作を定義する方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: b68550bce1f4e3fbe3845c21598720083c8e384c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295278"
---
# <a name="implement-the-device-model-behavior"></a>デバイス モデルの動作の実装

記事「[デバイス モデル スキーマについて](iot-accelerators-device-simulation-device-schema.md)」では、シミュレートされたデバイス モデルを定義するスキーマについて説明しました。 この記事では、シミュレートされたデバイスの動作を実装する 2 種類の JavaScript ファイルに言及しました。

- **状態**: デバイスの内部状態を更新するために一定の間隔で実行される JavaScript ファイル。
- **メソッド**: ソリューションがデバイスに対するメソッドを呼び出したときに実行される JavaScript ファイル。

この記事では、次のことについて説明します:

>[!div class="checklist"]
> * シミュレートされたデバイスの状態を制御する
> * シミュレートされたデバイスが、それが接続されている IoT Hub からのメソッド呼び出しに応答する方法を定義する
> * スクリプトをデバッグする

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>次の手順

この記事では、独自のシミュレートされたカスタム デバイス モデルの動作を定義する方法について説明しました。 この記事では、次の方法について説明しました。

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * シミュレートされたデバイスの状態を制御する
> * シミュレートされたデバイスが、それが接続されている IoT Hub からのメソッド呼び出しに応答する方法を定義する
> * スクリプトをデバッグする

シミュレートされたデバイスの動作を指定する方法について理解できたら、次のステップとして、[シミュレートされたデバイスを作成する](iot-accelerators-remote-monitoring-test.md)方法について学習することをお勧めします。

デバイス シミュレーション ソリューションについての開発者情報については、[開発者向けリファレンス ガイド](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide)を参照してください。
