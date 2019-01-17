---
title: Azure Stack のストレージ アカウントについて | Microsoft Docs
description: Azure Stack のストレージ アカウントの作成方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/14/2019
ms.author: mabrigg
ms.openlocfilehash: 66a6fa88956db40ced9ee3fd13a0f74585c528fc
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2019
ms.locfileid: "54303209"
---
# <a name="storage-accounts-in-azure-stack"></a>Azure Stack のストレージ アカウント
ストレージ アカウントには、BLOB や Table service、およびストレージ データ オブジェクトの一意の名前空間が含まれます。 既定では、アカウントのデータはストレージ アカウント所有者だけが使用できます。

1. Azure Stack POC コンピューターで、[管理者](azure-stack-connect-azure-stack.md)として `https://adminportal.local.azurestack.external` にログインし、**[+ Create a resource]\(+ リソースの作成\)** > **[データ + ストレージ]** > **[ストレージ アカウント]** の順にクリックします。

   ![](media/azure-stack-provision-storage-account/image01.png)
2. **[ストレージ アカウントの作成]** ブレードで、ストレージ アカウントの名前を入力します。 新しい**リソース グループ**を作成するか、既存のものを選択し、**[作成]** をクリックしてストレージ アカウントを作成します。

   ![](media/azure-stack-provision-storage-account/image02.png)
3. 新しいストレージ アカウントを表示するには、**[すべてのリソース]** をクリックしてストレージ アカウントを検索し、ストレージ アカウント名をクリックします。

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>次の手順
[Azure リソース マネージャー テンプレートの使用](user/azure-stack-arm-templates.md)

[Azure ストレージ アカウントについて](../storage/common/storage-create-storage-account.md)

[Azure Stack Azure 互換ストレージの検証ガイドのダウンロード](https://aka.ms/azurestacktp1doc)
