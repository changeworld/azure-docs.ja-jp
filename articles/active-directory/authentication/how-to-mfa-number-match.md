---
title: 多要素認証 (MFA) 通知で数値の一致を使用する (プレビュー) - Azure Active Directory
description: MFA 通知で数値の一致を使用する方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/17/2021
ms.author: justinha
author: mjsantani
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6428bf7a2680d59ae42a8236b3a7a5aea82b1b2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725197"
---
# <a name="how-to-use-number-matching-in-multifactor-authentication-mfa-notifications-preview---authentication-methods-policy"></a>多要素認証 (MFA) 通知で数値の一致を使用する方法 (プレビュー) - 認証方法ポリシー

このトピックでは、ユーザーのサインイン セキュリティを向上するために、Microsoft Authenticator のプッシュ通知で数値の一致を有効にする方法について説明します。  

>[!NOTE]
>数値の一致は、Microsoft Authenticator アプリの従来の第 2 要素通知への重要なセキュリティ アップグレードであり、一般提供 (GA) から数か月後にすべてのテナントに対して既定で有効になります。<br> サインイン セキュリティを強化するために、数値の一致をすぐに有効にすることを強くお勧めします。

## <a name="prerequisites"></a>前提条件

組織では、新しい認証方法ポリシー API を使用して Microsoft Authenticator (従来の第 2 要素) プッシュ通知を一部のユーザーまたはグループに対して有効にする必要があります。 組織で ADFS アダプターまたは NPS 拡張機能を使用している場合は、エクスペリエンスが整合するように最新バージョンにアップグレードしてください。 

## <a name="number-matching"></a>数値の一致

<!---check below with Mayur. The bit about the policy came from the number match FAQ at the end.--->

数値の一致は 1 つのグループのみを対象にすることができ、動的または入れ子の場合があります。 認証方法ポリシーでは、オンプレミスの同期されたセキュリティ グループとクラウド専用のセキュリティ グループがサポートされます。 

数値の一致は、次のシナリオで使用できます。 数値の一致を有効にすると、すべてのシナリオでサポートされるようになります。

- [多要素認証](tutorial-enable-azure-mfa.md)
- [セルフサービス パスワード リセット](howto-sspr-deployment.md)
- [Authenticator アプリの設定時における SSPR と MFA の登録の統合](howto-registration-mfa-sspr-combined.md)
- [AD FS アダプター](howto-mfaserver-adfs-windows-server.md)
- [NPS 拡張機能](howto-mfa-nps-extension.md)

### <a name="multifactor-authentication"></a>多要素認証

ユーザーが Microsoft Authenticator を使用して MFA プッシュ通知に応答すると、ユーザーに数値が表示されます。 承認を完了するために、その数値をアプリに入力する必要があります。 

