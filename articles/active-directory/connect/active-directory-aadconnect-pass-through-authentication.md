---
title: "Azure AD Connect: パススルー認証 | Microsoft Docs"
description: "この記事では、Azure Active Directory (Azure AD) パススルー認証について説明します。パススルー認証は、オンプレミス Active Directory に対してユーザーのパスワードを検証することで Azure AD のサインインを可能にします。"
services: active-directory
keywords: "Azure AD Connect パススルー認証とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 4fc3c822e657ce1a66a59e15c1a7d636a9f699e6
ms.contentlocale: ja-jp
ms.lasthandoff: 05/12/2017

---

# <a name="configure-user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Azure Active Directory パススルー認証によるユーザー サインインの構成

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Azure Active Directory パススルー認証とは

ユーザーがオンプレミス リソースとクラウド ベースのサービスへのサインインに同じ資格情報 (パスワード) を使用できるようにすると、ユーザーにとっても組織にとってもメリットがあります。 ユーザーにとっては覚えておかなければならないパスワードが 1 つ減り、楽になります。 サインイン方法を忘れるという可能性も減ります。 ヘルプ デスクのコストも削減されます。一般に、パスワード関連の問題に最も多くのサポート リソースが使用されるためです。

多くの組織が、オンプレミス リソースとクラウド ベースのサービスでユーザーに同じ資格情報を提供する方法として、[Azure AD パスワード同期](active-directory-aadconnectsync-implement-password-synchronization.md)を使用しています。これは、ユーザーのパスワード ハッシュをオンプレミス Active Directory から Azure Active Directory (Azure AD) に同期する [Azure AD Connect](active-directory-aadconnect.md) の機能です。 ただし、パスワード (場合によってはハッシュ形式のパスワード) を内部の組織境界内で使用することを求める組織もあります。

Azure AD パススルー認証機能は、このような組織にシンプルなソリューションを提供します。 この機能では、ユーザーが Azure AD にサインインするときに、ユーザーのパスワードをオンプレミス Active Directory に対して直接検証できます。 この機能には次のような利点もあります。

- 使いやすさ
  - 複雑なオンプレミス デプロイやネットワーク構成を必要とせずに、パスワード検証が実行されます。
  - 軽量オンプレミス コネクタがパスワード検証要求を待ち受け、応答します。
  - オンプレミス コネクタは機能改善とバグ修正を自動的に受け取ります。
  - [Azure AD Connect](active-directory-aadconnect.md) と共に構成できます。 軽量オンプレミス コネクタは、Azure AD Connect と同じサーバーにインストールされます。
- セキュリティ保護
  - オンプレミス パスワードが何らかの形でクラウドに保存されることはありません。
  - 軽量オンプレミス コネクタは、ネットワーク内からの送信接続のみを行います。 そのため、境界ネットワーク (別名は DMZ) にコネクタをインストールするという要件はありません。
  - パススルー認証は Azure Multi-Factor Authentication とシームレスに連携します。
- 信頼性とスケーラビリティ
  - 追加の軽量オンプレミス コネクタを複数のサーバーにインストールすることで、サインイン要求の高可用性を実現できます。

