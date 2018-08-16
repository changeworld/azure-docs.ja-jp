---
title: Azure AD パススルー認証 - クイック スタート | Microsoft Docs
description: この記事では、Azure Active Directory (Azure AD) パススルー認証を使用する方法について説明します。
services: active-directory
keywords: Azure AD Connect パススルー認証, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 5a93a21c3884d742479bdd30417a846942cb1ed1
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524092"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Azure Active Directory パススルー認証: クイック スタート

## <a name="deploy-azure-ad-pass-through-authentication"></a>Azure AD パススルー認証をデプロイする

Azure Active Directory (Azure AD) パススルー認証を使用すると、ユーザーは同じパスワードを使用して、オンプレミスのアプリケーションとクラウド ベースのアプリケーションの両方にサインインできます。 パススルー認証では、オンプレミスの Active Directory に対してパスワードを直接検証することで、ユーザーをサインインします。

>[!IMPORTANT]
>AD FS (または他のフェデレーション テクノロジ) からパススルー認証に移行する場合は、[こちら](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx)に公開されている詳細なデプロイ ガイドに従うよう強くお勧めします。

テナントでパススルー認証をデプロイするには、次の手順を実行します。

## <a name="step-1-check-the-prerequisites"></a>手順 1: 前提条件を確認する

次の前提条件が満たされていることを確認します。

### <a name="in-the-azure-active-directory-admin-center"></a>Azure Active Directory 管理センター

1. Azure AD テナントで、クラウド専用のグローバル管理者アカウントを作成します。 その方法を採用すると、オンプレミス サービスが利用できなくなったとき、テナントの構成を管理できます。 クラウド専用のグローバル管理者アカウントを追加する手順については、[こちら](../active-directory-users-create-azure-portal.md)をご覧ください。 テナントからロックアウトされないようにするには、この手順を必ず完了する必要があります。
2. 1 つ以上の[カスタム ドメイン名](../active-directory-domains-add-azure-portal.md)を Azure AD テナントに追加します。 ユーザーは、このドメイン名のいずれかを使用してサインインできます。

### <a name="in-your-on-premises-environment"></a>オンプレミスの環境の場合

1. Azure AD Connect を実行するための、Windows Server 2012 R2 以降を実行しているサーバーを特定します。 このサーバーを、パスワードの検証が必要なユーザーと同じ Active Directory フォレストに追加します。
2. [最新バージョンの Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) を、前の手順で特定したサーバーにインストールします。 Azure AD Connect が既に実行されている場合は、バージョンが 1.1.750.0 以降であることを確認します。

    >[!NOTE]
    >Azure AD Connect のバージョン 1.1.557.0、1.1.558.0、1.1.561.0、1.1.614.0 には、パスワード ハッシュ同期に関連する問題があります。 パスワード ハッシュ同期をパススルー認証と組み合わせて使用_しない_場合については、[Azure AD Connect のリリース ノート](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470)をご覧ください。

3. Windows Server 2012 R2 以降が実行されている 1 つまたは複数の追加のサーバーを特定します。このサーバーでは、スタンドアロンの認証エージェントを実行できます。 これらの追加のサーバーは、サインイン要求の高可用性を確保するために必要です。 これらのサーバーを、パスワードの検証が必要なユーザーと同じ Active Directory フォレストに追加します。

    >[!IMPORTANT]
    >運用環境では、テナントで少なくとも 3 つの認証エージェントを実行することをお勧めします。 認証エージェントの数は、テナントあたり 12 個に制限されています。 また、ベスト プラクティスとして、認証エージェントを実行するすべてのサーバーは Tier 0 システムとして扱うようにしてください ([リファレンス](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)を参照)。

