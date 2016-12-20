---
title: "VM を作成するための Azure DevTest Labs のカスタム イメージの管理 | Microsoft Docs"
description: "VHD ファイルまたは Azure DevTest Labs 内の既存の VM からカスタム イメージを作成する方法について説明します。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 62d64f5a59a003b2ca15d424c8844dee716bf147


---
# <a name="manage-azure-devtest-labs-custom-images-to-create-vms"></a>VM を作成するための Azure DevTest Labs のカスタム イメージの管理
Azure DevTest Labs では、カスタム イメージを使用すると、対象のコンピューターに必要なすべてのソフトウェアのインストールを待たずに、すぐに VM を作成できます。 カスタム イメージを使用すると、事前に VHD ファイルに必要なすべてのソフトウェアをインストール後、VHD ファイルを使用して VM を作成できます。 既にソフトウェアがインストールされているため、VM の作成時間が大幅に短縮されます。 さらに、VM からカスタム イメージを作成後、そのカスタム イメージから VM を作成することで、カスタム イメージが VM の複製に使用されます。

この記事では、次のことについて説明します:

* [VHD ファイルからカスタム イメージを作成します](#create-a-custom-image-from-a-vhd-file) 。 
* [VM からのカスタム イメージの作成します](#create-a-custom-image-from-a-vm) 。

## <a name="create-a-custom-image-from-a-vhd-file"></a>VHD ファイルからカスタム イメージを作成します
このセクションでは、VHD ファイルからカスタム イメージを作成する方法を説明します。
なお、このセクションのすべての手順を実行するには、有効な VHD ファイルへのアクセス権が必要です。   

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
2. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。  
4. ラボのブレードで、 **[構成]**を選択します。 
5. ラボの **[構成]** ブレードで、**[Custom images (カスタム イメージ)]** を選択します。
6. **[Custom images (カスタム イメージ)]** ブレードで、**[+ Custom image (+ カスタム イメージ)]** を選択します。
   
    ![カスタム イメージの追加](./media/devtest-lab-create-template/add-custom-image.png)
7. カスタム イメージの名前を入力します。 この名前は、VM を作成するときにベース イメージの一覧に表示されます。
8. カスタム イメージの説明を入力します。 この説明は、VM を作成するときにベース イメージの一覧に表示されます。
9. **[VHD ファイル]**を選択します。
10. 一覧表示されていない VHD ファイルにアクセスする場合は、「 [VHD ファイルのアップロード](#upload-a-vhd-file) 」セクションの手順に従って VHD ファイルを追加し、完了したら、ここに戻ります。
11. 目的の VHD ファイルを選択します。
12. **[OK]** を選択して、**[VHD ファイル]** ブレードを閉じます。
13. **[OS 構成]**を選択します。
14. **[OS 構成]** タブで、**[Windows]** または **[Linux]** を選択します。
15. **[Windows]** を選択した場合は、チェック ボックスを使用して、コンピューターで *Sysprep* が実行されたかどうかを指定します。
16. **[OK]** を選択して、**[OS 構成]** ブレードを閉じます。
17. **[OK]** を選択して、カスタム イメージを作成します。
18. 「 [次のステップ](#next-steps) 」セクションを参照してください。

### <a name="upload-a-vhd-file"></a>VHD ファイルのアップロード
カスタム イメージを追加するには、VHD ファイルにアクセスする必要があります。

1. **[VHD ファイル]** ブレードで、**[Upload a VHD file using PowerShell (PowerShell を使用して VHD ファイルをアップロード)]** を選択します。
   
    ![イメージのアップロード](./media/devtest-lab-create-template/upload-image-using-psh.png)
2. 次のブレードには、VHD ファイルを Azure サブスクリプションにアップロードする PowerShell スクリプトの変更と実行の手順が表示されます。 
   **注:** VHD ファイルのサイズと接続速度によっては、このプロセスに時間がかかる可能性があります。

## <a name="create-a-custom-image-from-a-vm"></a>VM からのカスタム イメージの作成します
既に構成されている VM がある場合は、その VM からカスタム イメージを作成し、後でそのカスタム イメージを使用して、他と同じ VM を作成することができます。 次の手順では、VM からカスタム イメージを作成する方法を説明します。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
2. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。  
4. ラボのブレードで、 **[仮想マシン]**を選択します。
5. **[仮想マシン]** ブレードで、カスタム イメージを作成する VM を選択します。
6. VM のブレードで、 **[Create custom image (VHD) (カスタム イメージ を作成する (VHD))]**を選択します。
   
    ![カスタム イメージのメニュー項目の作成](./media/devtest-lab-create-template/create-custom-image.png)
7. **[Create image (イメージの作成)]** ブレードで、カスタム イメージの名前と説明を入力します。 この情報は、VM を作成する際にベースの一覧に表示されます。
   
    ![カスタム イメージの作成ブレード](./media/devtest-lab-create-template/create-custom-image-blade.png)
8. sysprep を VM で実行したかどうかを選択します。 sysprep を VM で実行していない場合は、カスタム イメージから VM を作成するときに sysprep を実行するかどうかを指定します。
9. 完了したら **[OK]** を選択して、カスタム イメージを作成します。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>関連するブログ記事
* [Custom images or formulas? (カスタム イメージか数式か?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
* [Copying Custom Images between Azure DevTest Labs (Azure DevTest Labs 間でのカスタム イメージのコピー)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>次のステップ
VM を作成するときに使用するカスタム イメージを追加したら、次は [VM をラボに追加](devtest-lab-add-vm-with-artifacts.md)します。




<!--HONumber=Nov16_HO3-->


