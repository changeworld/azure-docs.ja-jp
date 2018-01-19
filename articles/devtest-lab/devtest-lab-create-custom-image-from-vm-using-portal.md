---
title: "VM から Azure DevTest Labs カスタム イメージを作成する | Microsoft Docs"
description: "Azure ポータルを使用して、プロビジョニングされた VM から Azure DevTest Labs にカスタム イメージを作成する方法について説明します"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: v-craic
ms.openlocfilehash: dc315bcc625ea98244bb5804ce6ff1c13d0ec7b1
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="create-a-custom-image-from-a-vm"></a>VM からカスタム イメージを作成する

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>詳細な手順

プロビジョニングされた VM からカスタム イメージを作成し、その後、そのカスタム イメージを使用して同一の VM を作成できます。 次の手順では、VM からカスタム イメージを作成する方法を説明します。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。  

1. ラボのメイン ウィンドウで **[仮想マシン]** を選択します。
 
1. **[仮想マシン]** ウィンドウで、カスタム イメージを作成する VM を選択します。

1. VM の管理ウィンドウで、**[カスタム イメージの作成 (VHD)]** を選択します。

    ![カスタム イメージのメニュー項目の作成](./media/devtest-lab-create-template/create-custom-image.png)

1. **[カスタム イメージ]** ウィンドウで、カスタム イメージの名前と説明を入力します。 この情報は、VM を作成する際にベースの一覧に表示されます。

    ![[カスタム イメージの作成] ウィンドウ](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. sysprep を VM で実行したかどうかを選択します。 sysprep を VM で実行していない場合は、カスタム イメージから VM を作成するときに sysprep を実行するかどうかを指定します。

1. 完了したら **[OK]** を選択して、カスタム イメージを作成します。

数分後にカスタム イメージが作成され、ラボのストレージ アカウント内に保存されます。 ラボのユーザーが新しい VM を作成する場合、基本イメージの一覧からそのイメージを使用できます。

![基本イメージの一覧で使用できるカスタム イメージ](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>関連するブログ記事

- [Custom images or formulas? (カスタム イメージか数式か?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Azure DevTest Labs 間でのカスタム イメージのコピー)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>次の手順

- [VM をラボに追加する](devtest-lab-add-vm.md)
