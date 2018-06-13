---
title: Azure AD B2B ユーザーとしてローカルで管理されているパートナー アカウントにクラウド リソースへのアクセス権を付与する | Microsoft Docs
description: Azure AD B2B コラボレーションと同じ資格情報を使用して、ローカルで管理されている外部パートナーにローカル リソースとクラウド リソースの両方へのアクセス権を付与します。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/24/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: dc4c8b3f5cb20eaf76fc0ee47d195aca26d06f90
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33928576"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Azure AD B2B コラボレーションを使用してローカルで管理されたパートナーのアカウントにクラウド リソースへのアクセスを許可する

Azure Active Directory (Azure AD) 以前は、オンプレミスの ID システムを持つ組織は、パートナーのアカウントを従来、オンプレミスのディレクトリで管理してきました。 このような組織では、アプリケーションを Azure AD に移行するときに、パートナーが必要なリソースにアクセスできるようにする必要があります。 リソースがオンプレミスにあるかクラウド内にあるかは問題ではありません。 また、パートナー ユーザーがオンプレミスと Azure AD の両方のリソースに同じサインイン資格情報を使用できるようにする必要があります。 

オンプレミス ディレクトリに外部パートナーのアカウントを作成する場合 (たとえば、partners.contoso.com ドメイン内に Wendy Moran という外部ユーザー用にサインイン名 "wmoran" のアカウントを作成する場合) これらのアカウントをクラウドと同期することができます。 具体的には、Azure AD Connect を使用し、Azure AD B2B ユーザー (UserType が Guest のユーザー) として、パートナー アカウントをクラウドと同期することができます。 その結果、パートナー ユーザーは、ローカル アカウントと同じ資格情報を使用してクラウド リソースにアクセスすることができます。 

## <a name="identify-unique-attributes-for-usertype"></a>UserType の一意の属性を識別する

UserType 属性の同期を有効にする前に、まず、UserType 属性をオンプレミス Active Directory から派生させる方法を決める必要があります。 つまり、オンプレミス環境内で、外部コラボレーターに対して一意のパラメーターはどれでしょうか。 こうした外部コラボレーターと、組織のメンバーを区別するパラメーターを特定してください。

これには一般的なアプローチが 2 つあります。

- ソース属性として使用する未使用のオンプレミス Active Directory 属性 (extensionAttribute1 など) を指定する。 
- または、UserType 属性の値を他のプロパティから派生させる。 たとえば、オンプレミス Active Directory UserPrincipalName 属性の末尾がドメイン *@partners.contoso.com* である場合、すべてのユーザーを "ゲスト" として同期する必要があるとします。
 
詳細な属性要件については、[UserType の同期の有効化](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype)に関するページをご覧ください。 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>クラウドに対してユーザーが同期するように Azure AD Connect を構成する

一意の属性を識別したら、そのユーザーが Azure AD B2B ユーザー (UserType がゲストのユーザー) としてクラウドと同期するように、Azure AD Connect を構成できます。 承認の観点から、これらのユーザーと、Azure AD B2B コラボレーション招待プロセスによって作成された B2B ユーザーを区別することはできません。

実装手順については、[UserType の同期の有効化](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

- [ハイブリッド組織向けの Azure Active Directory B2B コラボレーション](active-directory-b2b-hybrid-organizations.md)
- [Azure AD の B2B ユーザーにオンプレミスのアプリケーションへのアクセス許可する](active-directory-b2b-hybrid-cloud-to-on-premises.md)
- Azure AD Connect の概要については、「[オンプレミスのディレクトリと Azure Active Directory の統合](connect/active-directory-aadconnect.md)」を参照してください。