![ユーザーが数値の一致で数値を入力するスクリーンショット。](media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

### <a name="sspr"></a>SSPR

セルフサービス パスワード リセット中に Microsoft Authenticator 通知で数値が表示され、ユーザーはそれを Authenticator アプリ通知に入力する必要があります。 この数値は、数値の一致が有効になっているユーザーにのみ表示されます。

>[!NOTE]
>SSPR 中、管理者ロールの数値の一致は保留され、数日間使用できません。

### <a name="combined-registration"></a>統合された登録

ユーザーは、統合された登録を行って Microsoft Authenticator を設定すると、アカウント追加の一環として通知を承認するように求められます。 数値の一致が有効になっているユーザーの場合、この通知に数値が表示され、それを Authenticator アプリ通知に入力する必要があります。 

### <a name="ad-fs-adapter"></a>AD FS アダプター

AD FS アダプターでは、更新プログラムのインストール後に数値の一致がサポートされます。 以前のバージョンの Windows Server では、数値の一致はサポートされません。 以前のバージョンでは、ユーザーには引き続き **[許可]** / **[禁止]** のエクスペリエンスが表示され、アップグレードするまで数値の一致は表示されません。

| バージョン | 更新 |
|---------|--------|
| Windows Server 2022 | [2021 年 10 月 26 日 — KB5006745 (OS ビルド 20348.320) プレビュー](https://support.microsoft.com/topic/october-26-2021-kb5006745-os-build-20348-320-preview-8ff9319a-19e7-40c7-bbd1-cd70fcca066c) |
| Windows Server 2019 | [2021 年 10 月 19 日 — KB5006744 (OS ビルド 17763.2268) プレビュー](https://support.microsoft.com/topic/october-19-2021-kb5006744-os-build-17763-2268-preview-e043a8a3-901b-4190-bb6b-f5a4137411c0) |
| Windows Server 2016 | [2021 年 10 月 12 日 — KB5006669 (OS ビルド 14393.4704)](https://support.microsoft.com/topic/october-12-2021-kb5006669-os-build-14393-4704-bcc95546-0768-49ae-bec9-240cc59df384) |


### <a name="nps-extension"></a>NPS 拡張機能

必ず最新バージョンの [NPS 拡張機能](https://www.microsoft.com/download/details.aspx?id=54688)をお使いください。 1\.0.1.40 から始まる NPS 拡張機能のバージョンでは、数値の一致がサポートされます。 

NPS 拡張機能では数値を表示できないので、数値の一致が有効になっているユーザーの場合でも **[許可]** / **[禁止]** の選択が求められます。 ただし、プッシュ通知をオーバーライドするレジストリ キーを作成すると、ユーザーにワンタイム パスコード (OTP) の入力が求められるようになります。 この動作を得るには、ユーザーが OTP 認証方法を登録する必要があります。 一般的な OTP 認証方法には、Microsoft Authenticator アプリで使用できる OTP、その他のソフトウェア トークンが含まれます。 

OTP 方式を登録していないユーザーには、引き続き **[許可]** / **[禁止]** のエクスペリエンスが表示されます。 数値の一致を無効にしているユーザーには、常に **[許可]** / **[禁止]** のエクスペリエンスが表示されます。

プッシュ通知をオーバーライドするレジストリ キーを作成するには、次のようにします。

1. NPS サーバーで、レジストリ エディターを開きます。
1. HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa に移動します。
1. 次のキー値ペアを設定します。キー: OVERRIDE_NUMBER_MATCHING_WITH_OTP、値 = TRUE
1. NPS サービスを再起動します。 

### <a name="policy-schema-changes"></a>ポリシー スキーマの変更 

スキーマ構成の単一のターゲット グループを特定します。 その後に、次の API エンドポイントを使用して、numberMatchingRequiredState プロパティを **enabled** に変更します。

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
| targetType | authenticationMethodTargetType | 指定できる値: **user**、**group**。<br>注: 数値の一致の対象に設定できるグループまたはユーザーは 1 つのみです。 |
| numberMatchingRequiredState | advancedConfigState | 次のいずれかの値になります。<br>**enabled** では、選択したグループに対してこの機能を明示的に有効にします。<br>**disabled** では、選択したグループに対してこの機能を明示的に無効にします。<br>**default** では、選択したグループに対してこの機能を有効にするかどうかを Azure AD で管理できます。 |

>[!NOTE]
>数値の一致は、1 つのグループに対してのみ有効にできます。

#### <a name="example-of-how-to-enable-number-matching-for-all-users"></a>すべてのユーザーに対して数値の一致を有効にする方法の例

**numberMatchingRequiredState** を **default** から **enabled** に変更する必要があります。 

認証モードの値は、パスワードレスの電話によるサインインも有効にするかどうかに応じて、**any** または **push** のどちらでも指定できます。 これらの例では **any** を使用しますが、パスワードレスを許可しない場合は、**push** を使用します。 

>[!NOTE]
>パスワードレスのユーザーには、数値の一致を有効にしても影響はありません。パスワードレス エクスペリエンスに既に含まれているためです。 

以前の構成が上書きされるのを防ぐために、includeTarget 全体へのパッチの適用が必要になる場合があります。 その場合は、最初に GET を行い、関連するフィールドのみを更新してからパッチを適用します。 次の例では **numberMatchingRequiredState** に対する更新のみを示しています。 

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
 
これが適用されたことを確認するために、以下のエンドポイントを使用して、次の GET 要求を実行してください。
GET - https://graph.microsoft.com/beta/authenticationMethodsPolicy/authenticationMethodConfigurations/MicrosoftAuthenticator
 
 
#### <a name="example-of-how-to-enable-number-matching-for-a-single-group"></a>1 つのグループに対して数値の一致を有効にする方法の例
 
**numberMatchingRequiredState** の値を **default** から **enabled** に変更する必要があります。 Azure AD ポータルで、**id** を **all_users** からグループの ObjectID に変更する必要があります。

以前の構成がff上書きされるのを防ぐために、includeTarget 全体へのパッチの適用が必要になる場合があります。 最初に GET を実行してから関連するフィールドのみを更新し、その後にパッチを適用すすることをお勧めします。 次の例では **numberMatchingRequiredState** に対する更新のみを示しています。 

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
 
確認するには、GET をもう一度実行し、ObjectID GET を確認します: https://graph.microsoft.com/beta/authenticationMethodsPolicy/authenticationMethodConfigurations/MicrosoftAuthenticator
 

#### <a name="example-of-error-when-enabling-number-matching-for-multiple-groups"></a>複数のグループに対して数値の一致を有効にした場合のエラーの例

PATCH 要求が 400 Bad Request で失敗し、このエラーに次のメッセージが含まれます。 


`Persistance of policy failed with error: You cannot enable multiple targets for feature 'Require Number Matching'. Choose only one of the following includeTargets to enable: aede0efe-c1b4-40dc-8ae7-2c402f23e312,aede0efe-c1b4-40dc-8ae7-2c402f23e317.`

### <a name="test-the-end-user-experience"></a>エンドユーザー エクスペリエンスをテストする
テスト ユーザー アカウントを Microsoft Authenticator アプリに追加します。 このアカウントで電話によるサインインを有効にする必要は **ありません**。 

aka.ms/MFAsetup にサインインして、Authenticator MFA プッシュ通知と数値の一致のエンド ユーザー エクスペリエンスを確認します。 

### <a name="turn-off-number-matching"></a>数値の一致をオフにする

数値の一致をオフにするには、**numberMatchingRequiredState** を **enabled** から **disabled**/**default** に変更してパッチ削除する必要があります。

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

## <a name="enable-number-matching-in-the-portal"></a>ポータルで数値の一致を有効にする

Azure AD ポータルで数値の一致を有効にするには、次の手順を実行します。

1. Azure AD ポータルで、 **[セキュリティ]**  >  **[認証方法]**  >  **[Microsoft Authenticator]** をクリックします。
1. ターゲット ユーザーを選択し、右側の 3 つのドットをクリックし、 **[構成]** をクリックします。
   
   ![数値の一致の構成のスクリーンショット。](media/howto-authentication-passwordless-phone/configure.png)

1. **[認証モード]** を選択し、 **[数値の一致を必須にする (プレビュー)]** で **[有効化]** をクリックし、 **[完了]** をクリックします。 

   ![数値の一致の有効化のスクリーンショット。](media/howto-authentication-passwordless-phone/enable-number-matching.png)


## <a name="known-issues"></a>既知の問題

- SSPR 中、管理者ロールの数値の一致は保留され、数日間使用できません。

## <a name="next-steps"></a>次の手順

[Azure Active Directory の認証方法 - Microsoft Authenticator アプリ](concept-authentication-authenticator-app.md)

