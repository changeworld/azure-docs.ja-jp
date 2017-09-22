---
title: "Azure から Marketplace の項目をダウンロードする | Microsoft Docs"
description: "Azure から Azure Stack のデプロイに Marketplace の項目をダウンロードすることができます。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4baa1b675d2930cd111b5b8368ac081dc2b77841
ms.contentlocale: ja-jp
ms.lasthandoff: 09/15/2017

---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Azure から Azure Stack に Marketplace の項目をダウンロードする

Azure Stack Marketplace に含めるコンテンツを決定するときは、Azure Marketplace から利用可能なコンテンツを検討する必要があります。 Azure Stack 上で動くことがテスト済みの Azure Marketplace の項目の精選された一覧からダウンロードすることができます。 一覧には新しい項目が頻繁に追加されるので、ときどき新しいコンテンツを確認してください。

Marketplace の項目をダウンロードするには、最初に [Azure Stack を Azure に登録](azure-stack-register.md)します。 

## <a name="download"></a>ダウンロード
1. Azure Stack 管理ポータル (https://portal.local.azurestack.external) にサインインします。
2. Marketplace の項目の中には非常に大きいものがあります。  **[リソース プロバイダー]** > **[ストレージ]** をクリックして、システムに十分な領域があることを確認してください。

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

3. **[その他のサービス]** > **[Marketplace Management]\(Marketplace の管理\)** をクリックします。

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. **[Add from Azure]\(Azure から追加\)** をクリックして、ダウンロードできる項目の一覧を表示します。 一覧の各項目をクリックして、説明とダウンロード サイズを見ることができます。

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. 一覧で必要な項目を選び、**[ダウンロード]** をクリックします。 選んだ項目の VM イメージのダウンロードが始まります。 ダウンロードの時間は項目によって異なります。

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. ダウンロードが完了したら、クラウド オペレーターまたはテナント ユーザーとして、新しい Marketplace の項目をデプロイできます。 **[+ 新規]** をクリックして、カテゴリの中から新しい Marketplace の項目を探し、選択します。
7. **[作成]** をクリックして、新しくダウンロードした項目の作成エクスペリエンスを開きます。 表示される手順に従って項目をデプロイします。

## <a name="next-steps"></a>次のステップ

[Marketplace アイテムを作成および発行する](azure-stack-create-and-publish-marketplace-item.md)

