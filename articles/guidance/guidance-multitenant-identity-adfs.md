<properties
   pageTitle="顧客の AD FS とのフェデレーション | Microsoft Azure"
   description="マルチテナント アプリケーションで顧客の AD FS とのフェデレーションを行う方法"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# 顧客の AD FS とのフェデレーション

この記事は[シリーズの一部]です。このシリーズに付属する完成した[サンプル アプリケーション]もあります。

この記事では、顧客の AD FS とのフェデレーションを行うために、マルチテナント SaaS アプリケーションで Active Directory フェデレーション サービス (AD FS) を使用して認証をサポートする方法について説明します。

## 概要

Azure Active Directory (Azure AD) を使用すると、Office365 や Dynamics CRM Online の顧客を含め、Azure AD テナントのユーザーによるサインインが簡単になります。では、企業のイントラネットでオンプレミスの Active Directory を使用する顧客についてはどうでしょうか。

このような顧客には、[Azure AD Connect] を使用してオンプレミスの AD と Azure AD を同期するという方法があります。ただし、企業の IT ポリシーやその他の理由により、この方法を使用できない顧客も存在します。そのような場合には、Active Directory フェデレーション サービス (AD FS) を使用してフェデレーションを行う方法もあります。

このシナリオを実施するには:

-	顧客がインターネットに接続された AD FS ファームを持っている必要があります。
-	SaaS プロバイダーが独自の AD FS ファームをデプロイします。
-	顧客と SaaS プロバイダーは、[フェデレーションの信頼]を設定する必要があります。この設定は手動で行います。

信頼関係には、3 つの主要なロールがあります。

-	顧客の AD FS は[アカウント パートナー]です。顧客の AD でユーザーを認証し、ユーザー要求が含まれるセキュリティ トークンを作成します。
-	SaaS プロバイダーの AD FS は[リソース パートナー]です。アカウント パートナーを信頼し、ユーザー要求を受け取ります。
-	アプリケーションは、SaaS プロバイダーの AD FS で証明書利用者 (RP) として構成されます。

	![Federation trust](media/guidance-multitenant-identity/federation-trust.png)

> [AZURE.NOTE] この記事では、アプリケーションで認証プロトコルとして OpenID Connect を使用することを想定しています。そのほか、WS-Federation を使用する方法もあります。

> OpenID Connect を使用する場合、SaaS プロバイダーは、Windows Server 2016 で実行される AD FS 4.0 (現在は Technical Preview 段階) を使用する必要があります。AD FS 3.0 は、OpenID Connect をサポートしていません。

> ASP.NET Core 1.0 には、すぐに使用できる WS-Federation のサポートは含まれていません。

ASP.NET 4 で WS-Federation を使用する例については、https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation を参照してください。

## 認証フロー

1.	ユーザーが [サインイン] をクリックすると、アプリケーションにより、SaaS プロバイダーの AD FS の OpenID Connect エンドポイントにリダイレクトされます。
2.	ユーザーが組織のユーザー名 ("`alice@corp.contoso.com`") を入力します。AD FS がホーム領域検出を使用して顧客の AD FS (ユーザーが資格情報を入力する場所) にリダイレクトします。
3.	顧客の AD FS が WF-Federation (または SAML) を使用して SaaS プロバイダーの AD FS にユーザー要求を送信します。
4.	要求は、OpenID Connect を使用して、AD FS からアプリケーションに渡されます。これには、WS-Federation からのプロトコルの切り替えが必要です。

## 制限事項

この記事の執筆時には、アプリケーションが OpenID id\_token で取得する要求のセットは制限されています。AD FS 4.0 はまだプレビュー段階のため、このリストは変更される可能性があります。ただし、現時点では制限があるため、作業を進める前に、アプリケーションに追加の要求が必要かどうかに注意してください。

現在、id\_token で送信される要求を以下に示します。

クレーム | 説明
------|-------------
aud | 対象ユーザー - 要求の発行先のアプリケーション。
authenticationinstant | [認証のインスタント]
c\_hash | コード ハッシュ値
exp | [期限切れ日時]
iat | [発行時刻]
iss | 発行者。この要求の値は、常にリソース パートナーの AD FS です。顧客の AD FS ではありません (言い換えると、この要求により、顧客ではなく SaaS プロバイダーが発行者として識別されます)。
name | ユーザー名。例: `john@corp.fabrikam.com`.
nameidentifier | [名前の識別子]
nonce | セッション nonce
upn | ユーザー プリンシパル名 (UPN)。例: john@corp.fabrikam.com

