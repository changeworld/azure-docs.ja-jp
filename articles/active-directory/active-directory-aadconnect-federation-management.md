<properties
    pageTitle="Azure AD Connect による Active Directory フェデレーション サービスの管理とカスタマイズ | Microsoft Azure"
    description="Azure AD Connect を使用した AD FS の管理と、Azure AD Connect および PowerShell を使用したユーザー AD FS サインイン エクスペリエンスのカスタマイズ。"
    keywords="AD FS, ADFS, AD FS 管理, AAD Connect, Connect, サインイン, AD FS カスタマイズ, 信頼の修復, O365, フェデレーション, 証明書利用者"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>


# <a name="active-directory-federation-services-management-and-customization-with-azure-ad-connect"></a>Azure AD Connect による Active Directory フェデレーション サービスの管理とカスタマイズ

この記事では、Microsoft Azure Active Directory Connect を使って実行できる Active Directory フェデレーション サービス (AD FS) 関連のタスクと、AD FS ファームの完全な構成に必要になる可能性があるその他の一般的な AD FS タスクについて詳しく説明します。

| トピック | 内容 |
|:------|:-------------|
|**AD FS の管理**|
|[信頼を修復する](#repairthetrust)| Office 365 とのフェデレーション信頼の修復 |
|[AD FS サーバーを追加する](#addadfsserver) | 追加の AD FS サーバーによる AD FS ファームの拡張|
|[AD FS Web アプリケーション プロキシ サーバーを追加する](#addwapserver) | 追加の WAP サーバーによる AD FS ファームの拡張|
|[フェデレーション ドメインを追加します](#addfeddomain)| フェデレーション ドメインの追加|
| **AD FS のカスタマイズ**|
|[カスタムの会社のロゴまたはイラストを追加する](#customlogo)| 会社のロゴとイラストを使用した AD FS サインイン ページのカスタマイズ |
|[サインインの説明を追加する](#addsignindescription) | サインイン ページの説明の追加 |
|[AD FS の要求規則を変更する](#modclaims) | さまざまなフェデレーション シナリオに向けた AD FS 要求の変更 |

## <a name="ad-fs-management"></a>AD FS の管理

Azure AD Connect には、Azure AD Connect ウィザードを使用してユーザーの介入を最小限に抑えて実行できる、AD FS に関連したさまざまなタスクが用意されています。 ウィザードを実行して Azure AD Connect をインストールした後、もう一度ウィザードを実行するとその他のタスクを実行できます。

### <a name="repair-the-trust-<a-name=repairthetrust></a>"></a>信頼を修復する <a name=repairthetrust></a>

Azure AD Connect では、AD FS と Azure Active Directory の信頼の現在の正常性を確認し、信頼を修復するための適切な措置を講じることができます。 Azure AD と AD FS の信頼を修復するには、次の手順に従います。

1. 追加タスクの一覧から **[AAD と ADFS 信頼を修復します]** を選択します。
![[AAD と ADFS 信頼を修復します]](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

2. **[Azure AD に接続]** ページで、Azure AD の全体管理者の資格情報を入力し、**[次へ]** をクリックします。
![Azure への接続](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

3. **[リモート アクセスの資格情報]** ページで、ドメイン管理者の資格情報を入力します。
![[リモート アクセスの資格情報]](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

    **[次へ]**をクリックすると、Azure AD Connect によって証明書の正常性が確認され、問題がある場合はその問題が表示されます。

    ![State of certificates](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

    **[構成の準備完了]** ページに、信頼を修復するために実行されるアクションの一覧が表示されます。

    ![[構成の準備完了]](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

4. **[インストール]** をクリックして信頼を修復します。

>[AZURE.NOTE] Azure AD Connect では、自己署名されている証明書に対してのみ修復などのアクションを実行できます。 サード パーティの証明書を Azure AD Connect で修復することはできません。

### <a name="add-an-ad-fs-server-<a-name=addadfsserver></a>"></a>AD FS サーバーを追加する <a name=addadfsserver></a>

> [AZURE.NOTE] Azure AD Connect で AD FS サーバーを追加するには、PFX 証明書ファイルが必要です。 そのため、この操作を実行できるのは、Azure AD Connect を使用して AD FS ファームを構成した場合のみです。

1. **[追加のフェデレーション サーバーをデプロイします]** を選択し、**[次へ]** をクリックします。
![Additional federation server](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

2. **[Azure AD に接続]** ページで、Azure AD の全体管理者の資格情報を入力し、**[次へ]** をクリックします。
![Azure への接続](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

3. ドメイン管理者の資格情報を入力します。
![Domain administrator credentials](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

4. Azure AD Connect で新しい AD FS ファームを構成中に指定した PFX ファイルのパスワードを入力するよう求められます。 **[パスワードの入力]** をクリックし、PFX ファイルのパスワードを入力します。
![Certificate password](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

    ![[SSL 証明書の指定]](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

5. **[AD FS サーバー]** ページで、AD FS ファームに追加するサーバーの名前または IP アドレスを入力します。
![AD FS servers](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

6. **[次へ]** をクリックし、最後の **[構成]** ページに進みます。 Azure AD Connect によって AD FS ファームへのサーバーの追加が完了すると、接続を確認するためのオプションが表示されます。
![[構成の準備完了]](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

    ![インストールの完了](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### <a name="add-an-ad-fs-web-application-proxy-server-<a-name=addwapserver></a>"></a>AD FS Web アプリケーション プロキシ サーバーを追加する <a name=addwapserver></a>

> [AZURE.NOTE] Azure AD Connect で Web アプリケーション プロキシ サーバーを追加するには、PFX 証明書ファイルが必要です。 そのため、この操作を実行できるのは、Azure AD Connect を使用して AD FS ファームを構成した場合のみです。

1. 利用可能なタスクの一覧から **[Web アプリケーション プロキシのデプロイ]** を選択します。
![Deploy web application proxy](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

2. Azure の全体管理者の資格情報を入力します。
![Connect to Azure AD](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

3. **[SSL 証明書の指定]** ページで、Azure AD Connect で AD FS ファームの構成中に指定した PFX ファイルのパスワードを入力します。
![Certificate password](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

    ![[SSL 証明書の指定]](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

4. Web アプリケーション プロキシとして追加するサーバーを追加します。 Web アプリケーション プロキシ サーバーがドメインに参加していない可能性もあるため、ウィザードでは、追加するサーバーの管理資格情報を入力するよう求められます。
![Administrative server credentials](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

5. **[プロキシ信頼資格情報]** ページで、プロキシ信頼を構成して AD FS ファームのプライマリ サーバーにアクセスするために管理資格情報を入力します。
![[プロキシ信頼資格情報]](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

6. **[構成の準備完了]** ページに、実行されるアクションの一覧が表示されます。
![[構成の準備完了]](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

7. **[インストール]** をクリックして構成を終了します。 構成が完了すると、サーバーへの接続を確認するためのオプションが表示されます。 **[確認]** をクリックして接続を確認します。
![インストールの完了](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### <a name="add-a-federated-domain-<a-name=addfeddomain></a>"></a>フェデレーション ドメインを追加します <a name=addfeddomain></a>

Azure AD Connect を使用すると、Azure AD とのフェデレーションを設定するドメインを簡単に追加できます。 Azure AD Connect によりフェデレーション用のドメインが追加され、Azure AD とのフェデレーションを設定したドメインが複数ある場合に発行者を正しく反映するように要求規則が変更されます。

1. フェデレーション ドメインを追加するには、 **[Azure AD ドメインを追加します]**タスクを選択します。
![Additional Azure AD domain](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

2. ウィザードの次のページで、Azure AD の全体管理者の資格情報を入力します。
![Connect to Azure AD](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

3. **[リモート アクセスの資格情報]** ページで、ドメイン管理者の資格情報を入力します。
![[リモート アクセスの資格情報]](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

4. 次のページに、オンプレミスのディレクトリとのフェデレーションに使用できる Azure AD ドメインの一覧が表示されます。 一覧からドメインを選択します。
![Azure AD domain](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

    ドメインを選択すると、ウィザードによって実行されるさらなるアクションと構成の影響に関して適切な情報が表示されます。 Azure AD でまだ確認されていないドメインを選択すると、ドメインの確認に役立つ情報が表示される場合もあります。 詳細については、「 [Azure Active Directory へのカスタム ドメイン名の追加](active-directory-add-domain.md) 」を参照してください。

5. **[次へ]** をクリックします。**[構成の準備完了]** ページに、Azure AD Connect によって実行されるアクションの一覧が表示されます。 **[インストール]** をクリックして構成を終了します。
![[構成の準備完了]](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## <a name="ad-fs-customization"></a>AD FS のカスタマイズ

以下のセクションでは、AD FS サインイン ページをカスタマイズする際に必要になる可能性のある一般的なタスクの実行方法について詳しく説明します。

### <a name="add-a-custom-company-logo-or-illustration-<a-name=customlogo></a>"></a>カスタムの会社のロゴまたはイラストを追加する <a name=customlogo></a>

**サインイン** ページに表示される会社のロゴを変更するには、次の Windows PowerShell コマンドレットと構文を使用します。

> [AZURE.NOTE] ロゴについては、260 x 35 @ 96 dpi、ファイル サイズ 10 KB 以下をお勧めします。

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] *TargetName* パラメーターは必須です。 AD FS と共にリリースされている既定のテーマの名前は Default です。


### <a name="add-a-sign-in-description-<a-name=addsignindescription></a>"></a>サインインの説明を追加する <a name=addsignindescription></a>

**サインイン ページ**にサインイン ページの説明を追加するには、次の Windows PowerShell コマンドレットと構文を使用します。

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### <a name="modify-ad-fs-claim-rules-<a-name=modclaims></a>"></a>AD FS の要求規則を変更する <a name=modclaims></a>

AD FS では、カスタム要求規則の作成に使用できる、機能豊富な要求言語がサポートされています。 詳細については、「 [要求規則言語の役割](https://technet.microsoft.com/library/dd807118.aspx)」を参照してください。

以降のセクションでは、Azure AD と AD FS のフェデレーションに関連するいくつかのシナリオ向けにカスタム規則を作成する方法について説明します。

#### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>属性内に値が存在することを条件とする不変 ID

Azure AD Connect では、オブジェクトが Azure AD に同期されるときにソース アンカーとして使用される属性を指定できます。 カスタム属性内の値が空でない場合は、不変 ID 要求を発行することができます。 たとえば、**ms-ds-consistencyguid** をソース アンカーの属性として選択し、その属性に対応する値が含まれている場合は、**ImmutableID** を **ms-ds-consistencyguid** として発行するとします。 属性に対応する値が存在しない場合は、 **objectGuid** を不変 ID として発行します。  以下のセクションで説明する方法により、カスタム要求規則のセットを作成することができます。

**規則 1: 属性を照会する**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

この規則では、Active Directory のユーザーについて **ms-ds-consistencyguid** と **objectGuid** の値を照会します。 ストア名を、AD FS デプロイの適切なストア名に変更します。 要求の種類についても、**objectGuid** と **ms-ds-consistencyguid** に定義されているように、フェデレーションに適切な要求の種類に変更します。

また、**issue** ではなく **add** を使用することで、エンティティの発行を追加せずに、値を中間値として使用できます。 要求は、不変 ID として使用する値が確定してから、後の規則内で発行します。

**規則 2: ユーザーの ms-ds-consistencyguid が存在するかどうかを確認する**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

この規則では、ユーザーに対して **ms-ds-concistencyguid** が設定されていない場合に **useguid** に設定される、**idflag** という一時フラグを定義します。 この規則は、AD FS では空の要求が許可されないという事実に基づいて設計されています。 したがって、規則 1 に要求 http://contoso.com/ws/2016/02/identity/claims/objectguid と http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid を追加した場合、最終的に **msdsconsistencyguid** 要求が発行されるのは、ユーザーにこの値が設定されている場合のみです。 値が設定されていない場合、AD FS によって空の値を持つことになると判断され、すぐに削除されます。 すべてのオブジェクトは **objectGuid**を持つため、規則 1 が実行された後には、常に要求が存在することになります。

**規則 3: ms-ds-consistencyguid が存在する場合にそれを不変 ID として発行する**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

この規則は、暗黙的な **存在** 確認です。 要求の値が存在する場合は、それを不変 ID として発行します。 前の例では、 **nameidentifier** 要求を使用しています。 この要求は、お使いの環境の不変 ID に合わせて適切な種類の要求に変更してください。

**規則 4: ms-ds-consistencyGuid が存在しない場合に objectGuid を不変 ID として発行する**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

この規則では、一時フラグの **idflag**を確認します。 このフラグの値に基づき、要求を発行するかどうかを決定します。

> [AZURE.NOTE] これらの規則の順序は重要です。

#### <a name="sso-with-a-subdomain-upn"></a>サブドメイン UPN を使用した SSO

[新しいフェデレーション ドメインの追加](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain)に関するセクションで説明しているように、Azure AD Connect を使用して、フェデレーション対象の複数のドメインを追加できます。 フェデレーション ルート ドメインには子ドメインも含まれるため、発行者 ID がサブドメインではなく、ルート ドメインに対応するように、UPN 要求を変更する必要があります。

既定で、発行者の ID の要求規則は次のように設定されます。

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Default issuer ID claim](media\active-directory-aadconnect-federation-management\issuer_id_default.png)

既定の規則では、単に UPN サフィックスを取得し、発行者 ID 要求でそれを使用します。 たとえば、John は sub.contoso.com のユーザーであり、contoso.com には Azure AD とのフェデレーションが設定されています。 Azure AD へのサインイン時のユーザー名として、John が「 john@sub.contoso.com 」と入力すると、そのユーザー名は、AD FS での既定の発行者 ID 要求規則によって次のように処理されます。

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**要求の値:** http://sub.contoso.com/adfs/services/trust/

発行者の要求値にルート ドメインのみが含まれるようにするには、次のように要求規則を変更します。

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>次のステップ

[ユーザー サインイン オプション](active-directory-aadconnect-user-signin.md)の詳細を確認します。



<!--HONumber=Oct16_HO2-->


