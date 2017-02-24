---
title: "Azure AD Connect とフェデレーション | Microsoft Docs"
description: "このページは、Azure AD Connect を使用した AD FS の操作に関するすべてのドキュメントの中心となる場所です。"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: fd3699e4ce423ae9490d5bf788729c43eaa623eb
ms.openlocfilehash: 19ebfae5de8576402348e3163f99a54b9308a078


---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect とフェデレーション
Azure AD Connect により、オンプレミスの AD FS と Azure AD でフェデレーションを構成できます。 フェデレーション サインオンを設定すると、ユーザーは自分のオンプレミスのパスワードを使用して Azure AD ベースのサービスにサインオンすることができます。また、企業ネットワークにアクセスしているときはパスワードを再入力する必要はありません。 AD FS を使用するフェデレーション オプションでは、新しい AD FS をデプロイすることも、Windows Server 2012 R2 ファームの既存の AD FS を指定することもできます。

このトピックは Azure AD Connect のフェデレーション関連機能に関する情報のホームであり、関連する他のすべてのトピックへのリンクを示します。 Azure AD Connect へのリンクについては、「オンプレミス ID と Azure Active Directory の統合」を参照してください。

## <a name="azure-ad-connect---federation-topics"></a>Azure AD Connect - フェデレーションに関するトピック
| トピック | 内容 |
|:--- |:--- |
| **Azure AD Connect ユーザーのサインイン オプション** | |
| [ユーザー サインイン オプションについて](active-directory-aadconnect-user-signin.md) |さまざまなユーザー サインイン オプションの説明とそれらの Azure サインイン ユーザー エクスペリエンスへの影響 |
| **Azure AD Connect を使用した AD FS のインストール** | |
| [前提条件](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Azure AD Connect 経由の正常な AD FS インストールの前提条件 |
| [AD FS ファームの構成](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Azure AD Connect を使用して、新しい AD FS ファームをインストールする方法 |
| **AD FS 構成の変更** | |
| [信頼の修復](active-directory-aadconnect-federation-management.md#repairthetrust) |オンプレミスの AD FS と Office 365 または Azure 間の現在の信頼を修復する方法 |
| [新しい AD FS サーバーの追加](active-directory-aadconnect-federation-management.md#addadfsserver) |初期インストール後の追加の AD FS サーバーによる AD FS ファームの拡張 |
| [新しい AD FS WAP サーバーの追加](active-directory-aadconnect-federation-management.md#addwapserver) |初期インストール後の追加の WAP サーバーによる AD FS ファームの拡張 |
| [新しいフェデレーション ドメインの追加](active-directory-aadconnect-federation-management.md#addfeddomain) |Azure AD とフェデレーションする別のドメインの追加 |
| **インストール後のタスク** | |
| [カスタムの会社のロゴまたはイラストの追加](active-directory-aadconnect-federation-management.md#customlogo) |AD FS サインイン ページに表示されるカスタムのロゴを指定して、サインイン エクスペリエンスを変更する |
| [サインインの説明の追加](active-directory-aadconnect-federation-management.md#addsignindescription) |AD FS サインイン ページのサインインの説明の変更 |
| [AD FS の要求規則の変更](active-directory-aadconnect-federation-management.md#modclaims) |Azure AD Connect Sync 構成に対応する AD FS の要求規則の変更または追加 |

## <a name="additional-resources"></a>その他のリソース
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)
* [Azure への AD FS のデプロイ](active-directory-aadconnect-azure-adfs.md)
* [Azure Traffic Manager を使用した Azure への可用性に優れた地域間 AD FS デプロイ](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)



<!--HONumber=Feb17_HO3-->


