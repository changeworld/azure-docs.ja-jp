---
title: Azure Enterprise Agreement サービス プリンシパル名にロールを割り当てる
description: この記事は、PowerShell と REST API シリーズを使用してサービス プリンシパル名にロールを割り当てる際に役立ちます。
author: bandersmsft
ms.reviewer: sapnakeshari
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/22/2021
ms.author: banders
ms.openlocfilehash: 65f37b3f071286d143dc83674a7c7108a37cff44
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130260381"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>Azure Enterprise Agreement サービス プリンシパル名にロールを割り当てる

[Azure Enterprise Portal](https://ea.azure.com/) では、Enterprise Agreement (EA) の登録を管理できます。 ダイレクト Enterprise のお客様は、[Azure portal](https://portal.azure.com/) で Enterprise Agreement (EA) の登録を管理できるようになりました。
さまざまなロールを作成して、組織を管理したり、コストを表示したり、サブスクリプションを作成したりすることができます。 この記事は、Azure PowerShell と REST API シリーズ、さらに Azure のサービス プリンシパル名 (SPN) を使用してこれらのタスクの一部を自動化する際に役立ちます。

始める前に、次の記事の内容を理解しておいてください。

- [Enterprise Agreement の各種ロール](understand-ea-roles.md)
- [Azure PowerShell を使用してサインインする](/powershell/azure/authenticate-azureps)
- [Postman を使用して REST API を呼び出す方法](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>サービス プリンシパルを作成および認証する

SPN を使用して EA に関するアクションを自動化するには、Azure Active Directory (Azure AD) アプリケーションを作成する必要があります。 その認証は自動的に実行できます。

これらの記事の手順に従って、サービス プリンシパルを作成して認証します。

- [サービス プリンシパルの作成](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
- [サインインするためのテナントとアプリ ID の値を取得する](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

アプリケーション登録ページの例を次に示します。

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="アプリケーションを登録するようすを示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>SPN とテナント ID を特定する

SPN のオブジェクト ID とアプリのテナント ID も必要です。 この情報は、この記事で後述するアクセス許可の割り当て操作に必要になります。

1. Azure Active Directory を開き、 **[エンタープライズ アプリケーション]** を選択します。
1. 一覧内で対象のアプリを見つけます。

   :::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="サンプルのエンタープライズ アプリケーションを示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

1. アプリを選択して、アプリケーション ID とオブジェクト ID を見つけます。

   :::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="エンタープライズ アプリケーションのアプリケーション ID とオブジェクト ID を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

1. Microsoft Azure AD の **[概要]** ページに移動して、テナント ID を見つけます。

   :::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="テナント ID を示すスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

>[!NOTE]
>テナント ID は、他の場所ではプリンシパル ID、SPN、またはオブジェクト ID と呼ばれることもあります。 Azure AD のテナント ID の値は GUID とよく似ており、`11111111-1111-1111-1111-111111111111` の形式になっています。

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>SPN に割り当てることができるアクセス許可

この記事の後半では、EA ロールを使用して動作するためのアクセス許可を Azure AD アプリに付与します。 SPN には、次のロールのみを割り当てることができます。また、示されているとおりのロール定義 ID が必要になります。

| ロール | 許可されるアクション | ロール定義 ID |
| --- | --- | --- |
| EnrollmentReader | あらゆるアカウントとサブスクリプションの使用状況と料金を確認できます。 登録に関連付けられている Azure 前払い (旧称: 年額コミットメント) の残高を確認できます。 | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| EA 購入者 | 予約注文を購入し、予約トランザクションを表示できます。 あらゆるアカウントとサブスクリプションの使用状況と料金を確認できます。 登録に関連付けられている Azure 前払い (旧称: 年額コミットメント) の残高を確認できます。 | da6647fb-7651-49ee-be91-c43c4877f0c4  |
| DepartmentReader | 管理している部署について、使用状況の詳細をダウンロードできます。 自分の部署に関連付けられている使用状況と料金を確認できます。 | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | 一定範囲のアカウントで、新しいサブスクリプションを作成できます。 | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- SPN に EnrollmentReader ロールを割り当てることができるのは、登録ライター ロールが割り当てられているユーザーのみです。
- SPN に DepartmentReader ロールを割り当てることができるのは、登録ライター ロールまたは部署ライター ロールが割り当てられているユーザーのみです。
- SPN に SubscriptionCreator ロールを割り当てることができるのは、登録アカウントの所有者となっているユーザーのみです。 このロールは EA Portal に表示されません。 これは、プログラムによって作成され、プログラムによる使用のみを目的としています。
- EA 購入者ロールは EA Portal に表示されません。 これは、プログラムによって作成され、プログラムによる使用のみを目的としています。

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>SPN に登録アカウントのロールのアクセス許可を割り当てる

1. REST API に関する記事「[ロールの割り当て - Put](/rest/api/billing/2019-10-01-preview/role-assignments/put)」をお読みください。 記事を読んでいる間に、 **[使ってみる]** を選択して、SPN の使用を開始します。

   :::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Put に関する記事の中の [使ってみる] オプションを示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

1. 自分のアカウント資格情報を使用して、割り当てる登録アクセス権があるテナントにサインインします。

1. API 要求の一環として、次のパラメーターを指定します。

   - `billingAccountName`: このパラメーターは、**課金アカウント ID** です。 これは、Azure portal の **[コストの管理と請求]** の [概要] ページで見つけることができます。

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="課金アカウント ID を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName`: このパラメーターは一意の GUID であり、指定が必要です。 GUID は、PowerShell コマンド [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) を使って生成できます。 [Online GUID / UUID Generator](https://guidgenerator.com/) Web サイトを使って一意の GUID を生成することもできます。

   - `api-version`: **2019-10-01-preview** バージョンを使用します。 [「ロールの割り当て - Put」の「例」](/rest/api/billing/2019-10-01-preview/role-assignments/put#examples)のところにある要求本文のサンプルを使用します。

      要求の本文には、使用する必要のある 3 つのパラメーターを含む JSON コードが含まれています。

      | パラメーター | 参照先 |
      | --- | --- |
      | `properties.principalId` | これはオブジェクト ID の値です。 「[SPN とテナント ID を特定する](#find-your-spn-and-tenant-id)」を参照してください。 |
      | `properties.principalTenantId` | 「[SPN とテナント ID を特定する](#find-your-spn-and-tenant-id)」を参照してください。 |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` |

      課金アカウント名は、API のパラメーターで使用したパラメーターと同じです。 これは、EA Portal と Azure portal で確認できる登録 ID です。

      `24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` は EnrollmentReader の請求先のロール定義 ID であることに注意してください。

1. **[実行]** を選択してコマンドを起動します。

   :::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="「ロールの割り当て - Put」の例の [使ってみる] を選択し、サンプルの情報を入力して [実行] の準備が整っている状態を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

   `200 OK` という応答は、SPN が正常に追加されたことを示します。

これで、SPN を使用して EA API シリーズに自動的にアクセスできます。 SPN には EnrollmentReader ロールが割り当てられています。

## <a name="assign-ea-purchaser-role-permission-to-the-spn"></a>SPN に EA 購入者ロールのアクセス許可を割り当てる

EA 購入者ロールについては、登録リーダーのものと同じ手順を使用します。 次の例を使用して、`roleDefinitionId` を指定します。

`"/providers/Microsoft.Billing/billingAccounts/1111111/billingRoleDefinitions/ da6647fb-7651-49ee-be91-c43c4877f0c4"`

## <a name="assign-the-department-reader-role-to-the-spn"></a>SPN に部署リーダー ロールを割り当てる

1. REST API に関する記事「[登録部署のロールの割り当て - Put](/rest/api/billing/2019-10-01-preview/enrollment-department-role-assignments/put)」をお読みください。 記事を読んでいる間に、 **[使ってみる]** を選択します。

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="「登録部署のロールの割り当て - Put」の記事の [使ってみる] オプションを示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

1. 自分のアカウント資格情報を使用して、割り当てる登録アクセス権があるテナントにサインインします。

1. API 要求の一環として、次のパラメーターを指定します。

   - `billingAccountName`: このパラメーターは、**課金アカウント ID** です。 これは、Azure portal の **[コストの管理と請求]** の [概要] ページで見つけることができます。

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="課金アカウント ID を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName`: このパラメーターは一意の GUID であり、指定が必要です。 GUID は、PowerShell コマンド [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) を使って生成できます。 [Online GUID / UUID Generator](https://guidgenerator.com/) Web サイトを使って一意の GUID を生成することもできます。

   - `departmentName`: このパラメーターは部署 ID です。 部署 ID は、Azure portal の **[コストの管理と請求]**  >  **[部門]** ページで確認できます。

      この例では、ACE 部署を使用しました。 この例の ID は `84819` です。

      :::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="部署 ID の例を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

   - `api-version`: **2019-10-01-preview** バージョンを使用します。 「[登録部署のロールの割り当て - Put](/rest/api/billing/2019-10-01-preview/enrollment-department-role-assignments/put)」にあるサンプルを使用します。

      要求の本文には、使用する必要のある 3 つのパラメーターを含む JSON コードが含まれています。

      | パラメーター | 参照先 |
      | --- | --- |
      | `properties.principalId` | これはオブジェクト ID の値です。 「[SPN とテナント ID を特定する](#find-your-spn-and-tenant-id)」を参照してください。 |
      | `properties.principalTenantId` | 「[SPN とテナント ID を特定する](#find-your-spn-and-tenant-id)」を参照してください。 |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a` |

      課金アカウント名は、API のパラメーターで使用したパラメーターと同じです。 これは、EA Portal と Azure portal で確認できる登録 ID です。

      請求先のロール定義 ID `db609904-a47f-4794-9be8-9bd86fbffd8a` は、部署リーダーのものです。

1. **[実行]** を選択してコマンドを起動します。

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="「登録部署のロールの割り当て - Put」で REST の例の [使ってみる] を選択し、サンプルの情報を入力して [実行] の準備が整っている状態を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

   `200 OK` という応答は、SPN が正常に追加されたことを示します。

これで、SPN を使用して EA API シリーズに自動的にアクセスできます。 SPN には DepartmentReader ロールが割り当てられています。

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>SPN にサブスクリプション作成者ロールを割り当てる

1. 記事「[登録アカウントのロールの割り当て - Put](/rest/api/billing/2019-10-01-preview/enrollment-account-role-assignments/put)」をお読みください。 読んでいる間に、 **[使ってみる]** を選択して、SPN にサブスクリプション作成者ロールを割り当てます。

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="「登録アカウントのロールの割り当て - Put」の記事の [使ってみる] オプションを示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

1. 自分のアカウント資格情報を使用して、割り当てる登録アクセス権があるテナントにサインインします。

1. API 要求の一環として、次のパラメーターを指定します。 記事[「登録アカウントのロールの割り当て - Put」の「URI パラメーター」](/rest/api/billing/2019-10-01-preview/enrollment-account-role-assignments/put#uri-parameters)をお読みください。

   - `billingAccountName`: このパラメーターは、**課金アカウント ID** です。 これは、Azure portal の **[コストの管理と請求] の [概要]** ページで見つけることができます。

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="課金アカウント ID を示すスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName`: このパラメーターは一意の GUID であり、指定が必要です。 GUID は、PowerShell コマンド [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) を使って生成できます。 [Online GUID/UUID Generator](https://guidgenerator.com/) Web サイトを使って一意の GUID を生成することもできます。

   - `enrollmentAccountName`: このパラメーターは、アカウント **ID** です。 Azure portal の **[コストの管理と請求]** ページで、対象のアカウント名のアカウント ID を見つけます。

      この例では、GTM Test Account を使用しています。 ID は `196987` です。

      :::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="アカウント ID を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

   - `api-version`: **2019-10-01-preview** バージョンを使用します。 [「登録部署のロールの割り当て - Put」の「例」](/rest/api/billing/2019-10-01-preview/enrollment-department-role-assignments/put#examples)のところにあるサンプルを使用します。

      要求の本文には、使用する必要のある 3 つのパラメーターを含む JSON コードが含まれています。

      | パラメーター | 参照先 |
      | --- | --- |
      | `properties.principalId` | これはオブジェクト ID の値です。 「[SPN とテナント ID を特定する](#find-your-spn-and-tenant-id)」を参照してください。 |
      | `properties.principalTenantId` | 「[SPN とテナント ID を特定する](#find-your-spn-and-tenant-id)」を参照してください。 |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71` |

      課金アカウント名は、API のパラメーターで使用したパラメーターと同じです。 これは、EA Portal と Azure portal で確認できる登録 ID です。

      請求先のロール定義 ID `a0bcee42-bf30-4d1b-926a-48d21664ef71` は、サブスクリプション作成者ロールのものです。

1. **[実行]** を選択してコマンドを起動します。

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="「登録アカウントのロールの割り当て - Put」の記事の [使ってみる] オプションを示したスクリーンショット" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

   `200 OK` という応答は、SPN が正常に追加されたことを示します。

これで、SPN を使用して EA API シリーズに自動的にアクセスできます。 SPN には SubscriptionCreator ロールが割り当てられています。

## <a name="troubleshoot"></a>トラブルシューティング

EA ロールを付与したエンタープライズ アプリケーションのオブジェクト ID を特定して使用する必要があります。 他のアプリケーションのオブジェクト ID を使用すると、API 呼び出しは失敗します。 正しいエンタープライズ アプリケーションのオブジェクト ID を使用していることを確認します。

## <a name="next-steps"></a>次の手順

[Azure EA Portal の管理](ea-portal-administration.md)について学習します。
