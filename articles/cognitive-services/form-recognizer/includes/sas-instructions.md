---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 8ad682ccd7feb9c7f089e9cf0f066cb1be8c756c
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2020
ms.locfileid: "97505326"
---
カスタム モデルのトレーニング データの SAS URL を取得するには、Azure portal のストレージ リソースに移動し、 **[Storage Explorer]** タブを選択します。コンテナーに移動して右クリックし、 **[Get shared access signature]\(Shared Access Signature の取得\)** を選択します。 ストレージ アカウント自体ではなく、コンテナー用の SAS を取得することが重要です。 アクセス許可の **[読み取り]** と **[表示]** がオンになっていることを確認し、 **[作成]** をクリックします。 次に、**URL** セクションの値を一時的な場所にコピーします。 それは次の書式になります`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
   > [!div class="mx-imgBorder"]
   > ![alt-text](../media/quickstarts/get-sas-url.png)