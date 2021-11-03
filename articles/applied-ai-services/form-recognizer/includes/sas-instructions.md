---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5b9bf09957f70ed08a6d0c7a4988f4c0fde6fab9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021659"
---
カスタム モデルのトレーニング データの SAS URL を取得するには、Azure portal のストレージ リソースに移動し、 **[Storage Explorer]** タブを選択します。コンテナーに移動して右クリックし、 **[Get shared access signature]\(Shared Access Signature の取得\)** を選択します。 ストレージ アカウント自体ではなく、コンテナー用の SAS を取得することが重要です。 **[読み取り]** 、 **[書き込み]** 、 **[削除]** 、および **[表示]** 権限がオンになっていることを確認し、 **[作成]** をクリックします。 次に、**URL** セクションの値を一時的な場所にコピーします。 それは次の書式になります`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
