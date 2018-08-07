---
title: リモート監視ソリューションのデバイス スキーマ - Azure | Microsoft Docs
description: この記事では、リモート監視ソリューションのシミュレートされたデバイスを定義する JSON スキーマについて説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c153153313511640f7969938f63ea9fbe7b0847c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282653"
---
# <a name="understand-the-device-model-schema"></a>デバイス モデル スキーマについて

リモート監視ソリューションでシミュレートされたデバイスを使用して、その動作をテストすることができます。 リモート監視ソリューションをデプロイすると、一連のシミュレートされたデバイスが自動的にプロビジョニングされます。 既存のシミュレートされたデバイスをカスタマイズすることも、独自のデバイスを作成することもできます。

この記事では、シミュレートされたデバイスの機能と動作を指定するデバイス モデル スキーマについて説明します。 デバイス モデルは JSON ファイルに格納されます。

次の記事は、現在の記事に関連しています。

* 「[デバイス モデルの動作の実装](iot-accelerators-remote-monitoring-device-behavior.md)」では、シミュレートされたデバイスの動作を実装するために使用する JavaScript ファイルについて説明します。
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

JSON スキーマについて理解できたら、次のステップとして、[シミュレートされたデバイスの動作を実装する](iot-accelerators-remote-monitoring-device-behavior.md)方法について学習することをお勧めします。

リモート監視ソリューションに関する開発者向け情報の詳細については、以下をご覧ください。

* [開発者向けリファレンス ガイド](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [開発者向けトラブルシューティング ガイド](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
