---
title: ラボ内の VM を Azure DevTest Labs でサイズ変更する | Microsoft Docs
description: Azure DevTest Labs で仮想マシンをサイズ変更する方法を説明します
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 9b9a1839bf4b028aec13b764b4de66385de4189e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47090753"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>ラボ内の VM を Azure DevTest Labs でサイズ変更する
Azure 仮想マシンの重要な機能の 1 つは、CPU、ネットワーク、またはディスク パフォーマンスのニーズに基づいて仮想マシン (VM) のサイズを変更できることです。 Azure DevTest Labs は、この機能をラボ内の VM に対してサポートするようになりました。 サイズ変更機能は、ラボで許可されている VM のサイズのラボ ポリシーに準拠します。 つまり、VM のサイズを、ラボで許可されているサイズにのみ変更できます。 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>ラボで VM のサイズを変更する手順 
Azure DevTest Labs でラボ内の VM のサイズを変更するには、次の手順を実行します。 

> [!NOTE]
> リモート デスクトップ セッション (RDP) を介して VM に接続している場合、作業を保存し、サイズを変更する前に、VM から接続を切断します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で、サイズ変更する VM が含まれているラボを選択します。  
4. 左側のパネルで、**[仮想マシン]** を選択します。 
5. VM の一覧で、VM を選択します。
6. VM が実行中の場合は、ツールバーの **[停止]** を選択します。 **[通知]** ウィンドウでサイズ変更操作の状態を確認します。 VM が停止されるまで待って、**[通知]** ウィンドウを閉じます。 

    ![VM を停止する](media/devtest-lab-resize-vm/stop-vm.png)
1. VM の [仮想マシン] ページの左メニューにある **[設定]** の下の **[サイズ]** を選択します。

    ![[サイズ] メニュー](media/devtest-lab-resize-vm/size-menu.png)
1. **[サイズの選択]** ウィンドウで、VM を参照してサイズを選択し、**[選択]** をクリックします。     
1. **[通知]** ウィンドウでサイズ変更操作の状態を確認します。

    ![サイズ変更の状態](media/devtest-lab-resize-vm/resize-status.png)
10. サイズ変更操作が正常に終了したら、**[通知]** ウィンドウを閉じます。 
11. 左側メニューの **[概要]** を選択し、ツール バーの **[再起動]** を選択して VM を再起動します。 

## <a name="next-steps"></a>次の手順
Azure 仮想マシンがサポートするサイズ変更機能に関する詳しい情報については[仮想マシンのサイズ変更](https://azure.microsoft.com/blog/resize-virtual-machines/)をご覧ください。


