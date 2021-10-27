---
title: Azure portal を使用して Azure ロールを割り当てる - Azure RBAC
description: Azure portal と Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、ユーザー、グループ、サービス プリンシパル、またはマネージド ID に対して Azure リソースへのアクセス権を付与する方法について説明します。
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/15/2021
ms.author: rolyon
ms.custom: contperf-fy21q3-portal,subject-rbac-steps
ms.openlocfilehash: a1d48ccbc2687feea584d9da717ac97b9eabce72
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072109"
---
# <a name="assign-azure-roles-using-the-azure-portal"></a>Azure portal を使用して Azure ロールを割り当てる

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] この記事では、Azure portal を使用してロールを割り当てる方法について説明します。

Azure Active Directory で管理者ロールを割り当てる必要がある場合、「[Azure AD ロールをユーザーに割り当てる](../active-directory/roles/manage-roles-portal.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

#### <a name="current"></a>[Current](#tab/current/)

## <a name="step-1-identify-the-needed-scope"></a>手順 1:必要なスコープを特定する

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]詳細については、「[スコープを理解する](scope-overview.md)」を参照してください。

![Azure RBAC のスコープ レベルを示す図。](../../includes/role-based-access-control/media/scope-levels.png)

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 上部にある検索ボックスで、アクセス権を付与するスコープを検索します。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、または特定のリソースを検索します。

1. そのスコープの特定のリソースをクリックします。

    リソース グループの例を次に示します。

    ![リソース グループの [概要] ページのスクリーンショット。](./media/shared/rg-overview.png)

## <a name="step-2-open-the-add-role-assignment-page"></a>手順 2: [ロールの割り当ての追加] ページを開く

**アクセス制御 (IAM)** は、一般的には、ロールを割り当てて Azure リソースへのアクセスを付与するために使用するページです。 "ID とアクセス管理 (IAM)" とも呼ばれており、Azure portal のいくつかの場所に表示されます。

1. **[アクセス制御 (IAM)]** をクリックします。

    リソース グループの [アクセス制御 (IAM)] ページの例を次に示します。

    ![リソース グループの [アクセス制御 (IAM)] ページのスクリーンショット。](./media/shared/rg-access-control.png)

1. **[ロールの割り当て]** タブをクリックして、このスコープのロールの割り当てを表示します。

1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックします。

    ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

    ![[追加] > [ロールの割り当ての追加] メニューのスクリーンショット。](./media/shared/add-role-assignment-menu.png)

    [ロールの割り当ての追加] ページが開きます。

## <a name="step-3-select-the-appropriate-role"></a>手順 3: 適切なロールを選択する

1. **[ロール]** タブで、使用するロールを選択します。

    ロールは、名前または説明で検索できます。 種類とカテゴリでロールをフィルター処理することもできます。

   ![[ロールの割り当ての追加] ページで [ロール] タブが表示された状態のスクリーンショット。](./media/shared/roles.png)

1. **[詳細]** 列で **[表示]** をクリックして、ロールに関する詳細を表示します。

   ![[アクセス許可] タブが表示された [ロールの詳細の表示] ウィンドウのスクリーンショット。](./media/role-assignments-portal/select-role-permissions.png)

1. **[次へ]** をクリックします。

## <a name="step-4-select-who-needs-access"></a>手順 4:アクセスを必要とするユーザーを選択する

1. **[メンバー]** タブで、 **[ユーザー、グループ、またはサービス プリンシパル]** を選択して、選択したロールを 1 つ以上の Azure AD ユーザー、グループ、またはサービス プリンシパル (アプリケーション) に割り当てます。

   ![[ロールの割り当ての追加] ページで [メンバー] タブが表示された状態のスクリーンショット。](./media/shared/members.png)

1. **[メンバーの選択]** をクリックします。

1. ユーザー、グループ、またはサービス プリンシパルを検索して選択します。

    **[選択]** ボックスに表示名またはメール アドレスを入力してディレクトリを検索できます。

   ![[メンバーの選択] ウィンドウのスクリーンショット。](./media/shared/select-members.png)

1. **[選択]** をクリックして、メンバーの一覧にユーザー、グループ、またはサービス プリンシパルを追加します。

1. 選択したロールを 1 つ以上のマネージド ID に割り当てるには、 **[マネージド ID]** を選択します。

