---
title: Azure IoT OPC UA デバイス管理のアーキテクチャ | Microsoft Docs
description: OPC Twin のアーキテクチャ
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 904c889866d3aa24e1da387af44b3f589b7769aa
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759793"
---
# <a name="opc-twin-architecture"></a>OPC Twin のアーキテクチャ

次の図に、OPC Twin のアーキテクチャを示します。

## <a name="discover-and-activate"></a>検出とアクティブ化

1. オペレーターが、モジュールでネットワーク スキャンを有効にするか、探索 URL を使用して 1 回限りの検出を行います。 検出されたエンドポイントとアプリケーションの情報は、処理のためにテレメトリを介してオンボーディング エージェントに送信されます。  OPC UA デバイス オンボーディング エージェントは、検出モードまたはスキャン モードのときに、OPC Twin IoT Edge モジュールによって送信された OPC UA サーバー検出イベントを処理します。 検出イベントの結果、OPC UA デバイス レジストリでのアプリケーションの登録と更新が行われます。

   ![OPC Twin のしくみ](media/overview-opc-twin-architecture/opc-twin1.png)

1. オペレーターが、検出されたエンドポイントの証明書を検査し、アクセスのために登録済みエンドポイント ツインをアクティブにします。 

   ![OPC Twin のしくみ](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>参照と監視

1. アクティブ化が済むと、オペレーターは Twin サービス REST API を使用してサーバー情報モデルを参照または検査し、オブジェクト変数を読み書きし、メソッドを呼び出すことができます。  ユーザーは、HTTP および JSON で完全に表現された、簡略化された OPC UA API を使用します。

   ![OPC Twin のしくみ](media/overview-opc-twin-architecture/opc-twin3.png)

1. ツイン サービス REST インターフェイスを使用して、OPC Publisher で監視対象の項目とサブスクリプションを作成することもできます。 OPC Publisher では、OPC UA サーバー システムから IoT Hub にテレメトリを送信できます。 OPC Publisher の詳細については、GitHub の [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) のリポジトリを参照してください。

   ![OPC Twin のしくみ](media/overview-opc-twin-architecture/opc-twin4.png)