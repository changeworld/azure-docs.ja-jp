---
title: マッピングのために Azure Active Directory に属性を同期する
description: Azure Active Directory と SaaS アプリを使用してユーザー プロビジョニングを構成する際は、ディレクトリの拡張機能を使用して、既定では同期されないソース属性を追加します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2021
ms.author: kenwith
ms.openlocfilehash: f7a2429161cebe867d844b4ca7aa08ec3613edcd
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388212"
---
# <a name="syncing-extension-attributes-for-app-provisioning"></a>アプリ プロビジョニングの拡張属性の同期

Azure AD から [SaaS アプリ](../saas-apps/tutorial-list.md)にユーザー アカウントをプロビジョニングするときに、Azure Active Directory (Azure AD) には、ユーザー プロファイルの作成に必要なすべてのデータ (属性) が含まれていなければなりません。 ユーザー プロビジョニングのために属性マッピングをカスタマイズしているときに、マップ対象の属性が **[ソース属性]** の一覧に表示されないことがあります。 この記事では、欠落している属性を追加する方法について説明します。

Azure AD 内のユーザーについてのみ、[PowerShell または Microsoft Graph を使用してスキーマ拡張を作成](#create-an-extension-attribute-on-a-cloud-only-user)できます。

オンプレミスの Active Directory 内のユーザーについては、そのユーザーを Azure AD に同期する必要があります。 ユーザーと属性は、[Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) を使用して同期できます。 Azure AD Connect では自動的に特定の属性が Azure AD に同期されますが、すべての属性が同期されるわけではありません。 さらに、既定で同期される一部の属性 (SAMAccountName など) は、Azure AD Graph API を使用して公開されない可能性があります。 このような場合は、[Azure AD Connect ディレクトリ拡張機能を使用して、Azure AD に属性を同期する](#create-an-extension-attribute-using-azure-ad-connect)ことができます。 これにより、Azure AD Graph API と Azure AD プロビジョニング サービスが属性を認識できるようになります。

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>クラウドのみのユーザーに拡張属性を作成する
Microsoft Graph と PowerShell を使用して、Azure AD 内のユーザーのユーザー スキーマを拡張できます。 これらの拡張属性は、ほとんどの場合、自動的に検出されます。

サービス プリンシパルが 1,000 を超える場合は、ソース属性の一覧に拡張機能が欠落していることがあります。 作成した属性が自動的に表示されない場合は、その属性が作成されたことを確認し、それをスキーマに手動で追加します。 作成されたことを確認するには、Microsoft Graph と [Graph Explorer](/graph/graph-explorer/graph-explorer-overview) を使用します。 スキーマに手動で追加するには、「[サポートされている属性一覧の編集](customize-application-attributes.md#editing-the-list-of-supported-attributes)」を参照してください。

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Microsoft Graph を使用してクラウドのみのユーザーに拡張属性を作成する
[Microsoft Graph](/graph/overview) を使用して Azure AD ユーザーのスキーマを拡張できます。 

まず、テナント内のアプリを一覧表示して、作業中のアプリの ID を取得します。 詳細については、「[extensionProperties を一覧表示する](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true)」を参照してください。

```json
GET https://graph.microsoft.com/v1.0/applications
```

次に、拡張属性を作成します。 下の **ID** プロパティを、前の手順で取得した **ID** に置き換えます。 "appId" ではなく **"ID"** 属性を使用する必要があります。 詳細については、[Create extensionProperty]/graph/api/application-post-extensionproperty.md?view=graph-rest-1.0&tabs=http&preserve-view=true) を参照してください。

```json
POST https://graph.microsoft.com/v1.0/applications/{id}/extensionProperties
Content-type: application/json

{
    "name": "extensionName",
    "dataType": "string",
    "targetObjects": [
        "User"
    ]
}
```

前の要求で、`extension_appID_extensionName` という形式の拡張属性が作成されました。 これで、この拡張属性を使用してユーザーを更新できます。 詳細については、「[ユーザーの更新](/graph/api/user-update?view=graph-rest-1.0&tabs=http&preserve-view=true)」を参照してください。
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
最後に、ユーザーの属性を確認します。 詳細については、「[ユーザーの取得](/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true)」を参照してください。

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>PowerShell を使用してクラウドのみのユーザーに拡張属性を作成する
PowerShell を使用してカスタムの拡張機能を作成し、ユーザーに値を割り当てます。 

```
#Connect to your Azure AD tenant   
Connect-AzureAD

#Create an application (you can instead use an existing application if you would like)
$App = New-AzureADApplication -DisplayName “test app name” -IdentifierUris https://testapp

#Create a service principal
New-AzureADServicePrincipal -AppId $App.AppId

#Create an extension property
New-AzureADApplicationExtensionProperty -ObjectId $App.ObjectId -Name “TestAttributeName” -DataType “String” -TargetObjects “User”

#List users in your tenant to determine the objectid for your user
Get-AzureADUser

#Set a value for the extension property on the user. Replace the objectid with the ID of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Azure AD Connect を使用して拡張属性を作成する

1. Azure AD Connect ウィザードを起動して [タスク] を選択し、 **[同期オプションのカスタマイズ]** を選択します。

   ![Azure Active Directory Connect ウィザードの [追加のタスク] ページ](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Azure AD グローバル管理者としてサインインします。 

3. **[オプション機能]** ページで **[ディレクトリ拡張属性の同期]** を選択します。
 
   ![Azure Active Directory Connect ウィザードの [オプション機能] ページ](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Azure AD に拡張する属性を選択します。
   > [!NOTE]
   > **[使用可能な属性]** での検索は、大文字と小文字が区別されます。

   ![[ディレクトリ拡張機能] 選択ページを示すスクリーンショット](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Azure AD Connect ウィザードを終了し、完全同期サイクルの実行を許可します。 サイクルが完了したら、スキーマが拡張され、オンプレミスの AD と Azure AD の間で新しい値が同期されます。
 
6. これで、Azure portal で [ユーザー属性マッピングを編集する](customize-application-attributes.md)際に、追加した属性が **[ソース属性]** の一覧に `<attributename> (extension_<appID>_<attributename>)` の形式で含まれるようになります。 属性を選択し、プロビジョニングのためにターゲット アプリケーションにマップします。

   ![Azure Active Directory Connect ウィザードの [ディレクトリ拡張機能] 選択ページ](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> オンプレミスの AD から参照属性 (**managedby** や **DN/DistinguishedName** など) をプロビジョニングする機能は、現在サポートされていません。 [ユーザーの声](https://feedback.azure.com/forums/169401-azure-active-directory)に関するページで、この機能の要求をお送りください。 


## <a name="next-steps"></a>次のステップ

* [プロビジョニングの対象ユーザーを定義する](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
