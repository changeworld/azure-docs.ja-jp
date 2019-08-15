---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a843821a7720ddcb17f8a369d012facd7ba81e35
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912417"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

この記事では、デバイス ツインに必要なプロパティを追加した後、ID レジストリに対してクエリを実行し、適切に更新された reported プロパティを持つデバイスをすべて検索するバックエンド サービスを作成します。 そのサービスには、必要なデバイス ツインのプロパティを変更するための**サービス接続**アクセス許可と、ID レジストリに対してクエリを実行するための**レジストリ読み取り**アクセス許可が必要となります。 その 2 つのアクセス許可だけを含んだ既定の共有アクセス ポリシーは存在しないため、共有アクセス ポリシーを独自に作成する必要があります。
