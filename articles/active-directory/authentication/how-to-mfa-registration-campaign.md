---
title: Microsoft Authenticator アプリをセットアップするようにユーザーにナッジする - Azure Active Directory
description: 組織で安全性の低い認証方法から Microsoft Authenticator アプリに移行する方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/12/2021
ms.author: justinha
author: mjsantani
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84aa295b60c24aee46c30dad86f00d6afbcad867
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725194"
---
# <a name="how-to-run-a-registration-campaign-to-set-up-microsoft-authenticator---microsoft-authenticator-app"></a>Microsoft Authenticator アプリを設定してもらうための登録キャンペーンを実行する方法 - Microsoft Authenticator アプリ

サインイン時に Microsoft Authenticator をセットアップするようにユーザーにナッジすることができます。 ユーザーは通常のサインインを行い、いつもどおりに多要素認証を実行した後に、Microsoft Authenticator をセットアップするように求められます。 ユーザーまたはグループを含めるか除外して、アプリをセットアップするようにナッジするユーザーを制御することができます。 これにより、対象を絞ったキャンペーンで、安全性の低い認証方法から Microsoft Authenticator にユーザーを移行することができます。  

ナッジできるユーザーの選択に加えて、ユーザーがナッジを先送り ("再通知") できる日数を定義できます。 ユーザーが **[後で]** をタップしてアプリのセットアップを一時停止すると、再通知期間が経過した後、次回の MFA の試行時に再びナッジされます。 

## <a name="prerequisites"></a>前提条件 

- 組織で Azure MFA を有効にしている必要があります。 
- ユーザーは、自分のアカウントで Microsoft Authenticator をプッシュ通知用にまだセットアップしていない必要があります。 
- 管理者は、次のいずれかのポリシーを使用して、ユーザーに対して Microsoft Authenticator を有効にする必要があります。  
  - MFA 登録ポリシー: ユーザーが、**モバイル アプリ を使用した通知** に対して有効になっている必要があります。  
  - 認証方法ポリシー: ユーザーは、Microsoft Authenticator に対して有効にされていて、認証モードが **[任意]** または **[プッシュ]** に設定されている必要があります。 ポリシーが **[パスワードレス]** に設定されている場合、ユーザーはナッジの対象になりません。 

## <a name="user-experience"></a>ユーザー エクスペリエンス

1. ユーザーは Azure MFA を使用して MFA を正常に実行します。 

1. ユーザーには、サインイン エクスペリエンスを向上させるために Microsoft Authenticator アプリをセットアップするように求めるメッセージが表示されます。 注: Microsoft Authenticator のプッシュ通知が許可され、現在セットアップされていないユーザーにのみ、プロンプトが表示されます。 

   ![ユーザーが多要素認証を実行する](./media/how-to-nudge-authenticator-app/user-mfa.png)

1. ユーザーは **[次へ]** をタップし、Microsoft Authenticator のセットアップをステップ スルーします。 
   1. まず、アプリをダウンロードします。  

      ![ユーザーが Microsoft Authenticator をダウンロードする](./media/how-to-nudge-authenticator-app/download.png)

   1. Microsoft Authenticator をセットアップする方法を参照します。 
   
      ![ユーザーが Microsoft Authenticator をセットアップする](./media/how-to-nudge-authenticator-app/setup.png)

   1. QR コードをスキャンします。 

      ![ユーザーが QR コードをスキャンする](./media/how-to-nudge-authenticator-app/scan.png)

   1. テスト通知を承認します。

      ![ユーザーがテスト通知を承認する](./media/how-to-nudge-authenticator-app/test.png)

   1. 通知が承認されました。

      ![承認の確認](./media/how-to-nudge-authenticator-app/approved.png)

   1. Authenticator アプリが、ユーザーの既定のサインイン方法として正常にセットアップされました。

      ![インストールの完了](./media/how-to-nudge-authenticator-app/finish.png)

