---
title: "Azure Migrate のグループの依存関係マッピングを使用してアセスメント グループを絞り込む | Microsoft Docs"
description: "Azure Migrate サービスのグループの依存関係マッピングを使用して、アセスメントを絞り込む方法について説明します。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: c30d6546e7c2d471d4b262a8af1ce593b2c1c3fb
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2017
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>グループの依存関係マッピングを使用したグループの絞り込み

この記事では、[Azure Migrate](migrate-overview.md) でのアセスメントのためにグループの依存関係マッピングを設定する方法について説明します。 アセスメントを実行する前に、グループの依存関係を照合して既存のグループの設定を調整する場合、通常はこの方法を使用します。 グループの依存関係マッピングの実行対象となるグループには、10 個を超えるマシンを含めないでください。  

## <a name="modify-a-group"></a>グループの変更

1. Azure Migrate プロジェクトで、**[管理]** の  **[グループ]** をクリックし、グループを選択します。
2. グループ ページで  **[依存関係の表示]** をクリックして、グループの依存関係マップを開きます。 

     ![グループの表示](./media/how-to-create-group-dependencies/create-group.png)

3. さらに細かい依存関係を表示するには、時間の範囲をクリックして変更します。 既定では、範囲は 1 時間です。 時間の範囲を変更することも、開始日と終了日および期間を指定することもできます。
4. Ctrl キーを押しながらクリックして、マップ上の複数のマシンを選択します。 マップ上のマシンを追加または削除します。
    - 追加できるのは、検出済みのマシンだけです。
    - グループのマシンを追加または削除すると、そのグループの過去のアセスメントが無効になります。
    - 必要に応じて、グループを変更したときに新しいアセスメントを作成することもできます。
5. **[OK]** をクリックしてグループを保存します。

    ![追加と削除](./media/how-to-create-group-dependencies/add-remove.png)

グループの依存関係マップに表示されている特定のマシンの依存関係を確認する場合は、[マシンの依存関係マッピング](how-to-create-group-machine-dependencies.md)を設定します。


## <a name="next-steps"></a>次のステップ

評価の計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