1. **[メンバーの選択]** をクリックします。

1. **[マネージド ID の選択]** ウィンドウで、種類が [[ユーザー割り当てマネージド ID]](../active-directory/managed-identities-azure-resources/overview.md) であるか、[[システム割り当てマネージド ID]](../active-directory/managed-identities-azure-resources/overview.md) であるかを選択します。

1. マネージド ID を検索して選択します。

    システム割り当てマネージド ID の場合は、Azure サービス インスタンス別のマネージド ID を選択できます。

   ![マネージド ID ウィンドウを選択するスクリーンショット。](./media/role-assignments-portal/select-managed-identity.png)

1. **[選択]** をクリックして、メンバーの一覧にマネージド ID を追加します。

1. 必要に応じて、 **[説明]** ボックスにこのロール割り当ての説明を入力します。

    後で、ロールの割り当ての一覧にこの説明を表示できます。

1. **[次へ]** をクリックします。

## <a name="step-5-optional-add-condition-preview"></a>手順 5: (省略可能) 条件を追加する (プレビュー)

条件をサポートするロールを選択した場合は、 **[条件]** (オプション) タブが表示され、必要に応じてロールの割り当てに条件を追加できます。 [条件](conditions-overview.md)は、よりきめ細かなアクセス制御を行うために、必要に応じてロールの割り当てに追加できる追加のチェックです。

