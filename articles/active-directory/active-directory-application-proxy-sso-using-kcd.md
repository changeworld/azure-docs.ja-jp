<properties
	pageTitle="オンプレミス IWA アプリケーションへの SSO のための KCD とアプリケーション プロキシの使用"
	description="Azure AD アプリケーション プロキシをセットアップする方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="rkarlin"/>



# オンプレミス IWA アプリケーションへの SSO のための KCD とアプリケーション プロキシの使用


統合 Windows 認証 (IWA) を使用してアプリケーションへのシングル サインオン (SSO) ができるようにするには、アプリケーション プロキシ コネクタがユーザーの代理でトークンを送受信できるように Active Directory で設定します。

> [AZURE.IMPORTANT]アプリケーション プロキシは、Azure Active Directory の Premium または Basic エディションにアップグレードしている場合にのみ利用できる機能です。詳細については、「[Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。


## ネットワーク図

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


## Active Directory の構成

Active Directory の構成は、アプリケーション プロキシ コネクタと公開されたサーバーが同じドメイン内にあるかどうかによって異なります。

### 同じドメインにあるコネクタと公開されたサーバー



1. Active Directory で、**[ツール]**、**[ユーザーとコンピューター]** の順に移動します。 
2. コネクタを実行しているサーバーを選択します。 
3. 右クリックして、**[プロパティ]**、**[委任]** の順に選択します。 
4. **[指定されたサービスへの委任でのみこのコンピューターを信頼する]** を選択し、**[このアカウントが委任された資格情報を提示できるサービス]** の下で、アプリケーション サーバーのサービス プリンシパル名 (SPN) ID の値を追加します。 
5. これで、アプリケーション プロキシ コネクタは、AD において、リストで定義されたアプリケーションに対してユーザーの代理となることができるようになります。

![Connector-SVR のプロパティ ウィンドウのスクリーン ショット](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

### それぞれ異なるドメインにあるコネクタと公開されたサーバー

1. ドメイン間の KCD を使用するための前提条件の一覧については、「[ドメイン間の Kerberos の制約付き委任](https://technet.microsoft.com/library/hh831477.aspx)」を参照してください。
2. Windows 2012 R2 では、コネクタ サーバー上で `principalsallowedtodelegateto` プロパティを使用し、コネクタ サーバーで委任するアプリケーション プロキシを有効にします。ここで、公開されたサーバーは `sharepointserviceaccount` で、委任サーバーは `connectormachineaccount` です。

		$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

		Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

		Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE]`sharepointserviceaccount` には、SPS コンピューター アカウントか、SPS アプリ プールの実行に使用されているサービス アカウントを指定できます。


## Azure ポータルの構成

1. 「[アプリケーション プロキシを使用したアプリケーションの発行](active-directory-application-proxy-publish.md)」で説明されている手順に従って、アプリケーションを発行します。**[事前認証方法]** で **[Azure Active Directory]** が選択されていることを確認してください。
2. アプリケーションがアプリケーションの一覧に表示されたら、アプリケーションを選択して **[構成]** をクリックします。
3. **[プロパティ]** の下で、**[内部認証方法]** を **[統合 Windows 認証]** に設定します。

![高度なアプリケーションの構成](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)

4. アプリケーション サーバーの**内部アプリケーション SPN** を入力します。この例では、公開されたアプリケーションの SPN は、http/lob.contoso.com です。

>[AZURE.IMPORTANT]Azure Active Directory での UPN は、オンプレミスの Active Directory での UPN と同一であることが必要です。同一でない場合は事前認証ができなくなります。Azure Active Directory がオンプレミスの Active Directory と同期していることを確認してください。

| | |
| --- | --- |
| 内部認証方法 | 事前認証に Azure AD を使用する場合、内部認証方法を設定すると、ユーザーがこのアプリケーションへのシングル サインオン (SSO) を活用できるようになります。<br><br>アプリケーションで IWA を使用する場合、**[統合 Windows 認証]** (IWA) を選択すると、このアプリケーションに対する SSO が有効になるように Kerberos の制約付き委任 (KCD) を構成することができます。IWA を使用するアプリケーションは KCD を使用して構成する必要があります。そうしないと、アプリケーション プロキシでこれらのアプリケーションを発行できません。<br><br>アプリケーションで IWA を使用しない場合、**[なし]** を選択します。 |
| 内部アプリケーション SPN | これは、オンプレミスの Azure AD で構成されている、内部アプリケーションのサービス プリンシパル名 (SPN) です。SPN は、KCD を使用しているアプリケーションの Kerberos トークンを取得するために、アプリケーション プロキシ コネクタによって使用されます。 |

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg

<!---HONumber=Sept15_HO3-->