---
title: Azure Active Directory B2C を管理する
titleSuffix: Azure Active Directory B2C
description: Azure Active Directory B2C テナントを管理する方法を説明します。 Azure AD B2C でサポートされている Azure AD 機能、管理者ロールを使用してリソースを管理する方法、および仕事用アカウントとゲスト ユーザーを Azure AD B2C テナントに追加する方法を説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 10/25/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: c0713bd45f344ce6dc994cde61cb275f9fdef3a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036335"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C テナントを管理する

Azure Active Directory B2C (Azure AD B2C) で "テナント" は、コンシューマー ユーザーのディレクトリを表します。 各 Azure AD B2C テナントは、他の Azure AD B2C テナントと区別され、分離されています。 Azure AD B2C テナントは、既にお持ちかもしれない Azure Active Directory テナントとは別のものです。 この記事では、Azure AD B2C テナントを管理する方法を説明します。

## <a name="prerequisites"></a>前提条件
- 独自の [Azure AD B2C テナント](tutorial-create-tenant.md)をまだ作成していない場合、ここで作成してください。 既存の Azure AD B2C テナントを使用できます。
- [Azure AD B2C でのユーザー アカウント](user-overview.md)について理解する。
- [リソース アクセスを制御するユーザー ロール](roles-resource-access-control.md)について理解する。


## <a name="add-an-administrator-work-account"></a>管理者 (職場のアカウント) を追加する

新しい管理アカウントを作成する場合は、次の手順に従います。

