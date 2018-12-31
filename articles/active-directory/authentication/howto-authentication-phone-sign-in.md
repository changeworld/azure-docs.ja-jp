---
title: Microsoft Authenticator アプリを使用したパスワードなしの Azure AD サインイン (パブリック プレビュー)
description: パスワードを使用せずに、Microsoft Authenticator アプリを使用して Azure AD にサインインする (パブリック プレビュー)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: librown
ms.openlocfilehash: b09bb65cdb571c9df95d1922f4132abe5b77907c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963949"
---
# <a name="password-less-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>Microsoft Authenticator アプリを使用したパスワードなしの電話によるサインイン (パブリック プレビュー)

Microsoft Authenticator アプリを使用すると、パスワードを使用せずに Azure AD アカウントにサインインできます。 [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) のテクノロジと同様、Microsoft Authenticator は、キーベースの認証を使用して、デバイスに関連付けられていて生体認証または PIN を使用するユーザー資格情報を有効にします。

![Microsoft Authenticator アプリでのサインイン試行を承認するようユーザーに求めている、ブラウザーでのサインインの例](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

ユーザー名の入力後にパスワードの入力を求めるプロンプトを表示するのではなく、Microsoft Authenticator アプリで電話によるサインインを有効にしているユーザーには、アプリで番号をタップするよう伝えるメッセージが表示されます。 このアプリでは、ユーザーは、番号を照合し、[Approve]\(承認) を選択してから、PIN を入力するか生体認証を行う必要があります。その後、認証が完了します。

## <a name="enable-my-users"></a>ユーザーを有効にする

パブリック プレビューでは、管理者は、テナントで資格情報を使用できるように、最初に powershell を使用してポリシーを追加する必要があります。 この手順を実行する前に、「既知の問題」セクションを確認してください。

### <a name="tenant-prerequisites"></a>テナントの前提条件

* Azure Active Directory
* エンド ユーザーに Azure Multi-Factor Authentication が有効になっている
* ユーザーが自分のデバイスを登録できる

### <a name="steps-to-enable"></a>有効にする手順

Azure Active Directory V2 PowerShell モジュールのパブリック プレビューのリリースが最新バージョンであることを確認します。 パブリック プレビューをアンインストールおよび再インストールして最新バージョンにするには、次のコマンドを実行します。

1. `Uninstall-Module -Name AzureADPreview`
2. `Install-Module -Name AzureADPreview`

パスワードを入力せずに電話でプレビューにサインインできるようにするには、次の PowerShell コマンドを使用します。

1. `Connect-AzureAD`
   1. 認証ダイアログで、テナントのアカウントでサインインします。 このアカウントは、セキュリティ管理者またはグローバル管理者のいずれかでなければなりません。
1. `New-AzureADPolicy -Type AuthenticatorAppSignInPolicy -Definition '{"AuthenticatorAppSignInPolicy":{"Enabled":true}}' -isOrganizationDefault $true -DisplayName AuthenticatorAppSignIn`

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>エンド ユーザーは電話によるサインインをどのように有効にするのですか。

パブリック プレビューでは、ユーザーにこの新しい資格情報の作成または使用を適用する方法はありません。 エンド ユーザーには、管理者がテナントを有効にしていて、ユーザーが電話によるサインインを有効にするように Microsoft Authenticator アプリを更新した後でのみ、パスワードなしのサインインが発生します。

> [!NOTE]
> この機能は 2017 年 3 月以降はアプリ内にあったため、テナントのポリシーを有効にすると、ユーザーにはすぐにこのフローが発生する可能性もあります。 この変更を認識して、ユーザーに用意をさせてください。
>

1. Azure Multi-Factor Authentication への登録
1. iOS 8.0 以降、または Android 6.0 以降を実行しているデバイスにインストールされている最新バージョンの Microsoft Authenticator。
1. アプリにプッシュ通知が追加されている職場または学校のアカウント。 エンド ユーザーのドキュメントについては、[https://aka.ms/authappstart](https://aka.ms/authappstart)をご覧ください。

ユーザーが Microsoft Authenticator アプリでプッシュ通知が設定されている MFA アカウントを持つと、「[パスワードではなく携帯電話でサインインする](../user-help/microsoft-authenticator-app-phone-signin-faq.md)」の記事に記載の手順に従って電話によるサインイン登録を完了することができます。

## <a name="known-issues"></a>既知の問題

### <a name="ad-fs-integration"></a>AD FS の統合

ユーザーが Microsoft Authenticator のパスワードなしの資格情報を有効にすると、そのユーザーの認証は常に、既定で承認のための通知を送信します。 このロジックは、ハイブリッド テナントのユーザーが、[Use your password instead]\(代わりにパスワードを使用する) をクリックする追加の手順を取ることなく、サインイン確認のために ADFS にリダイレクトされることを防ぎます。 このプロセスは、オンプレミスの条件付きアクセス ポリシーとパススルー認証フローもバイパスします。 このプロセスの例外として、login_hint が指定されている場合、ユーザーは AD FS に自動転送され、パスワードなしの資格情報を使用するオプションがバイパスされます。

### <a name="azure-mfa-server"></a>Azure MFA サーバー

組織のオンプレミスの Azure MFA サーバーを介して MFA を有効にしたエンド ユーザーは、1 つのパスワードなしの電話によるサインイン資格情報を引き続き作成し、使用することができます。 ユーザーが資格情報で Microsoft Authenticator の複数のインストール (5 つ以上) をアップグレードしようとすると、この変更はエラーになることがあります。  

### <a name="device-registration"></a>デバイス登録

この新しい、強力な資格情報を作成するための前提条件の 1 つは、それを配置するデバイスが、個々のユーザーに対し、Azure AD テナント内に登録されていることです。 デバイス登録の制限により、デバイスは、1 つのテナントにしか登録できません。 この制限は、電話によるサインインを有効にできるのは、Microsoft Authenticator アプリの職場または学校の 1 つのアカウントのみであることを意味します。

## <a name="next-steps"></a>次の手順

[デバイス登録の詳細](../devices/overview.md#getting-devices-under-the-control-of-azure-ad)

[Azure Multi-Factor Authentication の詳細](../authentication/howto-mfa-getstarted.md)