1. ユーザーが Authenticator アプリをインストールしたくない場合は、 **[後で]** をタップして、数日後 (管理者が定義可能) にナッジが再通知されるようにすることができます。 
 
   ![インストールの再通知](./media/how-to-nudge-authenticator-app/snooze.png)

## <a name="enable-the-registration-campaign-policy-using-the-portal"></a>ポータルを使用して登録キャンペーン ポリシーを有効にする

Azure AD ポータルで登録キャンペーンを有効にするには、次の手順を実行します。

1. Azure AD ポータルで、 **[セキュリティ]**  >  **[認証方法]**  >  **[登録キャンペーン]** をクリックします。
1. **[状態]** では、 **[有効]** をクリックし、登録キャンペーンから除外するユーザーまたはグループを選択してから、 **[保存]** をクリックします。
   
   ![登録キャンペーンの有効化のスクリーンショット。](./media/how-to-nudge-authenticator-app/registration-campaign.png)

## <a name="enable-the-registration-campaign-policy-using-graph-explorer"></a>Graph エクスプローラーを使用して登録キャンペーンのポリシーを有効にする

登録キャンペーンのポリシーを有効にするには、Graph API を使用して認証方法ポリシーを使用する必要があります。 **グローバル管理者** と **認証方法ポリシー管理者** は、ポリシーを更新できます。 

Graph エクスプローラーを使用してポリシーを構成するには、次の手順を実行します。

1. Graph エクスプローラーにサインインし、**Policy.Read.All** および **Policy.ReadWrite.AuthenticationMethod** のアクセス許可に同意していることを確認します。

   [アクセス許可] パネルを開きます。

   ![Graph エクスプローラーのスクリーンショット](./media/how-to-nudge-authenticator-app/permissions.png)

1. 認証方法ポリシーを取得します: `GET https://graph.microsoft.com/beta/policies/authenticationmethodspolicy`

1. ポリシーの registrationEnforcement および authenticationMethodsRegistrationCampaign セクションを更新して、ユーザーまたはグループへのナッジを有効にします。

   ![キャンペーン セクション](./media/how-to-nudge-authenticator-app/campaign.png)

ポリシーを更新するには、更新された registrationEnforcement セクションのみを使用して、認証方法ポリシーに対して PATCH を実行します: `PATCH https://graph.microsoft.com/beta/policies/authenticationmethodspolicy`

次の表に、**authenticationMethodsRegistrationCampaign** のプロパティの一覧を示します。

| Name | 指定できる値 | 説明 |
|------|-----------------|-------------|
| state | "enabled"<br>"disabled"<br>"default" | この機能を有効または無効にすることができます。<br>この構成が明示的に設定されていない場合は、既定値が使用され、Azure AD の既定値がこの設定に使用されます。 現在は disabled にマップされます。<br>必要に応じて、状態を有効または無効に変更します。  |
| snoozeDurationInDays | 範囲: 0 – 14 | ユーザーにナッジが再び表示されるまでの日数を定義します。<br>値が 0 の場合、ユーザーは MFA を試行するたびにナッジされます。<br>既定値: 1 日 |
| includeTargets | N/A | 機能の対象にするさまざまなユーザーとグループを含めることができます。 |
| excludeTargets | N/A | 機能の対象としないさまざまなユーザーとグループを除外できます。 ユーザーが除外されるグループと含められるグループのメンバーである場合、そのユーザーは機能から除外されます。|

次の表に、**includeTargets** のプロパティの一覧を示します。

| Name | 指定できる値 | 説明 |
|------|-----------------|-------------|
| targetType| "user"<br>"group" | 対象となるエンティティの種類。 |
| Id | GUID 識別子 | 対象となるユーザーまたはグループの ID。 |
| targetedAuthenticationMethod | "microsoftAuthenticator" | 認証方法のユーザーに登録を求めるメッセージが表示されます。 許容される値は "microsoftAuthenticator" のみです。 |

