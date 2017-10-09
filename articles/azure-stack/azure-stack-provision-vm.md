---
title: "Azure Stack でのテスト VM の作成 | Microsoft Docs"
description: "クラウド オペレーターとして Azure Stack でテスト VM をプロビジョニングする方法を説明します。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: c86646e1-a12e-493f-b396-f17bfacd60c2
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 98770945d5cccc27bfce971491e6ea30fee2460a
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---
# <a name="create-a-test-virtual-machine-in-azure-stack"></a>Azure Stack でのテスト仮想マシンの作成

*適用先: Azure Stack Development Kit*

クラウド オペレーターは、テスト仮想マシンを作成して、[Azure Stack](azure-stack-poc.md) のデプロイを検証することができます。

> [!NOTE]
> 仮想マシンをプロビジョニングする前に、[Azure Stack Marketplace に Windows Server 2016 評価版のイメージを追加する必要があります](azure-stack-add-default-image.md)。
> 
> 

## <a name="create-a-virtual-machine"></a>仮想マシンの作成
1. Azure Stack Development Kit ホストで、管理者ポータル (`https://adminportal.local.azurestack.external`) に[サインイン](azure-stack-connect-azure-stack.md)して、**[新規]** > **[計算]** > **[Windows Server 2016 Datacenter Eval]** > **[作成]** の順にクリックします。  
2. **[基本]** ブレードで、**[名前]**、**[ユーザー名]**、**[パスワード]** を入力します。 **[サブスクリプション]**を選択します。 **リソース グループ**を作成するか、既存のリソース グループを選択して、**[OK]** をクリックします。  
3. **[サイズの選択]** ブレードで、**[A1 標準]** をクリックしてから **[選択]** をクリックします。  
4. **[設定]** ブレードで、既定値のまま **[OK]** をクリックします。
5. **[概要]** ブレードで、**[OK]** をクリックして仮想マシンを作成します。  
6. 新しい仮想マシンを表示するには、**[すべてのリソース]** をクリックして仮想マシンを検索し、仮想マシン名をクリックします。
    ![](media/azure-stack-provision-vm/image06.png)


## <a name="next-steps"></a>次のステップ
[Azure Stack での管理者およびユーザー ポータルの使用](azure-stack-manage-portals.md)

