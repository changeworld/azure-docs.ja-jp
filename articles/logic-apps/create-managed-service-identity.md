---
title: マネージド ID を使用して接続を認証する
description: Azure Logic Apps でマネージド ID を使用して、Azure AD の保護されたリソースへのワークフロー接続を認証します。資格情報やシークレットを使用する必要はありません。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: devx-track-azurepowershell, subject-rbac-steps, ignite-fall-2021
ms.openlocfilehash: c6e814f0b0b36408210cac7657c947f16a0076c5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085369"
---
# <a name="authenticate-access-to-azure-resources-with-managed-identities-in-azure-logic-apps"></a>Azure Logic Apps でマネージド ID を使用して Azure リソースへのアクセスを認証する

ロジック アプリ ワークフローの一部のトリガーとアクションは、Azure Active Directory (Azure AD) によって保護されているリソースへの接続の認証に[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) (旧称はマネージド サービス ID (MSI)) を使用することをサポートしています。 ロジック アプリ リソースでマネージド ID が有効化されている場合、資格情報、シークレット、または Azure AD トークンを渡す必要はありません。 この ID は Azure で管理され、認証情報が安全に保たれます。この機密情報をユーザー側で管理する必要がないからです。

Azure Logic Apps は、[システム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) と[ユーザー割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の両方をサポートしています。ロジック アプリ ワークフローの実行場所に基づいて、前者は 1 つのロジック アプリ リソースに使用され、後者は複数のロジック アプリ リソースから成るグループで共有されます。 

| ロジック アプリのリソースの種類 | 環境 | 説明 |
|-------------------------|-------------|-------------|
| 従量課金 | - マルチテナント Azure Logic Apps <p><p>- 統合サービス環境 (ISE) | ロジック アプリ リソース レベルと接続レベルで、システム割り当て ID または単一のユーザー割り当て ID のいずれかを有効にして使用できます。  |
| Standard | - シングルテナントの Azure Logic Apps <p><p>- App Service Environment v3 (ASEv3) <p><p>- Azure Arc 対応 Logic Apps | 現時点ではシステム割り当て ID だけを使用でき、これが自動的に有効になります。 現在、ユーザー割り当て ID は利用できません。 |
|||

Azure Logic Apps でのマネージド ID の制限については、「[ロジック アプリのマネージド ID に関する制限](logic-apps-limits-and-config.md#managed-identity)」を参照してください。 ロジック アプリのリソースの種類が従量課金および Standard と環境の詳細については、次のドキュメントを参照してください。

* [Azure Logic Apps とは](logic-apps-overview.md#resource-environment-differences)
* [シングルテナントとマルチテナント、および統合サービス環境](single-tenant-overview-compare.md)
* [Azure Arc 対応 Logic Apps](azure-arc-enabled-logic-apps-overview.md)

<a name="triggers-actions-managed-identity"></a>
<a name="managed-connectors-managed-identity"></a>

## <a name="where-you-can-use-a-managed-identity"></a>マネージド ID を使用できる場合

Azure AD オープン認証 (Azure AD OAuth) をサポートする特定の組み込み型のマネージド コネクタ操作だけが、マネージド ID を認証で使用できます。 次の表は、"*一部のサンプル*" のみを示しています。 詳細な一覧については、「[認証がサポートされているトリガーおよびアクションの認証の種類](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)」と[マネージド ID を使用した Azure AD 認証がサポートされている Azure サービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)に関する記事を参照してください。

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

次の表に、リソースの種類 **Logic App (従量課金)** でシステム割り当てマネージド ID またはユーザー割り当てマネージド ID のいずれかを使用できる操作を示します。

| 演算の種類 | サポート対象の操作 |
|----------------|----------------------|
| 組み込み | - Azure API Management <br>- Azure App Services <br>- Azure Functions <br>- HTTP <br>- HTTP および Webhook <p>**注**: HTTP 操作では、システム割り当て ID を使用して Azure ファイアウォールの内側にある Azure Storage アカウントへの接続を認証できます。 ただし、同じ接続の認証に、ユーザー割り当てマネージド ID はサポートされていません。 |
| マネージド コネクタ (**プレビュー**) | 単一認証: <br>- Azure Automation <br>- Azure Event Grid <br>- Azure Key Vault <br>- Azure Resource Manager <br>- Azure AD を使用した HTTP <p>マルチ認証: <br> - Azure Blob ストレージ <br>- SQL Server |
|||

### <a name="standard"></a>[Standard](#tab/standard)

次の表に、リソースの種類 **Logic App (Standard)** でシステム割り当てマネージド ID を使用できる操作を示します。

| 演算の種類 | サポート対象の操作 |
|----------------|----------------------|
| 組み込み | - HTTP <br>- HTTP および Webhook <p>**注**: HTTP 操作では、システム割り当て ID を使用して Azure ファイアウォールの内側にある Azure Storage アカウントへの接続を認証できます。 |
| マネージド コネクタ (**プレビュー**) | 単一認証: <br>- Azure Automation <br>- Azure Event Grid <br>- Azure Key Vault <br>- Azure Resource Manager <br>- Azure AD を使用した HTTP <p>マルチ認証: <br> - Azure Blob ストレージ <br>- SQL Server |
|||

---

この記事では、リソースの種類として **Logic App (従量課金)** と **Logic App (Standard)** のどちらを使用するかに基づいて、システム割り当て ID またはユーザー割り当て ID を有効にして設定する方法を示します。 手動で作成する必要はないシステム割り当て ID とは異なり、リソースの種類が **Logic App (従量課金)** の場合のユーザー割り当て ID は手動で作成する必要があります。 この記事では、ユーザー割り当て ID を作成する手順について説明します。Azure portal と Azure Resource Manager テンプレート (ARM テンプレート) を使用します。 Azure PowerShell、Azure CLI、Azure REST API については、次のドキュメントを参照してください。

| ツール | ドキュメント |
|------|---------------|
| Azure PowerShell | [ユーザー割り当て ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) |
| Azure CLI | [ユーザー割り当て ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |
| Azure REST API | [ユーザー割り当て ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md) |
|||

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 マネージド ID とアクセスする必要があるターゲット Azure リソースの両方で、同じ Azure サブスクリプションを使用する必要があります。

* マネージド ID に Azure リソースへのアクセス権を付与するには、その ID のターゲット リソースにロールを追加する必要があります。 ロールを追加するには、対応する Azure AD テナント内の ID にロールを割り当てることができる、[Azure AD 管理者のアクセス許可](../active-directory/roles/permissions-reference.md)が必要です。

* アクセスするターゲット Azure リソース。 このリソースでは、マネージド ID のロールを追加します。これにより、ロジック アプリのリソースまたは接続はターゲット リソースへのアクセスの認証を受けることができます。

* [マネージド ID がサポートされているトリガーまたはアクション](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)を使用するロジック アプリのリソース。

  | ロジック アプリのリソースの種類 | マネージド ID のサポート |
  |-------------------------|--------------------------|
  | 従量課金 | システム割り当てまたはユーザー割り当ての ID |
  | Standard | システム割り当て ID (自動的に有効) |
  |||

<a name="system-assigned-azure-portal"></a>
<a name="azure-portal-system-logic-app"></a>

## <a name="enable-system-assigned-identity-in-azure-portal"></a>Azure portal でシステム割り当て ID を有効にする

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ リソースを開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[ID]** を選択します。

<<<<<<< HEAD
* [Azure Portal](#azure-portal-system-logic-app)
* [Azure Resource Manager テンプレート](#template-system-logic-app)
=======
1. **[ID]** ペインの **[システム割り当て]** で、 **[オン]**  >  **[保存]** を選びます。 確認を求めるメッセージが表示されたら、 **[はい]** を選択します。
>>>>>>> repo_sync_working_branch

   ![Azure portal の従量課金ロジック アプリの [ID] ペインおよび [システム割り当て] タブの [オン] と [保存] が選ばれている状態を示すスクリーンショット。](./media/create-managed-service-identity/enable-system-assigned-identity-consumption.png)

   > [!NOTE]
   > マネージド ID を 1 つだけ持つことができるというエラーが表示される場合、ロジック アプリのリソースは既にユーザー割り当て ID に関連付けられています。 システム割り当て ID を追加できるようにするには、まず、ロジック アプリのリソースからユーザー割り当て ID を削除する必要があります。

   これで、ロジック アプリのリソースでシステム割り当て ID を使用できるようになりました。これは Azure AD に登録され、オブジェクト ID で表されます。

   ![従量課金ロジック アプリの [ID] ペインと、システム割り当て ID のオブジェクト ID を示すスクリーンショット。](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **オブジェクト (プリンシパル) ID** | <*identity-resource-ID*> | Azure AD テナント内のロジック アプリのシステム割り当て ID を表すグローバル一意識別子 (GUID)。 |
   ||||

1. 次に、このトピックの後半にある、[ID にリソースへのアクセス権を付与する手順](#access-other-resources)に従います。

### <a name="standard"></a>[Standard](#tab/standard)

**Logic App (Standard)** のリソースでは、システム割り当て ID が自動的に有効になります。

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ ワークフローをデザイナーで開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[ID]** を選択します。

1. **[ID]** ペインの **[システム割り当て]** で、 **[オン]**  >  **[保存]** を選びます。 確認を求めるメッセージが表示されたら、 **[はい]** を選択します。

   ![Azure portal の Standard ロジック アプリの [ID] ペインおよび [システム割り当て] タブの [オン] と [保存] が選ばれている状態を示すスクリーンショット。](./media/create-managed-service-identity/enable-system-assigned-identity-standard.png)

   これで、ロジック アプリのリソースでシステム割り当て ID を使用できるようになりました。これは Azure AD に登録され、オブジェクト ID で表されます。

   ![Standard ロジック アプリの [ID] ペインと、システム割り当て ID のオブジェクト ID を示すスクリーンショット。](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **オブジェクト (プリンシパル) ID** | <*identity-resource-ID*> | Azure AD テナント内のロジック アプリのシステム割り当て ID を表すグローバル一意識別子 (GUID)。 |
   ||||

1. 次に、このトピックの後半にある、[ID にリソースへのアクセス権を付与する手順](#access-other-resources)に従います。

---

<a name="system-assigned-template"></a>
<a name="template-system-logic-app"></a>

## <a name="enable-system-assigned-identity-in-an-arm-template"></a>ARM テンプレートでシステム割り当て ID を有効にする

ロジック アプリなどの Azure リソースの作成とデプロイを自動化するために、[ARM テンプレート](logic-apps-azure-resource-manager-templates-overview.md)を使用できます。 テンプレートでロジック アプリのリソースのシステム割り当てマネージド ID を有効にするには、次のように、テンプレートのロジック アプリのリソース定義に `identity` オブジェクトと `type` 子プロパティを追加します。

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {},
   <...>
}
```

### <a name="standard"></a>[Standard](#tab/standard)

```json
{
   "apiVersion": "2021-01-15",
   "type": "Microsoft.Web/sites",
   "name": "[variables('sites_<logic-app-resource-name>_name')]",
   "location": "[resourceGroup().location]",
   "kind": "functionapp,workflowapp",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {},
   <...>
}
```

---

Azure によってロジック アプリのリソース定義が作成されると、`identity` オブジェクトでこれらの他のプロパティが取得されます。

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

<<<<<<< HEAD
<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>ユーザー割り当て ID を有効にする

ロジック アプリにユーザー割り当てのマネージド ID を設定するには、最初にその ID を別のスタンドアロン Azure リソースとして作成する必要があります。 次に、使用できるオプションを示します。

* [Azure Portal](#azure-portal-user-identity)
* [Azure Resource Manager テンプレート](#template-user-identity)
* Azure PowerShell
  * [ユーザー割り当て ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [ロールの割り当てを追加する](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [ユーザー割り当て ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [ロールの割り当てを追加する](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST API
  * [ユーザー割り当て ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [ロールの割り当てを追加する](../role-based-access-control/role-assignments-rest.md)

=======
>>>>>>> repo_sync_working_branch
<a name="azure-portal-user-identity"></a>
<a name="user-assigned-azure-portal"></a>

## <a name="create-user-assigned-identity-in-the-azure-portal-consumption-only"></a>Azure portal でユーザー割り当て ID を作成する (従量課金のみ)

**ロジック アプリ (従量課金)** のリソースでユーザー割り当て ID を有効にするには、まずその ID を別の Azure リソースとして作成する必要があります。

1. [Azure portal](https://portal.azure.com) の検索ボックスに、「`managed identities`」と入力します。 **[マネージド ID]** を選びます。

   ![Azure portal で [マネージド ID] が選ばれている状態を示すスクリーンショット。](./media/create-managed-service-identity/find-select-managed-identities.png)

1. **[マネージド ID]** ペインで、 **[作成]** を選びます。

   ![[マネージド ID] ペインで [作成] が選ばれている状態を示すスクリーンショット。](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. 次の例のようにマネージド ID に関する情報を指定し、 **[確認と作成]** を選択します。次に例を示します。

   ![[ユーザー割り当てマネージド ID の作成] ペインにマネージド ID の詳細が表示された状態を示すスクリーンショット。](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **サブスクリプション** | はい | <*Azure サブスクリプション名*> | 使用する Azure サブスクリプションの名前。 |
   | **リソース グループ** | はい | <*Azure-resource-group-name*> | 使用する Azure リソース グループの名前。 新しいグループを作成するか、既存のグループを選択します。 この例では、`fabrikam-managed-identities-RG` という名前の新しいグループを作成します。 |
   | **[リージョン]** | はい | <*Azure-region*> | リソースに関する情報の保存先となる Azure リージョン。 この例では **米国西部** を使用します。 |
   | **名前** | はい | <*user-assigned-identity-name*> | ユーザー割り当て ID に付与する名前。 この例では、`Fabrikam-user-assigned-identity` を使用します。 |
   |||||

   これらの情報の検証後、Azure によってマネージド ID が作成されます。 これで、ユーザー割り当て ID をロジック アプリのリソースに追加できます。ユーザー割り当て ID は 1 つのみ持つことができます。

1. Azure portal で、ロジック アプリ リソースを開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[ID]** を選択します。

1. **[ID]** ペインで、 **[ユーザー割り当て]**  >  **[オン]** を選びます。

   ![[ID] ペインで [追加] が選ばれた状態を示すスクリーンショット。](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. **[ユーザー割り当てマネージド ID の追加]** ペインで、こちらの手順を実行します。

   1. **[サブスクリプション]** の一覧でまだ選ばれていない場合、使用する Azure サブスクリプションを選びます。

   1. そのサブスクリプションのすべてのマネージド ID を示す一覧から、目的のユーザー割り当て ID を選びます。 一覧をフィルター処理するには、 **[ユーザー割り当て済みマネージド ID]** 検索ボックスに、ID またはリソース グループの名前を入力します。

      ![ユーザー割り当て ID が選ばれた状態を示すスクリーンショット。](./media/create-managed-service-identity/select-user-assigned-identity.png)

   1. 終了したら、 **[追加]** を選択します。

      > [!NOTE]
      > マネージド ID を 1 つだけ持つことができるというエラーが表示される場合、ロジック アプリは既にシステム割り当て ID に関連付けられています。 ユーザー割り当て ID を追加できるようにするには、まず、システム割り当て ID を無効にする必要があります。

   これで、ロジック アプリがユーザー割り当てのマネージド ID に関連付けられました。

   ![ユーザー割り当て ID とロジック アプリのリソースの関連付けを示すスクリーンショット。](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. 次に、このトピックの後半にある、[ID にリソースへのアクセス権を付与する手順](#access-other-resources)に従います。

<a name="template-user-identity"></a>

## <a name="create-user-assigned-identity-in-an-arm-template-consumption-only"></a>ARM テンプレートでユーザー割り当て ID を作成する (従量課金のみ)

ロジック アプリなどの Azure リソースの作成とデプロイを自動化するために、[ARM テンプレート](logic-apps-azure-resource-manager-templates-overview.md)を使用できます。これは[認証のためのユーザー割り当て ID](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) をサポートしています。 テンプレートの `resources` セクションでは、ロジック アプリのリソース定義に次の項目が必要です。

* `type` プロパティが `UserAssigned` に設定されている `identity` オブジェクト

* ユーザー割り当てリソースと名前が指定されている子 `userAssignedIdentities` オブジェクト

この例では、HTTP PUT 要求のロジック アプリのリソース定義を示しています。パラメーター化されていない `identity` オブジェクトが含まれています。 PUT 要求への応答と後続の GET 操作にも、この `identity` オブジェクトが含まれています。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

テンプレートにマネージド ID のリソース定義も含まれている場合は、`identity` オブジェクトをパラメーター化できます。 この例では、子 `userAssignedIdentities` オブジェクトが、テンプレートの `variables` セクションで定義した `userAssignedIdentity` 変数を参照する方法を示しています。 この変数は、ユーザー割り当て ID のリソース ID を参照しています。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>ID にリソースへのアクセス権を付与する

ロジック アプリのマネージド ID を認証に使用するには、その ID を使用する予定の Azure リソースで、Azure のロールベースのアクセス制御 (Azure RBAC) を使用して自分の ID のアクセスを設定する必要があります。 このセクションの手順では、[Azure portal](#azure-portal-assign-access) と [Azure Resource Manager テンプレート (ARM テンプレート)](../role-based-access-control/role-assignments-template.md) を使用して、Azure リソースの該当する ID に適切なロールを割り当てる方法について説明します。 Azure PowerShell、Azure CLI、Azure REST API については、次のドキュメントを参照してください。

| ツール | ドキュメント |
|------|---------------|
| Azure PowerShell | [ロールの割り当てを追加する](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) |
| Azure CLI | [ロールの割り当てを追加する](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md) |
| Azure REST API | [ロールの割り当てを追加する](../role-based-access-control/role-assignments-rest.md) |
|||

<a name="azure-portal-assign-access"></a>

### <a name="assign-managed-identity-role-based-access-in-the-azure-portal"></a>Azure portal でマネージド ID のロールベースのアクセスを割り当てる

認証にマネージド ID を使用する予定の Azure リソースで、ターゲット リソースにアクセスできるロールにその ID を割り当てる必要があります。 このタスクに関する一般的な情報については、[Azure RBAC を使用した別のリソースへのマネージド ID アクセスの割り当て](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)に関するページを参照してください。

> [!NOTE]
> あるマネージド ID が同じサブスクリプション内のある Azure リソースにアクセスできる場合、その ID はそのリソースにのみアクセスできます。 ただし、マネージド ID をサポートする一部のトリガーとアクションでは、ターゲット リソースが含まれている Azure リソース グループを最初に選ぶ必要があります。 ID にそのリソース グループ レベルでのアクセス権がない場合は、ターゲット リソースへのアクセス権があっても、そのグループ内のリソースは表示されません。
>
> この動作に対応するには、リソースだけではなく、リソース グループにも ID アクセス権を付与する必要があります。 同様に、ターゲット リソースを選ぶ前にサブスクリプションを選ぶ必要がある場合は、そのサブスクリプションへの ID アクセス権を付与する必要があります。

1. [Azure portal](https://portal.azure.com)で、ID を使用する予定のリソースを開きます。

1. リソースのメニューで **[アクセス制御 (IAM)]**  >  **[追加]**  >  **[ロールの割り当ての追加]** を選択します。

   > [!NOTE]
   > **[ロールの割り当ての追加]** オプションが無効な場合は、ロールを割り当てるためのアクセス許可がありません。 詳細については、[Azure AD の組み込みロール](../active-directory/roles/permissions-reference.md)に関するページを確認してください。

1. ここで、必要なロールをマネージド ID に割り当てます。 **[ロール]** タブで、現在のリソースへの必要なアクセス権を自分の ID に付与するロールを割り当てます。

   この例では、**ストレージ BLOB データ共同作成者** という名前のロールを割り当てます。これには、Azure Storage コンテナー内の BLOB への書き込みアクセスが含まれます。 特定のストレージ コンテナー ロールの詳細については、[Azure Storage コンテナー内の BLOB にアクセスできるロール](../storage/blobs/authorize-access-azure-active-directory.md#assign-azure-roles-for-access-rights)に関するページを確認してください。

1. 次に、ロールを割り当てるマネージド ID を選択します。 **[アクセスの割り当て先]** で、 **[マネージド ID]**  >  **[メンバーを追加する]** を選択します。

1. マネージド ID の種類に基づいて、次の値を選択または指定します。

   | 型 | Azure サービス インスタンス | サブスクリプション | メンバー |
   |------|------------------------|--------------|--------|
   | **システム割り当て** | **ロジック アプリ** | <*Azure サブスクリプション名*> | <*ロジック アプリ名*> |
   | **ユーザー割り当て** (従量課金のみ) | 該当なし | <*Azure サブスクリプション名*> | <*ユーザー割り当て ID 名*> |
   |||||

   ロールの割り当ての詳細については、ドキュメント「[Azure portal を使用したロールの割り当て](../role-based-access-control/role-assignments-portal.md)」を参照してください。

1. ID のアクセスの設定が完了したら、その ID を使用して、[マネージド ID をサポートするトリガーとアクションのアクセスを認証](#authenticate-access-with-identity)できます。

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>マネージド ID を利用してアクセスを認証する

[ロジック アプリのリソースに対してマネージド ID を有効](#azure-portal-system-logic-app)にし、[その ID にターゲット リソースまたはエンティティへのアクセス権を付与](#access-other-resources)したら、その ID を[マネージド ID がサポートされているトリガーとアクション](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)で使用できます。

> [!IMPORTANT]
> システム割り当て ID を使用する Azure 関数がある場合は、先に [Azure Functions の認証を有効](logic-apps-azure-functions.md#enable-authentication-for-functions)にします。

次の手順では、Azure portal を通じて、トリガーまたはアクションでマネージド ID を使用する方法を示します。 トリガーまたはアクションの基になる JSON 定義でマネージド ID を指定する方法については、「[マネージド ID 認証](logic-apps-securing-a-logic-app.md#managed-identity-authentication)」をご覧ください。

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ リソースを開きます。

1. まだ行っていない場合は、[マネージド ID がサポートされているトリガーまたはアクション](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)を追加します。

   > [!NOTE]
   > すべてのトリガーとアクションで認証の種類を追加できるわけではありません。 詳細については、「[認証がサポートされているトリガーおよびアクションの認証の種類](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)」を参照してください。

1. 追加したトリガーまたはアクションで、次の手順に従います。

   * **マネージド ID 認証をサポートする組み込み操作**

     1. **[新しいパラメーターの追加]** の一覧で、"**認証**" プロパティがまだ表示されていない場合はこのプロパティを追加します。

        ![[新しいパラメーターの追加] の一覧が表示され、[認証] が選ばれている組み込みアクションの例を示すスクリーンショット (従量課金)。](./media/create-managed-service-identity/built-in-authentication-consumption.png)

     1. **[認証タイプ]** 一覧から、 **[マネージド ID]** を選びます。

        ![[認証タイプ] の一覧が表示され、[マネージド ID] が選ばれている組み込みアクションの例を示すスクリーンショット (従量課金)。](./media/create-managed-service-identity/built-in-managed-identity-consumption.png)

     詳細については、「[例: マネージド ID を使用して組み込みのトリガーまたはアクションを認証する](#authenticate-built-in-managed-identity)」を参照してください。

   * **マネージド ID 認証をサポートするマネージド コネクタの操作** (プレビュー)

     1. テナントの選択ページで、 **[マネージド ID を使用して接続する]** を選びます。

        ![Azure Resource Manager アクションと [マネージド ID を使用して接続する] が選ばれた状態を示すスクリーンショット (従量課金)。](./media/create-managed-service-identity/select-connect-managed-identity-consumption.png)

     1. 次のページで、 **[接続名]** に、接続に使用する名前を入力します。

     1. 認証タイプには、マネージド コネクタに基づいて次のいずれかのオプションを選びます。

        * **[Single-authentication]\(単一認証\)** : これらのコネクタは、認証の種類を 1 つだけサポートします。 **[マネージド ID]** の一覧から、現在有効なマネージド ID を選びます (まだ選択されていない場合)。次に、 **[作成]** を選びます。次に例を示します。

          ![[接続名] のページで、選ばれた単一のマネージド ID を示すスクリーンショット (従量課金)。](./media/create-managed-service-identity/single-system-identity-consumption.png)

        * **[Multi-authentication]\(マルチ認証\)** : これらのコネクタは、複数の認証の種類をサポートします。 **[認証タイプ]** 一覧から、 **[Logic Apps Managed Identity]\(Logic Apps マネージド ID\)**  >  **[作成]** を選びます。次に例を示します。

          ![[接続名] ページで、選ばれた [Logic Apps Managed Identity]\(Logic Apps のマネージド ID\) を示すスクリーンショット (従量課金)。](./media/create-managed-service-identity/multi-system-identity-consumption.png)

        詳細については、「[例: マネージド ID を使用してマネージドコネクタのトリガーまたはアクションを認証する](#authenticate-managed-connector-managed-identity)」を参照してください。

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ リソースを開きます。

1. まだ行っていない場合は、[マネージド ID がサポートされているトリガーまたはアクション](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)を追加します。

   > [!NOTE]
   > すべてのトリガーとアクションで認証の種類を追加できるわけではありません。 詳細については、「[認証がサポートされているトリガーおよびアクションの認証の種類](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)」を参照してください。

1. 追加したトリガーまたはアクションで、次の手順に従います。

   * **マネージド ID 認証をサポートする組み込み操作**

     1. **[新しいパラメーターの追加]** の一覧で、"**認証**" プロパティがまだ表示されていない場合はこのプロパティを追加します。

        ![[新しいパラメーターの追加] の一覧が表示され、[認証] が選ばれている組み込みアクションの例を示すスクリーンショット (Standard の場合)。](./media/create-managed-service-identity/built-in-authentication-standard.png)

     1. **[認証タイプ]** 一覧から、 **[マネージド ID]** を選びます。

        ![[認証タイプ] の一覧が表示され、[マネージド ID] が選ばれている組み込みアクションの例を示すスクリーンショット (Standard)。](./media/create-managed-service-identity/built-in-managed-identity-standard.png)

     詳細については、「[例: マネージド ID を使用して組み込みのトリガーまたはアクションを認証する](#authenticate-built-in-managed-identity)」を参照してください。

   * **マネージド ID 認証をサポートするマネージド コネクタの操作** (プレビュー)

     1. テナントの選択ページで、 **[マネージド ID を使用して接続する]** を選びます。

        ![Azure Resource Manager アクションと [マネージド ID を使用して接続する] が選ばれた状態を示すスクリーンショット (Standard)。](./media/create-managed-service-identity/select-connect-managed-identity-standard.png)

     1. 次のページで、 **[接続名]** に、接続に使用する名前を入力します。

     1. 認証タイプには、マネージド コネクタに基づいて次のいずれかのオプションを選びます。

        * **[Single-authentication]\(単一認証\)** : これらのコネクタは、認証の種類を 1 つだけサポートします。 **[マネージド ID]** の一覧から、現在有効なマネージド ID を選びます (まだ選択されていない場合)。次に、 **[作成]** を選びます。次に例を示します。

          ![[接続名] のページで、選ばれた単一のマネージド ID を示すスクリーンショット (Standard)。](./media/create-managed-service-identity/single-system-identity-standard.png)

        * **[Multi-authentication]\(マルチ認証\)** : これらのコネクタは、複数の認証の種類をサポートします。 **[認証タイプ]** 一覧から、 **[Logic Apps Managed Identity]\(Logic Apps マネージド ID\)**  >  **[作成]** を選びます。次に例を示します。

          ![[接続名] ページで、選ばれた [Logic Apps Managed Identity]\(Logic Apps のマネージド ID\) を示すスクリーンショット (Standard)。](./media/create-managed-service-identity/multi-system-identity-standard.png)

        詳細については、「[例: マネージド ID を使用してマネージドコネクタのトリガーまたはアクションを認証する](#authenticate-managed-connector-managed-identity)」を参照してください。

---

<a name="authenticate-built-in-managed-identity"></a>

## <a name="example-authenticate-built-in-trigger-or-action-with-a-managed-identity"></a>例:マネージド ID を使用して組み込みのトリガーまたはアクションを認証する

組み込みの HTTP トリガーまたはアクションでは、ロジック アプリのリソースに対して有効にしたシステム割り当て ID を使用できます。 一般に、HTTP トリガーまたはアクションでは、次のプロパティを使用して、アクセスするリソースまたはエンティティを指定します。

| プロパティ | 必須 | 説明 |
|----------|----------|-------------|
| **方法** | はい | 実行する操作によって使用される HTTP メソッド |
| **URI** | はい | ターゲットの Azure リソースまたはエンティティにアクセスするためのエンドポイント URL。 URI 構文には、通常、Azure リソースまたはサービスの[リソース ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) が含まれています。 |
| **ヘッダー** | いいえ | コンテンツ タイプなど、送信要求に含める必要がある (または含めたい) ヘッダーの値 |
| **クエリ** | いいえ | 特定の操作のパラメーターや実行する操作の API バージョンなど、要求に含める必要がある (または含めたい) クエリ パラメーター |
| **認証** | はい | ターゲット リソースまたはエンティティへのアクセスの認証に使用する認証の種類 |
||||

具体的な例として、以前に ID 用のアクセスを設定した Azure Storage アカウントの BLOB に対して [Snapshot Blob 操作](/rest/api/storageservices/snapshot-blob)を実行するとします。 しかし、[Azure Blob Storage コネクタ](/connectors/azureblob/)では現在、この操作が提供されていません。 代わりに、[HTTP アクション](logic-apps-workflow-actions-triggers.md#http-action)または別の [BLOB サービス REST API 操作](/rest/api/storageservices/operations-on-blobs)を使用して、この操作を実行できます。

> [!IMPORTANT]
> HTTP 要求とマネージド ID を使用してファイアウォールの背後にある Azure Storage アカウントにアクセスするには、[信頼された Microsoft サービスによるアクセスを許可する例外](../connectors/connectors-create-api-azureblobstorage.md#access-blob-storage-with-managed-identities)を使用してストレージ アカウントを設定する必要もあります。

[Snapshot Blob 操作](/rest/api/storageservices/snapshot-blob)を実行するには、HTTP アクションで次のプロパティを指定します。

| プロパティ | 必須 | 値の例 | 説明 |
|----------|----------|---------------|-------------|
| **方法** | はい | `PUT`| Snapshot Blob 操作で使用する HTTP メソッド |
| **URI** | はい | `https://<storage-account-name>/<folder-name>/{name}` | この構文を使用する Azure Global (パブリック) 環境内の Azure Blob Storage ファイルのリソース ID |
| **ヘッダー** | Azure Storage の場合 | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r')}` | Azure Storage の操作には、`x-ms-blob-type`、`x-ms-version`、および `x-ms-date` ヘッダー値が必要です。 <p><p>**重要**:Azure Storage の発信 HTTP トリガーおよびアクションの要求では、ヘッダーに `x-ms-version` プロパティと実行する操作の API バージョンが必要です。 `x-ms-date` は現在の日付である必要があります。 それ以外の場合、ワークフローは `403 FORBIDDEN` エラーで失敗します。 現在の日付を必要な形式で取得するには、例の値で式を使用します。 <p>詳細については、次のトピックをご覧ください。 <p><p>- [要求ヘッダー - Snapshot Blob](/rest/api/storageservices/snapshot-blob#request) <br>- [Azure Storage サービスのバージョン管理](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
| **クエリ** | スナップショット BLOB 操作の場合のみ | `comp` = `snapshot` | 操作のクエリ パラメーターの名前と値。 |
|||||

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

次の例では、BLOB のスナップショット操作に使用する、前述のすべてのプロパティ値を使用したサンプル HTTP アクションを示します。

![リソースにアクセスするために、従量課金のロジック アプリ ワークフローと HTTP アクションが設定された Azure portal を示すスクリーンショット。](./media/create-managed-service-identity/http-action-example.png)

1. HTTP アクションを追加した後、HTTP アクションに **Authentication** プロパティを追加します。 **[新しいパラメーターの追加]** 一覧で **[認証]** を選択します。

   ![従量課金ワークフローの HTTP アクションで [新しいパラメーターの追加] の一覧が開き、"認証" プロパティが選ばれた状態を示すスクリーンショット。](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > すべてのトリガーとアクションで認証の種類を追加できるわけではありません。 詳細については、「[認証がサポートされているトリガーおよびアクションの認証の種類](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)」を参照してください。

1. **[認証タイプ]** 一覧から、 **[マネージド ID]** を選びます。

   ![従量課金ワークフローの HTTP アクションの "認証" プロパティで [マネージド ID] が選ばれた状態を示すスクリーンショット。](./media/create-managed-service-identity/select-managed-identity.png)

1. マネージド ID の一覧から、シナリオに基づいて使用可能なオプションを選択します。

   * システム割り当て ID を設定した場合は、 **[システム割り当てマネージド ID]** を選びます (まだ選択されていない場合)。

     ![従量課金ワークフローの HTTP アクションの "マネージド ID" プロパティで [システム割り当てマネージド ID] の値が選ばれた状態を示すスクリーンショット。](./media/create-managed-service-identity/select-system-assigned-identity.png)

   * ユーザー割り当て ID を設定した場合は、その ID を選択します (まだ選択されていない場合)。

     ![従量課金ワークフローの HTTP アクションの "マネージド ID" プロパティでユーザー割り当て ID が選ばれた状態を示すスクリーンショット。](./media/create-managed-service-identity/select-user-assigned-identity-action.png)

   この例では、 **[システム割り当てマネージド ID]** を使用して先に進みます。

1. 一部のトリガーとアクションでは、ターゲット リソース ID を設定するために **[対象ユーザー]** プロパティも表示されます。 **[対象ユーザー]** プロパティは [ターゲット リソースまたはサービスのリソース ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) に設定します。 そうしないと、既定では、 **[対象ユーザー]** プロパティに Azure Resource Manager のリソース ID である `https://management.azure.com/` リソース ID が使用されます。
  
    たとえば、[グローバル Azure クラウド内の Key Vault リソース](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-key-vault)へのアクセスを認証するには、**Audience** プロパティを、"*正確に*" このリソース ID `https://vault.azure.net` のように設定する必要があります。 この特定のリソース ID には、末尾のスラッシュが含まれて "*いない*" ことに注意してください。 実際、末尾にスラッシュを含めると、`400 Bad Request` エラーまたは `401 Unauthorized` エラーが発生する可能性があります。

   > [!IMPORTANT]
   > ターゲット リソース ID は、必要な末尾のスラッシュも含めて、Azure Active Directory (AD) で予想される値と *正確に一致* するようにします。 たとえば、すべての Azure Blob Storage アカウントのリソース ID には、末尾のスラッシュが必要です。 ただし、特定のストレージ アカウントのリソース ID については、末尾のスラッシュは必要ありません。 「[Azure AD 認証をサポートしている Azure サービスのリソース ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)」をご覧ください。

   この例では、 **[対象ユーザー]** プロパティを `https://storage.azure.com/` に設定して、認証に使用されるアクセス トークンをすべてのストレージ アカウントに対して有効にしています。 ただし、特定のストレージ アカウントに対してルート サービス URL (`https://<your-storage-account>.blob.core.windows.net`) を指定することもできます。

   ![従量課金ワークフローの HTTP アクションの Audience (対象ユーザー) プロパティがターゲット リソース ID に設定された状態を示すスクリーンショット。](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Azure Storage の Azure AD を使用してアクセスを承認する方法の詳細については、次のドキュメントを参照してください。

   * [Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを承認する](../storage/blobs/authorize-access-azure-active-directory.md)

   * [Azure Active Directory を使用して Azure Storage へのアクセスを承認する](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. 必要な方法でワークフローの構築を続行します。

### <a name="standard"></a>[Standard](#tab/standard)

次の例では、BLOB のスナップショット操作に使用する、前述のすべてのプロパティ値を使用したサンプル HTTP アクションを示します。

![リソースにアクセスするために、Standard のロジック アプリ ワークフローと HTTP アクションが設定された Azure portal を示すスクリーンショット。](./media/create-managed-service-identity/http-action-example-standard.png)

1. HTTP アクションを追加した後、HTTP アクションに **Authentication** プロパティを追加します。 **[新しいパラメーターの追加]** 一覧で **[認証]** を選択します。

   ![Standard ワークフローの HTTP アクションで [新しいパラメーターの追加] の一覧が開き、"認証" プロパティが選ばれた状態を示すスクリーンショット。](./media/create-managed-service-identity/add-authentication-property-standard.png)

   > [!NOTE]
   > すべてのトリガーとアクションで認証の種類を追加できるわけではありません。 詳細については、「[認証がサポートされているトリガーおよびアクションの認証の種類](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)」を参照してください。

1. **[認証タイプ]** 一覧から、 **[マネージド ID]** を選びます。

   ![Standard ワークフローの HTTP アクションの "認証" プロパティで [マネージド ID] が選ばれた状態を示すスクリーンショット。](./media/create-managed-service-identity/select-managed-identity-standard.png)

1. マネージド ID の一覧で、 **[システム割り当てマネージド ID]** を選びます (まだ選択されていない場合)。

     ![Standard ワークフローの HTTP アクションの [マネージド ID] の一覧で [システム割り当てマネージド ID] が選ばれた状態を示すスクリーンショット。](./media/create-managed-service-identity/select-system-assigned-identity-standard.png)

1. 一部のトリガーとアクションでは、ターゲット リソース ID を設定するために **[対象ユーザー]** プロパティも表示されます。 **[対象ユーザー]** プロパティは [ターゲット リソースまたはサービスのリソース ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) に設定します。 そうしないと、既定では、 **[対象ユーザー]** プロパティに Azure Resource Manager のリソース ID である `https://management.azure.com/` リソース ID が使用されます。
  
    たとえば、[グローバル Azure クラウド内の Key Vault リソース](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-key-vault)へのアクセスを認証するには、**Audience** プロパティを、"*正確に*" このリソース ID `https://vault.azure.net` のように設定する必要があります。 この特定のリソース ID には、末尾のスラッシュが含まれて "*いない*" ことに注意してください。 実際、末尾にスラッシュを含めると、`400 Bad Request` エラーまたは `401 Unauthorized` エラーが発生する可能性があります。

   > [!IMPORTANT]
   > ターゲット リソース ID は、必要な末尾のスラッシュも含めて、Azure Active Directory (AD) で予想される値と *正確に一致* するようにします。 たとえば、すべての Azure Blob Storage アカウントのリソース ID には、末尾のスラッシュが必要です。 ただし、特定のストレージ アカウントのリソース ID については、末尾のスラッシュは必要ありません。 「[Azure AD 認証をサポートしている Azure サービスのリソース ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)」をご覧ください。

   この例では、 **[対象ユーザー]** プロパティを `https://storage.azure.com/` に設定して、認証に使用されるアクセス トークンをすべてのストレージ アカウントに対して有効にしています。 ただし、特定のストレージ アカウントに対してルート サービス URL (`https://<your-storage-account>.blob.core.windows.net`) を指定することもできます。

   ![[対象ユーザー] プロパティをターゲット リソース ID に設定する](./media/create-managed-service-identity/specify-audience-url-target-resource-standard.png)

   Azure Storage の Azure AD を使用してアクセスを承認する方法の詳細については、次のドキュメントを参照してください。

   * [Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを承認する](../storage/blobs/authorize-access-azure-active-directory.md)
   * [Azure Active Directory を使用して Azure Storage へのアクセスを承認する](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. 必要な方法でワークフローの構築を続行します。

---

<a name="authenticate-managed-connector-managed-identity"></a>

## <a name="example-authenticate-managed-connector-trigger-or-action-with-a-managed-identity"></a>例:マネージド ID を使用してマネージド コネクタのトリガーまたはアクションを認証する

Azure Resource Manager のマネージド コネクタには、**リソースの読み取り** というアクションがあり、ロジック アプリのリソースに対して有効にしたマネージド ID を使用できます。 この例では、システム割り当てマネージド ID を使用する方法を示します。

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. ワークフローにアクションを追加した後、Azure AD のテナントを選び、 **[マネージド ID を使用して接続する (プレビュ)]** を選びます。

   ![Azure Resource Manager アクションと [マネージド ID を使用して接続する] が選ばれた状態を示すスクリーンショット。](./media/create-managed-service-identity/select-connect-managed-identity-consumption.png)

1. 接続名のページで、接続の名前を指定し、使用するマネージド ID を選びます。

   Azure Resource Manager アクションは単一認証アクションであるため、[接続情報] ペインに表示される **[マネージド ID]** の一覧では、ロジックアプリのリソースで現在有効になっているマネージド ID が自動的に選ばれます。 システム割り当てマネージド ID を有効にした場合、 **[マネージド ID]** の一覧では **[システム割り当てマネージド ID]** が選ばれます。 代わりにユーザー割り当てマネージド ID を有効にした場合は、その ID が代わりに選ばれます。

   Azure Blob Storage など、マルチ認証のトリガーまたはアクションを使用している場合、[接続情報] ペインには、 **[認証タイプ]** の一覧が表示され、これには他の認証の種類に加えて **[Logic Apps Managed Identity]\(Logic Apps のマネージド ID\)** オプションが含まれています。

   この例では、 **[システム割り当てマネージド ID]** だけを選ぶことができます。

   ![接続名が入力され、[システム割り当てマネージド ID] が選ばれている Azure Resource Manager アクションを示すスクリーンショット。](./media/create-managed-service-identity/single-system-identity-consumption.png)

   > [!NOTE]
   > 接続を作成しようとしたとき、接続を変更したとき、またはマネージド ID がまだ存在する状態で削除されたときにマネージド ID が有効になっていない場合、ID を有効にし、ターゲット リソースへのアクセスを許可する必要があるというエラーが表示されます。

1. 準備ができたら、 **[作成]** を選択します。

1. 接続が正常に作成されると、デザイナーはマネージド ID 認証を使用して動的な値、コンテンツ、またはスキーマを取得できます。

1. 必要な方法でワークフローの構築を続行します。

### <a name="standard"></a>[Standard](#tab/standard)

1. ワークフローにアクションを追加した後、アクションの **[接続の作成]** ペインで Azure AD のテナントを選び、 **[マネージド ID を使用して接続する (プレビュー)]** を選びます。

   ![Azure Resource Manager アクションと [マネージド ID を使用して接続する] が選ばれた状態を示すスクリーンショット。](./media/create-managed-service-identity/select-connect-managed-identity-standard.png)

1. 接続名ページで、接続の名前を入力します。

   Azure Resource Manager アクションは単一認証アクションであるため、[接続情報] ペインに表示される **[マネージド ID]** の一覧では、ロジックアプリのリソースで現在有効になっているマネージド ID が自動的に選ばれます。 システム割り当てマネージド ID を有効にした場合、 **[マネージド ID]** の一覧では **[システム割り当てマネージド ID]** が選ばれます。 代わりにユーザー割り当てマネージド ID を有効にした場合は、その ID が代わりに選ばれます。

   この例では、 **[システム割り当てマネージド ID]** だけを選ぶことができます。

   ![接続名が入力され、[システム割り当てマネージド ID] が選ばれている Azure Resource Manager アクションを示すスクリーンショット。](./media/create-managed-service-identity/single-system-identity-standard.png)

   Azure Blob Storage など、マルチ認証のトリガーまたはアクションを使用している場合、[接続情報] ペインには、 **[認証タイプ]** の一覧が表示され、これには他の認証の種類に加えて **[マネージド ID]** オプションが含まれています。

   > [!NOTE]
   > 接続を作成しようとしたとき、接続を変更したとき、またはマネージド ID がまだ存在する状態で削除されたときにマネージド ID が有効になっていない場合、ID を有効にし、ターゲット リソースへのアクセスを許可する必要があるというエラーが表示されます。

1. 準備ができたら、 **[作成]** を選択します。

1. 接続が正常に作成されると、デザイナーはマネージド ID 認証を使用して動的な値、コンテンツ、またはスキーマを取得できます。

1. 必要な方法でワークフローの構築を続行します。

---

<a name="logic-app-resource-definition-connection-managed-identity"></a>

## <a name="logic-app-resource-definition-and-connections-that-use-a-managed-identity-consumption"></a>マネージド ID を使用するロジック アプリのリソース定義と接続 (従量課金)

マネージド ID を有効にして、使用する接続は、マネージ ID を使用する場合のみ動作する特殊な接続の種類です。 実行時には、ロジック アプリのリソースで有効になっているマネージド ID がその接続で使用されます。 実行時には、ロジック アプリのワークフロー内のすべてのマネージド コネクタのトリガーおよびアクションでマネージド ID が使用されるように設定されているかどうかと、必要なすべてのアクセス許可が、トリガーとアクションで指定されたターゲット リソースにアクセスするためにマネージド ID を使用するように設定されているかが確認されます。 成功した場合、マネージド ID に関連付けられている Azure AD トークンが取得され、その ID を使用してターゲット リソースへのアクセスが認証され、トリガーとアクション内の構成済み操作が実行されます。

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

**Logic App (従量課金プラン)** のリソースでは、接続構成は、ロジック アプリのリソース定義の `parameters` オブジェクトに保存されます。これには、`$connections` オブジェクトが含まれ、この中に、接続のリソース ID へのポインターと、ユーザー割り当て ID が有効になっている場合はその ID のリソース ID が入っています。

この例は、ロジック アプリでシステム割り当てマネージド ID を有効にした場合の構成内容を示しています。

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

この例は、ロジック アプリでユーザー割り当てマネージド ID を有効にした場合の構成内容を示しています。

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "identity": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/microsoft.managedidentity/userassignedidentities/{managed-identity-name}",
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

### <a name="standard"></a>[Standard](#tab/standard)

**Logic App (Standard)** のリソースでは、接続構成がロジック アプリのリソースまたはプロジェクトの `connections.json` ファイルに保存されます。これには、ワークフローで使用される各マネージド コネクタの接続構成情報を格納した `managedApiConnections` JSON オブジェクトが含まれています。 たとえば、ユーザー割り当て ID が有効になっている場合、この接続情報には、リソース ID などのマネージド ID のプロパティと共に、接続のリソース ID へのポインターが含まれます。

この例は、ロジック アプリでユーザー割り当てマネージド ID を有効にした場合の構成内容を示しています。

```json
{
    "managedApiConnections": {
        "<connector-name>": {
            "api": {
                "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{region}/managedApis/<connector-name>"
            },
            "connection": {
                "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/connections/<connection-name>"
            },
            "connectionRuntimeUrl": <connection-URL>,
            "authentication": { // Authentication with APIHub
                "type": "ManagedServiceIdentity"
            },
            "connectionProperties": {
                "authentication": { //Authentication with the target resource
                    "type": "ManagedServiceIdentity",
                    "identity": "<user-assigned-identity>", // Optional
                    "audience": "<resource-URL>"
                }
            }
        }
    }
}
```

---

<a name="arm-templates-connection-resource-managed-identity"></a>

## <a name="arm-template-for-managed-connections-and-managed-identities-consumption"></a>マネージド接続とマネージド ID の ARM テンプレート (従量課金プラン)

ARM テンプレートを使用したデプロイを自動化していて、ワークフローに含まれるマネージド コネクタのトリガーまたはアクションがマネージド ID を使用する場合は、基になる接続リソース定義に、`parameterValueType` プロパティがプロパティ値の `Alternative` と共に含まれていることを確認します。 そうでない場合、ARM のデプロイで、認証にマネージド ID を使用する接続が設定されず、接続がロジック アプリのワークフローで機能しません。 この要件は、[ **[Connect with managed identity]** (マネージド ID で接続) オプション](#authenticate-managed-connector-managed-identity)を選択した [特定のマネージ コネクタのトリガーとアクション](#triggers-actions-managed-identity)にのみ適用されます。

たとえば、次に示すのは、プロパティ値が `Alternative` に設定されている `parameterValueType` プロパティが定義に含まれているマネージド ID を使用する、Azure Automation アクションの基になる接続リソース定義です。

```json
{
    "type": "Microsoft.Web/connections",
    "name": "[variables('automationAccountApiConnectionName')]",
    "apiVersion": "2016-06-01",
    "location": "[parameters('location')]",
    "kind": "V1",
    "properties": {
        "api": {
            "id": "[subscriptionResourceId('Microsoft.Web/locations/managedApis', parameters('location'), 'azureautomation')]"
        },
        "customParameterValues": {},
        "displayName": "[variables('automationAccountApiConnectionName')]",
        "parameterValueType": "Alternative"
    }
},
```

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>マネージド ID を無効にする

認証にマネージド ID の使用することを止めるには、まず、[ターゲット リソースに対する ID のアクセス権](#disable-identity-target-resource)を削除します。 次に、ロジック アプリのリソースで、[システム割り当て ID をオフにするか、ユーザー割り当て ID を削除](#disable-identity-logic-app)します。

ロジック アプリのリソースでマネージド ID を無効にすると、その ID がアクセス権を持っていた Azure リソースへのアクセスを要求する機能がその ID から削除されます。

<<<<<<< HEAD
* [Azure Portal](#azure-portal-disable)
* [Azure Resource Manager テンプレート](#template-disable)
* Azure PowerShell
  * [ロールの割り当てを削除する](../role-based-access-control/role-assignments-powershell.md)
  * [ユーザー割り当て ID を削除する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [ロールの割り当てを削除する](../role-based-access-control/role-assignments-cli.md)
  * [ユーザー割り当て ID を削除する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST API
  * [ロールの割り当てを削除する](../role-based-access-control/role-assignments-rest.md)
  * [ユーザー割り当て ID を削除する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
=======
> [!NOTE]
> システム割り当て ID を無効にした場合、そのロジック アプリのワークフローが使用していたすべての接続は、直ちにその ID を再び有効にした場合でも、実行時に機能しなくなります。 このような動作が生じるのは、ID を無効にするとそのオブジェクト ID が削除されるためです。 ID を有効にするたびに、異なる一意のオブジェクト ID を持つ ID が Azure によって生成されます。 この問題を解決するには、現在のシステム割り当て ID の現在のオブジェクト ID を使用するように、接続を再作成する必要があります。
>
> システム割り当て ID を無効にするのは、できるだけ避けてください。 Azure リソースへの ID のアクセス権を削除する場合は、ターゲット リソースから ID のロールの割り当てを削除します。 ロジック アプリのリソースを削除すると、マネージド ID が Azure AD から自動的に削除されます。
>>>>>>> repo_sync_working_branch

このセクションの手順では、[Azure portal](#azure-portal-disable) と [Azure Resource Manager テンプレート (ARM テンプレート)](#template-disable) を使用する方法について説明します。 Azure PowerShell、Azure CLI、Azure REST API については、次のドキュメントを参照してください。

| ツール | ドキュメント |
|------|---------------|
| Azure PowerShell | 1. [ロールの割り当てを削除する](../role-based-access-control/role-assignments-powershell.md)。 <br>2. [ユーザー割り当て ID を削除する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)。 |
| Azure CLI | 1. [ロールの割り当てを削除する](../role-based-access-control/role-assignments-cli.md)。 <br>2. [ユーザー割り当て ID を削除する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)。 |
| Azure REST API | 1. [ロールの割り当てを削除する](../role-based-access-control/role-assignments-rest.md)。 <br>2. [ユーザー割り当て ID を削除する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)。 |
|||

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Azure portal でマネージド ID を無効にする

マネージド ID のアクセス権を削除するには、ターゲット リソースから ID のロールの割り当てを削除してから、マネージド ID を無効にします。

<a name="disable-identity-target-resource"></a>

#### <a name="remove-role-assignment"></a>ロールの割り当てを削除する

次の手順では、マネージド ID からターゲット リソースへのアクセス権を削除します。

1. [Azure portal](https://portal.azure.com) で、マネージド ID のアクセス権を削除するターゲット Azure リソースに移動します。

1. ターゲット リソースのメニューから **[アクセス制御 (IAM)]** を選択します。 ツール バーで、 **[ロールの割り当て]** を選択します。

1. ロールの一覧で、削除するマネージド ID を選択します。 ツール バーの **[削除]** を選択します。

   > [!TIP]
   > **[削除]** オプションが無効になっている場合は、ご自分にアクセス許可がない可能性があります。 リソースのロールを管理するためのアクセス許可の詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](../active-directory/roles/permissions-reference.md)」をご覧ください。

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app-resource"></a>ロジック アプリのリソースのマネージド ID を無効にする

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ リソースを開きます。

1. ロジック アプリのナビゲーション メニューの **[設定]** で、 **[ID]** を選んでから、お使いの ID の手順に従います。

   * **[システム割り当て済み]**  >  **[オン]**  >  **[保存]** を選択します。 確認を求めるメッセージが表示されたら、 **[はい]** を選択します。

   * **[ユーザー割り当て済み]** とマネージド ID を選択し、 **[削除]** を選択します。 確認を求めるメッセージが表示されたら、 **[はい]** を選択します。

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-an-arm-template"></a>ARM テンプレートでマネージド ID を無効にする

ARM テンプレートを使用してロジック アプリのマネージド ID を作成した場合は、`identity` オブジェクトの `type` 子プロパティを `None` に設定します。

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps におけるアクセスとデータのセキュリティ保護](logic-apps-securing-a-logic-app.md)