次の表に、**excludeTargets** のプロパティの一覧を示します。

| Name       | 指定できる値   | 説明                           |
|------------|-------------------|---------------------------------------|
| targetType | "user"<br>"group" | 対象となるエンティティの種類。          |
| Id         | 文字列          | 対象となるユーザーまたはグループの ID。 |

### <a name="examples"></a>例

作業を開始するために使用できるいくつかの JSON の例を次に示します。 

- すべてのユーザーを含める 
  
  テナントのすべてのユーザーを含める場合は、単純に[この JSON をダウンロード](https://download.microsoft.com/download/1/4/E/14E6151E-C40A-42FB-9F66-D8D374D13B40/All%20Users%20Enabled.json)し、Graph エクスプローラーにそれを貼り付けて、エンドポイントで `PATCH` を実行します。 

  ```json
  {
  "registrationEnforcement": {
          "authenticationMethodsRegistrationCampaign": {
              "snoozeDurationInDays": 0,
              "state": "enabled",
              "excludeTargets": [],
              "includeTargets": [
                  {
                      "id": "all_users",
                      "targetType": "group",
                      "targetedAuthenticationMethod": "microsoftAuthenticator"
                  }
              ]
          }
      }
  }
  ```

- 特定のユーザーまたはユーザーのグループを含める

  テナントの特定のユーザーまたはグループを含める場合は、[この JSON をダウンロード](https://download.microsoft.com/download/1/4/E/14E6151E-C40A-42FB-9F66-D8D374D13B40/Multiple%20Includes.json)し、ユーザーとグループの関連する GUID を使用してそれを更新します。 次に、Graph エクスプローラーにその JSON を貼り付けて、エンドポイントで `PATCH` を実行します。 

  ```json
  {
  "registrationEnforcement": {
        "authenticationMethodsRegistrationCampaign": {
            "snoozeDurationInDays": 0,
            "state": "enabled",
            "excludeTargets": [],
            "includeTargets": [
                {
                    "id": "*********PLEASE ENTER GUID***********",
                    "targetType": "group",
                    "targetedAuthenticationMethod": "microsoftAuthenticator"
                },
                {
                    "id": "*********PLEASE ENTER GUID***********",
                    "targetType": "user",
                    "targetedAuthenticationMethod": "microsoftAuthenticator"
                }
            ]
        }
    }
  ```

- 特定のユーザーまたはユーザーのグループを含めたり除外したりする

  テナントの特定のユーザーまたはユーザーのグループを含めたり除外したりする場合は、[この JSON をダウンロード](https://download.microsoft.com/download/1/4/E/14E6151E-C40A-42FB-9F66-D8D374D13B40/Multiple%20Includes%20and%20Excludes.json)し、Graph エクスプローラーにそれを貼り付けて、エンドポイントで `PATCH` を実行します。 ユーザーとグループの正しい GUID を入力します。

  ```json
  {
  "registrationEnforcement": {
          "authenticationMethodsRegistrationCampaign": {
              "snoozeDurationInDays": 0,
              "state": "enabled",
              "excludeTargets": [
                  {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "group"
                  },
                {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "user"
                  }
              ],
              "includeTargets": [
                  {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "group",
                      "targetedAuthenticationMethod": "microsoftAuthenticator"
                  },
                  {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "user",
                      "targetedAuthenticationMethod": "microsoftAuthenticator"
                  }
              ]
          }
      }
  }
  ```

### <a name="identify-the-guids-of-users-to-insert-in-the-jsons"></a>JSON に挿入するユーザーの GUID を識別する

1. Azure portal に移動します。
1. **[Azure Active Directory]** をタップします。
1. **[管理]** ブレードで、 **[ユーザー]** をタップします。
1. **[ユーザー]** ページで、対象とする特定のユーザーを識別します。
1. 特定のユーザーをタップすると、ユーザーの GUID である **[オブジェクト ID]** が表示されます。

   ![ユーザー オブジェクト ID](./media/how-to-nudge-authenticator-app/object-id.png)

### <a name="identify-the-guids-of-groups-to-insert-in-the-jsons"></a>JSON に挿入するグループの GUID を識別する

1. Azure portal に移動します。
1. **[Azure Active Directory]** をタップします。
1. **[管理]** ブレードで、 **[グループ]** をタップします。
1. **[グループ]** ページで、対象にする特定のグループを識別します。
1. そのグループをタップして、 **[オブジェクト ID]** を取得します。

   ![グループにナッジする](./media/how-to-nudge-authenticator-app/group.png)

<!---comment out PS until ready>

### PowerShell

1. Install the module.
1. Ensure you pass the right roles:
   
   ```powershell
   Connect-MgGraph -Scopes "User.Read.All","Group.ReadWrite.All"
   ```

1. Select the beta profile.
1. Call `Update-MgPolicyAuthenticationMethod`.

<---->

## <a name="limitations"></a>制限事項

このナッジは、Android または iOS が実行されているモバイル デバイスには表示されません。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**この機能は MFA サーバーで使用できますか?** いいえ。 この機能は、Azure MFA を使用するユーザーに対してのみ使用できます。 

**キャンペーンはどのくらいの期間実行されますか?** API を使用して、キャンペーンを必要な期間だけ有効にすることができます。 キャンペーンの実行を終了するときは、単純に API を使用してキャンペーンを無効にします。  
 
**ユーザーのグループにそれぞれ異なる再通知期間を設定することはできますか?** いいえ。 プロンプトの再通知期間は、テナント全体の設定であり、スコープ内のすべてのグループに適用されます。 

**パスワードレスの電話によるサインインをセットアップするようにユーザーにナッジできますか?** この機能は、MFA がセットアップされているユーザーに Authenticator アプリを使用するように促す機能を管理者に提供することを目的としています。パスワードレスのサインイン用ではありません。  

**サード パーティの認証システム アプリがセットアップされているユーザーにナッジは表示されますか?** このユーザーがプッシュ通知用に Microsoft Authenticator アプリをセットアップしておらず、ポリシーによって有効になっている場合、ユーザーにナッジが表示されます。 

**TOTP コード用にのみ Microsoft Authenticator アプリがセットアップされているユーザーにナッジは表示されますか?** はい。 Microsoft Authenticator アプリがプッシュ通知用にセットアップされておらず、ユーザーがポリシーによって有効になっている場合、ユーザーにナッジが表示されます。

**ユーザーが MFA の登録を行ったばかりの場合、同じサインイン セッションでナッジされますか?** いいえ。 優れたユーザー エクスペリエンスを提供するために、ユーザーは、他の認証方法を登録したのと同じセッションでは、Authenticator をセットアップすることをナッジされません。  

**別の認証方法を登録するようにユーザーにナッジすることはできますか?** いいえ。 現時点では、この機能は、Microsoft Authenticator アプリのみをセットアップするようにユーザーにナッジすることを目的としています。 

**再通知オプションを非表示にして、ユーザーに Authenticator アプリのセットアップを強制する方法はありますか?**  
ナッジの再通知オプションを非表示にする方法はありません。 snoozeDuration に 0 を設定すると、MFA を試行するたびにユーザーにナッジが表示されるようになります。  

**自分が Azure MFA を使用していない場合、ユーザーにナッジできますか?** いいえ。 ナッジは、Azure MFA サービスを使用して MFA を実行しているユーザーに対してのみ機能します。 

**テナントのゲスト/B2B ユーザーにナッジできますか?** はい。 ポリシーを使用して、ナッジの対象範囲として設定されている場合。 

**ユーザーがブラウザーを閉じた場合はどうなりますか?** これは再通知と同じです。


## <a name="next-steps"></a>次のステップ

[Microsoft Authenticator アプリでパスワードなしのサインインを有効にする](howto-authentication-passwordless-phone.md)