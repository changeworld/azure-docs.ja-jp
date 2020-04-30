---
title: 仮想マシンの Azure NetApp Files ボリュームをマウントする
description: Azure で Windows 仮想マシンまたは Linux 仮想マシンのボリュームをマウントまたはマウント解除する方法について説明します。
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: c439ff8df95d759e96d2fc82356bda8551507e8d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084942"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する 

必要に応じて、Windows または Linux 仮想マシンのボリュームをマウント/マウント解除できます。  Linux 仮想マシンのマウント手順は、Azure NetApp Files で使用可能です。  

1. **[ボリューム]** ブレードをクリックして、マウントする対象のボリュームを選択します。 
2. 選択したボリュームから **[Mount instructions]** \(マウント手順\) をクリックして、手順に従ってボリュームをマウントします。 

    ![マウント手順 NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![マウント手順 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    NFSv4.1 を使用している場合は、`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT` のコマンドを使用してご自分のファイル システムをマウントします。  

3. Azure VM の起動時または再起動時に NFS ボリュームを自動的にマウントしたい場合は、ホストの `/etc/fstab` にエントリを追加します。 

    次に例を示します。`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` は、ボリューム プロパティのブレードにある Azure NetApp Files ボリュームの IP アドレスです。
    * `$FILEPATH` は、Azure NetApp Files ボリュームのエクスポート パスです。
    * `$MOUNTPOINT` は、NFS エクスポートのマウントに使用する、Linux ホスト上に作成されたディレクトリです。

4. NFS を使用して Windows にボリュームをマウントする場合:

    a. まず、ボリュームを Unix または Linux VM にマウントします。  
    b. ボリュームに対して `chmod 777` または `chmod 775` コマンドを実行します。  
    c. Windows 上の NFS クライアントを使用してボリュームをマウントします。

## <a name="next-steps"></a>次のステップ

* [Azure NetApp Files 用に NFSv4.1 の既定のドメインを構成する](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS に関する FAQ](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [ネットワーク ファイル システムの概要](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