特に、"iss" 要求は、顧客の AD FS を指定するものではないという点に注意してください。顧客のドメインを把握するには、UPN を調べる必要があります。この記事の残りの部分で、RP (アプリケーション) とアカウント パートナー (顧客) の間に信頼関係を設定する方法について説明します。

## AD FS のデプロイ

SaaS プロバイダーは、オンプレミスまたは Azure VM に AD FS をデプロイできます。セキュリティと可用性を確保するうえで、次のガイドラインが重要です。

-	AD FS サービスの可用性を最大限高めるために、AD FS サーバーと AD FS プロキシ サーバーを少なくとも 2 つずつデプロイします。
-	ドメイン コントローラーと AD FS サーバーは直接インターネットに公開せず、これらに直接アクセスできる仮想ネットワーク内に設置する必要があります。
-	Web アプリケーション プロキシ (以前の AD FS プロキシ) を使用して、インターネットに AD FS サーバーを公開する必要があります。

このようなトポロジを Azure で設定するには、仮想ネットワーク、NSG、Azure VM、可用性セットを使用する必要があります。詳細については、「[Azure の仮想マシンでの Windows Server Active Directory のデプロイ ガイドライン](MSDN)」を参照してください。

## AD FS で OpenID Connect 認証を使用するようにアプリケーションを構成する

SaaS プロバイダーは、アプリケーションと AD FS の間で OpenID Connect を利用できるようにする必要があります。そのためには、AD FS にアプリケーション グループを追加します。詳細な手順については、この[ブログ記事]の「Setting up a Web App for OpenId Connect sign in AD FS (OpenId Connect サインイン AD FS 向けに Web アプリケーションを設定する)」のセクションを参照してください。 次に、OpenID Connect ミドルウェアを構成します。メタデータ エンドポイントは `https://domain/adfs/.well-known/openid-configuration` です。domain は、SaaS プロバイダーの AD FS ドメインです。

通常、このメタデータ エンドポイントとその他の OpenID Connect エンドポイント (AAD など) を組み合わせます。ユーザーが正しい認証エンドポイントに送られるように、2 つの異なるサインイン ボタンを使用するか、他の方法で 2 つのエンドポイントを区別する必要があります。

## AD FS リソース パートナーの構成

SaaS プロバイダーは、ADFS 経由で接続する顧客ごとに、次の操作を実行する必要があります。

1.	要求プロバイダー信頼を追加します。
2.	要求規則を追加します。
3.	ホーム領域検出を有効にします。

詳細な手順を以下に示します。

### 要求プロバイダー信頼の追加

1.	サーバー マネージャーで、**[ツール]** をクリックし、**[AD FS 管理]** を選択します。
2.	コンソール ツリーの **[AD FS]** で、**[要求プロバイダー信頼]** を右クリックします。**[要求プロバイダー信頼の追加]** を選択します。
3.	**[開始]** をクリックしてウィザードを開始します。
4.	[オンラインまたはローカル ネットワーク上で発行された要求プロバイダーに関するデータをインポートする] オプションを選択します。顧客のフェデレーション メタデータ エンドポイントの URI を入力します (例: `https://contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`)。 この URI は顧客から入手する必要があります。
5.	既定のオプションを使用してウィザードを完了します。

### 要求規則の編集

1.	新しく追加した要求プロバイダー信頼を右クリックし、**[要求規則の編集]** を選択します。
2.	**[規則の追加]** をクリックします。
3.	[入力方向の要求をパススルーするかフィルター処理する] を選択し、**[次へ]** をクリックします。![Add Transform Claim Rule Wizard](media/guidance-multitenant-identity/edit-claims-rule.png)
4.	規則の名前を入力します。
5.	[入力方向の要求の種類] で、**[UPN]** を選択します。
6.	[すべての要求値をパススルーする] を選択します。 ![Add Transform Claim Rule Wizard](media/guidance-multitenant-identity/edit-claims-rule2.png)
7.	**[完了]** をクリックします。
8.	手順 2. ～ 7. を繰り返し、入力方向の要求の種類として **[アンカー要求の種類]** を指定します。
9.	**[OK]** をクリックしてウィザードを完了します。

### ホーム領域検出の有効化
次の PowerShell スクリプトを実行します。

```
Set-ADFSClaimsProviderTrust -TargetName "name" -OrganizationalAccountSuffix @("suffix")
```

