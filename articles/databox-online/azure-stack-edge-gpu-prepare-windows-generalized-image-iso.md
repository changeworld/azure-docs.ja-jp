---
title: ISO イメージから一般化したイメージを作成し、Azure Stack Edge Pro GPU の VM にデプロイする
description: 一般化した Windows VM イメージを ISO イメージから作成する方法を説明します。 この一般化したイメージを使用して Azure Stack Edge Pro GPU デバイスに仮想マシンをデプロイします。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2021
ms.author: alkohli
ms.openlocfilehash: 22f9c86342428d3d011eb787745f74ac6ea026f8
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970415"
---
# <a name="prepare-generalized-image-from-iso-to-deploy-vms-on-azure-stack-edge-pro-gpu"></a>ISO イメージから一般化したイメージを作成し、Azure Stack Edge Pro GPU の VM にデプロイする 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU デバイスに VM をデプロイするには、VM の作成に使用するカスタム仮想マシン (VM) イメージを作成できる必要があります。 この記事では、ISO インストール メディアを使用して Windows VM イメージを作成し、そのイメージを一般化して、Azure Stack Edge Pro GPU デバイスに複数の新しい VM をデプロイできるようにする方法を説明します。

Windows VHD または VHDX から一般化したイメージを作成する方法は、[Windows VHD から一般化したイメージを作成して Azure Stack Edge Pro GPU にデプロイする](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)方法に関する記事をご覧ください。

## <a name="about-vm-images"></a>VM イメージについて

Windows VHD または VHDX を使用すると、"*特殊化された*" イメージまたは "*一般化された*" イメージを作成できます。 次の表は、"*特殊化*" されたイメージと "*一般化*" されたイメージの主な違いをまとめたものです。

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>ワークフロー 

ISO イメージを使用して、一般化した Windows VHD のイメージを作成する全体的なワークフローは次のとおりです。

1. ISO イメージを使用してソースとなる VM を作成します。
   1. 新しい、空の、サイズ固定の VHD を Hyper-V Manager に作成します。
   1. その VHD を使用して新しい仮想マシンを作成します。
   1. 新しい VM の DVD ドライブに ISO イメージをマウントします。
1. VM を起動し、Windows オペレーティング システムをインストールします。
1. *sysprep* ユーティリティを使用して VHD を一般化します。
1. 一般化したイメージを Azure Blob ストレージにコピーします。

## <a name="prerequisites"></a>前提条件

ISO イメージを使用して一般化した Windows VHD イメージを作成する前に、次のことを確認してください。

