---
title: マネージド ID を使用して認証する - Azure Logic Apps | Microsoft Docs
description: サインインなしで認証するために、マネージド ID (以前はマネージド サービス ID (MSI) と呼ばれていました) を作成できます。そうすることで、ロジック アプリは資格情報やシークレットなしで他の Azure Active Directory (Azure AD) テナント内のリソースにアクセスできます
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 10/05/2018
ms.openlocfilehash: 19e6693de673eae6fe0b885580975c4cefc35d60
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725150"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Azure Logic Apps でマネージド ID を使用して認証し、リソースにアクセスする

サインインすることなく他の Azure Active Directory (Azure AD) テナント内のリソースへのアクセスと ID の認証を行うために、ロジック アプリは、資格情報やシークレットではなく [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) (以前はマネージド サービス ID (MSI) と呼ばれていました) を使用できます。 この ID は、ユーザーの代わりに Azure で管理されます。ユーザーがシークレットを提供したりローテーションしたりする必要がないため、資格情報の保護に役立ちます。 この記事では、ロジック アプリのためにシステム割り当てのマネージド ID を作成して使用する方法を示します。 マネージド ID の詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

> [!NOTE]
> 現在、システム割り当てのマネージド ID は、各 Azure サブスクリプションで最大 10 個までのロジック アプリ ワークフローに割り当てることができます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。サブスクリプションをお持ちでない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

