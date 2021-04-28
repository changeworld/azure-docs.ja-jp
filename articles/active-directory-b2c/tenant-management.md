---
title: Azure Active Directory B2C を管理する
titleSuffix: Azure Active Directory B2C
description: Azure Active Directory B2C テナントを管理する方法を説明します。 Azure AD B2C でサポートされている Azure AD 機能、管理者ロールを使用してリソースを管理する方法、および仕事用アカウントとゲスト ユーザーを Azure AD B2C テナントに追加する方法を説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c3ea17a4f6dc2fb5134c6ceb1ae37d25e0881365
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715249"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C テナントを管理する

Azure Active Directory B2C (Azure AD B2C) で "テナント" は、コンシューマー ユーザーのディレクトリを表します。 各 Azure AD B2C テナントは、他の Azure AD B2C テナントと区別され、分離されています。 Azure AD B2C テナントは、既にお持ちかもしれない Azure Active Directory テナントとは別のものです。 この記事では、Azure AD B2C テナントを管理する方法を説明します。

## <a name="supported-azure-ad-features"></a>サポートされる Azure AD の機能

Azure AD B2C は、Azure AD プラットフォームに依存しています。 Azure AD B2C テナントでは、次の Azure AD 機能を使用できます。

|機能  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [グループ](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | グループを使用して、管理アカウントとユーザー アカウントを管理できます。| グループを使用して、管理アカウントを管理できます。 [コンシューマー アカウント](user-overview.md#consumer-user) はグループをサポートしていません。 |
| [外部 ID のゲストの招待](../active-directory//external-identities/add-users-administrator.md)| ゲスト ユーザーを招待し、Facebook と Google のアカウントでのフェデレーションやサインインなどの外部 ID 機能を構成することができます。 | アプリケーションにアクセスしたり、テナントを管理したりするために Azure AD テナントにゲストとして招待できるのは、Microsoft アカウントまたは Azure AD ユーザーのみです。 [コンシューマー アカウント](user-overview.md#consumer-user)の場合は、Azure AD B2C のユーザー フローとカスタム ポリシーを使用して、ユーザーと、Google や Facebook などの外部 ID プロバイダーでのサインアップまたはサインインを管理します。 |
| [ロールと管理者](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| 管理アカウントとユーザー アカウントに対して完全にサポートされています。 | ロールは、[コンシューマー アカウント](user-overview.md#consumer-user)ではサポートされていません。 コンシューマー アカウントは、どの Azure リソースにもアクセスできません。|
| [カスタム ドメイン名](../active-directory/roles/permissions-reference.md#) |  Azure AD カスタム ドメインは管理アカウントにのみ使用できます。 | [コンシューマー アカウント](user-overview.md#consumer-user)は、ユーザー名、電話番号、または任意の電子メール アドレスを使用してサインインできます。 [カスタム ドメイン](custom-domain.md)はリダイレクト URL で使用できます。|
| [条件付きアクセス](../active-directory/roles/permissions-reference.md#) | 管理アカウントとユーザー アカウントに対して完全にサポートされています。 | [コンシューマー アカウント](user-overview.md#consumer-user)では、Azure AD の条件付きアクセス機能のサブセットがサポートされています。Azure AD B2C [カスタムドメイン](conditional-access-user-flow.md)の構成方法を参照してください。|

## <a name="other-azure-resources-in-your-tenant"></a>テナント内のその他の Azure リソース

Azure AD B2C テナントでは、仮想マシン、Azure Web アプリ、Azure 関数などの他の Azure リソースをプロビジョニングすることはできません。 これらのリソースは、Azure AD テナントで作成する必要があります。

## <a name="azure-ad-b2c-accounts-overview"></a>Azure AD B2C アカウントの概要

Azure AD B2C テナントには、次の種類のアカウントを作成できます。

Azure AD B2C テナントには、「[Azure Active Directory B2C のユーザー アカウントの概要](user-overview.md)」という記事で説明されているように、いくつかの種類のアカウントを作成できます。

- **職場アカウント** - 職場アカウントは、テナントのリソースにアクセスでき、管理者ロールを使用してテナントを管理できます。
- **ゲスト アカウント** - ゲスト アカウントは、Microsoft アカウント、またはアプリケーションにアクセスするかテナントを管理するために使用できる Azure Active Directory ユーザーのみが可能です。
- **コンシューマー アカウント** - コンシューマー アカウントは、Azure AD B2C に登録したアプリケーションのユーザーによって使用されます。

これらのアカウントの種類の詳細については、「[Azure Active Directory B2C のユーザー アカウントの概要](user-overview.md)」を参照してください。 Azure AD B2C テナントを管理するために割り当てられるすべてのユーザーは、Azure 関連のサービスにアクセスできるように、Azure AD ユーザーアカウントを持っている必要があります。 このようなユーザーを追加するには、Azure AD B2C テナントで[アカウント (職場のアカウント) を作成](#add-an-administrator-work-account)するか、ゲスト ユーザーとして Azure AD B2C テナントに[招待](#invite-an-administrator-guest-account)します。

## <a name="use-roles-to-control-resource-access"></a>ロールを使用したリソース アクセスの制御

アクセス制御戦略を計画するときは、リソースにアクセスするために必要な最小限の特権をユーザーに割り当てることをお勧めします。 次の表は、Azure AD B2C テナントのプライマリ リソースと、それらを管理するユーザーに最も適した管理者ロールを説明しています。

|リソース  |説明  |Role  |
|---------|---------|---------|
|[アプリケーションの登録](tutorial-register-applications.md) | Azure AD B2C 内で、Web、モバイル、およびネイティブのアプリケーション登録のすべての側面を作成し、管理します。|[アプリケーション管理者](../active-directory/roles/permissions-reference.md#global-administrator)|
|[ID プロバイダー](add-identity-provider.md)| [ローカル ID プロバイダー](identity-provider-local.md)と外部のソーシャルまたはエンタープライズ ID プロバイダーを構成します。 | [外部 ID プロバイダー管理者](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[API コネクタ](add-api-connector.md)| ユーザー フローを Web API と統合してユーザー エクスペリエンスをカスタマイズし、外部システムと統合します。|[外部 ID ユーザー フロー属性管理者](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[会社のブランド](customize-ui.md#configure-company-branding)| ユーザー フローの各ページをカスタマイズします。| [全体管理者](../active-directory/roles/permissions-reference.md#global-administrator)|
|[ユーザー属性](user-flow-custom-attributes.md)| すべてのユーザー フローに使用可能なカスタム属性を追加するか、削除します。| [外部 ID ユーザー フロー属性管理者](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|ユーザーの管理| この記事で説明されているように、[コンシューマー アカウント](manage-users-portal.md) と管理アカウントを管理します。| [ユーザー管理者](../active-directory/roles/permissions-reference.md#user-administrator)|
|ロールと管理者| Azure AD B2C ディレクトリでロールの割り当てを管理します。 Azure AD B2C ロールに割り当てることができるグループを作成し、管理します。 |[ブローバル管理者](../active-directory/roles/permissions-reference.md#global-administrator)、[特権ロール管理者](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[ユーザー フロー](user-flow-overview.md)|サインアップ、サインイン、プロファイル編集などの一般的な ID タスクを短時間で構成し、有効にします。| [外部 ID ユーザー フロー属性管理者](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[カスタム ポリシー](user-flow-overview.md)| Azure AD B2C ですべてのカスタムポリシーの作成、読み取り、更新、および削除を行います。| [B2C IEF ポリシー管理者](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[ポリシー キー](policy-keys-overview.md)|カスタム ポリシーで使用されるトークン、クライアント シークレット、証明書、パスワードの署名と検証のための暗号化キーを追加し、管理します。|[B2C IEF キーセット管理者](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|


## <a name="add-an-administrator-work-account"></a>管理者 (職場のアカウント) を追加する

新しい管理アカウントを作成する場合は、次の手順に従います。

1. グローバル管理者または特権ロール管理者のアクセス許可を使用して [Azure portal](https://portal.azure.com/) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。 または、検索ボックスを使用して検索し、 **[Azure AD B2C]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. **[ 新規ユーザー]** を選択します。
1. **[ユーザー]** ページで、このユーザーの情報を入力します。

   - **名前**。 必須。 新しいユーザーの氏名です。 たとえば、*Mary Parker* にします。
   - **ユーザー名**。 必須。 新しいユーザーのユーザー名です。 たとえば、「 `mary@contoso.com` 」のように入力します。
     ユーザー名のドメイン部分には、既定の初期ドメイン名の *\<yourdomainname>.onmicrosoft.com* を使用する必要があります。
   - **グループ**。 オプションで、1 つまたは複数の既存のグループにユーザーを追加できます。 後でグループにユーザーを追加することもできます。 
   - **ディレクトリ ロール**: ユーザーに Azure AD 管理アクセス許可が必要な場合は、Azure AD ロールに追加することができます。 ユーザーには、グローバル管理者を割り当てることも、Azure AD の限られた管理者ロールを 1 つ以上割り当てることもできます。 ロールの割り当ての詳細については、「[ロールを使用してリソースへのアクセスを制御する](#use-roles-to-control-resource-access)」を参照してください。
   - **ジョブ情報**:こちらにはユーザーに関する詳細情報を追加できます。これは後で行うこともできます。 

1. **[パスワード]** ボックスに表示されている自動生成されたパスワードをコピーします。 このパスワードは初回のサインインのためにユーザーに渡す必要があります。
1. **［作成］** を選択します

ユーザーが作成され、Azure AD B2C テナントに追加されます。 Azure AD B2C テナントに対してネイティブな 1 つ以上の作業アカウントにグローバル管理者ロールを割り当てることをお勧めします。 このアカウントは、緊急用アカウントと見なすことができます。

## <a name="invite-an-administrator-guest-account"></a>管理者 (ゲスト アカウント) を招待する

テナントを管理するために、新しいゲスト ユーザーを招待することもできます。 Azure AD も組織に存在する場合は、ゲスト アカウントをお勧めします。これは、この ID のライフサイクルは外部で管理できるためです。 

ユーザーを招待するには、次の手順に従います。

1. グローバル管理者または特権ロール管理者のアクセス許可を使用して [Azure portal](https://portal.azure.com/) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。 または、検索ボックスを使用して検索し、 **[Azure AD B2C]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. **[新しいゲスト アカウント]** を選択します。
1. **[ユーザー]** ページで、このユーザーの情報を入力します。

   - **名前**。 必須。 新しいユーザーの氏名です。 たとえば、*Mary Parker* にします。
   - **電子メール アドレス**。 必須。 招待するユーザーの電子メール アドレス。 たとえば、「 `mary@contoso.com` 」のように入力します。   
   - **個人用メッセージ**: 招待メールに含める個人用メッセージを追加します。
   - **グループ**。 オプションで、1 つまたは複数の既存のグループにユーザーを追加できます。 後でグループにユーザーを追加することもできます。
   - **ディレクトリ ロール**: ユーザーに Azure AD 管理アクセス許可が必要な場合は、Azure AD ロールに追加することができます。 ユーザーには、グローバル管理者を割り当てることも、Azure AD の限られた管理者ロールを 1 つ以上割り当てることもできます。 ロールの割り当ての詳細については、「[ロールを使用してリソースへのアクセスを制御する](#use-roles-to-control-resource-access)」を参照してください。
   - **ジョブ情報**:こちらにはユーザーに関する詳細情報を追加できます。これは後で行うこともできます。

1. **［作成］** を選択します

招待メールがユーザーに送信されます。 ユーザーは、招待に同意すると、サインインできるようになります。

### <a name="resend-the-invitation-email"></a>招待メールを再送信する

ゲストが招待メールを受け取っていない場合、または招待の有効期限が切れた場合は、招待を再送信できます。 招待メールの代わりに、アプリまたはポータルへの直接リンクをゲストに知らせることができます。 招待を再送信し、直接リンクを取得する手順は、次のとおりです。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
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
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
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
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。 または、検索ボックスを使用して検索し、 **[Azure AD B2C]** を選択します。
1. **[管理]** で **[ロールと管理者]** を選択します。
1. **[グローバル管理者]** などのロールを選択します。 **[ロール| 割り当て]** ページには、そのロールのユーザーが一覧表示されます。

## <a name="delete-an-administrator-account"></a>管理者アカウントを削除する

既存のユーザーを削除するには、*グローバル管理者* ロールが割り当てられている必要があります。 グローバル管理者は、他の管理者を含むすべてのユーザーを削除できます。 *ユーザー管理者* は、管理者以外のユーザーを削除できます。

1. Azure AD B2C ディレクトリで、 **[ユーザー]** を選択し、次に、削除するユーザーを選択します。
1. **[削除]** を選択し、 **[はい]** を選択して削除を確定します。

ユーザーが削除され、 **[ユーザー] - [すべてのユーザー]** ページに表示されなくなります。 ユーザーは、削除後 30 日間は **[削除済みのユーザー]** ページに表示され、その期間内であれば復元できます。 ユーザーの復元の詳細については、「[Azure Active Directory を使用して最近削除されたユーザーを復元または削除する](../active-directory/fundamentals/active-directory-users-restore.md)」を参照してください。

## <a name="protect-administrative-accounts"></a>管理アカウントを保護する

セキュリティを強化するために、すべての管理者アカウントを多要素委認証 (MFA) で保護することをお勧めします。 MFA は、モバイル デバイスの確認コードや Microsoft Authenticator アプリ内の要求など、より多くの形式の ID をユーザーに求める、サインイン時 の ID 検証プロセスです。

![サインイン スクリーンショットで使用されている認証方法](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

[Azure AD セキュリティの既定値](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)を有効にすると、すべての管理アカウントでの MFA の使用を強制することができます。



## <a name="next-steps"></a>次のステップ

- [Azure Portal で Azure Active Directory B2C テナントを作成する](tutorial-create-tenant.md)

