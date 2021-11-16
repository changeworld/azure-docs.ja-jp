---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 153f5c71925e3f4fe1078f014684a12c60a054b1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477926"
---
- Azure サブスクリプション。 [無料の Azure アカウント](https://azure.microsoft.com/free/)を作成します。

- [Synapse Studio](../data-explorer-create-pool-studio.md) または [Azure portal](../data-explorer-create-pool-portal.md) を使用して Data Explorer プールを作成します
- Data Explorer データベースを作成します。
    1. Synapse Studio の左側のペインで、 **[データ]** を選択します。
    1. **&plus;** (新しいリソースの追加) > **[Data Explorer プール]** を選択し、次の情報を使用します。

        | 設定 | 推奨値 | 説明 |
        |--|--|--|
        | プール名 | *contosodataexplorer* | 使用する Data Explorer プールの名前 |
        | Name | *TestDatabase* | データベース名はクラスター内で一意である必要があります。 |
        | 既定のリテンション期間 | *365* | クエリにデータを使用できることが保証される期間 (日数) です。 期間は、データが取り込まれた時点から測定されます。 |
        | 既定のキャッシュ期間 | *31* | 頻繁にクエリされるデータが、長期ストレージではなく SSD ストレージまたは RAM で利用できるように保持される期間 (日数) です。 |

    1. **[作成]** を選択してデータベースを作成します。 通常、作成にかかる時間は 1 分未満です。
