---
title: インクルード ファイル
description: インクルード ファイル
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0dcb608553d4455403c073e34e83bccb81cc64db
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38750095"
---
Azure Batch で実行するタスクによって、実行時に出力データが生成されることがあります。 タスクの出力データは、多くの場合、ジョブ内の他のタスク、またはそのジョブを実行したクライアント アプリケーション (あるいは両方) が取得できるように格納される必要があります。 タスクは Batch コンピューティング ノードのファイル システムに出力データを書き込みますが、再イメージ化されるとき、またはノードがプールを終了するときに、ノードのすべてのデータが失われます。 タスクには、ファイル リテンション期間が設定されている場合もあります。この期間が過ぎると、タスクによって作成されたファイルが削除されます。 これらの理由から、後で必要になるタスク出力をデータ ストア ([Azure Storage](https://docs.microsoft.com/azure/storage/) など) に永続化することが重要です。

Batch のストレージ アカウント オプションについては、[Batch 機能の概要](../articles/batch/batch-api-basics.md#azure-storage-account)に関するページをご覧ください。
