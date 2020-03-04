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
ms.openlocfilehash: b248bbb526baf355faf2564358884fd83422b037
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565089"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) では、[Active Directory (AD)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (プレビュー) および[ Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (GA) を介した、サーバー メッセージ ブロック (SMB) 経由の ID ベースの認証がサポートされます。 この記事では、Azure Files がオンプレミスまたは Azure のいずれかのドメイン サービスを利用して、SMB 経由の Azure Files への ID ベースのアクセスをサポートする方法に焦点を当てています。 これにより、既存のファイル サーバーを Azure Files に置き換えて、既存のディレクトリ サービスを使用し続けることができるため、共有へのシームレスなユーザー アクセスを維持できます。 

Azure Files では、共有およびディレクトリ/ファイル レベルの両方へのユーザー アクセスで承認が適用されます。 共有レベルのアクセス許可の割り当ては、一般的な [ロールベースのアクセス制御 (RBAC)](../articles/role-based-access-control/overview.md) モデルを使用して管理されている Azure AD ユーザーまたはグループに割り当てることができます。 RBAC では、ファイルへのアクセスに使用する資格情報を使用できるようにするか、Azure AD に同期する必要があります。 Azure ファイル共有に読み取りアクセス権を付与するには、記憶域ファイル データの SMB 共有の閲覧者などの組み込みの RBAC ロールを Azure AD のユーザーまたはグループに割り当てることができます。

ディレクトリまたはファイル レベルでは、Azure Files は、Windows ファイル サーバーと同様に、[Windows DACL](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) の保持、継承、および適用をサポートしています。 SMB 経由でデータをファイル共有から Azure Files、またはその逆にコピーする場合は、Windows DACL を保持することを選択できます。 承認を適用するかどうかにかかわらず、Azure Files を利用し、データと共に ACL をバックアップできます。 
