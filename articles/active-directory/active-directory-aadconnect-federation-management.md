<properties
	pageTitle="Azure AD Connect による Active Directory フェデレーション サービスの管理とカスタマイズ | Microsoft Azure"
	description="Azure AD Connect を使用した AD FS の管理と、Azure AD Connect と PowerShell を使用したユーザー AD FS サインイン エクスペリエンスのカスタマイズ。"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="anandy"/>

# Azure AD Connect による Active Directory フェデレーション サービスの管理とカスタマイズ

この記事では、Azure AD Connect を使って実行できるさまざまな Active Directory フェデレーション サービス関連のタスクと、AD FS ファームの完全な構成に必要になる可能性があるその他の一般的な AD FS タスクについて説明します。

## AD FS の管理

Azure AD Connect には、Azure AD Connect ウィザードを使用してユーザーの介入を最小限に抑えて実行できるさまざまな AD FS 関連のタスクが用意されています。ウィザードを実行して Azure AD Connect をインストールした後、もう一度ウィザードを実行するとその他のタスクを実行できます。

### 信頼の修復

Azure AD Connect では、AD FS と Azure AD の信頼の現在の正常性を確認し、信頼を修復するための適切な操作を実行することができます。Azure AD および AD FS の信頼を修復するには、次の手順に従います。

利用可能なタスクの一覧から **[AAD と ADFS 信頼を修復します]** を選択します。