- サポートしているバージョンの Windows の ISO イメージを、一般化した VHD イメージの作成元として用意している。 Windows の ISO イメージは [Microsoft Evaluation Center](https://www.microsoft.com/en-us/evalcenter/) でダウンロードできます。

- Hyper-V マネージャーがインストールされている Windows クライアントにアクセスできる。

- 作成した VHD を保存する Azure Blob ストレージ アカウントへのアクセス権がある。

## <a name="prepare-source-vm-using-an-iso"></a>ISO イメージを使用してソース VM を作成する

ISO イメージを使用してオペレーティング システムを VM イメージにインストールするため、まず、空の、サイズ固定の VHD を Hyper-V Manager に作成します。 その VHD を使用して仮想マシンを作成します。 ISO イメージを仮想マシンに適用します。

#### <a name="create-new-vhd-in-hyper-v-manager"></a>Hyper-V Manager に新しい VHD を作成する

最初に、新しい Generation 1 VHD を Hyper-V Manager に作成します。これが新しい仮想マシンのソース VHD になります。

この手順で VHD を作成します。

1. お使いのクライアント システムで Hyper-V マネージャーを開きます。 **[Action]\(アクション\)** で、 **[New]\(新規\)** 、 **[Hard Disk]\(ハード ディスク\)** を順にクリックします。

   ![[New]\(新規\)、[Hard Disk]\(ハード ディスク\) を順にクリックする](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-01.png)

1. **[Choose Disk Format]\(ディスク フォーマットの選択\)** で **[VHD]** を選びます。 **[Next >]\(次へ >\)** をクリックします。 

   ![ディスク フォーマットに VHD を選ぶ](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-02.png)

2. **[Choose Disk Type]\(ディスクの種類を選択\)** で **[Fixed size]\(固定サイズ\)** を選びます。 **[Next >]\(次へ >\)** をクリックします。

   ![ディスクの種類に固定サイズを選ぶ](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-03.png)

3. **[Specify Name and Location]\(名前と場所の指定\)** に新しい VHD の名前と場所を入力します。 **[Next >]\(次へ >\)** をクリックします。

   ![VHD の名前と場所を入力する](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-04.png)

4. **[ディスクの設定]\(Configure Disk\)** で **[Create a new blank virtual hard disk]\(新しい空の仮想ハード ディスクを作成\)** をクリックして作成するディスクのサイズを入力します (Windows Server では通常 20 GB 以上です)。 **[Next >]\(次へ >\)** をクリックします。

   ![新しい空の仮想ハード ディスク作成とサイズ指定の設定](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-05.png)

5. **[Summary]\(概要\)** で、指定内容を確認して **[Finish]\(終了\)** をクリックし、新しい VHD を作成します。 作成する VHD のサイズにより異なりますが、この処理には 5 分以上かかります。

   ![VHD 設定の概要](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-06.png)

#### <a name="create-hyper-v-vm-from-vhd"></a>VHD から Hyper-V VM を作成する

作成した VHD を使用して新しい仮想マシンを作成します。

この手順で、新しい仮想マシンを作成します。

1. Windows クライアントで Hyper-V Manager を開きます。

2. **[Actions]\(アクション\)** ペインで **[New]\(新規\)** 、 **[Virtual Machine]\(仮想マシン\)** を順にクリックします。

   ![右側のメニューで [New]\(新規\)、[Virtual Machine]\(仮想マシン\) を順にクリックする。](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-07.png)

3. New Virtual Machine Wizard (新規仮想マシン ウィザード) で VM の名前と場所を指定します。

   ![New Virtual Machine Wizard (新規仮想マシン ウィザード)、[Specify Name and Location]\(名前と場所の指定\)](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-08.png)

4. **[Specify Generation]\(世代の指定\)** で **[Generation 1]** を選びます。 **[Next >]\(次へ >\)** をクリックします。

   ![New Virtual Machine Wizard (新規仮想マシン ウィザード) で作成する仮想マシンの世代を選ぶ](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-09.png)

5. **[Assign Memory]\(メモリの割り当て\)** で必要なメモリを仮想マシンに割り当てます。 **[Next >]\(次へ >\)** をクリックします。

   ![New Virtual Machine Wizard (新規仮想マシン ウィザード)、[Assign Memory]\(メモリの割り当て\)](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-10.png)

6. **[Configure Networking]\(ネットワークの設定\)** でネットワークの構成を入力します。 **[Next >]\(次へ >\)** をクリックします。

   ![New Virtual Machine Wizard (新規仮想マシン ウィザード)、[Configure Networking]\(ネットワークの設定\)](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-11.png)

7. **[Connect Virtual Hard Disk]\(仮想ハード ディスクの接続\)** で **[Use an existing virtual hard disk]\(既存の仮想ハード ディスクを使用\)** を選び、前の手順で作成した固定 VHD を選択します。 **[Next >]\(次へ >\)** をクリックします。 

   ![New Virtual Machine Wizard (新規仮想マシン ウィザード) で、既存の仮想ハード ディスクをソースに選択する](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-12.png)

8. 概要を確認し、 **[Finish]\(終了\)** をクリックして仮想マシンを作成します。 

#### <a name="mount-iso-image-on-dvd-drive-of-vm"></a>ISO イメージを VM の DVD ドライブにマウントする

新しい仮想マシンの作成後、この手順で ISO イメージを仮想マシンの DVD ドライブにマウントします。

1. Hyper-V Manager で作成した VM を選択し、 **[Settigns]\(設定\)** をクリックします。
 
   ![Hyper-V Manager で仮想マシンの設定を開く](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-13.png)

2. **[BIOS]** で、 **[CD]** が **[Startup order]\(起動順序\)** リストの一番上にあることを確認します。

   ![BIOS の設定で、[CD] が [Startup order]\(起動順序\) の一番上にある必要がある](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-14.png)


3. **[DVD Drive]\(DVD ドライブ\)** で **[Image file]\(イメージ ファイル\)** をクリックし、ISO イメージを選択します。  

   ![DVD ドライブの設定で、VHD のイメージ ファイルを選択する](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-15.png)

4. **[OK]** をクリックして VM の設定を保存します。

## <a name="start-vm-and-complete-os-installation"></a>VM を起動し、OS のインストールを完了する

仮想マシンの構築を完了するには、仮想マシンを起動し、オペレーティング システムのインストール作業を一通り実施する必要があります。

[!INCLUDE [Connect to Hyper-V VM](../../includes/azure-stack-edge-connect-to-hyperv-vm.md)]

> [!NOTE]
> Windows Server 2019 Standard オペレーティング システムを仮想マシンにインストールする場合は、 **[BIOS]** の設定項目を **[IDE]** に変更してから [VHD の一般化を行う](#generalize-the-vhd)必要があります。 

## <a name="generalize-the-vhd"></a>VHD を汎用化する

*sysprep* ユーティリティを使用して VHD を一般化します。

1. Windows Server 2019 Standard VM を一般化する場合は、VHD を一般化する前に、IDE を 仮想マシンの **[BIOS]** 設定の第一に持って来てください。

    1. Hyper-V Manager で VM を選択し、 **[Setting]\(設定\)** をクリックします。
 
       ![Hyper-V Manager で、選択した VM の設定を開く操作のスクリーンショット](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-01.png)

     1. **[BIOS]** で、 **[Startup order]\(起動順序\)** リストの一番上に **[IDE]** を持って来ます。 **[OK]** をクリックして設定を保存します。

        ![Hyper-V Manager の VM の BIOS 設定で、起動順序の一番上に IDE がある画面のスクリーンショット](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-02.png)

1. VM 内でコマンド プロンプトを開きます。

1. 次のコマンドを実行して VHD を一般化します。 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    
    詳しくは「[Sysprep (システム準備) の概要](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)」をご覧ください。

1.  コマンドが完了すると、VM はシャットダウンされます。 **VM は再起動しないでください**。

<!--[!INCLUDE [Generalize the VHD](../../includes/azure-stack-edge-generalize-vhd.md)]-->

これで、VHD を使用して一般化したイメージを作成し、それを Azure Stack Edge Pro GPU で使用できるようになりました。

## <a name="upload-generalized-vhd-to-azure-blob-storage"></a>一般化した VHD を Azure Blob ストレージにアップロードする

[!INCLUDE [Upload VHD to Blob storage](../../includes/azure-stack-edge-upload-vhd-to-blob-storage.md)]

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用して、一般化されたイメージから VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Azure Stack Edge Pro GPU に VM をデプロイするために Windows VHD から一般化されたイメージを準備する](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [特定の目的に特化したイメージを作成し、そのイメージを使用して VM をデプロイする](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md) 
