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
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "70049066"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

この記事では、デバイス上で直接メソッドを呼び出すようにジョブをスケジュールし、デバイス ツインを更新するようにジョブをスケジュールし、各ジョブの進行状況を監視するバックエンド サービスを作成します。 これらの操作を実行するには、サービスに**レジストリ読み取り**および**レジストリ書き込み**のアクセス許可が必要です。 既定では、すべての IoT ハブは、これらのアクセス許可を付与する **registryReadWrite** という名前の共有アクセス ポリシーがある状態で作成されます。
