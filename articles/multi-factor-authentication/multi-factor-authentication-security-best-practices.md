<properties 
	pageTitle="Azure MFA を使用するためのセキュリティのベスト プラクティス"
	description="このドキュメントでは、Azure アカウントで Azure MFA を使用する場合のベスト プラクティスについて説明します。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# Azure AD アカウントで Azure Multi-Factor Authentication を使用するためのセキュリティのベスト プラクティス

認証プロセスの強化に関して、ほとんどの組織には多要素認証を選択することをお勧めします。Azure Multi-Factor Authentication (MFA) を使用すると、企業はセキュリティやコンプライアンスの要件を満たすと同時に、ユーザーに簡単なサインイン エクスペリエンスを提供することができます。この記事では、Azure MFA の導入を計画する際に考慮すべきベスト プラクティスをいくつか紹介します。

## クラウドの Azure Multi-Factor Authentication のベスト プラクティス
すべてのユーザーに多要素認証を提供し、Azure Multi-Factor Authentication に用意された拡張機能を利用するために、すべてのユーザーに対して Azure Multi-Factor Authentication を有効にする必要があります。これを行うには、次のいずれかを使用します。

- Azure AD Premium または Enterprise Mobility Suite
- 多要素認証プロバイダー

### Azure AD Premium/Enterprise Mobility Suite

