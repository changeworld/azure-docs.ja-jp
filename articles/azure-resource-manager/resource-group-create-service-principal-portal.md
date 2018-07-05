---
title: ポータルでの Azure アプリ ID の作成 | Microsoft Docs
description: Azure Resource Manager でロール ベースのアクセス制御と共に使用してリソースへのアクセスを管理できる、新しい Azure Active Directory のアプリケーションとサービス プリンシパルを作成する方法について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: tomfitz
ms.openlocfilehash: 7a1b6aa9afd26116253482a2e1a9c6a25bdf3c55
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441575"
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルをポータルで作成する

コードでリソースにアクセスしたり変更を加えたりするには、Azure Active Directory (AD) アプリケーションをセットアップする必要があります。 そして、AD アプリケーションに必要な権限を割り当てます。 このアプローチは、自分の資格情報の下でアプリを実行するよりも優れています。自分のアクセス許可とは異なるアプリ ID に対してアクセス許可を割り当てることができるためです。 通常、こうしたアクセス許可は、アプリが行う必要があることに制限されます。

この記事では、ポータル経由でそれらの手順を実行する方法を示しています。 ここでは、シングル テナント アプリケーション (1 つの組織内でのみ実行することを目的としたアプリケーション) に焦点を絞って説明します。 一般に、組織内で実行される基幹業務アプリケーションには、シングル テナント アプリケーションが使用されます。

> [!IMPORTANT]
> サービス プリンシパルを作成する代わりに、アプリケーション ID 用に Azure AD の管理対象サービス ID を使用することを検討します。 Azure AD の MSI は、コードのために ID の作成を簡略化する、Azure Active Directory のパブリック プレビュー機能です。 コードが、Azure AD の MSI をサポートするサービス上で実行され、Azure Active Directory 認証をサポートするリソースにアクセスする場合、Azure AD の MSI は優れた選択肢となります。 Azure AD の MSI の詳細 (どのサービスが現在 MSI をサポートしているかなど) については、「[Azure リソースの管理対象サービス ID](../active-directory/managed-service-identity/overview.md)」を参照してください。

## <a name="required-permissions"></a>必要なアクセス許可

この記事の手順を実行するには、アプリケーションを Azure AD テナントに登録し、Azure サブスクリプションでアプリケーションをロールに割り当てるための十分なアクセス許可が必要です。 これらの手順を実行するための適切なアクセス許可があることを確認しましょう。

### <a name="check-azure-active-directory-permissions"></a>Azure Active Directory のアクセス許可を確認する

