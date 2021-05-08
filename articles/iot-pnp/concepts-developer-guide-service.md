---
title: サービス開発者ガイド - IoT プラグ アンド プレイ | Microsoft Docs
description: サービス開発者向けの IoT プラグ アンド プレイの説明
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: a5889be88dfd0870a2eee868c97787ff354cff68
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582733"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT プラグ アンド プレイ サービス開発者ガイド

IoT プラグ アンド プレイを使用すると、Azure IoT アプリケーションに機能を宣伝するスマート デバイスをビルドできます。 IoT プラグ アンド プレイ デバイスでは、ユーザーが IoT プラグ アンド プレイ対応アプリケーションに接続するときに、手動で構成する必要がありません。

IoT プラグ アンド プレイでは、モデル ID をアナウンスしたデバイスを IoT ハブで使用できます。 たとえば、デバイスのプロパティとコマンドに直接アクセスできます。

IoT ハブに接続されている IoT プラグ アンド プレイ デバイスを使用するには、いずれかの IoT サービス SDK を使用します。

## <a name="service-sdks"></a>サービス SDK

ソリューション内で Azure IoT サービス SDK を使用して、デバイスとモジュールを操作します。 たとえば、サービス SDK を使用して、ツインのプロパティの読み取りと更新、コマンドの呼び出しを行うことができます。 サポートされている言語には、C#、Java、Node.js、Python などがあります。

サービス SDK を使用すると、デスクトップや Web アプリケーションなどのソリューションからデバイス情報にアクセスできます。 サービス SDK には、モデル ID を取得するために使用できる 2 つの名前空間とオブジェクト モデルが含まれています。

- IoT Hub サービス クライアント。 このサービスは、モデル ID をデバイス ツイン プロパティとして公開します。

- Digital Twins クライアント。 新しい Digital Twins API は、コンポーネント、プロパティ、コマンドなどの、[Digital Twins Definition Language (DTDL)](concepts-digital-twin.md) モデル コンストラクト上で機能します。 Digital Twin API を使用すると、ソリューション ビルダーで IoT プラグ アンド プレイ ソリューションを簡単に作成できます。

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>次のステップ

ここまでで、デバイスのモデリングについて学習しました。その他のリソースを次に示します。

- [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C デバイス SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [IoT プラグ アンド プレイ モデリング ガイド](concepts-modeling-guide.md)
- [DTDL 作成ツールをインストールして使用する](howto-use-dtdl-authoring-tools.md)