---
title: Azure AD B2C でリソース所有者のパスワード資格情報フローを構成する | Microsoft Docs
description: Azure AD B2C でリソース所有者のパスワード資格情報フローを構成する方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/24/2018
ms.author: davidmu
ms.openlocfilehash: c1b4d641f6830751e2cb9e66d5052eb20a48d371
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158256"
---
# <a name="configure-the-resource-owner-password-credentials-flow-ropc-in-azure-ad-b2c"></a>Azure AD B2C でリソース所有者のパスワード資格情報フロー (ROPC) を構成する

リソース所有者のパスワード資格情報 (ROPC) フローは、OAUTH の標準認証フローです。アプリケーションは、証明書利用者とも呼ばれ、ID トークン、アクセス トークン、および更新トークンのユーザー ID とパスワードなどの有効な資格情報を交換します。 

> [!NOTE]
> この機能はプレビュー段階にあります。

Azure AD B2C では、次のオプションがサポートされています。

- **ネイティブ クライアント** - 認証時のユーザー操作は、ユーザー側デバイス上で実行されているコードを使用して行われます。このコードは、Android などのネイティブ オペレーティング システムで実行されているモバイル アプリケーションや、JavaScript などのブラウザーで実行されているモバイル アプリケーションの可能性があります。
- **パブリック クライアント フロー** - アプリケーションによって収集されたユーザーの資格情報のみが API 呼び出しで送信されます。 アプリケーションの資格情報は送信されません。
- **新しい要求の追加** - 新しい要求を追加するために、ID トークンの内容を変更することができます。 

これらのフローはサポートされていません。

- **サーバー対サーバー** - ID 保護システム (IDPS) は、対話の一環として、呼び出し元 (ネイティブ クライアント) から収集された信頼できる IP アドレスが必要です。  サーバー側の API 呼び出しでは、サーバーの IP アドレスのみが使用されます。失敗した認証の動的しきい値を超えた場合、IDPS によって、繰り返される IP アドレスが攻撃者として特定される可能性があります。
- **機密性の高いクライアント フロー** - アプリケーション クライアント ID は検証されますが、アプリケーション シークレットは検証されません。

##  <a name="create-a-resource-owner-policy"></a>リソース所有者のポリシーを作成する

1. Azure AD B2C テナントの全体管理者として、Azure Portal にログインします。
2. Azure AD B2C テナントに切り替えるには、ポータルの右上隅で B2C ディレクトリを選択します。
3. **[ポリシー]** で、**[リソース所有者のポリシー]** を選択します。
4. *ROPC_Auth* など、ポリシーの名前を入力して **[アプリケーション要求]** をクリックします。
5. *[表示名]*、*[メール アドレス]*、*[ID プロバイダー]* など、アプリケーションに必要なアプリケーション要求を選択します。
6. **[OK]** をクリックし、**[作成]** をクリックします。

次の例のようなエンドポイントが表示されます。

`https://login.microsoftonline.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1A_ROPC_Auth`


## <a name="register-an-application"></a>アプリケーションを登録する

1. B2C の設定で **[アプリケーション]** をクリックし、**[+ 追加]** をクリックします。
2. *ROPC_Auth_app* などのアプリケーションの名前を入力します。
3. **Web アプリ/Web API** の場合は **[いいえ]** をクリックし、**ネイティブ クライアント**の場合は **[はい]** をクリックします。
4. その他のすべての値はそのままにして、**[作成]** をクリックします。
5. 新しいアプリケーションを選択し、アプリケーション ID をメモします。

## <a name="test-the-policy"></a>ポリシーをテストする

お気に入りの API 開発アプリケーションを使用して API 呼び出しを生成し、応答を確認して、ポリシーをデバッグします。 次の表の情報を使用して、POST 要求の本文として、このような呼び出しを作成します。
- *yourtenant.onmicrosoft.com* は実際の B2C テナント名に置き換えます
- *B2C_1A_ROPC_Auth* は実際の ROPC ポリシーのフル ネームに置き換えます
- *bef2222d56-552f-4a5b-b90a-1988a7d634c3* は実際の登録のアプリケーション ID に置き換えます。

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| キー | 値 |
| --- | ----- |
| username | leadiocl@outlook.com |
| password | Passxword1 |
| grant_type | password |
| scope | openid bef2222d56-552f-4a5b-b90a-1988a7d634c3 offline_access |
| client_id | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| response_type | token id_token |

*Client_id* は、以前にアプリケーション ID としてメモした値です。 更新トークンを受信する場合、*Offline_access* は省略可能です。 

実際の POST 要求は次のようになります。

```
POST /yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: te.cpim.windows.net
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


オフラインアクセスで成功した応答は、次の例のようになります。

```
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg" 
} 
```

## <a name="redeem-a-refresh-token"></a>更新トークンを利用する

次の表の情報を使用して、POST 要求の本文として、このような POST 呼び出しを作成します。

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| キー | 値 |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| resource | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*Client_id* と *resource* は、以前にアプリケーション ID としてメモした値です。 *Refresh_token* は、前述の認証呼び出しで受け取ったトークンです。

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>好みのネイティブ SDK を使用して実装するか、App-Auth を使用する


Azure AD B2C の実装は、OAuth 2.0 標準またはパブリック クライアント ROPC を満たしており、ほとんどのクライアント SDK と互換性があります。  このフローは、AppAuth for iOS と AppAuth for Android を使用して、運用環境で幅広くテストしました。  最新情報については、https://appauth.io/ を参照してください。

Azure AD B2C で使用するように構成された作業サンプルは、GitHub の https://aka.ms/aadb2cappauthropc (Android 用) と https://aka.ms/aadb2ciosappauthropc でダウンロードできます。




