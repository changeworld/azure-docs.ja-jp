---
title: Azure Stack Edge Pro GPU に VM をデプロイするための Windows VHD から汎用イメージを準備する
description: 汎用 VM イメージの作成を Windows VHD または VHDX ISO から開始する方法について説明します。 この汎用 VM イメージを使用して、Azure Stack Edge Pro GPU デバイスに仮想マシンをデプロイします。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/18/2021
ms.author: alkohli
ms.openlocfilehash: e60f7e7195ef81b3bf17a85c5662824df945ef91
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968461"
---
# <a name="prepare-generalized-image-from-windows-vhd-to-deploy-vms-on-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU に VM をデプロイするための Windows VHD から汎用イメージを準備する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU デバイスに VM をデプロイするには、VM を作成するために使用できるカスタム VM イメージを作成できる必要があります。 この記事では、Windows VHD または VHDX から汎用イメージを準備する方法について説明します。このイメージを使用して、仮想マシンを Windows Stack Edge Pro GPU デバイスにデプロイできます。

ISO を使用した汎用 VM イメージを準備するには、「[ISO イメージから一般化したイメージを作成し、Azure Stack Edge Pro GPU の VM にデプロイする](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)」を参照してください。

## <a name="about-vm-images"></a>VM イメージについて

Windows VHD または VHDX を使用すると、"*特殊化された*" イメージまたは "*一般化された*" イメージを作成できます。 次の表は、"*特殊化*" されたイメージと "*一般化*" されたイメージの主な違いをまとめたものです。

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>ワークフロー

汎用イメージとして使用する Windows VHD の準備を既存の仮想マシンの VHD または VHDX から開始するための高レベルのワークフローには、次の手順があります。

1. Windows VHD からソース VM を準備します。
   1. ソース VHD または VHDX を固定サイズの VHD に変換します。
   1. その VHD を使用して新しい仮想マシンを作成します。<!--Can this procedure be generalized and moved to an include file?-->
1. VM を起動し、Windows オペレーティング システムをインストールします。
1. *sysprep* ユーティリティを使用して VHD を一般化します。
1. 一般化されたイメージを BLOB ストレージにコピーします。

## <a name="prerequisites"></a>前提条件

Azure Stack Edge Pro GPU デバイスで汎用イメージとして使用する Windows VHD を準備する前に、以下を確認します。

- サポート対象バージョンの Windows が含まれる VHD または VHDX を用意してある。 
- Hyper-V マネージャーがインストールされている Windows クライアントにアクセスできる。 
- 準備後に VHD を格納するため、Azure の BLOB ストレージ アカウントにアクセスできる。

## <a name="prepare-source-vm-from-windows-vhd"></a>Windows VHD からソース VM を準備する

VM ソースが Windows VHD または VHDX の場合は、まず、Windows VHD を固定サイズの VHD に変換する必要があります。 固定サイズの VHD を使用して、新しい仮想マシンを作成します。

> [!IMPORTANT]
> これらの手順では、ソース VHD がカスタムの構成と設定を使用して構成されている場合については扱いません。 たとえば、カスタムのファイアウォール規則やプロキシ設定が含まれる VHD を一般化するために、追加のアクションが必要になる場合があります。 これらの追加アクションの詳細については、[Azure Virtual Machines で、Azure にアップロードする Windows VHD を準備する](../virtual-machines/windows/prepare-for-upload-vhd-image.md)ことに関するページを参照してください。

#### <a name="convert-source-vhd-to-a-fixed-size-vhd"></a>ソース VHD を固定サイズの VHD に変換する

お使いのデバイス用の VM イメージを作成するためには、固定サイズの VHD が必要です。 ソースの Windows VHD または VHDX を固定 VHD に変換する必要があります。 

次の手順に従います。

