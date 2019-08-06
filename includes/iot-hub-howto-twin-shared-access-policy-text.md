---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: f97ab0a0bb91af90daaf60c5c2c85f0ede56a5e7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403973"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

この記事では、デバイス ツインに必要なプロパティを追加した後、ID レジストリに対してクエリを実行し、適切に更新された reported プロパティを持つデバイスをすべて検索するバックエンド サービスを作成します。 そのサービスには、必要なデバイス ツインのプロパティを変更するための**サービス接続**アクセス許可と、ID レジストリに対してクエリを実行するための**レジストリ読み取り**アクセス許可が必要となります。 その 2 つのアクセス許可だけを含んだ既定の共有アクセス ポリシーは存在しないため、共有アクセス ポリシーを独自に作成する必要があります。
