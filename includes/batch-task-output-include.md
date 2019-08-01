---
title: インクルード ファイル
description: インクルード ファイル
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 7ba4c90811bd8051ed9c307d9f9fa33e08e69dc7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "67181528"
---
Azure Batch で実行するタスクによって、実行時に出力データが生成されることがあります。 タスクの出力データは、多くの場合、ジョブ内の他のタスク、またはそのジョブを実行したクライアント アプリケーション (あるいは両方) が取得できるように格納される必要があります。 タスクは Batch コンピューティング ノードのファイル システムに出力データを書き込みますが、再イメージ化されるとき、またはノードがプールを終了するときに、ノードのすべてのデータが失われます。 タスクには、ファイル リテンション期間が設定されている場合もあります。この期間が過ぎると、タスクによって作成されたファイルが削除されます。 これらの理由から、後で必要になるタスク出力をデータ ストア ([Azure Storage](https://docs.microsoft.com/azure/storage/) など) に永続化することが重要です。

Batch のストレージ アカウント オプションについては、[Batch 機能の概要](../articles/batch/batch-api-basics.md#azure-storage-account)に関するページをご覧ください。