1. お使いのクライアント システムで Hyper-V マネージャーを開きます。 **[ディスクの編集]** に移動します。

    ![Hyper-V マネージャーを開きます](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. **[開始する前に]** ページで、 **[次へ>]** を選択します。

1. **[仮想ハード ディスクの場所]** ページで、変換するソース Windows VHD または VHDX の場所を参照します。 **[次へ>]** を選択します。

    ![[仮想ハード ディスクの場所] ページ](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. **[アクションの選択]** ページで、 **[変換]** を選択し、 **[次へ>]** を選択します。

    ![[アクションの選択] ページ](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. **[ディスク フォーマットの選択]** ページで、 **[VHD]** フォーマットを選択してから、 **[次へ>]** を選択します。

   ![[ディスク フォーマットの選択] ページ](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)

1. **[ディスクの種類の選択]** ページで、 **[固定サイズ]** を選択し、 **[次へ>]** を選択します。

   ![[ディスクの種類の選択] ページ](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)

1. **[ディスクの構成]** ページで、目的の場所に移動して、固定サイズの VHD ディスクの名前を指定します。 **[次へ>]** を選択します。

   ![[ディスクの構成] ページ](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)

1. 概要を確認し、 **[完了]** を選択します。 VHD または VHDX の変換には、数分間かかります。 変換の時間は、ソース ディスクのサイズによって異なります。

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
この記事の以降のすべての手順では、この固定サイズの VHD を使用します。

#### <a name="create-hyper-v-vm-from-the-fixed-size-vhd"></a>容量固定の VHD から Hyper-V VM を作成する

1. **Hyper-V マネージャー** のスコープ ペインで、システム ノードを右クリックしてコンテキスト メニューを開いてから、 **[新規]**  >  **[仮想マシン]** を選択します。

    ![[スコープ] ペインで新しい仮想マシンを選択します。](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. 仮想マシンの新規作成ウィザードの **[開始する前に]** ページで、 **[次へ]** を選択します。

1. **[名前と場所を指定]** ページで、仮想マシンの **名前** と **場所** を指定します。 **[次へ]** を選択します。

    ![VM の名前と場所を指定します](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. **[世代の指定]** ページで、.vhd デバイスのイメージの種類として **[第 1 世代]** を選択してから、 **[次へ]** を選択します。    

    ![世代を指定します](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. 必要なメモリとネットワークの構成を割り当てます。

1. **[仮想ハード ディスクの接続]** ページで、 **[既存の仮想ハード ディスクを使用する]** を選択し、先ほど作成した Windows 固定 VHD の場所を指定してから、 **[次へ]** をクリックします。

    ![[仮想ハード ディスクの接続] ページ](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. **[概要]** を確認してから、 **[完了]** を選択して仮想マシンを作成します。

仮想マシンの作成には数分かかります。

クライアント システムの仮想マシンの一覧に、VM が表示されます。

## <a name="start-vm-and-install-operating-system"></a>VM を起動し、オペレーティング システムをインストールする

仮想マシンの構築を完了するには、仮想マシンを起動し、オペレーティング システムのインストール作業を一通り実施する必要があります。

[!INCLUDE [Connect to Hyper-V VM](../../includes/azure-stack-edge-connect-to-hyperv-vm.md)]

VM に接続したら、マシンのセットアップ ウィザードを完了した後、VM にサインインします。<!--It's not clear what they are doing here. Where does the Machine setup wizard come in?-->

## <a name="generalize-the-vhd"></a>VHD を汎用化する

*sysprep* ユーティリティを使用して VHD を一般化します。 

[!INCLUDE [Generalize the VHD](../../includes/azure-stack-edge-generalize-vhd.md)]

<<<<<<< HEAD
    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    詳細については、[sysprep (システムの準備) の概要](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)に関するページを参照してください。
1.  コマンドが完了すると、VM はシャットダウンされます。 **VM は再起動しないでください**。

## <a name="upload-the-vhd-to-azure-blob-storage"></a>Azure Blob Storage に VHD をアップロードする
=======
これで、Azure Stack Edge Pro GPU で使用する汎用イメージを VHD を使用して作成できるようになりました。
>>>>>>> repo_sync_working_branch

## <a name="upload-generalized-vhd-to-azure-blob-storage"></a>汎用 VHD を Azure Blob Storageにアップロードする

<<<<<<< HEAD
1. Azure Blob Storage に VHD をアップロードします。 [Azure Storage Explorer を使用して VHD をアップロードする](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md)ことに関するページの詳細な手順を参照してください。
1. アップロードが完了したら、アップロードされたイメージを使用して VM イメージや VM を作成できます。 
=======
[!INCLUDE [Upload VHD to Blob storage](../../includes/azure-stack-edge-upload-vhd-to-blob-storage.md)]
>>>>>>> repo_sync_working_branch

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->

## <a name="next-steps"></a>次のステップ

デプロイの性質に応じて、次のいずれかの手順を選択できます。

- [Azure portal を使用して、一般化されたイメージから VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Azure Stack Edge Pro GPU に VM をデプロイするための汎用イメージを ISO から準備する](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
- [特化したイメージを準備し、そのイメージを使用して VM をデプロイする](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md) 