* システム割り当てのマネージド ID を使用するロジック アプリ。 ロジック アプリをお持ちでない場合は、[初めてのロジック アプリ ワークフローを作成する](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>マネージド ID の作成

ロジック アプリ用のシステム割り当てのマネージド ID は、Azure portal、Azure Resource Manager テンプレート、または Azure PowerShell から作成または有効化できます。 

### <a name="azure-portal"></a>Azure ポータル

Azure portal から、ロジック アプリ用のシステム割り当てのマネージド ID を有効にするには、目的のロジック アプリのワークフロー設定で、**[Azure Active Directory に登録する]** 設定をオンにします。

1. [Azure Portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. 次の手順に従います。 

   1. ロジック アプリのメニューの **[設定]** で、**[ワークフロー設定]** を選択します。 

   1. **[マネージド サービス ID]** > 
    **[Azure Active Directory に登録する]** の下で **[オン]** を選択します。

   1. 完了したら、ツールバーで **[保存]** を選択します。

      ![マネージド ID 設定をオンにする](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      これでロジック アプリに、これらのプロパティと値を持ち、Azure Active Directory に登録された、システム割り当てのマネージド ID が割り当てられました。

      ![プリンシパル ID とテナント ID の GUID](./media/create-managed-service-identity/principal-tenant-id.png)

      | プロパティ | 値 | 説明 | 
      |----------|-------|-------------| 
      | **プリンシパル ID** | <*principal-ID*> | Azure AD テナント内のロジック アプリを表すグローバル一意識別子 (GUID) | 
      | **テナント ID** | <*Azure-AD-tenant-ID*> | ご使用のロジック アプリが現在メンバーとなっている Azure AD テナントを表すグローバル一意識別子 (GUID)。 Azure AD テナント内では、サービス プリンシパルは、ロジック アプリ インスタンスと同じ名前を持ちます。 | 
      ||| 

### <a name="deployment-template"></a>デプロイ テンプレート

ロジック アプリなどの Azure リソースの作成とデプロイを自動化するときには、[Azure Resource Manager テンプレート](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)を利用できます。 テンプレートを使ってロジック アプリ用のシステム割り当てマネージド ID を作成するには、デプロイ テンプレートで、ロジック アプリのワークフロー定義に `"identity"` 要素と `"type"` プロパティを追加します。 

```json
"identity": {
   "type": "SystemAssigned"
}
```

例: 

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

Azure でロジック アプリを作成すると、そのロジック アプリのワークフロー定義には、これらの追加プロパティが含まれます。

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| プロパティ | 値 | 説明 | 
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | Azure AD テナント内のロジック アプリを表すグローバル一意識別子 (GUID) | 
| **tenantId** | <*Azure-AD-tenant-ID*> | ロジック アプリが現在メンバーとなっている Azure AD テナントを表すグローバル一意識別子 (GUID)。 Azure AD テナント内では、サービス プリンシパルは、ロジック アプリ インスタンスと同じ名前を持ちます。 | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>マネージド ID を使用してリソースにアクセスする

ロジック アプリ用にシステム割り当てマネージド ID を作成すると、[その ID に他の Azure リソースへのアクセスを与える](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)ことができます。 その後は、他の[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)と同様に、その ID を認証に使用できます。 

> [!NOTE]
> アクセスを割り当てるシステム割り当てマネージド ID とリソースの両方が、同じ Azure サブスクリプションを持っている必要があります。

### <a name="assign-access-to-managed-identity"></a>マネージド ID にアクセスを割り当てる

ロジック アプリのシステム割り当てマネージド ID に別の Azure リソースへのアクセスを付与するには、以下の手順に従います。

1. Azure portal で、マネージド ID にアクセスを割り当てる Azure リソースに移動します。 

1. リソースのメニューから **[アクセス制御 (IAM)]** を選択し、**[ロールの割り当ての追加]** を選択します。 

   ![ロールの割り当ての追加](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. **[ロールの割り当ての追加]** で、その ID の**ロール**を選択します。 

1. **[アクセスの割り当て先]** プロパティで、まだ選択されていなければ、**[Azure AD のユーザー、グループ、サービス プリンシパル]** を選択します。

1. **[選択]** ボックスで、ロジック アプリの名前の入力を、ロジック アプリの名前の最初の文字で始めます。 目的のロジック アプリが表示されたら、そのロジック アプリを選択します。

   ![マネージド ID を使用するロジック アプリを選択する](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. 完了したら、**[保存]** を選択します。

### <a name="authenticate-with-managed-identity-in-logic-app"></a>ロジック アプリでマネージド ID を使用して認証する

システム割り当てのマネージド ID を使用してロジック アプリを設定し、必要なリソースへのアクセスをその ID に割り当てたら、認証のためにその ID を使用できるようになっています。 たとえば、ロジック アプリでそのリソースに HTTP の要求や呼び出しを送信できるようにする HTTP アクションを使用できます。 

1. ロジック アプリで、**HTTP** アクションを追加します。 

1. 呼び出すリソースの要求**メソッド**と **URI** の場所など、そのアクションに必要な詳細を提供します。

1. HTTP アクションで、**[詳細オプションの表示]** を選択します。 

1. **[認証]** リストから、**[マネージド サービス ID]** を選択し、表示された **[対象ユーザー]** プロパティを次のように設定します。

   ![[マネージド サービス ID] を選択](./media/create-managed-service-identity/select-managed-service-identity.png)

1. ご希望の方法でロジック アプリのビルドを続行します。

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>マネージド ID の削除

Azure portal、Azure Resource Manager デプロイ テンプレート、または Azure PowerShell から ID を作成した方法と同様の手順に従えば、ロジック アプリでシステム割り当てのマネージド ID を無効にできます。 

ロジック アプリを削除すると、Azure によってロジック アプリのシステム割り当て ID が Azure AD から自動的に削除されます。

### <a name="azure-portal"></a>Azure ポータル

1. ロジック アプリ デザイナーでロジック アプリを開きます。

1. 次の手順に従います。 

   1. ロジック アプリのメニューの **[設定]** で、**[ワークフロー設定]** を選択します。 
   
   1. **[マネージド サービス ID]** の下の **[Azure Active Directory に登録する]** プロパティを **[オフ]** にします。

   1. 完了したら、ツールバーで **[保存]** を選択します。

      ![マネージド ID 設定をオフにする](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>デプロイ テンプレート

Azure Resource Manager デプロイ テンプレートを使用してロジック アプリのシステム割り当てマネージド ID を作成した場合は、`"identity"` 要素の `"type"` プロパティを `"None"` に設定します。 このアクションにより、プリンシパル ID も Azure AD から削除されます。 

```json
"identity": {
   "type": "None"
}
```

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