4. サーバーと Azure AD の間にファイアウォールがある場合は、次の項目を構成します。
   - 認証エージェントが次のポートを使用して Azure AD に*送信*リクエストを送れるようにします。
   
    | ポート番号 | 用途 |
    | --- | --- |
    | **80** | SSL 証明書を検証する際に証明書失効リスト (CRL) をダウンロードする |
    | **443** | サービスを使用したすべての送信方向の通信を処理する |
    | **8080** (省略可能) | ポート 443 が使用できない場合、認証エージェントは、ポート 8080 経由で 10 分ごとにその状態を報告します。 この状態は Azure AD ポータルに表示されます。 ポート 8080 は、ユーザー サインインには_使用されません_。 |
   
    ご利用のファイアウォールが送信元ユーザーに応じて規則を適用している場合は、ネットワーク サービスとして実行されている Windows サービスを送信元とするトラフィックに対してこれらのポートを開放します。
   - ファイアウォールまたはプロキシが DNS ホワイトリストを許可している場合は、**\*.msappproxy.net** と **\*.servicebus.windows.net** への接続をホワイトリストに登録できます。 そうでない場合は、毎週更新される [Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)へのアクセスを許可します。
   - 認証エージェントは初回の登録のために **login.windows.net** と **login.microsoftonline.com** にアクセスする必要があるため、 これらの URL にもファイアウォールを開きます。
   - 証明書の検証のために、URL **mscrl.microsoft.com:80**、**crl.microsoft.com:80**、**ocsp.msocsp.com:80**、**www.microsoft.com:80** のブロックを解除します。 他の Microsoft 製品でもこれらの URL を証明書の検証に使用しているので、URL のブロックを既に解除している可能性もあります。

## <a name="step-2-enable-the-feature"></a>手順 2: 機能を有効にする

[Azure AD Connect](active-directory-aadconnect.md) を使用してパススルー認証を有効にします。

>[!IMPORTANT]
>Azure AD Connect のプライマリ サーバーまたはステージング サーバーでパススルー認証を有効にできますが、 プライマリ サーバーから有効することを強くお勧めします。 今後 Azure AD Connect ステージング サーバーをセットアップする場合は、サインイン オプションとして引き続きパススルー認証を選択する**必要があります**。別のオプションを選択すると、テナントのパススルー認証が**無効になり**、プライマリ サーバーの設定が上書きされます。

Azure AD Connect を初めてインストールする場合は、[カスタム インストール パス](active-directory-aadconnect-get-started-custom.md)を選択します。 **[ユーザー サインイン]** ページで、**サインオン方式**として **[パススルー認証]** を選択します。 正常に完了すると、Azure AD Connect と同じサーバーにパススルー認証エージェントがインストールされます。 また、テナントでパススルー認証機能が有効になります。

![Azure AD Connect: [ユーザー サインイン]](./media/active-directory-aadconnect-sso/sso3.png)

[高速インストール](active-directory-aadconnect-get-started-express.md) パスまたは[カスタム インストール](active-directory-aadconnect-get-started-custom.md) パスを使用して Azure AD Connect が既にインストールされている場合は、Azure AD Connect で **[ユーザー サインインの変更]** タスクを選択してから **[次へ]** を選択します。 次に、サインイン方式として **[パススルー認証]** を選択します。 正常に完了すると、Azure AD Connect と同じサーバーにパススルー認証エージェントがインストールされ、テナントで機能が有効になります。

