---
title: Azure NetApp Files の SMB に関する FAQ | Microsoft Docs
description: Azure NetApp Files の SMB プロトコルに関するよくあるご質問 (FAQ) に回答します。
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: 7b1c6b1e3e9814ed83c92ada2388ff54f5b3829c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270018"
---
# <a name="smb-faqs-for-azure-netapp-files"></a>Azure NetApp Files の SMB に関するよくあるご質問

この記事では、Azure NetApp Files の SMB プロトコルに関するよくあるご質問 (FAQ) に回答します。

## <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Azure NetApp Files でサポートされている SMB のバージョンは何ですか?

Azure NetApp Files では、SMB 2.1 と SMB 3.1 (SMB 3.0 のサポートを含む) がサポートされています。 

## <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB でのアクセスに Active Directory 接続は必要ですか? 

はい。SMB ボリュームをデプロイする前に Active Directory の接続を作成する必要があります。 指定したドメイン コントローラーは、正常に接続するためには、Azure NetApp Files の委任されたサブネットからアクセス可能である必要があります。  詳細については、「[SMB ボリュームを作成する](./azure-netapp-files-create-volumes-smb.md)」を参照してください。 

## <a name="how-many-active-directory-connections-are-supported"></a>サポートされている Active Directory 接続はいくつですか?

Active Directory (AD) 接続を構成できるのは、サブスクリプションごとおよびリージョンごとに 1 つだけです。 詳細については、「[Active Directory 接続の要件](create-active-directory-connections.md#requirements-for-active-directory-connections)」を参照してください。 

ただし、同じサブスクリプションと同じリージョンにある複数の NetApp アカウントを、いずれかの NetApp アカウントで作成された共通の AD サーバーにマップすることができます。 「[同じサブスクリプションとリージョンにある複数の NetApp アカウントを AD 接続にマップする](create-active-directory-connections.md#shared_ad)」を参照してください。 

## <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp Files で Azure Active Directory はサポートされていますか? 

[Azure Active Directory (AD) Domain Services](../active-directory-domain-services/overview.md) と [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) の両方がサポートされます。 Azure NetApp Files で既存の Active Directory ドメイン コントローラーを使用することができます。 ドメイン コントローラーは、仮想マシンとして Azure に配置することも、ExpressRoute またはサイト間 VPN 経由でオンプレミスに配置することもできます。 Azure NetApp Files は現時点で、AD join for [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) をサポートしていません。

Azure Active Directory Domain Services と組み合わせて Azure NetApp Files を使用している場合、NetApp アカウント用に Active Directory を構成する際の組織単位のパスは `OU=AADDC Computers` になります。

## <a name="what-versions-of-windows-server-active-directory-are-supported"></a>サポートされる Windows Server Active Directory のバージョンを教えてください。

Azure NetApp Files では、Windows Server 2008r2SP1-2019 バージョンの Active Directory Domain Services がサポートされています。

## <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>SMB 共有に接続すると問題が発生します。 どうすればよいですか。

ベスト プラクティスとして、コンピューターのクロック同期の最大許容値を 5 分に設定します。 詳細については、[コンピューターのクロック同期の最大許容値](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11))に関するページをご覧ください。 

## <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>コンピューターの管理コンソール (MMC) を使用して、`SMB Shares`、`Sessions`、および `Open Files` を管理できますか?

コンピューターの管理コンソール (MMC) を使用した `SMB Shares`、`Sessions`、および `Open Files` の管理は、現在サポートされていません。

## <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>ポータルを使用して SMB ボリュームの IP アドレスを取得するにはどうすればよいですか?

ボリュームの概要ペインの **[JSON ビュー]** リンクを使用して、 **[プロパティ]**  ->  **[mountTargets]** の下で **[startIp]** 識別子を探します。

## <a name="can-an-azure-netapp-files-smb-share-act-as-an-dfs-namespace-dfs-n-root"></a>Azure NetApp Files SMB 共有を、DFS 名前空間 (DFS-N) のルートとして使用できますか。

いいえ。 ただし、Azure NetApp Files SMB 共有は、DFS 名前空間 (DFS-N) のフォルダー ターゲットとして使用できます。   
Azure NetApp Files SMB 共有を DFS-N のフォルダー ターゲットとして使用するには、[DFS のフォルダー ターゲット追加](/windows-server/storage/dfs-namespaces/add-folder-targets#to-add-a-folder-target)に関する記事の手順に従って、Azure NetApp Files SMB 共有のマウント ポイントの汎用名前付け規則 (UNC) パスを設定します。  

## <a name="can-the-smb-share-permissions-be-changed"></a>SMB の共有アクセス許可を変更することはできますか?   

いいえ、共有アクセス許可を変更することはできません。 ただし、`root` ボリュームの NTFS アクセス許可は、[NTFS ファイルとフォルダーのアクセス許可](azure-netapp-files-create-volumes-smb.md#ntfs-file-and-folder-permissions)の手順で変更することができます。 


## <a name="next-steps"></a>次のステップ  

- [Azure NetApp Files の SMB パフォーマンスに関する FAQ](azure-netapp-files-smb-performance.md)
- [Azure サポート要求を作成する方法](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [ネットワークに関する FAQ](faq-networking.md)
- [セキュリティに関する FAQ](faq-security.md)
- [パフォーマンスに関する FAQ](faq-performance.md)
- [NFS に関する FAQ](faq-nfs.md)
- [容量管理に関する FAQ](faq-capacity-management.md)
- [データの移行と保護に関する FAQ](faq-data-migration-protection.md)
- [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)
- [アプリケーションの回復性に関する FAQ](faq-application-resilience.md)
- [統合に関する FAQ](faq-integration.md)
