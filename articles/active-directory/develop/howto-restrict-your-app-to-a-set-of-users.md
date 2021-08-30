---
title: Azure AD アプリを一連のユーザーに制限する | Azure
titleSuffix: Microsoft identity platform
description: Azure AD に登録されたアプリへのアクセスを選択したユーザーのセットに制限する方法について説明します。
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5ced85c19eb8999c839861eb421ebb9d73ea63a6
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369377"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>方法:Azure AD アプリを Azure AD テナントの一連のユーザーに制限する

Azure Active Directory (Azure AD) テナントに登録されたアプリケーションは、既定ではテナントの正常に認証されたすべてのユーザーが利用できます。

同様に、[マルチテナント](howto-convert-app-to-be-multi-tenant.md) アプリの場合、このアプリがプロビジョニングされている Azure AD テナントのすべてのユーザーは、それぞれのテナントで正常に認証されると、このアプリケーションにアクセスできるようになります。

テナントの管理者と開発者には、多くの場合、アプリを特定のユーザーのセットに制限しなければならない要件があります。 開発者は Azure ロール ベースのアクセス制御 (Azure RBAC) のような一般的な承認パターンを使用して同じことを実現できますが、この方法では開発者に相当の負担がかかります。

Azure AD のこの組み込み機能も使用することにより、テナントの管理者と開発者が、テナント内のユーザーまたはセキュリティ グループの特定のセットにアプリを制限できるようになります。

## <a name="supported-app-configurations"></a>サポートされているアプリの構成

テナントのユーザーまたはセキュリティ グループの特定のセットにアプリを制限するオプションは、次の種類のアプリケーションで動作します。

- SAML ベースの認証を使用したフェデレーション シングル サインオン用に構成されたアプリケーション。
- Azure AD 事前認証を使用するアプリケーション プロキシのアプリケーション。
- ユーザーまたは管理者がそのアプリケーションに同意した後に OAuth 2.0/OpenID Connect 認証を使用する Azure AD アプリケーション プラットフォームに直接構築されたアプリケーション。

     > [!NOTE]
     > この機能は、Web アプリ/Web API およびエンタープライズ アプリケーションでのみ使用できます。 [ネイティブ](./quickstart-register-app.md)として登録されているアプリは、テナント内のユーザーまたはセキュリティ グループのセットに制限することができません。

## <a name="update-the-app-to-require-user-assignment"></a>アプリを更新してユーザーの割り当てを要求にする

アプリケーションを更新してユーザーの割当を要求するには、Enterprise アプリのアプリケーションの所有者である必要があります。または、**グローバル管理者**、**アプリケーション管理者** または **クラウド アプリケーション管理者** のいずれかのディレクトリ ロールを割り当てられている必要があります。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** を選択します。
1. 構成して割り当てを要求するアプリケーションを選択します。 ウィンドウの上部にあるフィルターを使用して、特定のアプリケーションを検索します。
1. アプリケーションの **[概要]** ページの **[管理]** の下で **[プロパティ]** を選択します。
1. **[ユーザーの割り当てが必要ですか?]** の設定を探し、 **[はい]** に設定します。 このオプションが **[はい]** に設定されている場合、アプリケーションまたはサービスにアクセスしようとするユーザーとサービスを最初にこのアプリケーションに割り当てる必要があります。そうしない場合は、サインインしたりアクセス トークンを取得したりできません。
1. **[保存]** を選択します。

> [!NOTE]
> アプリケーションで割り当てが必要な場合、そのアプリケーションに対するユーザーの同意は許可されません。 これは、そのアプリに対するユーザーの同意がそれ以外の場合に許可されている場合でも当てはまります。 割り当てを必要とするアプリに対して、[テナント全体の管理者の同意を付与](../manage-apps/grant-admin-consent.md)してください。 

## <a name="assign-the-app-to-users-and-groups"></a>アプリをユーザーとグループに割り当てる

ユーザーの割り当てを有効にするようにアプリを構成したら、次はユーザーとグループにそのアプリを割り当てることができます。

1. **[管理]** で、 **[ユーザーとグループ]**  >  **[ユーザー/グループの追加]** を選択します。
1. **[ユーザー]** セレクターを選択します。 

     ユーザーとセキュリティ グループの一覧と一緒に、特定のユーザーまたはグループを検索して探すためのテキスト ボックスが表示されます。 この画面では、一度に複数のユーザーとグループを選択できます。

1. ユーザーとグループの選択が完了したら、 **[選択]** を選択します。
1. (オプション) アプリケーションでアプリ ロールを定義している場合、 **[ロールの選択]** オプションを使用して、選択したユーザーとグループにアプリのロールを割り当てることができます。 
1. **[割り当て]** を選択して、ユーザーとグループへのアプリの割り当てを完了します。 
1. 追加したユーザーとグループが、更新された **[ユーザーとグループ]** のリストに表示されていることを確認します。

## <a name="more-information"></a>詳細情報

- [方法: ご利用のアプリケーションにアプリ ロールを追加する](./howto-add-app-roles-in-azure-ad-apps.md)
- [アプリ ロールおよびロール要求を使用して ASP.NET Core Web アプリに承認を追加する](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [アプリでのセキュリティ グループとアプリケーション ロールの使用 (ビデオ)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [グループ要求とアプリケーション ロールが追加された Azure Active Directory](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory のアプリ マニフェスト](./reference-app-manifest.md)