![Azure AD Connect: [ユーザー サインインの変更]](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>パススルー認証はテナント レベルの機能です。 有効にすると、テナントに含まれる "_すべての_" マネージド ドメインのユーザー サインインに影響を及ぼします。 Active Directory フェデレーション サービス (AD FS) からパススルー認証に切り替える場合は、12 時間以上経ってから AD FS インフラストラクチャをシャットダウンする必要があります。 これは、移行中もユーザーが Exchange ActiveSync にサインインできるようにするための措置です。 AD FS からパススルー認証への移行の詳細については、[こちら](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx)で公開されている詳しいデプロイ ガイドをご覧ください。

## <a name="step-3-test-the-feature"></a>手順 3: 機能をテストする

この手順に従って、パススルー認証の有効化を正しく行ったことを確認します。

1. テナントのグローバル管理者の資格情報を使って、[Azure Active Directory 管理センター](https://aad.portal.azure.com)にサインインします。
2. 左ウィンドウで、**[Azure Active Directory]** を選択します。
3. **[Azure AD Connect]** を選びます。
4. **[パススルー認証]** 機能が **[有効]** と表示されていることを確認します。
5. **[パススルー認証]** を選択します。 **[パススルー認証]** 　ウィンドウには、認証エージェントがインストールされているサーバーが一覧表示されます。

![Azure Active Directory 管理センター: [Azure AD Connect] ウィンドウ](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Azure Active Directory 管理センター: [パススルー認証] ウィンドウ](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

この段階で、テナントに含まれるすべてのマネージド ドメインのユーザーが、パススルー認証を使用してサインインできます。 ただし、フェデレーション ドメインのユーザーは引き続き、AD FS または既に構成済みのその他のフェデレーション プロバイダーを使用してサインインします。 ドメインをフェデレーションから管理対象に変換すると、そのドメインのすべてのユーザーが、パススルー認証を使用したサインインを自動的に開始します。 クラウド専用ユーザーはパススルー認証機能の影響を受けません。

## <a name="step-4-ensure-high-availability"></a>手順 4: 高可用性を確保する

運用環境にパススルー認証をデプロイする場合は、追加のスタンドアロン認証エージェントをインストールする必要があります。 これらの認証エージェントは、Azure AD Connect を実行しているサーバー "_以外_" のサーバーにインストールします。 この設定により、ユーザー サインイン要求の高可用性が確保されます。

>[!IMPORTANT]
>運用環境では、テナントで少なくとも 3 つの認証エージェントを実行することをお勧めします。 認証エージェントの数は、テナントあたり 12 個に制限されています。 また、ベスト プラクティスとして、認証エージェントを実行するすべてのサーバーは Tier 0 システムとして扱うようにしてください ([リファレンス](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)を参照)。

次の手順に従って、認証エージェント ソフトウェアをダウンロードします。

1. 最新バージョン (1.5.193.0 以降) の認証エージェントをダウンロードするには、テナントのグローバル管理者の資格情報で [Azure Active Directory 管理センター](https://aad.portal.azure.com)にサインインします。
2. 左ウィンドウで、**[Azure Active Directory]** を選択します。
3. **[Azure AD Connect]**、**[パススルー認証]**、**[エージェントのダウンロード]** の順に選択します。
4. **[使用条件に同意してダウンロードする]** をクリックします。

![Azure Active Directory 管理センター: 認証エージェントのダウンロード ボタン](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Azure Active Directory 管理センター: [エージェントのダウンロード] ウィンドウ](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>認証エージェント ソフトウェアは[ここ](https://aka.ms/getauthagent)から直接ダウンロードすることもできます。 認証エージェントをインストールする "_前_" に[サービス使用条件](https://aka.ms/authagenteula)を確認して同意してください。

スタンドアロン認証エージェントをデプロイする方法は 2 つあります。

1 つ目は、ダウンロードした認証エージェントの実行可能ファイルを実行し、メッセージが表示されたらテナントのグローバル管理者の資格情報を提供することにより、対話的に行うことができます。

2 つ目は、自動デプロイ スクリプトを作成して実行できます。 これは、一度に複数の認証エージェントをデプロイするときや、ユーザー インターフェイスが有効になっていない、またはリモート デスクトップでアクセスできない Windows サーバーに認証エージェントをインストールするときに便利です。 この方法を使用する手順を以下に示します。

1. 次のコマンドを実行して、認証エージェント `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q` をインストールしてください。
2. 認証エージェントは、Windows PowerShell を使用して Microsoft のサービスに登録できます。 テナントのグローバル管理者のユーザー名とパスワードを格納する PowerShell 資格情報オブジェクト `$cred` を作成します。 *\<username\>* と *\<password\>* を置き換えて、次のコマンドを実行します。
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
3. **C:\Program Files\Microsoft Azure AD Connect 認証エージェント**に移動し、作成済みの `$cred` オブジェクトを使用して次のスクリプトを実行します。
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

## <a name="next-steps"></a>次の手順
- [AD FS からパススルー認証への移行](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx) - AD FS (または他のフェデレーション テクノロジ) からパススルー認証に移行するための詳細なガイドです。
- [スマート ロックアウト](../authentication/howto-password-smart-lockout.md): ユーザー アカウントを保護するようにテナントのスマート ロックアウト機能を構成する方法を確認します。
- [現在の制限](active-directory-aadconnect-pass-through-authentication-current-limitations.md): パススルー認証でサポートされているシナリオと、サポートされていないシナリオを確認します。
- [技術的な詳細](active-directory-aadconnect-pass-through-authentication-how-it-works.md): パススルー認証機能のしくみを理解します。
- [よく寄せられる質問](active-directory-aadconnect-pass-through-authentication-faq.md): よく寄せられる質問とその回答です。
- [トラブルシューティング](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): パススルー認証機能に関する一般的な問題を解決する方法を確認します。
- [セキュリティの詳細](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): パススルー認証機能に関する技術情報を取得します。
- [Azure AD シームレス SSO](active-directory-aadconnect-sso.md): この補完的な機能の詳細を確認します。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory フォーラムで、新しい機能の要望を出します。

