---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d1b10b55481b41f42c6c872522d3dd4dd4be0e77
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570600"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) では、[Azure Active Directory Domain Services (Azure AD)](../articles/active-directory-domain-services/overview.md) を使用した、SMB (Server Message Block) 上の ID ベースの認証がサポートされます。 ドメインに参加している Windows 仮想マシン (VM) は、[Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) の資格情報を使用して Azure ファイル共有にアクセスできます。

Azure AD で[ロールベースのアクセス制御 (RBAC)](../articles/role-based-access-control/overview.md) を使用して、ユーザーやグループなどの ID に対する Azure Files 共有レベルのアクセスを管理できます。 Azure Files へのアクセスに使用されるアクセス許可の共通セットを含むカスタムの RBAC ロールを定義することができます。 カスタムの RBAC ロールを Azure AD ID に割り当てると、その ID には、それらのアクセス許可に従って Azure ファイル共有へのアクセス権が与えられます。

プレビューの一環として、Azure Files は、ファイル共有内のすべてのファイルとディレクトリに対する [NTFS DACL](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) の保持、継承、および適用もサポートしています。 ファイル共有から Azure Files に (または逆方向に) データをコピーする場合、NTFS DACL が保持されるように指定することができます。 このように、Azure Files を使用してバックアップ シナリオを実装し、オンプレミスのファイル共有とクラウドのファイル共有の間で NTFS DACLS を保持することができます。 

> [!NOTE]
> - Linux VM では、SMB (Server Message Block) アクセスに対する Azure AD DS 認証はサポートされていません。 サポートされているのは Windows VM のみです。
> - Active Directory ドメイン参加済みコンピューターでは、SMB アクセスに対する Azure AD DS 認証はサポートされていません。 暫定的に、[Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) を使用して、Azure Files へのデータの移行を開始し、オンプレミスの Active Directory ドメイン参加済みマシンから Active Directory 資格情報を使用して、アクセス制御の適用を続行することを検討してください。 
> - SMB アクセスに対する Azure AD DS 認証は、2018 年 9 月 24 日より後に作成されたストレージ アカウントでのみ利用できます。
> - Azure File Sync によって管理される Azure ファイル共有では、SMB アクセスに対する Azure AD DS 認証と NTFS DACL の永続化はサポートされません。
