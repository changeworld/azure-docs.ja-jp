---
title: Azure Stack Edge Pro GPU デバイスの VM イメージを作成する
description: Azure Stack Edge Pro GPU デバイスで使用する Linux または Windows の VM イメージを作成する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/04/2020
ms.author: alkohli
ms.openlocfilehash: 745d0df07b6d0d01acf0d564df8c242e16f3f56d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90890997"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro デバイスのカスタム VM イメージを作成する

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Azure Stack Edge Pro デバイスで VM をデプロイするには、VM の作成に使用するカスタム VM イメージを作成できる必要があります。 この記事では、Azure Stack Edge Pro デバイスに VM をデプロイするときに使用できる Linux または Windows VM のカスタム イメージを作成するために必要な手順について説明します。

## <a name="vm-image-workflow"></a>VM イメージのワークフロー

このワークフローでは、Azure に仮想マシンを作成して VM をカスタマイズし、その VM に対応する VHD を一般化してダウンロードする必要があります。 この一般化した VHD が Azure Stack Edge Pro にアップロードされ、その VHD からマネージド ディスクが作成されます。さらに、マネージド ディスクからイメージが作成されて、最終的にそのイメージから VM が作成されます。   

詳細については、[Azure PowerShell を使用して Azure Stack Edge Pro デバイスに VM をデプロイする方法](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)に関するページを参照してください。


## <a name="create-a-windows-custom-vm-image"></a>Windows カスタム VM イメージを作成する

Windows VM イメージを作成するには、次の手順に従います。

1. Windows 仮想マシンを作成します。 詳細については、[チュートリアルの 「Azure PowerShell を使用して Windows VM を作成および管理する」](../virtual-machines/windows/tutorial-manage-vm.md)を参照してください。

2. 既存の OS ディスクをダウンロードします。

    - [VHD のダウンロード](../virtual-machines/windows/download-vhd.md)に関するページの手順に従います。

    - 前述の手順で説明されているコマンドの代わりに、次の `sysprep` コマンドを使用してください。
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       「[Sysprep (システムの準備) の概要](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)」も参照してください。

では、この VHD を使用して、Azure Stack Edge Pro デバイスに VM を作成、デプロイしましょう。

## <a name="create-a-linux-custom-vm-image"></a>Linux カスタム VM イメージを作成する

Linux VM イメージを作成するには、次の手順に従います。

1. Linux 仮想マシンを作成する。 詳細については、[チュートリアルの 「Azure CLI を使用した Linux VM の作成と管理」](../virtual-machines/linux/tutorial-manage-vm.md)を参照してください。

1. VM のプロビジョニングを解除します。 Azure VM エージェントを使用し、マシン固有のファイルとデータを削除します。 ソース Linux VM で `-deprovision+user` パラメーターを指定して `waagent` コマンドを実行します。 詳細については、「[Azure Linux エージェントの理解と使用](../virtual-machines/extensions/agent-linux.md)」を参照してください。

    1. SSH クライアントを使って Linux VM に接続します。
    2. SSH のウィンドウで、次のコマンドを入力します。
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > このコマンドはイメージとしてキャプチャする VM に対して実行します。 このコマンドでは、イメージからすべての機密情報が削除されることや、イメージが再配布に適した状態になることが保証されるわけではありません。 `+user` パラメーターにより、前回プロビジョニングされたユーザー アカウントも削除されます。 ユーザー アカウントの資格情報を VM に保持するには、`-deprovision` のみを使用します。
     
    3. 「**y**」と入力して続行します。 `-force` パラメーターを追加すると、この確認手順を省略できます。
    4. コマンドが完了したら、**exit** を入力して SSH クライアントを閉じます。  この時点で、VM はまだ実行されています。


1. [既存の OS ディスクをダウンロード](../virtual-machines/linux/download-vhd.md)します。

では、この VHD を使用して、Azure Stack Edge Pro デバイスに VM を作成、デプロイしましょう。 Linux カスタム イメージは、次の 2 つの Azure Marketplace イメージを使用して作成できます。

|項目名  |説明  |Publisher  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |Ubuntu Server は、クラウド環境の世界で最も人気のある Linux です。|Canonical|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux は最も人気のある Linux ディストリビューションの 1 つです。     |credativ|

使用できる可能性のある Azure Marketplace イメージの完全な一覧 (現在テストは未実施) については、「[Azure Stack Hub で使用できる Azure Marketplace 項目](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910)」を参照してください。


## <a name="next-steps"></a>次のステップ

[Azure Stack Edge Pro デバイスに VM をデプロイする](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)。