現時点では、[ストレージ BLOB データ アクション](conditions-format.md#actions)を使用する組み込みまたはカスタムのロール割り当てに対して条件を追加できます。 これらには、次の組み込みロールが含まれます。


- [ストレージ BLOB データ共同作成者](built-in-roles.md#storage-blob-data-contributor)
- [ストレージ BLOB データ所有者](built-in-roles.md#storage-blob-data-owner)
- [ストレージ BLOB データ閲覧者](built-in-roles.md#storage-blob-data-reader)

1. ストレージ BLOB の属性に基づいてロールの割り当てをさらに調整する場合は、 **[条件の追加]** をクリックします。 詳細については、[Azure ロールの割り当て条件の追加または編集](conditions-role-assignments-portal.md)に関するページを参照してください。

   ![[ロールの割り当ての追加] ページで [条件の追加] タブが表示された状態のスクリーンショット。](./media/shared/condition.png)

1. **[次へ]** をクリックします。

## <a name="step-6-assign-role"></a>手順 6: ロールを割り当てる

1. **[Review + assign]\(確認と割り当て\)** タブで、ロールの割り当ての設定を確認します。

   ![[Review + assign]\(確認と割り当て\) タブが表示された [ロールの割り当て] ページのスクリーンショット。](./media/role-assignments-portal/review-assign.png)

1. **[Review + assign]\(確認と割り当て\)** をクリックしてロールを割り当てます。

   しばらくすると、セキュリティ プリンシパルに選択されたスコープのロールが割り当てられます。

    ![ロールを割り当てた後のロールの割り当ての一覧のスクリーンショット。](./media/role-assignments-portal/rg-role-assignments.png)

1. ロールの割り当ての説明が表示されない場合は、 **[列の編集]** をクリックして **[説明]** 列を追加します。

#### <a name="classic"></a>[クラシック](#tab/classic/)

## <a name="step-1-identify-the-needed-scope-classic"></a>手順 1: 必要なスコープを特定する (クラシック)

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)] 詳細については、[スコープの概要](scope-overview.md)に関する記事を参照してください。

![Azure RBAC のスコープ レベル (クラシック エクスペリエンス) を示す図。](../../includes/role-based-access-control/media/scope-levels.png)

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 上部にある検索ボックスで、アクセス権を付与するスコープを検索します。 たとえば、 **[管理グループ]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、または特定のリソースを検索します。

1. そのスコープの特定のリソースをクリックします。

    リソース グループの例を次に示します。

    ![リソース グループの [概要] ページ (クラシック エクスペリエンス) のスクリーンショット。](./media/shared/rg-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane-classic"></a>手順 2: [ロールの割り当ての追加] ウィンドウを開く (クラシック)

**アクセス制御 (IAM)** は、一般的には、ロールを割り当てて Azure リソースへのアクセスを付与するために使用するページです。 "ID とアクセス管理 (IAM)" とも呼ばれており、Azure portal のいくつかの場所に表示されます。

1. **[アクセス制御 (IAM)]** をクリックします。

    リソース グループの [アクセス制御 (IAM)] ページの例を次に示します。

    ![リソース グループの [アクセス制御 (IAM)] ページ (クラシック エクスペリエンス) のスクリーンショット。](./media/shared/rg-access-control.png)

1. **[ロールの割り当て]** タブをクリックして、このスコープのロールの割り当てを表示します。

1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックします。
   ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

   ![[追加] > [ロールの割り当ての追加] メニュー (クラシック エクスペリエンス) のスクリーンショット。](./media/shared/add-role-assignment-menu.png)

1. [ロールの割り当ての追加] ページで、**[クラシック エクスペリエンスの使用]** をクリックします。

   ![クラシック エクスペリエンスの [クラシック エクスペリエンスの使用] リンクが表示された [ロールの割り当ての追加] ページのスクリーンショット。](./media/role-assignments-portal/add-role-assignment-page-use-classic.png)

    [ロールの割り当ての追加] ウィンドウが開きます。

   ![クラシック エクスペリエンスの [ロール]、[アクセスの割り当て先]、[選択] オプションが表示された [ロールの割り当ての追加] ページのスクリーンショット。](./media/role-assignments-portal/add-role-assignment-page.png)

## <a name="step-3-select-the-appropriate-role-classic"></a>手順 3: 適切なロールを選択する (クラシック)

1. **[ロール]** リストで、割り当てるロールを検索またはスクロールします。

    適切なロールを判断するため、情報アイコンをポイントすると、ロールの説明が表示されます。 詳細については、「[Azure 組み込みロール](built-in-roles.md)」の記事をご覧ください。

   ![クラシック エクスペリエンスの [ロールの割り当ての追加] の [ロールの選択] の一覧のスクリーンショット。](./media/role-assignments-portal/add-role-assignment-role.png)

1. クリックしてロールを選択します。

## <a name="step-4-select-who-needs-access-classic"></a>手順 4: アクセスを必要とするユーザーを選択する (クラシック)

1. **[アクセスの割り当て先]** リストで、アクセス権を割り当てるセキュリティ プリンシパルの種類を選択します。

    | Type | 説明 |
    | --- | --- |
    | **ユーザー、グループ、またはサービス プリンシパル** | ロールをユーザー、グループ、またはサービス プリンシパル (アプリケーション) に割り当てるには、この種類を選択します。 |
    | **ユーザー割り当てマネージド ID** | [ユーザー割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) にロールを割り当てるには、この種類を選択します。 |
    | *システム割り当てマネージド ID* | [システム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) にロールを割り当てるには、マネージド ID が配置されている Azure サービス インスタンスを選択します。 |

   ![クラシック エクスペリエンスの [ロールの割り当ての追加] でセキュリティ プリンシパルを選択するスクリーンショット。](./media/role-assignments-portal/add-role-assignment-type.png)

1. ユーザー割り当てマネージド ID またはシステム割り当てマネージド ID を選択した場合は、マネージド ID が配置されている **サブスクリプション** を選択します。

1. **[選択]** セクションで、文字列を入力するかリストをスクロールして、セキュリティ プリンシパルを検索します。

   ![クラシック エクスペリエンスの [ロールの割り当ての追加] でユーザーを選択するスクリーンショット。](./media/role-assignments-portal/add-role-assignment-user.png)

1. セキュリティ プリンシパルが見つかったら、クリックして選択します。

## <a name="step-5-assign-role-classic"></a>手順 5: ロールを割り当てる (クラシック)

1. ロールを割り当てるには、 **[保存]** をクリックします。

   しばらくすると、セキュリティ プリンシパルに選択されたスコープのロールが割り当てられます。

1. **[ロールの割り当て]** タブで、リストにロールの割り当てが表示されていることを確認します。

    ![ロールを割り当てた後のロール割り当ての一覧 (クラシック エクスペリエンス) のスクリーンショット。](./media/role-assignments-portal/rg-role-assignments.png)

---

## <a name="next-steps"></a>次のステップ

- [Azure サブスクリプションの管理者としてユーザーを割り当てる](role-assignments-portal-subscription-admin.md)
- [Azure ロールの割り当ての削除](role-assignments-remove.md)
- [Azure RBAC のトラブルシューティング](troubleshooting.md)
