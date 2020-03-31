---
title: Azure Active Directory B2C のユーザー アカウントの概要
description: Azure Active Directory B2C で使用できるユーザー アカウントの種類について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9630e1f23b6595ca690ecafcf0c4b9bfff603f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185660"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のユーザー アカウントの概要

Azure Active Directory B2C (Azure AD B2C) には、作成できるアカウントの種類がいくつかあります。 Azure Active Directory、Active Directory B2B、および Active Directory B2C は、使用できるユーザー アカウントの種類を共有します。

次の種類のアカウントを使用できます。

- **職場アカウント** - 職場アカウントは、テナントのリソースにアクセスでき、管理者ロールを使用してテナントを管理できます。
- **ゲスト アカウント** - ゲスト アカウントは、Microsoft アカウント、またはアプリケーションにアクセスするかテナントを管理するために使用できる Azure Active Directory ユーザーのみが可能です。
- **コンシューマー アカウント** - コンシューマー アカウントは、Azure AD B2C に登録したアプリケーションのユーザーによって使用されます。 コンシューマー アカウントは、次によって作成できます。
  - Azure AD B2C アプリケーションでサインアップ ユーザー フローを実行するユーザー
  - Microsoft Graph API の使用
  - Azure ポータルの使用

## <a name="work-account"></a>職場アカウント

職場アカウントは、Azure AD に基づいて、すべてのテナントに対して同じ方法で作成されます。 職場アカウントを作成するには、「[クイック スタート: Azure Active Directory に新しいユーザーを追加する](../active-directory/fundamentals/add-users-azure-active-directory.md)」の情報を使用できます。 職場アカウントは、Azure portal で **[新しいユーザー]** を選択して作成できます。

新しい職場アカウントを追加する場合は、次の構成設定を考慮する必要があります。

- **[名前]** と **[ユーザー名]** - **[名前]** プロパティには、ユーザーの姓名が含まれます。 **[ユーザー名]** は、ユーザーがサインインするために入力する識別子です。 ユーザー名には、完全なドメインが含まれます。 ユーザー名のドメイン名の部分は、既定の初期ドメイン名 *your-domain.onmicrosoft.com*、または検証済みの非フェデレーション [カスタム ドメイン](../active-directory/fundamentals/add-custom-domain.md)名 (*contoso.com* など) のいずれかである必要があります。
- **プロファイル** - アカウントは、ユーザー データのプロファイルを使用して設定されます。 姓、名、役職、および部署名を入力できます。 プロファイルは、アカウントの作成後に編集できます。
- **グループ** - グループを使用して管理タスクを実行します。たとえば、複数のユーザーやデバイスにライセンスまたはアクセス許可を一度に割り当てることができます。 新しいアカウントを、テナントの既存の[グループ](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)に配置できます。
- **ディレクトリ ロール** - ユーザー アカウントが所有する、テナントのリソースへのアクセス レベルを指定する必要があります。 次のアクセス許可レベルを使用できます。

    - **ユーザー** - ユーザーは、割り当てられたリソースにアクセスできますが、テナントのリソースの大半を管理できません。
    - **グローバル管理者** - グローバル管理者は、テナントのすべてのリソースに対するフル コントロールの権限を持ちます。
    - **制限付き管理者**- ユーザーの管理ロールまたはロールを選択します。 選択できるロールの詳細については、「[Azure Active Directory の管理者ロールの割り当て](../active-directory/users-groups-roles/directory-assign-admin-roles.md)」を参照してください。

### <a name="create-a-work-account"></a>職場アカウントを作成する

次の情報を使用して、新しい職場アカウントを作成できます。

- [Azure Portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>ユーザー アカウントを更新する

次の情報を使用して、ユーザーのプロファイルを更新できます。

- [Azure Portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>ユーザーのパスワードをリセットする

次の情報を使用して、ユーザーのパスワードをリセットできます。

- [Azure Portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>ゲスト ユーザー

外部ユーザーをゲスト ユーザーとしてテナントに招待できます。 ゲスト ユーザーを Azure AD B2C テナントに招待する一般的なシナリオは、管理責任を共有することです。 ゲスト アカウントの使用例は、「[Azure Active Directory B2B コラボレーション ユーザーのプロパティ](../active-directory/b2b/user-properties.md)」を参照してください。

ゲスト ユーザーをテナントに招待するときは、受信者の電子メール アドレスと、招待であることを説明するメッセージを指定します。 ユーザーは招待リンクによって同意ページに移動します。そのページで、 **[作業開始]** ボタンを選択し、アクセス許可を受け取ったことを確認します。 受信トレイが電子メール アドレスにアタッチされていない場合、ユーザーは、招待資格情報を使用して Microsoft ページに移動することで、同意ページに移動できます。 その後、ユーザーは、電子メール内のリンクのクリックと同じ方法で招待を受け入れます。 (例: `https://myapps.microsoft.com/B2CTENANTNAME`)。

ゲスト ユーザーは、[Microsoft Graph API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) を使用して招待することもできます。

## <a name="consumer-user"></a>コンシューマー ユーザー

コンシューマー ユーザーは、Azure AD B2C によってセキュリティで保護されているアプリケーションにサインインできますが、Azure portal などの Azure リソースにはアクセスできません。 コンシューマー ユーザーは、ローカル アカウントまたは Facebook や Twitter などのフェデレーション アカウントを使用できます。 コンシューマー アカウントは、[サインアップ ユーザー フローまたはサインイン ユーザー フロー](user-flow-overview.md)を使用するか、Microsoft Graph API を使用するか、あるいは Azure portal を使用して作成されます。

コンシューマー ユーザー アカウントを作成するときに、カスタム ユーザー属性を使用して、収集されるデータを指定できます。 詳細については、「[Azure Active Directory B2C でカスタム属性を定義する](user-flow-custom-attributes.md)」を参照してください。

コンシューマー アカウントの管理の詳細については、「[Microsoft Graph での Azure AD B2C ユーザーアカウントの管理](manage-user-accounts-graph-api.md)」を参照してください。

### <a name="migrate-consumer-user-accounts"></a>コンシューマー ユーザー アカウントを移行する

既存のコンシューマー ユーザー アカウントを ID プロバイダーから Azure AD B2C に移行しなければならない場合があります。 詳細については、「[Azure AD B2C へユーザーを移行する](user-migration.md)」を参照してください。