1. **[Azure Active Directory]** を選択します。

   ![[Azure Active Directory] を選択する](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Azure Active Directory で **[ユーザー設定]** を選択します。

   ![[ユーザー設定] を選択する](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. **[アプリの登録]** 設定を確認します。 **[はい]** に設定されている場合は、管理者以外のユーザーが AD アプリを登録できます。 この設定は、Azure AD テナント内のすべてのユーザーがアプリを登録できることを意味します。 この場合、「[Azure サブスクリプションのアクセス許可を確認する](#check-azure-subscription-permissions)」に進んで構いません。

   ![[アプリの登録] を表示する](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. アプリの登録設定が **[いいえ]** に設定されている場合は、[グローバル管理者](../active-directory/users-groups-roles/directory-assign-admin-roles.md)だけがアプリを登録できます。 お使いのアカウントが Azure AD テナントの管理者かどうかを確認します。 **[概要]** を選択し、ユーザー情報を確認します。 アカウントがユーザー ロールに割り当てられていても、(前の手順の) アプリの登録設定が管理者ユーザーに制限されている場合は、管理者に連絡して、グローバル管理者ロールに割り当ててもらうか、ユーザーがアプリを登録できるようにしてもらいます。

   ![ユーザーを検索する](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>Azure サブスクリプションのアクセス許可を確認する

Azure サブスクリプションで、AD アプリをロールに割り当てるには、アカウントに `Microsoft.Authorization/*/Write` アクセス権が必要です。 このアクションは、[所有者](../role-based-access-control/built-in-roles.md#owner)ロールまたは[ユーザー アクセス管理者](../role-based-access-control/built-in-roles.md#user-access-administrator)ロールを通じて許可されます。 アカウントが**共同作成者**ロールに割り当てられている場合は、適切なアクセス許可がありません。 この場合、サービス プリンシパルをロールに割り当てようとすると、エラーが発生します。

サブスクリプションのアクセス許可を確認するには、次の手順に従います。

1. 右上隅にあるアカウントを選択し、**[アクセス許可]** を選択します。

   ![ユーザー アクセス許可の選択](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. ドロップダウン リストでサブスクリプションを選択します。 **[Click here to view complete access details for this subscription]\(サブスクリプションの完全なアクセスの詳細を表示するにはここをクリック\)** を選択します。

   ![ユーザーを検索する](./media/resource-group-create-service-principal-portal/view-details.png)

1. 割り当て済みのロールを表示し、AD アプリをロールに割り当てるための適切なアクセス許可があるかどうかを確認します。 ない場合は、サブスクリプション管理者に連絡して、ユーザー アクセス管理者ロールに追加してもらいます。 次の図では、ユーザーは所有者ロールに割り当てられているので、このユーザーには適切なアクセス許可があります。

   ![アクセス許可を表示する](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory アプリケーションを作成する

1. [Azure Portal](https://portal.azure.com) で Azure アカウントにログインします。
1. **[Azure Active Directory]** を選択します。

   ![[Azure Active Directory] を選択する](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. **[アプリの登録]** を選択します。

   ![[アプリの登録] を選択する](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. **[新しいアプリケーションの登録]** を選択します。

   ![アプリを追加する](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. アプリケーションの名前と URL を指定します。 作成するアプリケーションの種類として、**[Web アプリ/API]** を選択します。 [ネイティブ アプリケーション](../active-directory/manage-apps/application-proxy-configure-native-client-application.md)の資格情報を作成することはできません。したがって、このタイプは、自動化されたアプリケーションでは正しく機能しません。 値を設定したら、**[作成]** をクリックします。

   ![アプリケーションの名前指定](./media/resource-group-create-service-principal-portal/create-app.png)

これでアプリケーションが作成されます。

## <a name="get-application-id-and-authentication-key"></a>アプリケーション ID と認証キーを取得する

プログラムによってログインするときは、アプリケーションの ID と認証キーが必要です。 これらの値を取得するには、次の手順に従います。

1. Azure Active Directory の **[アプリの登録]** で、アプリケーションを選択します。

   ![アプリケーションを選択する](./media/resource-group-create-service-principal-portal/select-app.png)

1. **アプリケーション ID** をコピーし、アプリケーション コードに保存します。 一部の[サンプル アプリケーション](#log-in-as-the-application)では、この値はクライアント ID と呼ばれます。

   ![クライアント ID](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. 認証キーを生成するには、**[設定]** を選択します。

   ![設定を選択](./media/resource-group-create-service-principal-portal/select-settings.png)

1. 認証キーを生成するには、**[キー]** を選択します。

   ![[キー] を選択する](./media/resource-group-create-service-principal-portal/select-keys.png)

1. キーの説明を入力し、キーの期間を指定します。 操作が完了したら、**[保存]** をクリックします。

   ![キーを保存する](./media/resource-group-create-service-principal-portal/save-key.png)

   キーを保存すると、キーの値が表示されます。 キーは後で取得できないため、この値をコピーしておきます。 キー値は、アプリケーションとしてログインする際にアプリケーション ID と共に入力します。 アプリケーションが取得できる場所にキー値を保存します。

   ![保存されたキー](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>テナント ID を取得する

プログラムによってログインするときは、認証要求と共にテナント ID を渡す必要があります。

1. **[Azure Active Directory]** を選択します。

   ![[Azure Active Directory] を選択する](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. テナント ID を取得するには、Azure AD テナントの **[プロパティ]** を選択します。

   ![Azure AD のプロパティを選択する](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. **ディレクトリ ID** をコピーします。 この値がテナント ID です。

   ![テナント ID](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>アプリケーションをロールに割り当てる

サブスクリプション内のリソースにアクセスするには、アプリケーションをロールに割り当てる必要があります。 アプリケーションにとって適切なアクセス許可を表すのはどのロールであるかを判断します。 利用可能なロールについては、「[RBAC: 組み込みのロール](../role-based-access-control/built-in-roles.md)」を参照してください。

スコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。 アクセス許可は、スコープの下位レベルに継承されます。 たとえば、アプリケーションをリソース グループの閲覧者ロールに追加すると、アプリケーションではリソース グループとそれに含まれているすべてのリソースを読み取ることができます。

1. アプリケーションを割り当てるスコープのレベルに移動します。 たとえば、サブスクリプション スコープでロールを割り当てるには、**[サブスクリプション]** を選択します。 リソース グループまたはリソースを選択することもできます。

   ![サブスクリプションを選択する](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. アプリケーションを割り当てる特定のサブスクリプション (リソース グループまたはリソース) を選択します。

   ![割り当てのためのサブスクリプションの選択](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. **[アクセス制御 (IAM)]** を選択します。

   ![アクセスの選択](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. **[追加]** を選択します。

   ![select add](./media/resource-group-create-service-principal-portal/select-add.png)

1. アプリケーションに割り当てるロールを選択します。 次の図は、**閲覧者**ロールを示しています。

   ![select role](./media/resource-group-create-service-principal-portal/select-role.png)

1. 既定では、Azure Active Directory アプリケーションは、使用可能なオプションに表示されません。 目的のアプリケーションを見つけるには、その名前を検索フィールドに指定する必要があります。 それを選択します。

   ![アプリを検索する](./media/resource-group-create-service-principal-portal/search-app.png)

1. **[保存]** を選択して、ロールの割り当てを完了します。 該当のスコープのロールに割り当てられたユーザーの一覧にアプリケーションが表示されます。

## <a name="next-steps"></a>次の手順
* マルチテナント アプリケーションのセットアップについては、「 [Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。
* セキュリティ ポリシーを指定する方法については、「[Azure のロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md)」を参照してください。  
* ユーザーに対して許可または拒否される場合がある使用可能なアクションの一覧については、「[Azure Resource Manager のリソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md)」を参照してください。
