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
ms.date: 04/24/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: d3c3f6ba0da73a8297f437a56f190f90274957ab
ms.contentlocale: ja-jp
ms.lasthandoff: 04/27/2017

---

# <a name="configure-user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Azure Active Directory パススルー認証によるユーザー サインインの構成

## <a name="what-is-azure-active-directory-azure-ad-pass-through-authentication"></a>Azure Active Directory (Azure AD) パススルー認証とは

ユーザーがオンプレミス リソースとクラウド ベースのサービスへのサインインに同じ資格情報 (パスワード) を使用できるようにすると、ユーザーにとっても組織にとってもメリットがあります。 ユーザーは覚えておくパスワードが 1 つ減ることになります。 これにより、ユーザー エクスペリエンスが向上し、ユーザーがサインインの方法を忘れる可能性が低減されます。 さらに、ヘルプ デスクのコストも削減されます。一般に、パスワード関連の問題に最も多くのサポート リソースが使用されるためです。

多くの組織が、オンプレミス リソースとクラウド ベースのサービスでユーザーに同じ資格情報を提供する方法として、[Azure AD パスワード同期](active-directory-aadconnectsync-implement-password-synchronization.md)を使用しています。これは、ユーザーのパスワードをオンプレミス Active Directory から Azure AD に同期する [Azure AD Connect](active-directory-aadconnect.md) の機能です。 ただし、パスワード (場合によってはハッシュ形式のパスワード) を内部の組織境界内で使用することを求める組織もあります。

Azure AD パススルー認証は、このような組織にシンプルなソリューションを提供します。 パススルー認証では、ユーザーが Azure AD にサインインするときに、ユーザーのパスワードをオンプレミス Active Directory に対して直接検証できます。 この機能には次のような利点もあります。

- 使いやすさ
  - 複雑なオンプレミス デプロイやネットワーク構成を必要とせずに、パスワード検証が実行されます。
  - パスワード検証要求をリッスンし、要求に応答する軽量オンプレミス コネクタのみが使用されます。
  - オンプレミス コネクタには自動更新機能があるため、機能の改善とバグの修正を自動的に受信できます。
  - [Azure AD Connect](active-directory-aadconnect.md) と共に構成できます。 軽量オンプレミス コネクタは、Azure AD Connect と同じサーバーにインストールされます。
- セキュリティ保護
  - オンプレミス パスワードが何らかの形でクラウドに保存されることはありません。
  - 軽量オンプレミス コネクタは、ネットワーク内からの送信接続のみを行います。 そのため、DMZ へのコネクタのインストール要件はありません。
  - パススルー認証は Azure Multi-Factor Authentication とシームレスに連携します。
- 信頼性とスケーラビリティ
  - 追加の軽量オンプレミス コネクタを複数のサーバーにインストールすることで、サインイン要求の高可用性を実現できます。