1. グローバル管理者または特権ロール管理者のアクセス許可を使用して [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。 または、検索ボックスを使用して検索し、 **[Azure AD B2C]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. **[ 新規ユーザー]** を選択します。
1. **[ユーザーの作成]** を選択します ( **[ユーザーを一括で作成する]** を選択すると、一度に多数のユーザーを作成できます)。
1. **[ユーザー]** ページで、このユーザーの情報を入力します。

  
   - **ユーザー名**。 *[必須]* 。 新しいユーザーのユーザー名です。 たとえば、「 `mary@contoso.com` 」のように入力します。
     ユーザー名のドメイン部分には、既定の初期ドメイン名の *\<tenant name>.onmicrosoft.com*、または[カスタム ドメイン](custom-domain.md) (`contoso.com` など) のいずれかを使用する必要があります。
   - **名前**。 *[必須]* 。 新しいユーザーの氏名です。 たとえば、*Mary Parker* にします。
   - **グループ**。 *オプション*。 1 つまたは複数の既存のグループにユーザーを追加できます。 後でグループにユーザーを追加することもできます。 
   - **ディレクトリ ロール**: ユーザーに Azure AD 管理アクセス許可が必要な場合は、Azure AD ロールに追加することができます。 ユーザーには、グローバル管理者を割り当てることも、Azure AD の限られた管理者ロールを 1 つ以上割り当てることもできます。 ロールの割り当ての詳細については、「[ロールを使用してリソースへのアクセスを制御する](roles-resource-access-control.md)」を参照してください。
   - **ジョブ情報**:こちらにはユーザーに関する詳細情報を追加できます。これは後で行うこともできます。 

1. **[パスワード]** ボックスに表示されている自動生成されたパスワードをコピーします。 このパスワードは初回のサインインのためにユーザーに渡す必要があります。
1. **［作成］** を選択します

ユーザーが作成され、Azure AD B2C テナントに追加されます。 Azure AD B2C テナントに対してネイティブな 1 つ以上の作業アカウントにグローバル管理者ロールを割り当てることをお勧めします。 このアカウントは、"*緊急用アカウント*" と見なすことができます。

## <a name="invite-an-administrator-guest-account"></a>管理者 (ゲスト アカウント) を招待する

テナントを管理するために、新しいゲスト ユーザーを招待することもできます。 Azure AD も組織に存在する場合は、ゲスト アカウントをお勧めします。これは、この ID のライフサイクルは外部で管理できるためです。 

ユーザーを招待するには、次の手順に従います。

1. グローバル管理者または特権ロール管理者のアクセス許可を使用して [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。 または、検索ボックスを使用して検索し、 **[Azure AD B2C]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. **[新しいゲスト アカウント]** を選択します。
1. **[ユーザー]** ページで、このユーザーの情報を入力します。

   - **名前**。 *[必須]* 。 新しいユーザーの氏名です。 たとえば、*Mary Parker* にします。
   - **電子メール アドレス**。 *[必須]* 。 招待するユーザーのメール アドレス。これは Microsoft アカウントである必要があります。 たとえば、「 `mary@contoso.com` 」のように入力します。   
   - **個人用メッセージ**: 招待メールに含める個人用メッセージを追加します。
   - **グループ**。 *オプション*。 1 つまたは複数の既存のグループにユーザーを追加できます。 後でグループにユーザーを追加することもできます。
   - **ディレクトリ ロール**: ユーザーに Azure AD 管理アクセス許可が必要な場合は、Azure AD ロールに追加することができます。 ユーザーには、グローバル管理者を割り当てることも、Azure AD の限られた管理者ロールを 1 つ以上割り当てることもできます。 ロールの割り当ての詳細については、「[ロールを使用してリソースへのアクセスを制御する](roles-resource-access-control.md)」を参照してください。
   - **ジョブ情報**:こちらにはユーザーに関する詳細情報を追加できます。これは後で行うこともできます。

1. **［作成］** を選択します

招待メールがユーザーに送信されます。 ユーザーは、招待に同意すると、サインインできるようになります。

### <a name="resend-the-invitation-email"></a>招待メールを再送信する

ゲストが招待メールを受け取っていない場合、または招待の有効期限が切れた場合は、招待を再送信できます。 招待メールの代わりに、アプリまたはポータルへの直接リンクをゲストに知らせることができます。 招待を再送信し、直接リンクを取得する手順は、次のとおりです。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。 または、検索ボックスを使用して検索し、 **[Azure AD B2C]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. 招待を再送信するユーザーを検索し、選択します。
1. **[ユーザー | プロファイル]** ページの **[ID]** で **[(管理)]** を選択します。
        ![ゲスト アカウントに招待メールを再送信する方法を示すスクリーンショット。](./media/tenant-management/guest-account-resend-invite.png)

1. **[招待を再送信しますか?]** で **[はい]** を選択します。 **[招待を再送信してもよろしいですか?]** が表示されたら、 **[はい]** を選択します。
1. Azure AD B2C によって招待が送信されます。 招待 URL をコピーし、ゲストに直接、渡すこともできます。
    
    ![招待 URL を取得する方法を示すスクリーンショット。](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>ロールの割り当てを追加する

[ユーザーを作成](#add-an-administrator-work-account)するか、[ゲスト ユーザーを招待](#invite-an-administrator-guest-account)するときにロールを割り当てることができます。 ユーザーに対してロールの追加、変更、または削除を実行できます。

1. グローバル管理者または特権ロール管理者のアクセス許可を使用して [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。 または、検索ボックスを使用して検索し、 **[Azure AD B2C]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. ロールを変更するユーザーを選択します。 次に、 **[割り当てられたロール]** を選択します。
1. **[割り当ての追加]** を選択し、割り当てるロール ( *[アプリケーション管理者]* など) を選択した後、 **[追加]** を選択します。

## <a name="remove-a-role-assignment"></a>ロールの割り当てを削除する

ユーザーに割り当てたロールを削除する必要がある場合は、次の手順を実行します。

1. **[Azure AD B2C]** 、 **[ユーザー]** の順に選択した後、ユーザーを検索し、選択します。
1. **[割り当てられたロール]** を選択します。 削除するロール ( *[アプリケーション管理者]* など) を選択し、 **[割り当ての削除]** を選択します。

## <a name="review-administrator-account-role-assignments"></a>管理者アカウントのロールの割り当てを確認する

監査プロセスの一環として、通常は、Azure AD B2C ディレクトリ内の特定のロールにどのユーザーが割り当てられているかを確認します。 現在、どのユーザーに特権ロールが割り当てられているかを監査するには、次の手順に従います。

1. グローバル管理者または特権ロール管理者のアクセス許可を使用して [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。 または、検索ボックスを使用して検索し、 **[Azure AD B2C]** を選択します。
1. **[管理]** で **[ロールと管理者]** を選択します。
1. **[グローバル管理者]** などのロールを選択します。 **[ロール| 割り当て]** ページには、そのロールのユーザーが一覧表示されます。

## <a name="delete-an-administrator-account"></a>管理者アカウントを削除する

既存のユーザーを削除するには、*グローバル管理者* ロールが割り当てられている必要があります。 グローバル管理者は、他の管理者を含むすべてのユーザーを削除できます。 *ユーザー管理者* は、管理者以外のユーザーを削除できます。

1. Azure AD B2C ディレクトリで、 **[ユーザー]** を選択し、次に、削除するユーザーを選択します。
1. **[削除]** を選択し、 **[はい]** を選択して削除を確定します。

ユーザーが削除され、 **[ユーザー] - [すべてのユーザー]** ページに表示されなくなります。 ユーザーは、削除後 30 日間は **[削除済みのユーザー]** ページに表示され、その期間内であれば復元できます。 ユーザーの復元の詳細については、「[Azure Active Directory を使用して最近削除されたユーザーを復元または削除する](../active-directory/fundamentals/active-directory-users-restore.md)」を参照してください。

## <a name="protect-administrative-accounts"></a>管理アカウントを保護する

セキュリティを強化するために、すべての管理者アカウントを多要素認証 (MFA) で保護することをお勧めします。 MFA は、モバイル デバイスの確認コードや Microsoft Authenticator アプリ内の要求など、より多くの形式の ID をユーザーに求める、サインイン時 の ID 検証プロセスです。

![サインイン スクリーンショットで使用されている認証方法](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

[Azure AD セキュリティの既定値](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)を有効にすると、すべての管理アカウントでの MFA の使用を強制することができます。

## <a name="get-your-tenant-name"></a>テナント名を取得する

Azure AD B2C テナント名を取得するには、これらの手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページの **[ディレクトリ名]** の一覧で自分の Azure AD B2C ディレクトリを見つけて、 **[切り替え]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[概要]** で **ドメイン名** をコピーします。

![Azure AD B2C テナント名を取得する方法を示すスクリーンショット。](./media/tenant-management/get-azure-ad-b2c-tenant-name.png)  

## <a name="get-your-tenant-id"></a>テナント ID を取得する

Azure AD B2C テナント ID を取得するには、これらの手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal で、 **[Azure Active Directory]** を検索して選択します。
1. **[概要]** で **テナント ID** をコピーします。

![Azure AD B2C テナント ID を取得する方法を示すスクリーンショット。](./media/tenant-management/get-azure-ad-b2c-tenant-id.png)  

## <a name="next-steps"></a>次のステップ

- [リソースをクリーンアップしてテナントを削除する](tutorial-delete-tenant.md)
