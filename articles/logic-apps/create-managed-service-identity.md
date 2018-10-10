---
title: サインインせずにアクセスおよび認証する - Azure Logic Apps | Microsoft Docs
description: 資格情報がなくても、ロジック アプリで他の Azure Active Directory (Azure AD) テナント内のリソースを認証してアクセスできるように、マネージド ID を作成する
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: fb1c31e6e7c075e20191a4e51d7b1a9323f3b979
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973968"
---
# <a name="access-resources-and-authenticate-as-managed-identities-in-azure-logic-apps"></a>Azure Logic Apps でマネージド ID としてリソースにアクセスし、認証する

サインインせずに、他の Azure Active Directory (Azure AD) テナント内のリソースにアクセスして、自分の ID を認証するため、ロジック アプリが資格情報の代わりに使用する[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を作成できます。 この ID は、ユーザーの代わりに Azure で管理されます。ユーザーがシークレットを提供したりローテーションしたりする必要がないため、資格情報の保護に役立ちます。 この記事では、ご使用のロジック アプリに対して、マネージド ID を作成して使用する方法を示します。 詳細については、[Azure リソースのマネージド ID](../app-service/app-service-managed-service-identity.md)に関するページを参照してください。

> [!NOTE]
> Azure リソースのマネージド ID は、以前のマネージド サービス ID (MSI) に替わる名前です。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。サブスクリプションをお持ちでない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

* マネージド ID を使用するロジック アプリ。 ロジック アプリをお持ちでない場合は、[初めてのロジック アプリ ワークフローを作成する](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>マネージド ID の作成

ロジック アプリ用のマネージド ID は、Azure portal、Azure Resource Manager テンプレート、または Azure PowerShell を使用して作成または有効化することができます。 

### <a name="azure-portal"></a>Azure ポータル

Azure portal を使用してロジック アプリ用のマネージド ID を作成するには、ご使用のロジック アプリのワークフロー設定で、**[Azure Active Directory に登録する]** 設定をオンにします。

1. [Azure Portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. 次の手順に従います。 

   1. ロジック アプリのメニューの **[設定]** で、**[ワークフロー設定]** を選択します。 

   1. **[マネージド サービス ID]** > 
    **[Azure Active Directory に登録する]** の下で **[オン]** を選択します。

   1. 完了したら、ツールバーで **[保存]** を選択します。

      ![マネージド ID 設定をオンにする](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      Azure に、これらのプロパティと、ご使用のロジック アプリのマネージド ID の値が表示されます。

      ![プリンシパル ID とテナント ID の GUID](./media/create-managed-service-identity/principal-tenant-id.png)

      | プロパティ | 値 | 説明 | 
      |----------|-------|-------------| 
      | **プリンシパル ID** | <*principal-ID-GUID*> | Azure AD テナント内のロジック アプリを表すグローバル一意識別子 (GUID) | 
      | **テナント ID** | <*Azure-AD-tenant--ID-GUID*> | ご使用のロジック アプリが現在メンバーとなっている Azure AD テナントを表すグローバル一意識別子 (GUID)。 Azure AD テナント内では、サービス プリンシパルは、ロジック アプリ インスタンスと同じ名前を持ちます。 | 
      ||| 

### <a name="deployment-template"></a>デプロイ テンプレート

ロジック アプリなどの Azure リソースの作成とデプロイを自動化するため、Azure Resource Manager テンプレートを設定できます。 詳細については、「[Azure Resource Manager テンプレートを使用してロジック アプリを作成してデプロイする](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)」を参照してください。 

テンプレートを使ってご使用のロジック アプリ用にマネージド ID を作成するには、**ID** 要素と**型**プロパティをデプロイ テンプレートのロジック アプリ ワークフロー定義に追加します。 これらの設定は、Azure でロジック アプリ用のこの ID を作成して管理することを指定します。

```json
"identity": {
    "type": "SystemAssigned"
}
```

たとえば、ロジック アプリは次のバージョンのようになります。

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
    "principalId": "<principal-ID-GUID>",
    "tenantId": "<Azure-AD-tenant-ID>-GUID"
}
```

| プロパティ | 値 | 説明 | 
|----------|-------|-------------|
| **principalId** | <*principal-ID-GUID*> | Azure AD テナント内のロジック アプリを表すグローバル一意識別子 (GUID) | 
| **tenantId** | <*Azure-AD-tenant--ID-GUID*> | ロジック アプリが現在メンバーとなっている Azure AD テナントを表すグローバル一意識別子 (GUID)。 Azure AD テナント内では、サービス プリンシパルは、ロジック アプリ インスタンスと同じ名前を持ちます。 | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>マネージド ID を使用してリソースにアクセスする

ロジック アプリ用にマネージド ID を作成すると、[その ID に他のリソースへのアクセス権を与える](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)ことができます。 その後、そのマネージド ID は、他の[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)と同じように、認証に使用できます。 

たとえば、別のリソースへのアクセス権を持つマネージド ID を使用してロジック アプリを既に設定しているとします。 ロジック アプリでそのリソースに HTTP 要求や呼び出しを送信できるように、HTTP アクションを追加できます。 

1. ロジック アプリで、**HTTP** アクションを追加します。 

1. 呼び出すリソースの要求**メソッド**と **URI** の場所など、そのアクションに必要な詳細を提供します。

1. HTTP アクションで、**[詳細オプションの表示]** を選択します。 

1. **[認証]** リストから、**[マネージド サービス ID]** を選択し、表示された **[対象ユーザー]** プロパティを次のように設定します。

   ![[マネージド サービス ID] を選択](./media/create-managed-service-identity/select-managed-service-identity.png)

1. ご希望の方法でロジック アプリのビルドを続行します。

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>マネージド ID の削除

ロジック アプリでマネージド ID を無効にするには、Azure portal、Azure Resource Manager デプロイ テンプレート、または Azure PowerShell を使用して ID を作成したのと同様の方法で無効にすることができます。 

ロジック アプリを削除すると、Azure によってロジック アプリのシステム割り当て ID が Azure AD から自動的に削除されます。

### <a name="azure-portal"></a>Azure ポータル

1. ロジック アプリ デザイナーでロジック アプリを開きます。

1. 次の手順に従います。 

   1. ロジック アプリのメニューの **[設定]** で、**[ワークフロー設定]** を選択します。 
   
   1. **[マネージド サービス ID]** の下の **[Azure Active Directory に登録する]** プロパティを **[オフ]** にします。

   1. 完了したら、ツールバーで **[保存]** を選択します。

      ![マネージド ID 設定をオフにする](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>デプロイ テンプレート

Azure Resource Manager デプロイ テンプレートを使用してロジック アプリのマネージド ID を作成した場合は、`"identity"` 要素の `"type"` プロパティを `"None"` に設定します。 このアクションにより、プリンシパル ID も Azure AD から削除されます。 

```json
"identity": {
    "type": "None"
}
```

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](http://aka.ms/logicapps-wish)にアクセスしてください。
