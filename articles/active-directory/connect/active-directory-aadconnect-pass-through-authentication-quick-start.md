---
title: "Azure AD パススルー認証 - クイック スタート | Microsoft Docs"
description: "この記事では、Azure Active Directory (Azure AD) パススルー認証を使用する方法について説明します。"
services: active-directory
keywords: "Azure AD Connect パススルー認証, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c1bc7cc5fe53d04019f68a520fb03c9187a6148b
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017

---

# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Azure Active Directory パススルー認証: クイック スタート

Azure Active Directory (Azure AD) パススルー認証を使用すると、ユーザーは同じパスワードを使用して、オンプレミスのアプリケーションとクラウド ベースのアプリケーションの両方にサインインできます。 この認証では、オンプレミスの Active Directory に対してパスワードを直接検証することで、ユーザーをサインインします。

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>Azure AD パススルー認証をデプロイする方法

パススルー認証をデプロイするには、次の手順を実行する必要があります。
1. "*前提条件を確認する*": 機能を有効にする前に、テナントとオンプレミス環境を正しく設定します。
2. "*機能を有効にする*": テナントでパスワード認証を有効にして、軽量なオンプレミスのエージェントをインストールし、パススルー検証要求を処理します。
3. "*機能をテストする*": パススルー認証を使用して、ユーザー サインインをテストします。
4. "*高可用性を確保する*": 2 番目のスタンドアロン エージェントをインストールして、サインイン要求に高可用性を提供します。

## <a name="step-1-check-prerequisites"></a>手順 1: 前提条件を確認する

次の前提条件が満たされていることを確認します。

### <a name="on-the-azure-portal"></a>Azure Portal の場合

1. Azure AD テナントで、クラウド専用のグローバル Administrator アカウントを作成します。 その方法を採用すると、オンプレミス サービスが利用できなくなったとき、テナントの構成を管理できます。 クラウド専用のグローバル管理者アカウントを追加する手順については、[こちら](../active-directory-users-create-azure-portal.md)をご覧ください。 テナントからロックアウトされないようにするには、この手順を必ず実行する必要があります。
2. 1 つ以上の[カスタム ドメイン名](../active-directory-add-domain.md)を Azure AD テナントに追加します。 ユーザーは、このドメイン名のいずれかを使用してにサインインします。

### <a name="in-your-on-premises-environment"></a>オンプレミスの環境の場合

