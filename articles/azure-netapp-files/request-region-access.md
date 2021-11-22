---
title: Azure NetApp Files のリージョン アクセスを要求する | Microsoft Docs
description: Azure NetApp Files を使用するためにリージョンへのアクセスを要求する方法について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/15/2021
ms.author: b-juche
ms.openlocfilehash: 9d610a74e63c85a66cd0be42cfdda3139c673f67
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520638"
---
# <a name="request-region-access-for-azure-netapp-files"></a>Azure NetApp Files のリージョン アクセスを要求する

特殊な状況では、リージョンへのアクセスを明示的に要求することが必要になる場合があります。 この記事では、要求を送信する方法について説明します。 

## <a name="steps"></a>手順

1. **[サポートとトラブルシューティング]** にある **[新しいサポート リクエスト]** に移動します。   

2. **[問題の説明]** タブで、必要な情報を入力します。
    1. **[問題の種類]** で、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。
    2. **[サブスクリプション]** で、ご使用のサブスクリプションを選択します。 
    3. **[クォータの種類]** で、 **[ストレージ: Azure NetApp Files の制限]** を選択します。

    ![[問題の説明] タブを示すスクリーンショット。](../media/azure-netapp-files/support-problem-descriptions.png)

3. **[追加詳細]** タブの要求の詳細フィールドにある **[詳細を入力]** をクリックします。  

    ![[詳細] タブと [詳細の入力] フィールドを示すスクリーンショット。](../media/azure-netapp-files/quota-additional-details.png)

4. リージョン アクセスを要求するには、表示された [クォータの詳細] ウィンドウで次の情報を指定します。   
    1. **[クォータの種類]** で、 **[リージョン アクセス]** を選択します。
    2. **[要求されたリージョン]** で、自分のリージョンを選択します。

    ![リージョン アクセスを要求するための [クォータの詳細] ウィンドウを示すスクリーンショット。](../media/azure-netapp-files/quota-details-region-access.png)

5. **保存して続行** をクリックします。 **[確認と作成]** をクリックして要求を作成します。

## <a name="next-steps"></a>次のステップ  

- [Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md)
- [Azure NetApp Files のリージョンの容量クォータ](regional-capacity-quota.md)
- [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
