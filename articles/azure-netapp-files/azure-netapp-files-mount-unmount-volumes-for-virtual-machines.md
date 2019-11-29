---
title: Windows または Linux 仮想マシンの Azure NetApp Files ボリュームをマウントまたはマウント解除する | Microsoft Docs
description: 仮想マシンまたは Linux 仮想マシンのボリュームをマウントまたはマウント解除する方法について説明します。
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
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 4cd3bc7e4f95869d3efd2d92a7cdf1addc7ce5b2
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953098"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する 

必要に応じて、Windows または Linux 仮想マシンのボリュームをマウント/マウント解除できます。  Linux 仮想マシンのマウント手順は、Azure NetApp Files で使用可能です。  

1. **[ボリューム]** ブレードをクリックして、マウントする対象のボリュームを選択します。 
2. 選択したボリュームから **[Mount instructions]** \(マウント手順\) をクリックして、手順に従ってボリュームをマウントします。 

    ![マウント手順 NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![マウント手順 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
NFSv4.1 を使用している場合は、次のコマンドを使用してファイル システムをマウントします。  

`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

## <a name="next-steps"></a>次の手順

* [Azure NetApp Files 用に NFSv4.1 の既定のドメインを構成する](azure-netapp-files-configure-nfsv41-domain.md)
* Azure VM の起動時または再起動時に NFS ボリュームを自動的にマウントする方法については、[NFS に関する FAQ](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs) を参照してください。
