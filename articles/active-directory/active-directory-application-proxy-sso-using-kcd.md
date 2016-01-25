<properties
	pageTitle="アプリケーション プロキシを使用したシングル サインオン | Microsoft Azure"
	description="Azure AD アプリケーション プロキシを使用してシングル サインオンを提供する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="kgremban"/>


# アプリケーション プロキシを使用したシングル サインオン

> [AZURE.NOTE]アプリケーション プロキシは、Azure Active Directory の Premium または Basic エディションにアップグレードしている場合にのみ利用できる機能です。詳細については、「[Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。

シングル サインオンは、Azure AD の重要な要素です。それは、最高のユーザー エクスペリエンスを提供します。このエクスペリエンスでは、ユーザーがクラウドにサインインすると、すべてのセキュリティ検証がクラウドで発生し (事前認証)、オンプレミス アプリケーションに要求が送信されるときにアプリケーション プロキシ コネクタがユーザーの代理となり、バックエンド アプリケーションは、それがドメイン参加デバイスからの正規ユーザーであると考えます。

![アプリケーション プロキシ経由のエンド ユーザーから企業ネットワークへのアクセス図](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Azure AD アプリケーション プロキシによって、ユーザーにシングル サインオン (SSO) エクスペリエンスを提供できます。SSO を使用するアプリを発行するには、次の手順に従います。


## オンプレミス IWA アプリケーションへの SSO のための KCD とアプリケーション プロキシの使用
統合 Windows 認証 (IWA) を使用してアプリケーションへのシングル サインオンができるようにするには、アプリケーション プロキシ コネクタがユーザーの代理でトークンを送受信できるように Active Directory で設定します。


### ネットワーク図

![Microsoft AAD 認証のフロー図](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

この図は、IWA を使用するオンプレミス アプリケーションにユーザーがアクセスしようとしたときの流れを説明するものです。全体的な流れは次のとおりです。

1. ユーザーは、オンプレミスのアプリケーションにアプリケーション プロキシをとおしてアクセスするための URL を入力します。
2. この要求がアプリケーション プロキシによって Azure AD 認証サービスにリダイレクトされて、事前認証が行われます。この時点で、Azure AD の認証および承認のポリシーのうち、該当するものが適用されます (たとえば多要素認証)。ユーザーの正当性が確認された場合は、Azure AD によってトークンが作成されてユーザーに送信されます。
3. ユーザーは、このトークンをアプリケーション プロキシに渡します。
4. アプリケーション プロキシはこのトークンを検証し、ユーザー プリンシパル名 (UPN) をトークンから取り出してから、要求、UPN、およびサービス プリンシパル名 (SPN) をコネクタに送信します。この送信は、二重認証済みのセキュリティで保護されたチャネルをとおして行われます。
5. コネクタは、オンプレミス AD との KCD (Kerberos の制約付き委任) ネゴシエーションを実行します。このときに、ユーザーの代理でアプリケーションに対する Kerberos トークンを取得します。
6. Active Directory は、そのアプリケーション用の Kerberos トークンをコネクタに送信します。
7. コネクタは、AD から受信した Kerberos トークンを使用して、元の要求をアプリケーション サーバーに送信します。
8. アプリケーションから応答がコネクタに送信されます。この応答はアプリケーション プロキシ サービスに返され、最終的にユーザーに返されます。

### 前提条件

1. アプリケーション (たとえば SharePoint Web アプリケーション) が統合 Windows 認証を使用するように設定されていることを確認します。詳細については、「[Kerberos 認証のサポートを有効にする](https://technet.microsoft.com/library/dd759186.aspx)」を参照してください。SharePoint の場合は、「[SharePoint 2013 で Kerberos 認証を計画する](https://technet.microsoft.com/library/ee806870.aspx)」を参照してください。
2. アプリケーションのサービス プリンシパル名を作成します。
3. コネクタを実行するサーバーと、発行しようとしているアプリケーションを実行するサーバーが、同じドメインに参加していることを確認します。ドメインへの参加の詳細については、「[コンピューターをドメインに参加させる](https://technet.microsoft.com/library/dd807102.aspx)」を参照してください。


### Active Directory の構成

Active Directory の構成は、アプリケーション プロキシ コネクタと公開されたサーバーが同じドメイン内にあるかどうかによって異なります。

#### 同じドメインにあるコネクタと公開されたサーバー

1. Active Directory で、**[ツール]**、**[ユーザーとコンピューター]** の順に移動します。
2. コネクタを実行しているサーバーを選択します。
3. 右クリックして、**[プロパティ]**、**[委任]** の順に選択します。
4. **[指定されたサービスへの委任でのみこのコンピューターを信頼する]** を選択し、**[このアカウントが委任された資格情報を提示できるサービス]** の下で、アプリケーション サーバーの SPN ID の値を追加します。
5. これで、アプリケーション プロキシ コネクタは、AD において、リストで定義されたアプリケーションに対してユーザーの代理となることができるようになります。

![Connector-SVR のプロパティ ウィンドウのスクリーン ショット](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### それぞれ異なるドメインにあるコネクタと公開されたサーバー

1. ドメイン間の KCD を使用するための前提条件の一覧については、「[ドメイン間の Kerberos の制約付き委任](https://technet.microsoft.com/library/hh831477.aspx)」を参照してください。
2. Windows 2012 R2 で、コネクタ サーバーの `principalsallowedtodelegateto` プロパティを使用してアプリケーション プロキシを有効にして、コネクタ サーバーを委任します。ここで、公開先サーバーは `sharepointserviceaccount` で、委任元サーバーは `connectormachineaccount` です。

		$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

		Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

		Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE]`sharepointserviceaccount` には、SPS コンピューター アカウントか、SPS アプリ プールの実行に使用されているサービス アカウントを指定できます。


### Azure クラシック ポータルの構成

1. 「[アプリケーション プロキシを使用したアプリケーションの発行](active-directory-application-proxy-publish.md)」で説明されている手順に従って、アプリケーションを発行します。**[事前認証方法]** で **[Azure Active Directory]** が選択されていることを確認してください。
2. アプリケーションがアプリケーションの一覧に表示されたら、アプリケーションを選択して **[構成]** をクリックします。
3. **[プロパティ]** の下で、**[内部認証方法]** を **[統合 Windows 認証]** に設定します。![高度なアプリケーションの構成](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. アプリケーション サーバーの**[内部アプリケーション SPN]** を入力します。この例では、公開されたアプリケーションの SPN は、http/lob.contoso.com です。  

>[AZURE.IMPORTANT]Azure Active Directory での UPN は、オンプレミスの Active Directory での UPN と同一であることが必要です。同一でない場合は事前認証ができなくなります。Azure AD がオンプレミスの AD と同期していることを確認してください。

| | |
| --- | --- |
| 内部認証方法 | 事前認証に Azure AD を使用する場合、内部認証方法を設定すると、ユーザーがこのアプリケーションへのシングル サインオン (SSO) を活用できるようになります。<br><br>アプリケーションで IWA を使用する場合、**[統合 Windows 認証]** (IWA) を選択すると、このアプリケーションに対する SSO が有効になるように Kerberos の制約付き委任 (KCD) を構成することができます。IWA を使用するアプリケーションは KCD を使用して構成する必要があります。そうしないと、アプリケーション プロキシでこれらのアプリケーションを発行できません。<br><br>アプリケーションで IWA を使用しない場合、**[なし]** を選択します。 |
| 内部アプリケーション SPN | これは、オンプレミスの Azure AD で構成されている、オンプレミス アプリケーションのサービス プリンシパル名 (SPN) です。SPN は、KCD を使用しているアプリケーションの Kerberos トークンを取得するために、アプリケーション プロキシ コネクタによって使用されます。 |


## Windows 以外のアプリの SSO
Azure AD Application Proxy での Kerberos 委任フローは、Azure AD がクラウドでユーザーを認証するときから始まります。要求がオンプレミスに到着すると、Azure AD アプリケーション プロキシ コネクタは、ローカルの Active Directory と交信することで、ユーザーに代わって Kerberos チケットを発行します。このプロセスは Kerberos の制約付き委任 (KCD) と呼ばれます。次のフェーズで、要求がこの Kerberos チケットを使用してバックエンド アプリケーションに送信されます。このような要求を送信する方法を定義するプロトコルはたくさんあります。ほとんどの Windows 以外のサーバーは Negotiate/SPNego を予期しています。これは Azure AD アプリケーション プロキシでサポートされるようになっています。

![Windows 以外の SSO の図](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### 部分的に委任された ID
Windows 以外のアプリケーションでは、ユーザー ID は、電子メール アドレス (username@domain) ではなく、ユーザー名またはソフトウェア アセット管理アカウント名の形式で取得するのが一般的です。これは、より包括的でドメインの重複がないことを保証する UPN を使用するほとんどの Windows ベースのシステムとは異なります。

このため、アプリケーション プロキシでは、Kerberos チケットに表示される ID をアプリケーションごとに選択できます。これらのオプションの一部は、電子メール アドレスの形式を受け入れないシステムに適しています。

![委任されたログイン ID パラメーターのスクリーンショット](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

部分 ID を使用する場合に、その ID が組織のすべてのドメインまたはフォレストで一意でない場合は、これらのアプリケーションを 2 つの異なるコネクタ グループを使用して 2 度発行できます。これは各アプリケーションのユーザーが異なり、そのコネクタを異なるドメインに参加させることができるためです。


## オンプレミス ID とクラウド ID が同一でない場合の SSO の操作
他に構成されていない限り、アプリケーション プロキシは、ユーザーのクラウド ID とオンプレミス ID は完全に同じであるとみなします。アプリケーションごとに、シングル サインオンを実行するときに使用する ID を構成できます。

この機能により、オンプレミス ID とクラウド ID が異なる多くの組織で、ユーザーに別のユーザー名とパスワードの入力を要求することなく、クラウドからオンプレミスのアプリに SSO させることができます。次のような組織が含まれます。

- 内部に複数のドメインがあり (joe@us.contoso.com、joe@eu.contoso.com)、クラウドに 1 つのドメインがある (joe@contoso.com)。

- 内部にルーティングできないドメイン名があり (joe@contoso.usa)、クラウドに法的なドメイン名がある。

- 内部でドメイン名を使用していない (joe)。

- オンプレミスとクラウドで異なるエイリアスを使用している。例: joe-johns@contoso.com と joej@contoso.com

これは、Windows 以外のバックエンド サーバーで非常に一般的なシナリオである電子メール アドレス形式のアドレスを受け入れないアプリケーションでも役立ちます。


### クラウド ID とオンプレミス ID が異なる場合の SSO の設定

1. Azure AD Connect の設定を、メイン ID が電子メール アドレス (mail) になるように構成します。これはカスタマイズ プロセスの一部として、同期設定の **[ユーザー プリンシパル名]** フィールドを変更することで実行します。これらの設定は、ユーザーが Office365、Windows10 デバイス、および Azure AD を ID ストアとして使用する他のアプリケーションにログインする方法も決定することに注意してください。![ユーザーを識別するスクリーンショット - [ユーザー プリンシパル名] ドロップダウン リスト](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. 変更するアプリケーションの [アプリケーションの構成] 設定で、使用する **[委任されたログイン ID]** を選択します。
  - ユーザー プリンシパル名: joe@contoso.com  
  - 代替ユーザー プリンシパル名: joed@contoso.local  
  - ユーザー プリンシパル名のユーザー名部分: joe  
  - 代替ユーザー プリンシパル名のユーザー名部分: joed  
  - オンプレミスのソフトウェア アセット管理アカウント名: オンプレミスのドメイン コント ローラーの構成に依存

  ![[委任されたログインID] ドロップダウン メニューのスクリーン ショット](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

### ID が異なる場合の SSO のトラブルシューティング
SSO プロセスにエラーがある場合は、「[トラブルシューティング](active-directory-application-proxy-troubleshoot.md)」で説明するように、コネクタ コンピューターのイベント ログに表示されます。ただし、場合によっては、バックエンド アプリケーションが他のさまざまなHTTP 応答に応答している間に、要求が正常に送信されることがあります。このような場合のトラブルシューティングは、アプリケーション プロキシ セッションのイベント ログで、コネクタ コンピューターのイベント番号 24029 を調べることから始める必要があります。委任のために使用されたユーザー ID は、イベント詳細の [ユーザー] フィールドに表示されます。セッション ログを有効にするには、イベント ビューアーで [表示] メニューの **[分析およびデバッグ ログの表示]** を選択します。


## 関連項目
アプリケーション プロキシを使ってできることは他にもたくさんあります。


- [アプリケーション プロキシを使用してアプリケーションを発行する](active-directory-application-proxy-publish.md)
- [独自のドメイン名でアプリケーションを発行する](active-directory-application-proxy-custom-domains.md)
- [条件付きアクセスを有効にする](active-directory-application-proxy-conditional-access.md)
- [要求に対応するアプリケーションを利用する](active-directory-application-proxy-claims-aware-apps.md)
- [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](active-directory-application-proxy-troubleshoot.md)

## アプリケーション プロキシに関するその他の情報
- [オンライン ヘルプ](active-directory-application-proxy-enable.md)
- [アプリケーション プロキシに関するブログ](http://blogs.technet.com/b/applicationproxyblog/)
- [Channel 9 ビデオ](http://channel9.msdn.com/events/Ignite/2015/BRK3864)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg

<!---HONumber=AcomDC_0114_2016-->