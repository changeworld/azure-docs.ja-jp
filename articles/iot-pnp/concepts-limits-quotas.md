---
title: IoT プラグ アンド プレイの制限とクォータ | Microsoft Docs
description: IoT プラグ アンド プレイを使用する場合に適用される制限、クォータ、およびスロットルについて説明します。
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d965d9cb8b87ce0b67f4fe0c07b660fdfd69cc07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577987"
---
# <a name="iot-plug-and-play-limits-quotas-and-throttles"></a>IoT プラグ アンド プレイの制限、クォータ、およびスロットル

この記事では、IoT プラグ アンド プレイ固有の制限、クォータ、およびスロットルについて説明します。 既存の [IoT Hub のクォータとスロットル](../iot-hub/iot-hub-devguide-quotas-throttling.md)も適用されます。

## <a name="iot-hub"></a>IoT Hub

IoT hub には以下の制限とクォータが適用されます。

| 制限、制約、およびスロットル | 値 | Notes |
|-----|-----|-----|
| ハブごとに登録できるインターフェイスの数 | 1500 ||
| コンポーネント名の最大サイズ | 1 ～ 64 文字 | 使用できる文字は、a-z、A-Z、0-9 (最初の文字にはできません)、およびアンダースコア (最初の文字または最後の文字にはできません) です。 |
| プロパティ名の最大サイズ | 1 ～ 64 文字 | 使用できる文字は、a-z、A-Z、0-9 (最初の文字にはできません)、およびアンダースコア (最初の文字または最後の文字にはできません) です。 |
| プロパティ値の最大サイズ | Digital Twins Definition Language の[プロパティ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)と同じ。 | 5 レベルの深さです。配列または配列を含む複雑なスキーマにすることはできません。 |
| コマンド名の最大サイズ | 1 ～ 64 文字 | 使用できる文字は、a-z、A-Z、0-9 (最初の文字にはできません)、およびアンダースコア (最初の文字または最後の文字にはできません) です。|
| デバイス ツインのサイズ | [IoT Hub の制限](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size)と同じ ||

## <a name="parser-library"></a>パーサー ライブラリ

パーサー ライブラリは、[Digital Twins 定義言語](https://github.com/Azure/opendigitaltwins-dtdl)に適用される制限に従います。

## <a name="next-steps"></a>次のステップ

次の手順では、[IoT プラグ アンド プレイのアーキテクチャ](concepts-architecture.md)を確認することをお勧めします。
