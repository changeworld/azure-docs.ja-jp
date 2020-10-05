---
title: ID 管理用の Azure Active Directory テナントを使用してアプリを表示する
description: ID 管理用の Azure Active Directory テナントを使用してすべてのアプリを表示する方法について説明します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 5229b123986149903d562bc0b3d6902c0136f647
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707885"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>ID 管理用の Azure AD テナントを使用してアプリを表示する
「[アプリケーション管理のクイックスタート シリーズ](view-applications-portal.md)」では、基本情報を紹介します。 ID 管理用の Azure AD テナントを使用して、すべてのアプリを表示する方法について説明します。 この記事では、表示されるアプリの種類についてもう少し詳しく説明します。

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>特定のアプリケーションがすべてのアプリケーション リストに表示される理由
**[すべてのアプリケーション]** にフィルターすると、 **[すべてのアプリケーションの** **一覧]** に、テナントのすべてのサービス プリンシパル オブジェクトが表示されます。 サービス プリンシパル オブジェクトは、さまざまな方法でこのリストに表示できます。
- アプリケーション ギャラリーから、次のようなアプリケーションを追加する場合:
   - **Azure AD - エンタープライズ アプリケーション** – Azure AD portal で **[エンタープライズ アプリケーション]** オプションを使用してテナントに追加されたアプリ。 通常、SAML 標準を使用して統合されたアプリです。
   - **Azure AD - アプリの登録** – Azure AD portal で **[アプリの登録]** オプションを使用してテナントに追加されたアプリ。 通常、Open ID Connect と OAuth 標準を使用してカスタム開発されたアプリです。
   - **アプリケーション プロキシ アプリケーション** - 外部へのセキュリティで保護されたシングル サインオンを提供するオンプレミスの環境で実行されるアプリケーション
- Azure Active Directory と統合されたサードパーティー製アプリケーションにサインアップまたはサインインする場合。 一例として、[Smartsheet](https://app.smartsheet.com/b/home) または [DocuSign](https://www.docusign.net/member/MemberLogin.aspx) があります。
- Microsoft 365 などの Microsoft アプリ。
- [アプリケーション レジストリ](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)でカスタム開発アプリケーションを作成して、新しいアプリケーション登録を追加する場合
- [V2.0 アプリケーション登録ポータル](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)でカスタム開発アプリケーションを作成して、新しいアプリケーション登録を追加する場合
- Visual Studio の [ASP.NET の認証方法](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)または [接続済みサービス](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)を使用して開発しているアプリケーションを追加する場合
- [Azure AD PowerShell モジュール](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)を使用してサービス プリンシパル オブジェクトを作成する場合
- 管理者としてテナントのデータを[アプリケーションが使用することに同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)する場合
- テナントのデータを[アプリケーションが使用することにユーザーが同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)する場合
- 特定のサービスがテナントにデータを格納できるようにする場合。 一例として、パスワード リセット ポリシーを安全に格納するためにサービス プリンシパルとしてモデル化されるパスワード リセットがあります。

アプリがディレクトリに追加される方法および理由について詳しくは、[アプリケーションを Azure AD に追加する方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ
[Azure Active Directory でのアプリケーションの管理](what-is-application-management.md)
