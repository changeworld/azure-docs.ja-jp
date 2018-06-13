---
title: Azure Migrate でのアセスメントのためにマシンをグループ化する | Microsoft Docs
description: Azure Migrate サービスでアセスメントを実行する前にマシンをグループ化する方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: 5c5b5dc97c0faf43c9543422406c4fa1c30ed679
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203701"
---
# <a name="group-machines-for-assessment"></a>アセスメントためのマシンのグループ化

この記事では、[Azure Migrate](migrate-overview.md) によるアセスメントのためにマシンのグループを作成する方法について説明します。 Azure Migrate では、グループ内のマシンを評価して Azure への移行に適しているかどうかを確認し、Azure でマシンを実行する際のサイズとコストの見積りを示します。


## <a name="create-a-group"></a>グループの作成

1. Azure Migrate プロジェクトの**概要**で、[管理] の  **[グループ]** > **[+グループ]** をクリックし、グループ名を指定します。
2. グループに 1 つ以上のマシンを追加し、 **[作成]** をクリックします。 
3. 必要に応じて、グループの新しいアセスメントの実行を選択することもできます。 

    ![グループの作成](./media/how-to-create-a-group/create-group.png)

グループが作成されたら、**[グループ]** ページでグループを選択し、マシンを追加または削除してグループを変更できます。

## <a name="next-steps"></a>次の手順

- [マシンの依存関係マッピング](how-to-create-group-machine-dependencies.md)を使用して、信頼度の高いグループを作成する方法を確認します。
- 評価の計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