![](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

**[Azure AD に接続]** ページで、Azure AD のグローバル管理者の資格情報を入力し、[次へ] をクリックします。

![](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

**[リモート アクセスの資格情報]** ページで、ドメイン管理者の資格情報を入力します。

![](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

[次へ] をクリックすると、Azure AD Connect によって証明書の正常性が確認され、問題がある場合はその問題が表示されます。

![](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

**[構成の準備完了]** ページに、信頼を修復するために実行されるアクションの一覧が表示されます。

![](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

[インストール] をクリックし、処理を続行して信頼を修復します。

>[AZURE.NOTE] Azure AD Connect で修復または対処できるのは、自己署名されている証明書のみです。サード パーティの証明書は Azure AD Connect で修復できません。

### 新しい AD FS サーバーの追加

> [AZURE.NOTE] Azure AD Connect で AD FS サーバーを追加するには、PFX 証明書ファイルが必要です。そのため、この操作を実行できるのは、Azure AD Connect を使用して AD FS ファームを構成した場合のみです。

**[追加のフェデレーション サーバーをデプロイします]** を選択し、[次へ] をクリックします。

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

**[Azure AD に接続]** ページで、Azure AD のグローバル管理者の資格情報を入力し、[次へ] をクリックします。

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

次のページで、ドメイン管理者の資格情報を入力します。

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

次のページでは、Azure AD Connect で新しい AD FS ファームを構成中に指定した pfx ファイルのパスワードを入力するよう要求されます。[パスワードの入力] をクリックし、PFX ファイルのパスワードを入力します。

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

次のページで、AD FS ファームに追加するその他のサーバーの名前または IP アドレスを指定します。

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

[次へ] をクリックして最後の [構成] ページに進みます。Azure AD Connect によって AD FS ファームへのサーバーの追加が完了すると、接続を確認するためのオプションが表示されます。

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### 新しい AD FS Web アプリケーション プロキシ サーバーの追加

> [AZURE.NOTE] Azure AD Connect で Web アプリケーション プロキシ サーバーを追加するには、PFX 証明書ファイルが必要です。そのため、この操作を実行できるのは、Azure AD Connect を使用して AD FS ファームを構成した場合のみです。

利用可能なタスクの一覧から **[Web アプリケーション プロキシのデプロイ]** を選択します。

![](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

次のページで、Azure のグローバル管理者の資格情報を入力します。

![](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

次に、**[SSL 証明書の指定]** ページが表示されます。ここでは、Azure AD Connect で AD FS ファームを構成中に指定した PFX ファイルのパスワードを入力する必要があります。

![](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

![](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

次のページでは、Web アプリケーション プロキシとして追加するサーバーを追加します。Web アプリケーション プロキシ サーバーはドメインに参加している場合もあれば参加していない場合もあるため、ウィザードでは、追加されているサーバーの管理資格情報を入力するよう求められます。

![](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

**[プロキシ信頼資格情報]** ページで、プロキシ信頼を構成して AD FS ファームのプライマリ サーバーにアクセスするために管理資格情報を入力します。

![](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

**[構成の準備完了]** ページに、実行されるアクションの一覧が表示されます。

![](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

[インストール] をクリックして構成を終了します。構成が完了すると、サーバーへの接続を確認するためのオプションが表示されます。[確認] をクリックして接続を確認します。

![](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### 新しいフェデレーション ドメインの追加

Azure AD Connect を使用して Azure AD とフェデレーションする新しいドメインを追加することは簡単です。Azure AD Connect は、新しいドメインをフェデレーション用に追加するだけでなく、Azure AD とフェデレーションされたドメインが複数あるときに発行者を正しく反映するために、要求規則を変更します。

新しいフェデレーション ドメインを追加するには、**[Azure AD ドメインを追加します]** タスクを選択します。

![](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

ウィザードの次のページで、Azure AD のグローバル管理者の資格情報を入力します。

![](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

[リモート アクセスの資格情報] ページで、ドメイン管理者の資格情報を入力します。

![](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

次のページに、オンプレミスのディレクトリをフェデレーションする Azure AD ドメインの一覧が表示されます。一覧からドメインを選択します。

![](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

ドメインを選択すると、ウィザードによって実行されるさらなるアクションと構成の影響に関して適切な情報が表示されます。Azure AD でまだ確認されていないドメインを選択すると、ドメインを確認するのに役立つ情報が表示される場合もあります。ドメインを確認する方法の詳細については、「[Azure Active Directory へのカスタム ドメイン名の追加](active-directory-add-domain.md)」を参照してください。

[次へ] をクリックします。**[構成の準備完了]** ページに、Azure AD Connect によって実行されるアクションの一覧が表示されます。[インストール] をクリックして構成を終了します。

![](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## AD FS のカスタマイズ

以下のセクションでは、AD FS サインイン ページをカスタマイズするための一般的なタスクを実行する方法について詳しく説明します。これらのタスクは、必要に応じて実行してください。

### カスタムの会社のロゴまたはイラストの追加

サインイン ページに表示される会社のロゴを変更するには、次の Windows PowerShell コマンドレットと構文を使用します。

> [AZURE.NOTE] ロゴについては、260 x 35、96 dpi、ファイル サイズ 10 KB 以下をお勧めします。

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] TargetName パラメーターは必須です。AD FS と共にリリースされている既定のテーマの名前は default です。
 

### サインインの説明の追加

サインイン ページにサインイン ページの説明を追加するには、次の Windows PowerShell コマンドレットと構文を使用します。

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### AD FS の要求規則の変更

AD FS には、要求を発行するためのカスタム規則を指定するオプションがあります。AD FS では、カスタム要求規則の作成に使用できる、優れた要求言語がサポートされています。詳細については、[こちらの記事](https://technet.microsoft.com/library/dd807118.aspx)を参照してください。

以降のセクションでは、Azure AD と AD FS のフェデレーションに関連するいくつかのシナリオに対してカスタム規則を作成する方法について詳しく説明します。

#### 属性に存在する値を条件とする不変 ID

Azure AD Connect では、オブジェクトが Azure AD と同期されるときにソース アンカーとして使用する属性を指定できます。カスタム属性の値が空でない場合に、条件に応じて不変 ID 要求を発行することが可能です。たとえば、ms-ds-consistencyguid をソース アンカーの属性として選択したとします。それとは対照的な値が属性に設定されている場合は ImmutableID を ms-ds-consistencyguid として発行し、それ以外の場合は objectGuid を不変 ID として発行することを検討してください。この場合、カスタム要求規則のセットを次のように作成することができます。

**規則 1: 属性を照会する**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

この規則では、Active Directory のユーザーについて ms-ds-consistencyguid と objectguid の値を照会するだけです。ストア名を ADFS デプロイで使用できる適切なストア名に変更し、要求の種類を、objectGUID と ms ds-consistencyguid に対して定義されているフェデレーション内の適切な要求の種類に変更してください。ここでは、テスト環境のカスタムの要求の種類を定義しました。

また、"issue" ではなく "add" を使用することで、エンティティの発行を追加せずに、値を単に中間値として使用しています。要求は、不変 ID として使用する値が確立されてから、後の規則内で発行します。

**規則 2: ユーザーの ms-ds-consistencyguid が存在するかどうかを確認する**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

この規則では、ユーザーの ms-ds-concistencyguid が設定されていない場合に "useguid" に設定される一時的なフラグ "idflag" を定義するだけです。この背後にあるロジックとして、ADFS では空の要求が許可されないという事実があります。したがって、規則 1 の要求 http://contoso.com/ws/2016/02/identity/claims/objectguid と http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid を追加した場合、最終的に msdsconsistencyguid 要求が発行されるのは、ユーザーの値が設定されている場合のみです。値が設定されていない場合、ADFS はこれを空の値と見なし、直ちに破棄します。ご存じのとおり、すべてのオブジェクトは ObjectGuid を保持するため、規則 1 が実行されると、要求は必ず存在します。

**規則 3: ms-ds-consistencyGuid が存在する場合にそれを不変 ID として発行する**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

これは、暗黙の存在確認です。要求の値が存在する場合は、それを不変 ID として発行します。ここでは nameidentifier 要求を発行しています。要求の種類は、自分の環境の不変 ID に合わせて適切に変更してください。

**規則 4: ms-ds-consistencyGuid が存在しない場合に ObjectGuid を不変 ID として発行する**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

この規則では、一時フラグ "idflag" を確認し、その値に基づいて、要求を発行するかどうかを決定するだけです。

> [AZURE.NOTE] これらの規則の順序は重要です。

#### サブドメイン UPN を持つ SSO

Azure AD Connect を使用して、フェデレーションする 1 つ以上のドメインを追加できます ([新しいフェデレーション ドメインの追加](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain))。フェデレーション ルート ドメインは子も含むため、発行者 ID がサブドメインではなく、ルート ドメインに対応するように、UPN 要求を変更する必要があります。

既定で、発行者の ID の要求規則は次のように設定されます。

	c:[Type 
	== “http://schemas.xmlsoap.org/claims/UPN“]

	=> issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![既定の発行者 id 要求](media\active-directory-aadconnect-federation-management\issuer_id_default.png)

既定の規則では、単に UPN サフィックスを取得し、発行者 id 要求でそれを使用します。たとえば、John は sub.contoso.com のユーザーであり、contoso.com は Azure AD とフェデレーションします。John は Azure AD にサインインするときに、ユーザー名として、john@sub.contoso.com と入力すると、AD FS で既定の発行者 id 要求規則によってそれが次のように処理されます。

c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

=> issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**要求値:** http://sub.contoso.com/adfs/services/trust/

発行者要求値にルート ドメインだけを指定するには、要求規則を次のように変更します。

	c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

	=> issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## 次のステップ

[ユーザー サインイン オプション](active-directory-aadconnect-user-signin.md)の詳細を確認する

<!---HONumber=AcomDC_0511_2016-->