1. Windows Server 2012 R2 以降が実行されているサーバーを特定します。このサーバーでは Azure AD Connect を実行します。 このサーバーを、パスワードの検証が必要なユーザーと同じ AD フォレストに追加します。
2. [最新バージョンの Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) を、手順 1. で特定したサーバーにインストールします。 Azure AD Connect が実行されている場合は、バージョンが 1.1.486.0 以降であることを確認します。
3. Windows Server 2012 R2 以降が実行されている追加のサーバーを特定します。このサーバーでは、スタンドアロンの認証エージェントを実行します。 認証エージェントのバージョンは 1.5.58.0 以降である必要があります。 このサーバーは、サインイン要求の高可用性を確保するために必要です。 このサーバーを、パスワードの検証が必要なユーザーと同じ AD フォレストに追加します。
4. サーバーと Azure AD の間にファイアウォールがある場合は、次の項目を構成する必要があります。
   - ポートを開く: サーバーの認証エージェントがポート 80 およびポート 443 経由で Azure AD に対する送信要求を実行できることを確認します。 ファイアウォールが送信元ユーザーに応じて規則を適用している場合は、ネットワーク サービスとして実行されている Windows サービスからのトラフィックに対してこれらのポートを開放します。
   - Azure AD エンドポイントを許可する: URL フィルタリングが有効になっている場合は、認証エージェントが **\*.msappproxy.net** および **\*.servicebus.windows.net** と通信できることを確認します。
   - 直接 IP 接続を確認する: サーバーの認証エージェントが [Azure データ センターの IP 範囲](https://www.microsoft.com/en-us/download/details.aspx?id=41653)に直接 IP 接続できることを確認します。

## <a name="step-2-enable-the-feature"></a>手順 2: 機能を有効にする

Azure AD パススルー認証は、[Azure AD Connect](active-directory-aadconnect.md) を使用して有効にできます。

>[!IMPORTANT]
>Azure AD Connect のプライマリ サーバーまたはステージング サーバーで Azure AD パススルー認証を有効にできますが、 プライマリ サーバーから有効することを強くお勧めします。

Azure AD Connect を初めてインストールする場合は、[カスタム インストール パス](active-directory-aadconnect-get-started-custom.md)を選択します。 **[ユーザー サインイン]** ページで、サインオン方式として **[パススルー認証]** を選択します。 正常に完了すると、Azure AD Connect と同じサーバーにパススルー認証エージェントがインストールされます。 また、テナントでパススルー認証機能が有効になります。

![Azure AD Connect - [ユーザー サインイン]](./media/active-directory-aadconnect-sso/sso3.png)

([高速インストール](active-directory-aadconnect-get-started-express.md) パスまたは[カスタム インストール](active-directory-aadconnect-get-started-custom.md) パスを使用して) Azure AD Connect を既にインストールした場合は、Azure AD Connect で **[ユーザー サインインの変更]** を選択し、**[次へ]** をクリックします。 次に、サインオン方式として **[パススルー認証]** を選択します。 正常に完了すると、Azure AD Connect と同じサーバーにパススルー認証エージェントがインストールされ、テナントで機能が有効になります。

![Azure AD Connect - [ユーザー サインインの変更]](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>パススルー認証はテナント レベルの機能です。 有効にすると、テナントに含まれる "_すべての_" 管理対象ドメインのユーザー サインインに影響を及ぼします。

## <a name="step-3-test-the-feature"></a>手順 3: 機能をテストする

手順 2 の後に、テナントに含まれるすべての管理対象ドメインのユーザーが、パススルー認証を使用してサインインします。 ただし、フェデレーション ドメインのユーザーは引き続き、Active Directory フェデレーション サービス (AD FS) または既に構成済みのその他のフェデレーション プロバイダーを使用してサインインします。 ドメインをフェデレーションから管理対象に変換すると、そのドメインのすべてのユーザーが、パススルー認証を使用したサインインを自動的に開始します。 クラウド専用ユーザーはパススルー認証機能の影響を受けません。

## <a name="step-4-ensure-high-availability"></a>手順 4: 高可用性を確保する

運用環境にパススルー認証をデプロイする場合は、スタンドアロン認証エージェントをインストールする必要があります。 この 2 番目の認証エージェントを、Azure AD Connect が実行されている、最初の認証エージェント "_以外_" のサーバーにインストールします。 この設定により、サインイン要求の高可用性が確保されます。 次の手順に従って、スタンドアロン認証エージェントをデプロイします。

### <a name="download-and-install-the-authentication-agent-software-on-your-server"></a>認証エージェントをサーバーにダウンロードしてインストールする

1.  最新の認証エージェント ソフトウェアを[ダウンロード](https://go.microsoft.com/fwlink/?linkid=837580)します。 バージョンが 1.5.58.0 以上であることを確認してください。
2.  管理者としてコマンド プロンプトを開きます。
3.  次のコマンドを実行します (**/q** オプションは "サイレント インストール" を意味します。このインストールでは、使用許諾契約書への同意を求めるメッセージは表示されません): `
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`

>[!NOTE]
>各サーバーにインストールできる認証エージェントは 1 つだけです。

### <a name="register-the-authentication-agent-with-azure-ad"></a>認証エージェントを Azure AD に登録します

1.  管理者として PowerShell ウィンドウを開きます。
2.  **C:\Program Files\Microsoft AAD App Proxy Connector** に移動し、次のスクリプトを実行します。`.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.  メッセージが表示されたら、Azure AD テナントのグローバル管理者アカウントの資格情報を入力します。

## <a name="next-steps"></a>次のステップ
- [**現時点での制限事項**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - この機能は、現在プレビューの段階にあります。 サポートされているシナリオと、サポートされていないシナリオを確認します。
- [**技術的な詳細**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - この機能のしくみを確認します。
- [**よく寄せられる質問**](active-directory-aadconnect-pass-through-authentication-faq.md) - よく寄せられる質問と回答です。
- [**トラブルシューティング**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 機能に関する一般的な問題を解決する方法を確認します。
- [**Azure AD のシームレスな SSO**](active-directory-aadconnect-sso.md) - この補完的な機能の詳細を確認します。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。