"name" は要求プロバイダー信頼のフレンドリ名、"suffix" は顧客の AD の UPN サフィックス ("corp.fabrikam.com" など) です。

この構成では、エンド ユーザーが組織のアカウントを入力すると、AD FS によって対応する要求プロバイダーが自動的に選択されます。詳細については、「[AD FS サインイン ページのカスタマイズ]」の「特定の電子メール サフィックスを使用するための ID プロバイダーの構成」セクションを参照してください。

## AD FS アカウント パートナーの構成

顧客は次の操作を実行する必要があります。

1.	証明書利用者 (RP) 信頼を追加します。
2.	要求規則を追加します。

### RP 信頼の追加

1.	サーバー マネージャーで、**[ツール]** をクリックし、**[AD FS 管理]** を選択します。
2.	コンソール ツリーの **[AD FS]** で、**[証明書利用者信頼]** を右クリックします。**[証明書利用者信頼の追加]** を選択します。
3.	**[要求に対応する]** を選択し、**[開始]** をクリックします。
4.	**[データ ソースの選択]** ページで、[オンラインまたはローカル ネットワーク上で発行された要求プロバイダーに関するデータをインポートする] オプションを選択します。SaaS プロバイダーのフェデレーション メタデータ エンドポイントの URI を入力します。 ![Add Relying Party Trust Wizard](media/guidance-multitenant-identity/add-rp-trust.png)
5.	**[表示名の指定]** ページで、任意の名前を入力します。
6.	**[アクセス制御ポリシーの選択]** で、ポリシーを選択します。組織内のすべてのユーザーを許可することも、特定のセキュリティ グループを選択することもできます。 ![Add Relying Party Trust Wizard](media/guidance-multitenant-identity/add-rp-trust2.png)
7.	**[次へ]** をクリックしてウィザードを完了します。

### 要求規則の追加

1.	新しく追加した証明書利用者信頼を右クリックし、**[要求発行ポリシーの編集]** を選択します。
2.	**[規則の追加]** をクリックします。
3.	[要求として LDAP 属性を送信] を選択し、**[次へ]** をクリックします。 ![Add Transform Claim Rule Wizard](media/guidance-multitenant-identity/add-claims-rules.png)
4.	"UPN" など、規則の名前を入力します。
5.	**[属性ストア]** で、**[Active Directory]** を選択します。
6.	**[LDAP 属性のマッピング]** セクションで次の操作を実行します。
  -	**[LDAP 属性]** で、**[User-Principal-Name]** を選択します。
  - **[出力方向の要求の種類]** で、**[UPN]** を選択します。 ![Add Transform Claim Rule Wizard](media/guidance-multitenant-identity/add-claims-rules2.png)
7.	**[完了]** をクリックします。
8.	もう一度 **[規則の追加]** をクリックします。
9.	[カスタムの規則を使用して要求を送信] を選択し、**[次へ]** をクリックします。
10.	"Anchor Claim" など、規則の名前を入力します。
11.	**[カスタムの規則]** で、次のように入力します。

	```
    EXISTS([Type == "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype"])=>
      issue (Type = "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype",
             Value = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn");
	```

    この規則は、UPN 要求をアンカー要求にマップします。

12.	**[完了]** をクリックします。
13.	**[OK]** をクリックしてウィザードを完了します。

<!-- Links -->
[シリーズの一部]: guidance-multitenant-identity.md
[Azure AD Connect]: ../active-directory/active-directory-aadconnect.md
[フェデレーションの信頼]: https://technet.microsoft.com/library/cc738707.aspx
[アカウント パートナー]: https://technet.microsoft.com/library/cc758463(v=ws.10).aspx
[リソース パートナー]: https://technet.microsoft.com/library/cc758463(v=ws.10).aspx
[認証のインスタント]: https://msdn.microsoft.com/library/system.security.claims.claimtypes.authenticationinstant%28v=vs.110%29.aspx
[期限切れ日時]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-25#section-4.1.4
[発行時刻]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-25#section-4.1.6
[名前の識別子]: https://technet.microsoft.com/library/ee913589.aspx
[Guidelines for Deploying Windows Server Active Directory on Azure Virtual Machines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[ブログ記事]: http://www.cloudidentity.com/blog/2015/08/21/OPENID-CONNECT-WEB-SIGN-ON-WITH-ADFS-IN-WINDOWS-SERVER-2016-TP3/
[AD FS サインイン ページのカスタマイズ]: https://technet.microsoft.com/library/dn280950.aspx
[サンプル アプリケーション]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->