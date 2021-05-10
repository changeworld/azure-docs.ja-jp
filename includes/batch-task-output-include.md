---
title: インクルード ファイル
description: インクルード ファイル
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 019e8db54c1cfd9f436f880b8ddbb9bfa31c50bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028072"
---
Azure Batch で実行するタスクによって、実行時に出力データが生成されることがあります。 タスクの出力データは、多くの場合、ジョブ内の他のタスク、またはそのジョブを実行したクライアント アプリケーション (あるいは両方) が取得できるように格納される必要があります。 タスクは Batch コンピューティング ノードのファイル システムに出力データを書き込みますが、再イメージ化されるとき、またはノードがプールを終了するときに、ノードのすべてのデータが失われます。 タスクには、ファイル リテンション期間が設定されている場合もあります。この期間が過ぎると、タスクによって作成されたファイルが削除されます。 これらの理由から、後で必要になるタスク出力をデータ ストア ([Azure Storage](../articles/storage/index.yml) など) に永続化することが重要です。

Batch のストレージ アカウント オプションについては、「[Batch アカウントと Azure Storage アカウント](../articles/batch/accounts.md#azure-storage-accounts)」を参照してください。