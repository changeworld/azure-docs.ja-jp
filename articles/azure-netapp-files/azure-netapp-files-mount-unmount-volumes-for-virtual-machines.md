---
title: 仮想マシンの Azure NetApp Files ボリュームをマウントする
description: Azure で Windows 仮想マシンまたは Linux 仮想マシンのボリュームをマウントまたはマウント解除する方法について説明します。
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: eebb0a8dc23a6a442a170dcc6e19495f96ce2921
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930595"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する 

必要に応じて、Windows または Linux 仮想マシンのボリュームをマウント/マウント解除できます。  Linux 仮想マシンのマウント手順は、Azure NetApp Files で使用可能です。  

## <a name="requirements"></a>必要条件 

* NFS ボリュームにアクセスできるようにするには、少なくとも 1 つのエクスポート ポリシーが必要です。
* NFS ボリュームを正常にマウントするには、クライアントと NFS ボリューム間で次の NFS ポートが開いていることを確認します。
    * 111 = `RPCBIND/Portmapper`
    * 635 = `mountd`
    * 2049 = `nfs`
    * 4045 = `nlockmgr` (NFSv3 のみ)
    * 4046 = `status` (NFSv3 のみ)

## <a name="steps"></a>手順

1. **[ボリューム]** ブレードをクリックして、マウントする対象のボリュームを選択します。 
2. 選択したボリュームから **[Mount instructions]** \(マウント手順\) をクリックして、手順に従ってボリュームをマウントします。 

    ![マウント手順 NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![マウント手順 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * NFS ボリュームをマウントしている場合は、必ず `mount` コマンドで `vers`オプションを使用して、マウントするボリュームに対応する NFS プロトコル バージョンを指定します。 
    * NFSv4.1 を使用している場合は、`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT` のコマンドを使用してご自分のファイル システムをマウントします。  
        > [!NOTE]
        > NFSv4.1 を使用する場合、エクスポートをマウントするすべての VM で確実に一意のホスト名を使用します。

3. Azure VM の起動時または再起動時に NFS ボリュームを自動的にマウントしたい場合は、ホストの `/etc/fstab` にエントリを追加します。 

    次に例を示します。`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` は、ボリューム プロパティのブレードにある Azure NetApp Files ボリュームの IP アドレスです。
    * `$FILEPATH` は、Azure NetApp Files ボリュームのエクスポート パスです。
    * `$MOUNTPOINT` は、NFS エクスポートのマウントに使用する、Linux ホスト上に作成されたディレクトリです。

4. NFS を使用して Windows にボリュームをマウントする場合:

    a. まず、ボリュームを Unix または Linux VM にマウントします。  
    b. ボリュームに対して `chmod 777` または `chmod 775` コマンドを実行します。  
    c. Windows 上の NFS クライアントを使用してボリュームをマウントします。
    
5. NFS Kerberos ボリュームをマウントする場合の詳細については、[NFSv4.1 Kerberos 暗号化の構成](configure-kerberos-encryption.md)に関する記事を参照してください。 

## <a name="next-steps"></a>次のステップ

* [Azure NetApp Files 用に NFSv4.1 の既定のドメインを構成する](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS に関する FAQ](./azure-netapp-files-faqs.md#nfs-faqs)
* [ネットワーク ファイル システムの概要](/windows-server/storage/nfs/nfs-overview)
* [NFS Kerberos ボリュームをマウントする](configure-kerberos-encryption.md#kerberos_mount)