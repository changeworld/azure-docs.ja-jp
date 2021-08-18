---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: lajanuar
ms.openlocfilehash: 85f5a40ef569ff6ec462bfdeca5fa966b918473b
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326356"
---
* カスタム モデルのトレーニング データの SAS URL を取得するには、Azure portal のストレージ リソースに移動し、 **[Storage Explorer]** タブを選択します。コンテナーに移動して右クリックし、 **[Get shared access signature]\(Shared Access Signature の取得\)** を選択します。 ストレージ アカウント自体ではなく、コンテナー用の SAS を取得することが重要です。 **[読み取り]** 、 **[書き込み]** 、 **[削除]** 、および **[表示]** 権限がオンになっていることを確認し、 **[作成]** をクリックします。 次に、**URL** セクションの値を一時的な場所にコピーします。 それは次の書式になります`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