![Azure AD パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

[シームレス シングル サインオン](active-directory-aadconnect-sso.md)機能と組み合わせると、ユーザーは企業ネットワーク内の会社のコンピューターを使用しているときに、Azure AD にサインインするためにパスワードを入力する必要もなくなり、真に統合されたエクスペリエンスを実現できます。

## <a name="whats-available-during-preview"></a>プレビュー期間中に利用できるもの

>[!NOTE]
>Azure AD パススルー認証は現在プレビュー段階です。 これは無料の機能であり、この機能を使用するために Azure AD の有料エディションは不要です。 

プレビュー期間中、次のシナリオが完全にサポートされています。

- すべての Web ブラウザー ベースのアプリケーション。
- [最新の認証](https://aka.ms/modernauthga)をサポートする Office 365 クライアント アプリケーション。

プレビュー期間中、次のシナリオはサポートされていません。

- 従来の Office クライアント アプリケーションと Exchange ActiveSync (モバイル デバイス上のネイティブ電子メール アプリケーション)。
  - 組織は、可能であれば最新の認証に切り替えることが推奨されます。 これにより、パススルー認証のサポートが可能になるだけでなく、多要素認証などの[条件付きアクセス](../active-directory-conditional-access.md)機能を使用して ID をセキュリティで保護することもできます。
- Windows 10 デバイスの Azure AD Join。

>[!IMPORTANT]
>パススルー認証機能で現在サポートされていないシナリオ (従来の Office クライアント アプリケーション、Exchange ActiveSync、Window 10 デバイスの Azure AD Join) に対処するために、パススルー認証を有効にすると、パスワード同期も既定で有効になります。 パスワード同期は、これらの特定のシナリオでのみフォールバックとして機能します。 これが不要な場合は、Azure AD Connect ウィザードの [[オプション機能]](active-directory-aadconnect-get-started-custom.md#optional-features) ページでパスワード同期を無効にすることができます。

## <a name="how-to-enable-azure-ad-pass-through-authentication"></a>Azure AD パススルー認証を有効にする方法

### <a name="pre-requisites"></a>前提条件

Azure AD パススルー認証を有効にするには、次の前提条件を満たしておく必要があります。

- 自分がグローバル管理者である Azure AD テナント。

>[!NOTE]
>オンプレミス サービスが失敗したり利用できなくなった場合にテナントの構成を管理できるように、グローバル管理者アカウントはクラウド専用のアカウントにすることを強くお勧めします。 クラウド専用のグローバル管理者アカウントを追加する手順については、[こちら](../active-directory-users-create-azure-portal.md)をご覧ください。

- Azure AD Connect バージョン 1.1.486.0 以降。 最新バージョンの [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) を使用することをお勧めします。
- Azure AD Connect を実行するための、Windows Server 2012 R2 以降を実行しているサーバー。
  - このサーバーは、パスワードを検証する必要があるユーザーと同じ AD フォレストのメンバーである必要があります。
  - パススルー認証コネクタは Azure AD Connect と同じサーバーにインストールされることに注意してください。 コネクタのバージョンが 1.5.58.0 以上であることを確認してください。

>[!NOTE]
>AD フォレスト間にフォレストの信頼があり、名前サフィックス ルーティングが正しく構成されていれば、複数フォレスト環境がサポートされます。

- 高可用性を実現する場合は、スタンドアロン コネクタ (1.5.58.0 以降のバージョンである必要があります) をインストールするために、Windows Server 2012 R2 以降を実行する追加のサーバーが必要になります。
- コネクタと Azure AD の間にファイアウォールがある場合は、次の点を確認してください。
    - URL フィルタリングが有効になっている場合は、コネクタが次の URL と通信できることを確認します。
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net
    - また、コネクタは [Azure データ センターの IP 範囲](https://www.microsoft.com/en-us/download/details.aspx?id=41653)に対して直接 IP 接続します。
    - コネクタがクライアント証明書を使用して Azure AD と通信する際に、ファイアウォールによって SSL 検査が実行されていないことを確認します。
    - コネクタがポート 80 およびポート 443 経由で Azure AD に対する送信要求を実行できることを確認します。
      - ファイアウォールが送信元ユーザーに応じて規則を適用している場合は、ネットワーク サービスとして実行されている Windows サービスからのトラフィックに対してこれらのポートを開放します。
      - コネクタは、SSL 証明書失効リストをダウンロードするために、ポート 80 で HTTP 要求を行います。 これは、自動更新機能が正常に機能するためにも必要です。
      - コネクタは、機能の有効化と無効化、コネクタの登録、コネクタ更新プログラムのダウンロード、すべてのユーザー サインイン要求の処理など、他のすべての操作についてはポート 443 で HTTPS 要求を行います。

>[!NOTE]
>コネクタがサービスと通信する際に必要なポートの数を減らすために、最近改善が行われました。 古いバージョンの Azure AD Connect やスタンドアロン コネクタを実行している場合は、その他のポート (5671、8080、9090、9091、9350、9352、10100 ～ 10120) を引き続き開いておく必要があります。

### <a name="enabling-azure-ad-pass-through-authentication"></a>Azure AD パススルー認証の有効化

Azure AD パススルー認証は、Azure AD Connect を使用して有効にすることができます。

Azure AD Connect の新しいインストールを実行する場合は、[カスタム インストール パス](active-directory-aadconnect-get-started-custom.md)を選択します。 [ユーザー サインイン] ページで [パススルー認証] を選択します。 正常に完了すると、Azure AD Connect と同じサーバーにパススルー認証コネクタがインストールされます。 また、テナントでパススルー認証機能が有効になります。

![Azure AD Connect - [ユーザー サインイン]](./media/active-directory-aadconnect-sso/sso3.png)

Azure AD Connect を既にインストールしている場合は、[高速インストール](active-directory-aadconnect-get-started-express.md) パスまたは[カスタム インストール](active-directory-aadconnect-get-started-custom.md) パスを使用してセットアップし、Azure AD Connect で [ユーザー サインインの変更] を選択して、[次へ] をクリックします。 次に、[パススルー認証] を選択して、Azure AD Connect と同じサーバーにパススルー認証コネクタをインストールし、テナントでこの機能を有効にします。

![Azure AD Connect - [ユーザー サインインの変更]](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Azure AD パススルー認証はテナント レベルの機能です。 テナント内のすべての管理対象ドメインのユーザー サインインに影響します。 ただし、フェデレーション ドメインのユーザーは、Active Directory フェデレーション サービス (ADFS) または既に構成済みのその他のフェデレーション プロバイダーを使用して引き続きサインインします。 ドメインをフェデレーションから管理対象に変換すると、そのドメインのすべてのユーザーが、パススルー認証を使用したサインインを自動的に開始します。 クラウドのみのユーザーはパススルー認証の影響を受けません。

### <a name="ensuring-high-availability"></a>高可用性の確保

パススルー認証を運用環境デプロイで使用することを計画している場合は、サインイン要求の高可用性を確保するために、別のサーバー (Azure AD Connect と最初のコネクタを実行しているサーバー以外) に 2 つ目のコネクタをインストールすることを強くお勧めします。 追加のコネクタは必要な数だけインストールできます。これは、テナントが処理するサインイン要求の最大数と平均数に基づきます。

スタンドアロン コネクタをデプロイするには、次の手順に従います。

#### <a name="step-1-download-and-install-the-connector"></a>手順 1: コネクタをダウンロードしてインストールする

この手順では、コネクタ ソフトウェアをサーバーにダウンロードしてインストールします。

1.    最新のコネクタを[ダウンロード](https://go.microsoft.com/fwlink/?linkid=837580)します。 コネクタのバージョンが 1.5.58.0 以上であることを確認してください。
2.    管理者としてコマンド プロンプトを開きます。
3.    次のコマンドを実行します (/q はサイレント インストールを意味します。このインストールでは、使用許諾契約書への同意を求めるメッセージは表示されません)。

`
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
3. コネクタは、標準の Windows API を使用して Active Directory に対してユーザー名とパスワードを検証します (ADFS で使用されているものと同様のメカニズム)。 ユーザー名には、オンプレミスの既定のユーザー名 (通常は "userPrincipalName") または Azure AD Connect で構成された別の属性 ("代替 ID" と呼ばれます) を指定できます。
4. オンプレミス ドメイン コントローラーが要求を評価し、コネクタに応答 (成功または失敗) を返します。
5. コネクタがこの応答を Azure AD に返します。
6. Azure AD が応答を評価し、ユーザーに適宜応答します。 たとえば、アプリケーションにトークンを発行したり、多要素認証を要求したりします。

次の図はさまざまな手順を示しています。 要求と応答はすべて HTTPS チャネル経由で行われます。

![パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### <a name="note-about-password-writeback"></a>パスワード ライトバックに関する注意事項

テナントで特定のユーザーに対して[パスワード ライトバック](../active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)を構成した場合は、ユーザーがパススルー認証を使用してサインインすると、ユーザーはこれまでと同様にパスワードを変更またはリセットできます。 パスワードはオンプレミス Active Directory に想定どおりに書き戻されます。

ただし、これらの条件のいずれかに該当しない場合 (テナントでパスワード ライトバックが構成されていない場合、またはユーザーに有効な Azure AD ライセンスが割り当てられていない場合)、パスワードの有効期限が切れた場合も含め、ユーザーはクラウドでパスワードを更新できなくなります。 代わりに、"このサイトでパスワードを変更することをお客様の組織が許可していません。 組織によって推奨されている方法でパスワードを更新するか、サポートが必要な場合は管理者にお問い合わせください。" というメッセージが表示されます。

## <a name="troubleshooting-pass-through-authentication"></a>パススルー認証のトラブルシューティング

このセクションでは、パススルー認証コネクタ (Azure AD Connect を使用またはスタンドアロン) のインストール時、登録時、またはアンインストール時に発生する一般的な問題に関するトラブルシューティング情報を提供します。 また、テナントで機能を有効にするときと機能の動作中に発生する問題についても説明します。

### <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>コネクタ (Azure AD Connect を使用またはスタンドアロン) のインストール時の問題

#### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Azure AD アプリケーション プロキシ コネクタが既に存在する

パススルー認証コネクタを [Azure AD アプリケーション プロキシ](../../active-directory/active-directory-application-proxy-get-started.md) コネクタと同じサーバーにインストールすることはできません。 パススルー認証コネクタは、別のサーバーにインストールする必要があります。

#### <a name="an-unexpected-error-occured"></a>予期しないエラーが発生する

サーバーから[コネクタのログを収集](#collecting-pass-through-authentication-connector-logs)し、問題について Microsoft サポートに連絡してください。

### <a name="issues-during-registration-of-connectors"></a>コネクタの登録時の問題

#### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>ポートがブロックされているため、コネクタを登録できない

コネクタがインストールされているサーバーが、[こちら](#pre-requisites)に記載されているサービス URL およびポートと通信できることを確認します。

#### <a name="registration-of-the-connector-failed-due-to-token-or-account-authorization-errors"></a>トークンまたはアカウント認証エラーのため、コネクタの登録に失敗した

すべての Azure AD Connect またはスタンドアロンコネクタのインストールおよび登録操作に、クラウド専用グローバル管理者アカウントを使用していることを確認します。 MFA 対応グローバル管理者アカウントには既知の問題があります。回避策として、MFA を一時的にオフにします (操作を完了するためのみ)。

#### <a name="an-unexpected-error-occurred"></a>予期しないエラーが発生する

サーバーから[コネクタのログを収集](#collecting-pass-through-authentication-connector-logs)し、問題について Microsoft サポートに連絡してください。

### <a name="issues-during-un-installation-of-connectors"></a>コネクタのアンインストール時の問題

#### <a name="warning-message-when-un-installing-azure-ad-connect"></a>Azure AD Connect のアンインストール時に警告メッセージが表示される

テナントでパススルー認証を有効にしている場合に、Azure AD Connect をアンインストールしようとすると、"Users will not be able to sign-in to Azure AD unless you have other pass-through authentication agents installed on other servers. (他のサーバーに他のパススルー認証エージェントがインストールされていない場合、ユーザーは Azure AD にサインインできなくなります。)" という警告メッセージ表示されます。

Azure AD Connect をアンインストールする前に、[高可用性](#ensuring-high-availability)を適切にセットアップして、ユーザー サインインが中断されないようにする必要があります。

### <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>パススルー認証機能の有効化に関する問題

#### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>使用できるコネクタがないため、機能を有効にすることができない

テナントでパススルー認証を有効にするには、アクティブなコネクタが少なくとも 1 つは必要です。 コネクタをインストールするには、Azure AD Connect またはスタンドアロン コネクタをインストールします。

#### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>ポートがブロックされているため、機能を有効にすることができない

Azure AD Connect がインストールされているサーバーが、[こちら](#pre-requisites)に記載されているサービス URL およびポートと通信できることを確認します。

#### <a name="the-enabling-of-the-feature-failed-due-to-token-or-account-authorization-errors"></a>トークンまたはアカウント認証エラーのため、機能の有効化に失敗した

機能を有効にする場合は、クラウド専用グローバル管理者アカウントを使用するようにします。 Multi-Factor Authentication (MFA) 対応グローバル管理者アカウントには既知の問題があります。回避策として、MFA を一時的にオフにします (操作を完了するためのみ)。

### <a name="issues-while-operating-the-pass-through-authentication-feature"></a>パススルー認証機能の動作中の問題

#### <a name="user-facing-sign-in-errors"></a>ユーザーに表示されるサインイン エラー

Azure AD サインイン画面でユーザーに表示されるエラーがあります。 これらのエラーの詳細と適切な解決手順を以下に示します。

|エラー|Description|解決策
| --- | --- | ---
|AADSTS80001|Unable to connect to Active Directory (Active Directory に接続できません)|コネクタ サーバーが、パスワードを検証する必要のあるユーザーと同じ AD フォレストのメンバーであり、Active Directory に接続できることを確認します。  
|AADSTS8002|A timeout occurred connecting to Active Directory (Active Directory への接続中にタイムアウトが発生しました)|Active Directory が使用可能であり、コネクタからの要求に応答していることを確認します。
|AADSTS80004|The username passed to the connector was not valid (コネクタに渡されたユーザー名が無効です)|サインインしようとしているユーザーのユーザー名が正しいことを確認してください。
|AADSTS80005|Validation encountered unpredictable WebException (検証で予測外の WebException が発生しました)|これは一時的なエラーの可能性があります。 要求をやり直してください。 引き続きエラーが発生する場合は、Microsoft サポートに連絡してください。
|AADSTS80007|An error occurred communicating with Active Directory (Active Directory との通信中にエラーが発生しました)|Check the connector logs for more information and verify that Active Directory is operating as expected. (コネクタ ログで詳細を確認し、Active Directory が期待通りに動作していることを確認してください)

### <a name="collecting-pass-through-authentication-connector-logs"></a>パススルー認証コネクタのログの収集

発生する問題の種類に応じて、異なる場所にあるパススルー認証コネクタのログを確認する必要があります。

#### <a name="connector-event-logs"></a>コネクタ イベント ログ

コネクタに関するエラーの場合、サーバーでイベント ビューアー アプリケーションを開き、**Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin** にあるログを確認します。

詳細な分析ログとデバッグ ログが必要な場合は、"セッション" ログを有効にすることができます。 通常の操作中に、このログを有効にした状態でコネクタを実行しないでください。これはトラブルシューティングにのみ使用します。 ログの内容は、ログを再度無効にした後にのみ表示されます。

#### <a name="detailed-trace-logs"></a>詳細なトレース ログ

ユーザー サインイン エラーのトラブルシューティングを行うときは、**C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace** にあるトレース ログを確認します。 これらのログには、パススルー認証機能を使用した特定のユーザー サインインが失敗した原因が記録されています。 ログ エントリの例を次に示します。

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

コマンド プロンプトを開き、次のコマンドを実行することで、エラー (上記の例では "1328") の詳細を取得できます。 注: "1328" は、ログに表示されている実際のエラー番号に置き換える必要があります。

`Net helpmsg 1328`

結果は次のようになります。

![パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

#### <a name="domain-controller-logs"></a>ドメイン コントローラーのログ

監査ログが有効になっている場合は、ドメイン コントローラーのセキュリティ ログで追加情報を確認できます。 パススルー認証コネクタから送信されたサインイン要求を簡単に照会する方法を次に示します。

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="feedback"></a>フィードバック

お客様のフィードバックは Microsoft にとって重要です。 [aadopauthfeedback@microsoft.com](mailto:aadopauthfeedback@microsoft.com) まで電子メールでお送りください。 新機能についてご要望があれば、[UserVoice フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)にお寄せください。

