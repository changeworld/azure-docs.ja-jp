---
title: ServiceNow の同期に関する問題を手動で修正する方法
description: ServiceNow への接続をリセットし、Microsoft Azure のアラートで ServiceNow を再び呼び出せるようにします
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 2a9a8f3fe7422468ff2c8886dff7415322cf70f0
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676880"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>ServiceNow の同期に関する問題を手動で修正する方法

Azure Monitor は、サードパーティの IT Service Management (ITSM) プロバイダーに接続できます。 ServiceNow は、これらのプロバイダーの 1 つです。

セキュリティ上の理由から、ServiceNow による接続に使用する認証トークンの更新が必要になる場合があります。
次の同期プロセスを使用して、接続を再アクティブ化し、トークンを更新します。


1. 上部の検索バナーでソリューションを検索し、関連するソリューションを選択します

    ![上部の検索バナーと、関連するソリューションを選択する場所を示すスクリーンショット。](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. ソリューション画面で、サブスクリプション フィルターで [すべてを選択] を選択し、"ServiceDesk" でフィルター処理します

    ![[すべてを選択] を選択する場所と、ServiceDesk によってフィルター処理する場所を示すスクリーンショット。](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. ITSM 接続のソリューションを選択します。
1. 左側のバナーで [ITSM connection]\(ITSM 接続\) を選択します。

    ![ITSM 接続を選択する場所を示すスクリーンショット。](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. 各コネクタを一覧から選択します。 
    1. 構成するにはコネクタ名をクリックします
    1. 使用されなくなったコネクタを削除します

    1. パートナーの種類に基づいて、[これらの定義](./itsmc-connections.md)に従ってフィールドを更新します

    1. [同期] をクリックします

       ![[同期] ボタンが強調表示されているスクリーンショット。](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. [保存] をクリックします

        ![新しい接続](media/itsmc-resync-servicenow/save-8bit.png)

f.    通知を確認して、プロセスが正常に終了したかどうかを確認します 

## <a name="next-steps"></a>次の手順

[IT Service Management 接続](itsmc-connections.md)の詳細を確認する
