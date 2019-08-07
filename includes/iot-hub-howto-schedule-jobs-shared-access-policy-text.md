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
ms.openlocfilehash: 47d46cf4b400e29de8c526f750a10f57d6400220
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402674"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

この記事では、デバイス上で直接メソッドを呼び出すようにジョブをスケジュールし、デバイス ツインを更新するようにジョブをスケジュールし、各ジョブの進行状況を監視するバックエンド サービスを作成します。 これらの操作を実行するには、サービスに**レジストリ読み取り**および**レジストリ書き込み**のアクセス許可が必要です。 既定では、どの IoT Hub も、これらのアクセス許可を付与する **registryReadWrite** という名前の共有アクセス ポリシーがある状態で作成されます。
