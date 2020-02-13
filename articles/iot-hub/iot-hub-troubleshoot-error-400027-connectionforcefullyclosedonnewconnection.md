---
title: Azure IoT Hub エラー 400027 ConnectionForcefullyClosedOnNewConnection のトラブルシューティング
description: エラー 400027 ConnectionForcefullyClosedOnNewConnection の修正方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960212"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

この記事では、**400027 ConnectionForcefullyClosedOnNewConnection** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

デバイスからクラウドへのツイン操作 (報告されたプロパティの読み取りやパッチなど) またはメソッドの直接呼び出しがエラー コード **400027** で失敗します。

## <a name="cause"></a>原因

別のクライアントで同じ資格情報を使用して IoT Hub への新しい接続が作成されたため、IoT Hub によって前の接続が閉じられました。 IoT Hub では、同じ資格情報のセットを使用して複数のクライアントが接続することを許可していません。

## <a name="solution"></a>解決策

各クライアントが独自の ID を使用して IoT Hub に接続していることを確認します。