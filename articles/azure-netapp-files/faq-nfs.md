---
title: Azure NetApp Files の NFS に関する FAQ | Microsoft Docs
description: Azure NetApp Files の NFS プロトコルに関するよくあるご質問 (FAQ) に回答します。
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/19/2021
ms.openlocfilehash: e9c38f573a613debbb9f3e3c6b1da00ab23f92f1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269741"
---
# <a name="nfs-faqs-for-azure-netapp-files"></a>Azure NetApp Files の NFS に関するよくあるご質問

この記事では、Azure NetApp Files の NFS プロトコルに関するよくあるご質問 (FAQ) に回答します。

## <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Azure VM が起動または再起動されたときに、ボリュームが自動的にマウントされるようにしたいと考えています。  NFS の永続ボリュームのホストを構成するにはどうすればよいですか?

VM の起動または再起動時に NFS ボリュームを自動的にマウントする場合は、ホスト上の `/etc/fstab` ファイルにエントリを追加します。 

詳細については、「[Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)」を参照してください。  

## <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp Files でサポートされている NFS のバージョンを何ですか?

Azure NetApp Files では、NFSv3 および NFSv4.1 がサポートされています。 いずれかの NFS バージョンを使用して[ボリュームを作成](azure-netapp-files-create-volumes.md)できます。 

## <a name="how-do-i-enable-root-squashing"></a>ルート スカッシュを有効にするにはどうすればよいですか?

ボリュームのエクスポート ポリシーを使用して、ルート アカウントがボリュームにアクセスできるかどうかを指定できます。 詳細については、「[NFS ボリュームのエクスポート ポリシーを構成する](azure-netapp-files-configure-export-policy.md)」を参照してください。

## <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>複数のボリュームに対して同じファイル パス (ボリューム作成トークン) を使用することはできますか?

はい、できます。 ただし、ファイル パスは各サブネット内で一意である必要があります。     

## <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Windows クライアントを使用して NFS ボリュームにアクセスしようとすると、クライアントによるフォルダーやサブフォルダーの検索に時間がかかるのはなぜですか?

フォルダーとサブフォルダーの検索を高速化するために、Windows クライアントで `CaseSensitiveLookup` が有効になっていることを確認します。

1. 次の PowerShell コマンドを使用して CaseSensitiveLookup を有効にします。   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Windows サーバーでそのボリュームをマウントします。   
    例:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

## <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Azure NetApp Files では NFSv4.1 のファイル ロックはどのようにサポートされますか? 

NFSv 4.1 クライアントの場合、Azure NetApp Files では、リース ベースのモデルですべてのファイル ロックの状態を維持する NFSv4.1 ファイル ロック メカニズムをサポートします。 

RFC 3530 に基づき、Azure NetApp Files では、NFS クライアントによって保持されるすべての状態に対して 1 つのリース期間を定義します。 定義された期間内にクライアントによってリースが更新されない場合、クライアントのリースに関連付けられているすべての状態がサーバーによって解放されます。  

たとえば、ボリュームをマウントするクライアントがタイムアウトを超えて応答しなくなったりクラッシュしたりすると、ロックが解放されます。 クライアントでは、ファイルの読み取りなどの操作を実行することで、明示的または暗黙的にリースを更新できます。   

猶予期間とは、クライアントがサーバーの回復中にロック状態を回収できるようにする特別な処理の期間を定義するものです。 リースの既定のタイムアウトは 30 秒で、猶予期間は 45 秒です。 その期間が経過すると、クライアントのリースが解放されます。 

## <a name="next-steps"></a>次のステップ  

- [Microsoft Azure ExpressRoute に関する FAQ](../expressroute/expressroute-faqs.md)
- [Microsoft Azure Virtual Network に関する FAQ](../virtual-network/virtual-networks-faq.md)
- [Azure サポート要求を作成する方法](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Azure NetApp Files の SMB パフォーマンスに関する FAQ](azure-netapp-files-smb-performance.md)
- [ネットワークに関する FAQ](faq-networking.md)
- [セキュリティに関する FAQ](faq-security.md)
- [パフォーマンスに関する FAQ](faq-performance.md)
- [SMB に関する FAQ](faq-smb.md)
- [容量管理に関する FAQ](faq-capacity-management.md)
- [データの移行と保護に関する FAQ](faq-data-migration-protection.md)
- [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)
- [アプリケーションの回復性に関する FAQ](faq-application-resilience.md)
- [統合に関する FAQ](faq-integration.md)