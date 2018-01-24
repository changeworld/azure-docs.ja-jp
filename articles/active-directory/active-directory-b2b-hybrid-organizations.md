---
title: "ハイブリッド組織向けの Azure Active Directory B2B コラボレーション | Microsoft Docs"
description: "Azure AD B2B コラボレーションによって、パートナーがローカル リソースとクラウド リソースの両方にアクセスできるようにします"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 12/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 2e690eeea6a9f7e1cc10830a913774daa3c66689
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>ハイブリッド組織向けの Azure Active Directory B2B コラボレーション

オンプレミス リソースとクラウド リソースの両方を使用しているハイブリッド組織では、外部パートナーが、ローカル リソースとクラウド リソースの両方にアクセスできるようにするとよいでしょう。 ローカル リソースへのアクセスについては、オンプレミスの Active Directory 環境でパートナー アカウントをローカルで管理できます。 パートナーはパートナー アカウントの資格情報でサインインして、組織のリソースにアクセスします。 パートナーが同じ資格情報を使ってクラウド リソースにアクセスできるようにするために、Azure Active Directory (Azure AD) Connect を使用し、Azure AD B2B ユーザー (UserType がゲストのユーザー) として、パートナー アカウントをクラウドと同期させることができます。

## <a name="identify-unique-attributes-for-usertype"></a>UserType の一意の属性を識別する

UserType 属性の同期を有効にする前に、まず、UserType 属性をオンプレミス Active Directory から派生させる方法を決める必要があります。 つまり、オンプレミス環境内で、外部コラボレーターに対して一意のパラメーターはどれでしょうか。 こうした外部コラボレーターと、組織のメンバーを区別するパラメーターを特定してください。

これには一般的なアプローチが 2 つあります。

- ソース属性として使用する未使用のオンプレミス Active Directory 属性 (extensionAttribute1 など) を指定する。 
- または、UserType 属性の値を他のプロパティから派生させる。 たとえば、オンプレミス Active Directory UserPrincipalName 属性の末尾がドメイン *@partners.fabrikam123.org* である場合、すべてのユーザーを "ゲスト" として同期する必要があるとします。
 
詳細な属性要件については、[UserType の同期の有効化](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype)に関するページをご覧ください。 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>クラウドに対してユーザーが同期するように Azure AD Connect を構成する

一意の属性を識別したら、そのユーザーが Azure AD B2B ユーザー (UserType がゲストのユーザー) としてクラウドと同期するように、Azure AD Connect を構成できます。 承認の観点から、これらのユーザーと、Azure AD B2B コラボレーション招待プロセスによって作成された B2B ユーザーを区別することはできません。

実装手順については、[UserType の同期の有効化](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

- Azure AD B2B コラボレーションの概要については、「[Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)」を参照してください。
- Azure AD Connect の概要については、「[オンプレミスのディレクトリと Azure Active Directory の統合](connect/active-directory-aadconnect.md)」を参照してください。

