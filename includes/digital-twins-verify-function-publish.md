---
author: baanders
description: Azure 関数が発行されたことを確認する方法を説明するインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 7/14/2021
ms.author: baanders
ms.openlocfilehash: 543c905cc9e35d985c5b1962067690a0f895eab6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750895"
---
### <a name="verify-the-publication-of-your-function"></a>関数の発行を確認する

関数の発行プロセスが完了したら、次の手順を使用して、発行が成功したことを確認できます。
 
1. [Azure portal](https://portal.azure.com/) に移動し、資格情報を使用してサインインします。
2. ウィンドウの上部にある検索ボックスで、作成した関数アプリの名前を検索し、選択します。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/search-function-app.png" alt-text="Azure portal を示すスクリーンショット。検索フィールドに関数アプリ名を入力します。" lightbox="../articles/digital-twins/media/includes/azure-functions/search-function-app.png":::

3. 表示される **[関数アプリ]** ページで、左側のメニューから **[関数]** を選択します。 一覧で関数の名前を見つけて、正常に発行されたことを確認します。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/view-published-functions.png" alt-text="Azure portal で発行された関数が表示されているスクリーンショット。" lightbox="../articles/digital-twins/media/includes/azure-functions/view-published-functions.png":::

    > [!Note] 
    > 発行された関数のリストに関数が表示されるまでに、数分待つか、ページを更新することが必要な場合があります。