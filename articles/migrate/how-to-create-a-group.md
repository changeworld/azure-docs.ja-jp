---
title: "Azure Migrate でのアセスメントのためにマシンをグループ化する | Microsoft Docs"
description: "Azure Migrate サービスでアセスメントを実行する前にマシンをグループ化する方法について説明します。"
author: rayne-wiselman
ms.service: azure-migrate
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 429a9150d1fbf50c0e3fa2046eb64affc8db8e5d
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2017
---
# <a name="group-machines-for-assessment"></a>アセスメントためのマシンのグループ化

この記事では、[Azure Migrate](migrate-overview.md) によるアセスメントのためにマシンのグループを作成する方法について説明します。 Azure Migrate では、グループ内のマシンを評価して Azure への移行に適しているかどうかを確認し、Azure でマシンを実行する際のサイズとコストの見積りを示します。


## <a name="create-a-group"></a>グループの作成

1. Azure Migrate プロジェクトの**ダッシュボード**で、 **[グループ]** > **[+グループ]** をクリックし、グループ名を指定します。
2. グループに 1 つ以上のマシンを追加し、 **[作成]** をクリックします。 
3. 必要に応じて、グループの新しいアセスメントの実行を選択することもできます。 

    ![グループの作成](./media/how-to-create-a-group/create-group.png)

グループが作成されたら、**[グループ]** ページでグループを選択し、マシンを追加または削除してグループを変更できます。

## <a name="next-steps"></a>次のステップ

- [マシンの依存関係マッピング](how-to-create-group-machine-dependencies.md)を使用して、より詳細なグループを作成する方法を確認します。
- アセスメントの計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
