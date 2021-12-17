---
title: 多要素認証 (MFA) 通知で追加のコンテキストを使用する (プレビュー) - Azure Active Directory
description: MFA 通知で追加のコンテキストを使用する方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: justinha
author: mjsantani
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 139c7a8ea707fc0c9614082cae577eda15f4f664
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725203"
---
# <a name="how-to-use-additional-context-in-multifactor-authentication-mfa-notifications-preview---authentication-methods-policy"></a>多要素認証 (MFA) 通知で追加のコンテキストを使用する方法 (プレビュー) - 認証方法ポリシー

このトピックでは、Microsoft Authenticator プッシュ通知の IP アドレスに基づいてアプリケーションの場所を追加することで、ユーザー サインインのセキュリティを向上させる方法について説明します。  

## <a name="prerequisites"></a>前提条件

組織は、新しい認証方法ポリシー API を使用して Microsoft Authenticator プッシュ通知を一部のユーザーまたはグループに対して有効にする必要があります。 

>[!NOTE]
>追加のコンテキストは 1 つのグループのみを対象にすることができ、動的または入れ子の場合があります。 認証方法ポリシーでは、オンプレミスの同期されたセキュリティ グループとクラウド専用のセキュリティ グループがサポートされます。

## <a name="passwordless-phone-sign-in-and-multifactor-authentication"></a>パスワードレス電話によるサインインと多要素認証 

ユーザーが Microsoft Authenticator アプリでパスワードレス電話によるサインインまたは MFA プッシュ通知を受信すると、承認を要求するアプリケーションの名前と、その IP アドレスに基づくアプリの場所が表示されます。

![MFA プッシュ通知の追加コンテキストのスクリーンショット。](media/howto-authentication-passwordless-phone/location.png)

追加のコンテキストを[数値の一致](how-to-mfa-number-match.md)と組み合わせることで、サインインのセキュリティをさらに向上させることができます。 

![MFA プッシュ通知の数値の一致を含む追加コンテキストのスクリーンショット。](media/howto-authentication-passwordless-phone/location-with-number-match.png)

### <a name="policy-schema-changes"></a>ポリシー スキーマの変更 

スキーマ構成の単一のターゲット グループを特定します。 その後に、次の API エンドポイントを使用して、displayAppInformationRequiredState プロパティを **enabled** に変更します。

https://graph.microsoft.com/beta/authenticationMethodsPolicy/authenticationMethodConfigurations/MicrosoftAuthenticator


#### <a name="microsoftauthenticatorauthenticationmethodconfiguration-properties"></a>MicrosoftAuthenticatorAuthenticationMethodConfiguration プロパティ

**プロパティ**

| プロパティ | Type | 説明 |
|---------|------|-------------|
| id | String | 認証方法ポリシー識別子。 |
| state | authenticationMethodState | 指定できる値: **enabled**<br>**disabled** |
 
**リレーションシップ**

| リレーションシップ | 型 | 説明 |
|--------------|------|-------------|
| includeTargets | [microsoftAuthenticatorAuthenticationMethodTarget](/graph/api/resources/passwordlessmicrosoftauthenticatorauthenticationmethodtarget.md?view=graph-rest-beta) |
| collection | この認証方法を使用できるユーザーまたはグループの集合。 |
 
#### <a name="microsoftauthenticator-includetarget-properties"></a>MicrosoftAuthenticator includeTarget プロパティ
 
**プロパティ**

| プロパティ | Type | 説明 |
|----------|------|-------------|
| authenticationMode | String | 次のいずれかの値になります。<br>**any:** パスワードレスの電話によるサインインと従来の第 2 要素通知の両方を使用できます。<br>**deviceBasedPush**: パスワードレスの電話によるサインイン通知のみを使用できます。<br>**push**: 従来の第 2 要素プッシュ通知のみを使用できます。 |
| id | String | Azure AD ユーザーまたはグループのオブジェクト ID。 |
| targetType | authenticationMethodTargetType | 指定できる値: **user**、**group**。<br>追加のコンテキストには、1 つのグループまたはユーザーのみを設定できます。 |
| displayAppInformationRequiredState | advancedConfigState | 次のいずれかの値になります。<br>**enabled** では、選択したグループに対してこの機能を明示的に有効にします。<br>**disabled** では、選択したグループに対してこの機能を明示的に無効にします。<br>**default** では、選択したグループに対してこの機能を有効にするかどうかを Azure AD で管理できます。 |

>[!NOTE]
>追加のコンテキストは、1 つのグループに対してのみ有効にできます。

#### <a name="example-of-how-to-enable-additional-context-for-all-users"></a>すべてのユーザーに対して追加のコンテキストを有効にする方法の例

**displayAppInformationRequiredState** を **default** から **enabled** に変更します。 

認証モードの値は、パスワードレスの電話によるサインインも有効にするかどうかに応じて、**any** または **push** のどちらにでも指定できます。 これらの例では **any** を使用しますが、パスワードレスを許可しない場合は、**push** を使用します。

以前の構成が上書きされるのを防ぐために、includeTarget 全体へのパッチの適用が必要です。 その場合は、最初に GET を行い、関連するフィールドのみを更新してからパッチを適用します。 次の例では **displayAppInformationRequiredState** に対する更新のみを示しています。 

