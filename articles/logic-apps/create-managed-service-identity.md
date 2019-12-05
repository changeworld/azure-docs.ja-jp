---
title: マネージド ID による認証
description: 資格情報やシークレットを使用してサインインすることなく、マネージド ID を使用して他の Azure Active Directory テナント内のリソースにアクセスする
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: 49c925cfe61084d8fedfdf953d469db4bd2c10b1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792682"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Azure Logic Apps でマネージド ID を使用して Azure リソースへのアクセスを認証する

サインインすることなく他の Azure Active Directory (Azure AD) テナント内のリソースへのアクセスと ID の認証を行うために、ロジック アプリでは、資格情報やシークレットではなく、システム割り当て[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) (以前はマネージド サービス ID (MSI) と呼ばれていました) を使用できます。 この ID は、ユーザーの代わりに Azure で管理されます。ユーザーがシークレットを提供したりローテーションしたりする必要がないため、資格情報の保護に役立ちます。 この記事では、ロジック アプリでシステム割り当てマネージド ID を設定して使用する方法を示します。 現在、マネージド ID は、マネージド コネクタまたは接続ではなく、[特定の組み込みトリガーおよびアクション](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-to-outbound-calls)でのみ動作します。

詳細については、以下のトピックを参照してください。

* [マネージド ID がサポートされているトリガーとアクション](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [マネージド ID を使用した Azure AD 認証がサポートされているサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)
* [送信呼び出しでサポートされている認証の種類](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [ロジック アプリのマネージド ID の制限](../logic-apps/logic-apps-limits-and-config.md#managed-identity)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。サブスクリプションをお持ちでない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 マネージド ID とアクセスするターゲット Azure リソースの両方で、同じ Azure サブスクリプションを使用する必要があります。

* ターゲット リソースと同じ Azure AD テナント内のマネージド ID にロールを割り当てることができる、[Azure AD 管理者のアクセス許可](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。 マネージド ID に Azure リソースへのアクセス権を付与するには、その ID のロールをターゲット リソースに追加する必要があります。

* アクセスするターゲット Azure リソース

* [マネージド ID がサポートされているトリガーとアクション](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)を使用するロジック アプリ

<a name="system-assigned"></a>

## <a name="enable-system-assigned-identity"></a>システム割り当て ID を有効にする

ユーザー割り当て ID とは異なり、システム割り当て ID を手動で作成する必要はありません。 ロジック アプリのシステム割り当て ID を設定する際は、次のオプションを使用できます。

* [Azure Portal](#azure-portal-system-logic-app)
* [Azure リソース マネージャーのテンプレート](#template-system-logic-app)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

<a name="azure-portal-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Azure portal でシステム割り当て ID を有効にする

1. [Azure Portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[ID]**  >  **[システム割り当て済み]** の順に選択します。 **[状態]** で、 **[オン]**  >  **[保存]**  >  **[はい]** の順に選択します。

   ![システム割り当て ID を有効にする](./media/create-managed-service-identity/turn-on-system-assigned-identity.png)

   これで、ロジック アプリでシステム割り当て ID を使用できるようになりました。この ID は Azure Active Directory に登録され、オブジェクト ID で表されます。

   ![システム割り当て ID のオブジェクト ID](./media/create-managed-service-identity/object-id.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **オブジェクト ID** | <*identity-resource-ID*> | Azure AD テナント内のロジック アプリのシステム割り当て ID を表すグローバル一意識別子 (GUID) |
   ||||

1. 次に、[ID にリソースへのアクセス権を付与する手順](#access-other-resources)に従います。

<a name="template-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Azure Resource Manager テンプレートでシステム割り当て ID を有効にする

ロジック アプリなどの Azure リソースの作成とデプロイを自動化するために、[Azure Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)を使用できます。 テンプレートでロジック アプリのシステム割り当てマネージド ID を有効にするには、次のように、テンプレートのロジック アプリのリソース定義に `identity` オブジェクトと `type` 子プロパティを追加します。

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Azure によってロジック アプリのリソース定義が作成されると、`identity` オブジェクトで次の追加のプロパティが取得されます。

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| プロパティ (JSON) | 値 | 説明 |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | Azure AD テナント内のロジック アプリを表すマネージド ID のサービス プリンシパル オブジェクトのグローバル一意識別子 (GUID)。 この GUID は、"オブジェクト ID" (`objectID`) として表されることがあります。 |
| `tenantId` | <*Azure-AD-tenant-ID*> | ロジック アプリが現在メンバーとなっている Azure AD テナントを表すグローバル一意識別子 (GUID)。 Azure AD テナント内では、サービス プリンシパルは、ロジック アプリ インスタンスと同じ名前を持ちます。 |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>ID にリソースへのアクセス権を付与する

ロジック アプリ用にマネージド ID を設定すると、[その ID に他の Azure リソースへのアクセス権を与える](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)ことができます。 その後、その ID を認証に使用できます。

1. [Azure portal](https://portal.azure.com) で、マネージド ID にアクセス権を与える Azure リソースに移動します。

1. リソースのメニューから、 **[アクセス制御 (IAM)]**  >  **[ロールの割り当て]** を選択します。ここで、そのリソースの現在のロールの割り当てを確認できます。 ツール バーで、 **[追加]**  >  **[ロールの割り当ての追加]** の順に選択します。

   ![[追加] > [ロールの割り当ての追加] の順に選択する](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > **[ロールの割り当ての追加]** オプションが無効になっている場合は、ご自分にアクセス許可がない可能性があります。 リソースのロールを管理するためのアクセス許可の詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](../active-directory/users-groups-roles/directory-assign-admin-roles.md)」をご覧ください。

1. **[ロールの割り当ての追加]** で、ID にターゲット リソースへのアクセスに必要なアクセス権を与える**ロール**を選択します。

   このトピックの例では、ID に [Azure Storage コンテナー内の BLOB にアクセスできるロール](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)が必要です。

   ![[ストレージ BLOB データ共同作成者] ロールを選択する](./media/create-managed-service-identity/assign-role.png)

1. **[アクセスの割り当て先]** ボックスで **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選択します。

   ![システム割り当て ID のアクセス権を選択する](./media/create-managed-service-identity/assign-access-system.png)

1. **[選択]** ボックスで、ご自分のロジック アプリを探して選択します。

   ![システム割り当て ID を設定するロジック アプリを選択する](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. 終了したら、 **[保存]** を選択します。

   ターゲット リソースの [ロールの割り当て] の一覧に、選択したマネージド ID とロールが表示されます。

   ![ターゲット リソースに追加されたマネージド ID とロール](./media/create-managed-service-identity/added-roles-for-identities.png)

1. 次に、マネージド ID がサポートされているトリガーまたはアクションで [ID 使用してアクセスを認証する手順](#authenticate-access-with-identity)に従います。

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>マネージド ID を利用してアクセスを認証する

[ロジック アプリに対してマネージド ID を有効](#azure-portal-system-logic-app)にし、[その ID にターゲット リソースまたはエンティティへのアクセス権を付与](#access-other-resources)したら、その ID を[マネージド ID がサポートされているトリガーとアクション](logic-apps-securing-a-logic-app.md#managed-identity-authentication)で使用できます。

> [!IMPORTANT]
> システム割り当て ID を使用する Azure 関数がある場合は、先に [Azure Functions の認証を有効](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)にします。

次の手順では、Azure portal を通じて、トリガーまたはアクションでマネージド ID を使用する方法を示します。 トリガーまたはアクションの基になる JSON 定義でマネージド ID を指定する方法については、「[マネージド ID 認証](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)」をご覧ください。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. まだ行っていない場合は、[マネージド ID がサポートされているトリガーまたはアクション](logic-apps-securing-a-logic-app.md#managed-identity-authentication)を追加します。

   たとえば、HTTP トリガーまたはアクションでは、ロジック アプリに対して有効にしたシステム割り当て ID を使用できます。 一般に、HTTP トリガーまたはアクションでは、次のプロパティを使用して、アクセスするリソースまたはエンティティを指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **メソッド** | はい | 実行する操作によって使用される HTTP メソッド |
   | **URI** | はい | ターゲットの Azure リソースまたはエンティティにアクセスするためのエンドポイント URL。 URI 構文には、通常、Azure リソースまたはサービスの[リソース ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) が含まれています。 |
   | **ヘッダー** | いいえ | コンテンツ タイプなど、送信要求に含める必要がある (または含めたい) ヘッダーの値 |
   | **クエリ** | いいえ | 特定の操作のパラメーターや実行する操作の API バージョンなど、要求に含める必要がある (または含めたい) クエリ パラメーター |
   | **認証** | はい | ターゲット リソースまたはエンティティへのアクセスの認証に使用する認証の種類 |
   ||||

   具体的な例として、以前に ID 用のアクセスを設定した Azure Storage アカウントの BLOB に対して [Snapshot Blob 操作](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)を実行するとします。 しかし、[Azure Blob Storage コネクタ](https://docs.microsoft.com/connectors/azureblob/)では現在、この操作が提供されていません。 代わりに、[HTTP アクション](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)または別の [BLOB サービス REST API 操作](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)を使用して、この操作を実行できます。

   > [!IMPORTANT]
   > HTTP 要求とマネージド ID を使用してファイアウォールの背後にある Azure Storage アカウントにアクセスするには、[信頼された Microsoft サービスによるアクセスを許可する例外](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)を使用してストレージ アカウントを設定する必要もあります。

   [Snapshot Blob 操作](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)を実行するには、HTTP アクションで次のプロパティを指定します。

   | プロパティ | 必須 | 値の例 | 説明 |
   |----------|----------|---------------|-------------|
   | **メソッド** | はい | `PUT`| Snapshot Blob 操作で使用する HTTP メソッド |
   | **URI** | はい | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | この構文を使用する Azure Global (パブリック) 環境内の Azure Blob Storage ファイルのリソース ID |
   | **ヘッダー** | はい (Azure Storage の場合) | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Azure Storage 操作に必要な `x-ms-blob-type` ヘッダーと `x-ms-version` ヘッダーの値。 <p><p>**重要**:Azure Storage の発信 HTTP トリガーおよびアクションの要求では、ヘッダーに `x-ms-version` プロパティと実行する操作の API バージョンが必要です。 <p>詳細については、以下のトピックを参照してください。 <p><p>- [要求ヘッダー - Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Azure Storage サービスのバージョン管理](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **クエリ** | はい (この操作の場合) | `comp` = `snapshot` | Snapshot Blob 操作のクエリ パラメーターの名前と値。 |
   | **認証** | はい | `Managed Identity` | Azure BLOB へのアクセスの認証に使用する認証の種類 |
   |||||

   上記すべてのプロパティ値を示す HTTP アクションの例:

   ![Azure リソースにアクセスするための HTTP アクションを追加する](./media/create-managed-service-identity/http-action-example.png)

   使用可能なすべての Azure REST API 操作の詳細については、[Azure REST API リファレンス](https://docs.microsoft.com/rest/api/azure/)を参照してください。

1. **[認証]** 一覧から、 **[マネージド ID]** を選択します。 [ **[認証]** プロパティがサポートされている](logic-apps-securing-a-logic-app.md#add-authentication-outbound)が非表示になっている場合は、 **[新しいパラメーターの追加]** 一覧を開き、 **[認証]** を選択します。

   > [!NOTE]
   > すべてのトリガーとアクションで認証の種類を選択できるわけではありません。 詳しくは、「[送信呼び出しに認証を追加する](logic-apps-securing-a-logic-app.md#add-authentication-outbound)」をご覧ください。

   ![[認証] プロパティで [マネージド ID] を選択する](./media/create-managed-service-identity/select-managed-identity.png)

1. **[マネージド ID]** を選択すると、一部のトリガーとアクションで **[対象ユーザー]** プロパティが表示されます。 **[対象ユーザー]** プロパティがサポートされているが非表示になっている場合は、 **[新しいパラメーターの追加]** 一覧を開き、 **[対象ユーザー]** を選択します。

1. ターゲット リソースまたはサービスのリソース ID には、必ず **[対象ユーザー]** 値を設定してください。 そうしないと、既定では、 **[対象ユーザー]** プロパティに Azure Resource Manager のリソース ID である `https://management.azure.com/` リソース ID が使用されます。

   > [!IMPORTANT]
   > ターゲット リソース ID は、必要な末尾のスラッシュも含めて、Azure Active Directory (AD) で予想される値と*正確に一致*するようにします。 たとえば、すべての Azure Blob Storage アカウントのリソース ID には、末尾のスラッシュが必要です。 ただし、特定のストレージ アカウントのリソース ID については、末尾のスラッシュは必要ありません。 「[Azure AD 認証をサポートしている Azure サービスのリソース ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)」をご覧ください。

   この例では、 **[対象ユーザー]** プロパティを `https://storage.azure.com/` に設定して、認証に使用されるアクセス トークンをすべてのストレージ アカウントに対して有効にしています。 ただし、特定のストレージ アカウントに対してルート サービス URL (`https://fabrikamstorageaccount.blob.core.windows.net`) を指定することもできます。

   ![[対象ユーザー] プロパティでターゲット リソース ID を指定する](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Azure AD を使用した Azure Storage へのアクセスの承認について詳しくは、次のトピックをご覧ください。

   * [Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを承認する](../storage/common/storage-auth-aad.md)
   * [Azure Active Directory を使用して Azure Storage へのアクセスを承認する](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

<a name="remove-identity"></a>

## <a name="remove-system-assigned-identity"></a>システム割り当て ID を削除する

ロジック アプリのシステム割り当て ID の使用を停止する場合は、次のオプションがあります。

* [Azure Portal](#azure-portal-disable)
* [Azure リソース マネージャーのテンプレート](#template-disable)
* [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/remove-azroleassignment)
* [Azure CLI](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-delete)

ロジック アプリを削除すると、Azure によってマネージド ID が Azure AD から自動的に削除されます。

<a name="azure-portal-disable"></a>

### <a name="remove-system-assigned-identity-in-the-azure-portal"></a>Azure portal でシステム割り当て ID を削除する

Azure portal で、システム割り当て ID を[ロジック アプリから](#disable-identity-logic-app)削除し、その ID のアクセス権を[ターゲット リソースから](#disable-identity-target-resource)削除します。

<a name="disable-identity-logic-app"></a>

#### <a name="remove-system-assigned-identity-from-logic-app"></a>ロジック アプリからシステム割り当て ID を削除する

1. [Azure Portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[ID]**  >  **[システム割り当て済み]** の順に選択します。 **[状態]** で、 **[オフ]**  >  **[保存]**  >  **[はい]** の順に選択します。

   ![システム割り当て ID の使用を停止する](./media/create-managed-service-identity/turn-off-system-assigned-identity.png)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>リソースから ID のアクセス権を削除する

1. [Azure portal](https://portal.azure.com) で、マネージド ID のアクセス権を削除するターゲット Azure リソースに移動します。

1. ターゲット リソースのメニューから **[アクセス制御 (IAM)]** を選択します。 ツール バーで、 **[ロールの割り当て]** を選択します。

1. ロールの一覧で、削除するマネージド ID を選択します。 ツール バーの **[削除]** を選択します。

   > [!TIP]
   > **[削除]** オプションが無効になっている場合は、ご自分にアクセス許可がない可能性があります。 リソースのロールを管理するためのアクセス許可の詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](../active-directory/users-groups-roles/directory-assign-admin-roles.md)」をご覧ください。

マネージド ID が削除され、ターゲット リソースにアクセスできなくなりました。

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Resource Manager テンプレートでマネージド ID を無効にする

Azure Resource Manager テンプレートを使用してロジック アプリのシステム マネージド ID を有効にした場合は、`identity` オブジェクトの `type` 子プロパティを `None` に設定します。 この操作により、システム マネージド ID のプリンシパル ID も Azure AD から削除されます。

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>次の手順

* [Azure Logic Apps におけるアクセスとデータのセキュリティ保護](../logic-apps/logic-apps-securing-a-logic-app.md)