![Azure AD パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

パススルー認証と[シームレス シングル サインオン](active-directory-aadconnect-sso.md) (SSO) 機能を組み合わせることができます。 この方法で、ユーザーは企業ネットワーク内の会社のコンピューターを使用しているときに、Azure AD にサインインするためにパスワードを入力する必要もなくなり、真に統合されたエクスペリエンスを実現できます。

## <a name="whats-available-during-preview"></a>プレビュー期間中に利用できるもの

>[!NOTE]
>Azure AD パススルー認証は現在プレビュー段階です。 これは無料の機能であり、この機能を使用するために Azure AD の有料エディションは不要です。

プレビュー期間中、次のシナリオが完全にサポートされています。

- すべての Web ブラウザー ベースのアプリケーション
- [最新の認証](https://aka.ms/modernauthga)をサポートする Office 365 クライアント アプリケーション

プレビュー期間中、次のシナリオはサポート_されていません_。

- 従来の Office クライアント アプリケーションと Exchange ActiveSync (モバイル デバイス上のネイティブ電子メール アプリケーション)。 <br>組織は、可能であれば最新の認証に切り替えることが推奨されます。 これにより、パススルー認証のサポートが可能になるだけでなく、多要素認証などの[条件付きアクセス](../active-directory-conditional-access.md)機能を使用して ID をセキュリティで保護することもできます。
- Windows 10 デバイスの Azure AD Join。

>[!IMPORTANT]
>パススルー認証機能で現在サポートされていないシナリオ (従来の Office クライアント アプリケーション、Exchange ActiveSync、Windows 10 デバイスの Azure AD Join) に対処するために、パススルー認証を有効にすると、パスワード同期も既定で有効になります。 パスワード同期は、これらの特定のシナリオでのみフォールバックとして機能します。 これが不要な場合は、Azure AD Connect ウィザードの [[オプション機能]](active-directory-aadconnect-get-started-custom.md#optional-features) ページでパスワード同期を無効にすることができます。

### <a name="prerequisites"></a>前提条件

Azure AD パススルー認証を有効にするには、以下を用意する必要があります。

- 自分がグローバル管理者である Azure AD テナント。

  >[!NOTE]
  >グローバル管理者アカウントをクラウド専用アカウントにすることを強くお勧めします。 その方法を採用すると、オンプレミス サービスが利用できなくなったとき、テナントの構成を管理できます。 クラウド専用のグローバル管理者アカウントを追加する手順については、[こちら](../active-directory-users-create-azure-portal.md)をご覧ください。

- Azure AD Connect バージョン 1.1.486.0 以降。 [最新バージョンの Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) を使用することをお勧めします。
- Azure AD Connect を実行するための、Windows Server 2012 R2 以降を実行しているサーバー。
  - このサーバーは、パスワードを検証する必要があるユーザーと同じ AD フォレストのメンバーである必要があります。
  - パススルー認証コネクタは Azure AD Connect と同じサーバーにインストールされることに注意してください。 コネクタのバージョンが 1.5.58.0 以上であることを確認してください。

    >[!NOTE]
    >AD フォレスト間にフォレストの信頼があり、名前サフィックス ルーティングが正しく構成されていれば、複数フォレスト環境がサポートされます。

- 高可用性を実現する場合は、スタンドアロン コネクタをインストールするために、Windows Server 2012 R2 以降を実行する追加のサーバーが必要になります。 (コネクタのバージョンは 1.5.58.0 以降になっている必要があります。)
- コネクタと Azure AD の間にファイアウォールがある場合は、次の点をご確認ください。
    - URL フィルタリングが有効になっている場合は、コネクタが次の URL と通信できることを確認します。
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net
    - また、コネクタが [Azure データ センターの IP 範囲](https://www.microsoft.com/en-us/download/details.aspx?id=41653)に対して直接 IP 接続することを確認します。
    - コネクタはクライアント証明書を使用して Azure AD と通信するため、ファイアウォールによって SSL 検査が実行されていないことを確認します。
    - コネクタがポート 80 およびポート 443 経由で Azure AD に対する送信要求を実行できることを確認します。
      - ファイアウォールが送信元ユーザーに応じて規則を適用している場合は、ネットワーク サービスとして実行されている Windows サービスからのトラフィックに対してこれらのポートを開放します。
      - コネクタは、SSL 証明書失効リストをダウンロードするために、ポート 80 で HTTP 要求を行います。 これは、自動更新機能が正常に機能するためにも必要です。
      - コネクタは、機能の有効化と無効化、コネクタの登録、コネクタ更新プログラムのダウンロード、すべてのユーザー サインイン要求の処理など、他のすべての操作についてはポート 443 で HTTPS 要求を行います。

     >[!NOTE]
     >コネクタがサービスと通信する際に必要なポートの数を減らすために、最近改善が行われました。 古いバージョンの Azure AD Connect やスタンドアロン コネクタを実行している場合は、その他のポート (5671、8080、9090、9091、9350、9352、10100 - 10120) を引き続き開いておく必要があります。

### <a name="enable-azure-ad-pass-through-authentication"></a>Azure AD パススルー認証を有効にする

Azure AD パススルー認証は、Azure AD Connect を使用して有効にすることができます。

Azure AD Connect の新しいインストールを実行する場合は、[カスタム インストール パス](active-directory-aadconnect-get-started-custom.md)を選択します。 **[ユーザー サインイン]** ページで **[パススルー認証]** を選択します。 正常に完了すると、Azure AD Connect と同じサーバーにパススルー認証コネクタがインストールされます。 また、テナントでパススルー認証機能が有効になります。

![Azure AD Connect - [ユーザー サインイン]](./media/active-directory-aadconnect-sso/sso3.png)

Azure AD Connect を既にインストールしている場合は、[高速インストール](active-directory-aadconnect-get-started-express.md) パスまたは[カスタム インストール](active-directory-aadconnect-get-started-custom.md) パスを使用してセットアップし、Azure AD Connect で **[ユーザー サインインの変更]** を選択して、**[次へ]** をクリックします。 次に、**[パススルー認証]** を選択して、Azure AD Connect と同じサーバーにパススルー認証コネクタをインストールし、テナントでこの機能を有効にします。

![Azure AD Connect - [ユーザー サインインの変更]](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Azure AD パススルー認証はテナント レベルの機能です。 テナント内のすべての管理対象ドメインのユーザー サインインに影響します。 ただし、フェデレーション ドメインのユーザーは、Active Directory フェデレーション サービス (ADFS) または既に構成済みのその他のフェデレーション プロバイダーを使用して引き続きサインインします。 ドメインをフェデレーションから管理対象に変換すると、そのドメインのすべてのユーザーが、パススルー認証を使用したサインインを自動的に開始します。 クラウドのみのユーザーはパススルー認証の影響を受けません。

パススルー認証を運用環境デプロイで使用することを計画している場合は、別のサーバー (Azure AD Connect と最初のコネクタを実行しているサーバー以外) に 2 つ目のコネクタをインストールすることを強くお勧めします。 サインイン要求の可用性が高くなります。 追加コネクタは必要な数だけインストールできます。 これは、テナントが処理するサインイン要求の最大数と平均数に基づきます。

スタンドアロン コネクタをデプロイするには、次の手順に従います。

#### <a name="step-1-download-and-install-the-connector"></a>手順 1: コネクタをダウンロードしてインストールする

この手順では、コネクタ ソフトウェアをサーバーにダウンロードしてインストールします。

1.    最新のコネクタを[ダウンロード](https://go.microsoft.com/fwlink/?linkid=837580)します。 コネクタのバージョンが 1.5.58.0 以上であることを確認してください。
2.    管理者としてコマンド プロンプトを開きます。
3.    次のコマンドを実行します。 **/q** オプションは "サイレント インストール" を意味します。このインストールでは、使用許諾契約書への同意を求めるメッセージは表示されません。`
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`

>[!NOTE]
>各サーバーにインストールできるコネクタは 1 つだけです。

#### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>手順 2: パススルー認証用にコネクタを Azure AD に登録する

この手順では、サーバーにインストールされたコネクタをサービスに登録し、サインイン要求を受信できるようにします。

1.    管理者として PowerShell ウィンドウを開きます。
2.    **C:\Program Files\Microsoft AAD App Proxy Connector** に移動し、次のスクリプトを実行します。`.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    メッセージが表示されたら、Azure AD テナントのグローバル管理者アカウントの資格情報を入力します。

## <a name="how-does-azure-ad-pass-through-authentication-work"></a>Azure AD パススルー認証のしくみ

テナントでパススルー認証が有効になっている場合、ユーザーが Azure AD へのサインインを試みると、次のことが行われます。

1. ユーザーが Azure AD サインイン ページにユーザー名とパスワードを入力します。 サービスがユーザー名と (公開キーを使用して暗号化された) パスワードを検証用のキューに配置します。
2. 使用可能なオンプレミス コネクタのいずれかがキューの送信呼び出しを実行し、ユーザー名とパスワードを取得します。
3. コネクタは、標準の Windows API を使用して Active Directory に対してユーザー名とパスワードを検証します (ADFS で使用されているものと同様のメカニズム)。 ユーザー名には、オンプレミスの既定のユーザー名 (通常は `userPrincipalName`) または Azure AD Connect で構成された別の属性 (`Alternate ID` と呼ばれます) を指定できます。
4. オンプレミス ドメイン コントローラーが要求を評価し、コネクタに応答 (成功または失敗) を返します。
5. コネクタがこの応答を Azure AD に返します。
6. Azure AD が応答を評価し、ユーザーに適宜応答します。 たとえば、アプリケーションにトークンを発行したり、多要素認証を要求したりします。

次の図は、さまざまな手順を示します。 要求と応答はすべて HTTPS チャネル経由で行われます。

![パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### <a name="password-writeback"></a>パスワードの書き戻し

テナントで特定のユーザーに対して[パスワード ライトバック](../active-directory-passwords-update-your-own-password.md)を構成した場合は、ユーザーがパススルー認証を使用してサインインすると、ユーザーはこれまでと同様にパスワードを変更またはリセットできます。 パスワードはオンプレミス Active Directory に想定どおりに書き戻されます。

ただし、テナントでパスワード ライトバックが構成されていない場合、またはユーザーに有効な Azure AD ライセンスが割り当てられていない場合、ユーザーはクラウドでパスワードを更新できなくなります。 これはパスワードの有効期限が切れている場合にも当てはまります。 代わりに、"このサイトでパスワードを変更することをお客様の組織が許可していません。 組織によって推奨されている方法でパスワードを更新するか、サポートが必要な場合は管理者にお問い合わせください。" というメッセージが表示されます。

## <a name="next-steps"></a>次のステップ
- テナントで [Azure AD シームレス SSO](active-directory-aadconnect-sso.md) 機能を有効にする方法をご確認ください。
- Azure AD パススルー認証に関する一般的問題を解決する方法については、[トラブルシューティング ガイド](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)をご覧ください。

## <a name="feedback"></a>フィードバック
お客様のフィードバックは Microsoft にとって重要です。 ご質問がございましたら、コメント セクションをご利用ください。 新しい機能に関するご要望がございましたら、[UserVoice フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)をご利用ください。