```json
//Retrieve your existing policy via a GET. 
//Leverage the Response body to create the Request body section. Then update the Request body similar to the Request body as shown below.
//Change the Query to PATCH and Run query
 
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#authenticationMethodConfigurations/$entity",
    "@odata.type": "#microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration",
    "id": "MicrosoftAuthenticator",
    "state": "enabled",
    "includeTargets@odata.context": "https://graph.microsoft.com/beta/$metadata#authenticationMethodsPolicy/authenticationMethodConfigurations('MicrosoftAuthenticator')/microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration/includeTargets",
    "includeTargets": [
        {
            "targetType": "group",
            "id": "all_users",
            "authenticationMode": "any",
            "displayAppInformationRequiredState": "enabled",
            "numberMatchingRequiredState": "enabled"
        }
    ]
}
 
```
 
この更新が適用されたことを確認するために、以下のエンドポイントを使用して、次の GET 要求を実行してください。
GET - https://graph.microsoft.com/beta/authenticationMethodsPolicy/authenticationMethodConfigurations/MicrosoftAuthenticator
 
 
#### <a name="example-of-how-to-enable-additional-context-for-a-single-group"></a>単一のグループに対して追加のコンテキストを有効にする方法の例
 
**displayAppInformationRequiredState** の値を **default** から **enabled** に変更します。 Azure AD ポータルで、**id** を **all_users** からグループの ObjectID に変更します。

以前の構成が上書きされるのを防ぐために、includeTarget 全体へのパッチの適用が必要です。 最初に GET を実行してから関連するフィールドのみを更新し、その後にパッチを適用すすることをお勧めします。 以下の例では **displayAppInformationRequiredState** に対する更新のみを示しています。 

```json
//Copy paste the below in the Request body section as shown below.
//Leverage the Response body to create the Request body section. Then update the Request body similar to the Request body as shown below.
//Change query to PATCH and run query

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#authenticationMethodConfigurations/$entity",
    "@odata.type": "#microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration",
    "id": "MicrosoftAuthenticator",
    "state": "enabled",
    "includeTargets@odata.context": "https://graph.microsoft.com/beta/$metadata#authenticationMethodsPolicy/authenticationMethodConfigurations('MicrosoftAuthenticator')/microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration/includeTargets",
    "includeTargets": [
        {
            "targetType": "group",
            "id": "1ca44590-e896-4dbe-98ed-b140b1e7a53a”,
            "authenticationMode": "any",
            "displayAppInformationRequiredState": "enabled",
            "numberMatchingRequiredState": "enabled"
        }
    ]
}
```
 
確認するには、GET をもう一度実行し、ObjectID GET https://graph.microsoft.com/beta/authenticationMethodsPolicy/authenticationMethodConfigurations/MicrosoftAuthenticator を確認します
 

#### <a name="example-of-error-when-enabling-additional-context-for-multiple-groups"></a>複数のグループに対して追加のコンテキストを有効にした場合のエラーの例

PATCH 要求が 400 Bad Request で失敗し、このエラーに次のメッセージが含まれます。 

`Persistance of policy failed with error: You cannot enable multiple targets for feature 'Require Display App Information'. Choose only one of the following includeTargets to enable: aede0efe-c1b4-40dc-8ae7-2c402f23e312,aede0efe-c1b4-40dc-8ae7-2c402f23e317.`

### <a name="test-the-end-user-experience"></a>エンドユーザー エクスペリエンスをテストする
テスト ユーザー アカウントを Microsoft Authenticator アプリに追加します。 このアカウントで電話によるサインインを有効にする必要は **ありません**。 

aka.ms/MFAsetup にサインインして、Authenticator MFA プッシュ通知と追加のコンテキストのエンド ユーザー エクスペリエンスを確認します。 

### <a name="turn-off-additional-context"></a>追加のコンテキストをオフにする

追加のコンテキストをオフにするには、**displayAppInformationRequiredState** を **enabled** から **disabled**/**default** へと PATCH remove する必要があります。

```json
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#authenticationMethodConfigurations/$entity",
    "@odata.type": "#microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration",
    "id": "MicrosoftAuthenticator",
    "state": "enabled",
    "includeTargets@odata.context": "https://graph.microsoft.com/beta/$metadata#authenticationMethodsPolicy/authenticationMethodConfigurations('MicrosoftAuthenticator')/microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration/includeTargets",
    "includeTargets": [
        {
            "targetType": "group",
            "id": "all_users",
            "authenticationMode": "any",
            "displayAppInformationRequiredState": "enabled",
            "numberMatchingRequiredState": "default"
        }
    ]
}
```

## <a name="enable-additional-context-in-the-portal"></a>ポータルで追加のコンテキストを有効にする

Azure AD ポータルで追加のコンテキストを有効にするには、次の手順を実行します。

1. Azure AD ポータルで、 **[セキュリティ]**  >  **[認証方法]**  >  **[Microsoft Authenticator]** をクリックします。
1. ターゲット ユーザーを選択し、右側の 3 つのドットをクリックし、 **[構成]** をクリックします。
   
   ![数値の一致を構成する方法のスクリーンショット。](media/howto-authentication-passwordless-phone/configure.png)

1. **[認証モード]** を選択し、 **[Show additional context in notifications (Preview)]\(通知で追加のコンテキストを表示する (プレビュー)\)** で **[有効化]** をクリックし、 **[完了]** をクリックします。

   ![追加のコンテキストを有効にする際のスクリーンショット。](media/howto-authentication-passwordless-phone/enable-additional-context.png)

## <a name="next-steps"></a>次の手順

[Azure Active Directory の認証方法 - Microsoft Authenticator アプリ](concept-authentication-authenticator-app.md)