![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Azure AD Premium または Enterprise Mobility Suite を使用してクラウドに Azure MFA を導入するには、まず、ユーザーにライセンスを割り当てることをお勧めします。Azure Multi-Factor Authentication はこれらのスイートに含まれるため、組織は、多要素認証機能をすべてのユーザーに展開する以外に追加操作は必要ありません。

Multi-Factor Authentication を設定する場合は、次の点を考慮してください。

- 多要素認証プロバイダーを作成する必要はありません。Azure AD Premium と Enterprise Mobility Suite には、Azure Multi-Factor Authentication が付属しています。認証プロバイダーを作成すると、二重に課金される場合があります。
- Azure AD Connect は、オンプレミスの Active Directory 環境を Azure AD ディレクトリと同期している場合にのみ必要です。オンプレミスの Active Directory インスタンスと同期されない Azure AD ディレクトリのみを使用する場合、Azure AD Connect は必要ありません。


### Multi-Factor Auth Provider

![Multi-Factor Auth Provider](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Azure AD Premium またはEnterprise Mobility Suite がない場合、クラウドに Azure MFA を導入するには、まず、MFA 認証プロバイダーを作成することをお勧めします。MFA は、Azure Active Directory を使用しているグローバル管理者が既定で使用できますが、組織に MFA をデプロイする際に、多要素認証機能をすべてのユーザーに展開する必要があります。これを行うには、多要素認証プロバイダーが必要です。認証プロバイダーを選択する場合は、ディレクトリを選択して次の点を考慮する必要があります。

- Multi-Factor Auth Provider の作成に、Azure AD ディレクトリは必要ありません。
- 多要素認証をすべてのユーザーに展開する場合や、グローバル管理者が管理ポータル、カスタムの案内応答、レポートなどの機能を利用できるようにする場合は、多要素認証プロバイダーを Azure AD ディレクトリに関連付ける必要があります。
- DirSync または AAD Sync は、オンプレミスの Active Directory 環境を Azure AD ディレクトリと同期している場合にのみ必要です。Active Directory のオンプレミスのインスタンスと同期されない Azure AD ディレクトリのみを使用する場合、DirSync または AAD Sync は必要ありません。
- Azure AD Premium または Enterprise Mobility Suite がある場合、Multi-Factor Auth Provider を作成する必要はありません。必要なのは、ユーザーにライセンスを割り当てることだけです。その後、ユーザーの MFA の有効化を開始できます。
- 必ず (認証または有効なユーザーごとに) ビジネスに適した使用モデルを選んでください。使用モデルはいったん選択すると変更できません。

### ユーザー アカウント
ユーザーの MFA を有効にすると、ユーザー アカウントは 3 つの主要な状態 (無効、有効、強制) のいずれかになります。次のガイドラインを使用して、デプロイに最適なオプションを使用していることを確認してください。

- ユーザーの状態が無効に設定されている場合、そのユーザーは多要素認証を使用していません。これが既定の状態です。
- ユーザーの状態が有効に設定されている場合、そのユーザーは有効ですが、登録プロセスが完了していないことを意味します。次回サインインするときにプロセスを完了するよう求められます。この設定は、ブラウザー以外のアプリには影響しません。登録プロセスが完了するまで、すべてのアプリが機能し続けます。
- ユーザーの状態が強制に設定されている場合、そのユーザーが登録を完了しているかどうかがわかりません。登録プロセスが完了している場合、多要素認証を使用しています。登録プロセスが完了していない場合は、次回サインインするときに登録プロセスを完了するよう求められます。この設定は、ブラウザー以外のアプリに影響します。アプリのパスワードを作成して使用するまで、これらのアプリは機能しません。
- 「[クラウドでの Azure Multi-Factor Authentication の概要](multi-factor-authentication-get-started-cloud.md)」で入手できるユーザー通知テンプレートを使用して、MFA の導入に関する電子メールをユーザーに送信します。

### サポート

ユーザーの大半は、パスワードのみを使用した認証に慣れているため、企業はこのプロセスに関してすべてのユーザーに認識してもらうことが重要です。このような認識により、ユーザーが MFA 関連の重要でない問題についてヘルプ デスクに問い合わせる可能性は低くなります。ただし、一部のシナリオでは MFA を一時的に無効にすることが必要になる場合があります。これらのシナリオへの対処方法を理解するには、次のガイドラインに従ってください。

- モバイル アプリや携帯電話が通知や電話呼び出しを受信せず、これが理由でユーザーがサインインできないシナリオに対処するためのトレーニングをテクニカル サポート担当者が受講済みであることを確認します。テクニカル サポート担当者は、ワンタイム バイパス オプションを有効にし、多要素認証を "バイパス" することによってユーザーを 1 回だけ認証できるようにすることができます。バイパスは一時的なものであり、指定された秒数が経過すると無効になります。
- 必要に応じて、Azure MFA で信頼できる IP 機能を利用できます。この機能を使用すると、管理者常駐型テナントまたはフェデレーション テナントの管理者は、会社のローカル イントラネットからサインインするユーザーの多要素認証をバイパスできます。この機能は、Azure AD Premium、Enterprise Mobility Suite、または Azure Multi-Factor Authentication ライセンスを持っている Azure AD テナントで使用できます。


## オンプレミスの Azure Multi-Factor Authentication のベスト プラクティス
企業が独自のインフラストラクチャを利用して MFA を有効にすることを決定した場合は、Azure Multi-Factor Authentication Server をオンプレミスでデプロイする必要があります。MFA Server のコンポーネントを次の図に示します。

![Multi-Factor Auth Provider](./media/multi-factor-authentication-security-best-practices/server.png) *既定ではインストールされていません**既定ではインストールされていますが、有効になっていません。


Azure Multi-Factor Authentication Server を使用すると、Azure AD アカウントがアクセスするクラウドのリソースとオンプレミスのリソースをセキュリティで保護することができます。ただし、これを実行できるのは、フェデレーションを使用した場合のみです。つまり、AD FS を用意し、それを Azure AD テナントとフェデレーションしておく必要があります。Multi-Factor Authentication Server を設定する場合は、次の点を考慮してください。

- Active Directory フェデレーション サービスを使用して Azure AD リソースをセキュリティで保護している場合、認証の第 1 要素は AD FS を使用してオンプレミスで実行され、第 2 要素は要求を受け入れることによりオンプレミスで実行されます。
- Azure Multi-Factor Authentication Server を AD FS フェデレーション サーバーにインストールすることは必須ではありませんが、AD FS 用の Multi-Factor Authentication Adapter は、AD FS を実行する Windows Server 2012 R2 にインストールする必要があります。サポートされているバージョンであれば、サーバーを別のコンピューターにインストールすることができ、AD FS アダプターを AD FS フェデレーション サーバーに別個にインストールすることができます。アダプターを別個にインストールする手順については、以下の手順を参照してください。
- Multi-Factor Authentication AD FS Adapter インストール ウィザードは、PhoneFactor Admins という名前のセキュリティ グループを Active Directory に作成し、フェデレーション サービスの AD FS サービス アカウントをこのグループに追加します。ドメイン コントローラーで、PhoneFactor Admins グループが本当に作成されていること、および AD FS サービス アカウントがこのグループのメンバーであることを確認するようお勧めします。必要に応じて、AD FS サービス アカウントをドメイン コント ローラーの PhoneFactor Admins グループに手動で追加します。

### ユーザー ポータル
このポータルはインターネット インフォメーション サーバー (IIS) Web サイトで動作します。ここでは、セルフ サービス機能を使用できるほか、ユーザー管理機能一式が用意されています。このコンポーネントを構成するには、次のガイドラインに従ってください。

- IIS 6 以降が必要です。
- ASP.NET v2.0.507207 をインストールして登録する必要があります。
- このサーバーは境界ネットワークにデプロイできます。



### アプリ パスワード
組織が SSO を使用して Azure AD とフェデレーションされているときに Azure MFA を使用する場合は、アプリ パスワードを使用する際に、次の点に注意してください (これは、フェデレーション (SSO) を使用する場合にのみ当てはまります)。

- アプリ パスワードは Azure AD によって検証されます。したがってフェデレーションをバイパスします。フェデレーションは、アプリ パスワードを設定するときにのみ使用されます。
- フェデレーション (SSO) ユーザーの場合、パスワードは組織 ID の中に保存されます。ユーザーが退職した場合、その情報は、DirSync を使用してリアルタイムで 組織 ID に送信される必要があります。アカウントの無効化/削除を同期させるには最大 3 時間かかる可能性があり、Azure AD 内のアプリ パスワードの無効化/削除が遅れることがあります。
- オンプレミスのクライアント アクセス制御設定は、アプリ パスワードには適用されません。
- アプリ パスワードに対するオンプレミス以外の認証ログ/監査機能はありません。
- Microsoft Lync 2013 クライアントには、より多くのエンドユーザー教育が必要です。
- ある種の高度なアーキテクチャ設計では、多要素認証をクライアントで使用するときに、認証場所によっては、組織のユーザー名とパスワードをアプリ パスワードと組み合わせて使用する必要があります。オンプレミスのインフラストラクチャに対して認証するクライアントの場合は、組織のユーザー名とパスワードを使用します。Azure AD に対して認証するクライアントはアプリケーション パスワードを使用します。
- 既定では、ユーザーがアプリ パスワードを作成することはできません。企業がそれを要求する場合、またはシナリオによってアプリ パスワードの作成をユーザーに許可する必要がある場合は、[ブラウザーではないアプリケーションへのサインイン用にアプリケーション パスワードの作成を許可する] オプションが選択されていることを確認します。

## 追加の考慮事項
オンプレミスでデプロイされる各コンポーネントの追加の考慮事項とベスト プラクティスについては、次の一覧を参照してください。

メソッド|Description
:------------- | :------------- |
[Active Directory フェデレーション サービス](multi-factor-authentication-get-started-adfs.md)|AD FS による Azure Multi-Factor Authentication の設定に関する情報です。
[RADIUS 認証](multi-factor-authentication-get-started-server-radius.md)| RADUIS による Azure MFA Server のセットアップと構成に関する情報です。
[IIS 認証](multi-factor-authentication-get-started-server-iis.md)|IIS を使用した Azure MFA Server のセットアップと構成に関する情報です。
[Windows 認証](multi-factor-authentication-get-started-server-windows.md)| Windows 認証による Azure MFA Server のセットアップと構成に関する情報です。
[LDAP 認証](multi-factor-authentication-get-started-server-ldap.md)|LDAP 認証による Azure MFA Server のセットアップと構成に関する情報です。
[RADIUS を使用したリモート デスクトップ ゲートウェイと Multi-Factor Authentication Server](multi-factor-authentication-get-started-server-rdg.md)| RADIUS を使用したリモート デスクトップ ゲートウェイによる Azure MFA Server のセットアップと構成に関する情報です。
[Windows Server Active Directory との同期](multi-factor-authentication-get-started-server-dirint.md)|Active Directory と Azure MFA Server 間の同期のセットアップと構成に関する情報です。
[Azure Multi-Factor Authentication Server モバイル アプリ Web サービスのデプロイ](multi-factor-authentication-get-started-server-webservice.md)|Azure MFA Server Web サービスのセットアップと構成に関する情報です。
[Azure Multi-Factor Authentication を使用した高度な VPN の構成](multi-factor-authentication-advanced-vpn-configurations.md)|Cisco ASA、Citrix Netscaler、Juniper/Pulse Secure などの VPN アプライアンスの LDAP または RADIUS を使用した構成に関する情報です。


## その他のリソース
この記事では Azure MFA のベスト プラクティスに重点を置いて説明しましたが、このほかにも、MFA のデプロイを計画する際に利用できるリソースがあります。このプロセスで役立つ重要な記事を次に示します。

- [Azure Multi-Factor Authentication のレポート](multi-factor-authentication-manage-reports.md)
- [Azure Multi-Factor Authentication のセットアップ エクスペリエンス](multi-factor-authentication-end-user-first-time.md)
- [Azure Multi-Factor Authentication についてよく寄せられる質問 (FAQ)](multi-factor-authentication-faq.md)

<!---HONumber=AcomDC_0921_2016-->