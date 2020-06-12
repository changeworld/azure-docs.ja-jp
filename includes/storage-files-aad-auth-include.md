---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d470160a9b54af8751371aa4ca58202646c8fab8
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84263358"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) では、[オンプレミス Active Directory Domain Services (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) と[ Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md) を介した、サーバー メッセージ ブロック (SMB) 経由の ID ベースの認証がサポートされます。 この記事では、Azure Files がオンプレミスまたは Azure のドメイン サービスを利用し、SMB 経由の Azure ファイル共有への ID ベースのアクセスをサポートする方法に焦点を当てています。 Azure ファイル共有のために ID ベースのアクセスを有効にすると、既存のディレクトリ サービスを置換することなく、既存のファイル サーバーを Azure ファイル共有に置換できます。共有への継ぎ目のないユーザー アクセスを維持できます。 

Azure Files では、共有とディレクトリ/ファイル レベルの両方へのユーザー アクセスで承認が適用されます。 共有レベルのアクセス許可の割り当ては、[ロールベースのアクセス制御 (RBAC)](../articles/role-based-access-control/overview.md) モデルを使用して管理されている Azure Active Directory (Azure AD) ユーザーまたはグループで実行できます。 RBAC では、ファイルへのアクセスに使用する資格情報を使用できるようにするか、Azure AD に同期する必要があります。 Azure ファイル共有に読み取りアクセス権を付与するには、記憶域ファイル データの SMB 共有の閲覧者などの組み込みの RBAC ロールを Azure AD のユーザーまたはグループに割り当てることができます。

ディレクトリまたはファイル レベルでは、Azure Files は、Windows ファイル サーバーと同様に、[Windows DACL](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) の保持、継承、および適用をサポートしています。 既存のファイル共有と Azure ファイル共有の間で SMB 経由でデータをコピーするとき、Windows DACL を維持することを選択できます。 承認を適用するかどうかにかかわらず、Azure ファイル共有を利用し、データと共に ACL をバックアップできます。 
