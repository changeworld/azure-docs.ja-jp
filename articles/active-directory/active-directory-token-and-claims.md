<properties 
   pageTitle="サポートされているトークンとクレームの種類"
   description="Azure Active Directory (AAD) によって発行された SAML 2.0 トークンおよび JSON Web トークン (JWT) のクレームを、理解および評価するためのガイド"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   services="active-directory" 
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/01/2015"
   ms.author="mbaldwin"/>

# サポートされているトークンとクレームの種類

このトピックでは、Azure Active Directory (Azure AD) が発行する SAML 2.0 トークンおよび JSON Web Tokens (JWT) トークン内のクレームを理解し評価するのに役立つ情報を提供します。

このトピックでは、各トークン クレームを説明してから、必要に応じて SAML トークンおよび JWT トークン内のクレームの例を示します。プレビュー状態にあるクレームは、別に一覧表示されます。最後に、コンテキスト内のクレームを確認できるように、サンプルのトークンを示します。

Azure では、トークンにクレームを追加していくことで、新しいシナリオが有効になります。通常は、これらのクレームをプレビュー状態で導入し、テスト期間終了後に完全なサポート状態に変換します。クレーム変更の準備として、新しいクレームによってアプリケーションに問題が発生しないように、Azure AD からトークンを受け入れるアプリケーションでは、不明なトークンのクレームが無視されるように設定する必要があります。プレビュー状態にあるクレームを使用するアプリケーションが、これらのクレームに影響を受けないようにするほか、クレームがトークン内に表示されなくても例外を発生しないようにする必要があります。Azure AD が発行する SAML トークンまたは JWT トークンには含まれていないクレームがアプリケーションで必要とされる場合は、このページの下部にある [コミュニティ追加機能] セクションを使用して、新しいクレームの種類を提案および検討します。

## トークン クレームのリファレンス

このセクションでは、Azure AD が返すトークン内のクレームを一覧表示して説明します。SAML バージョンと JWT バージョンの両方のクレームを取り上げて、クレームの内容と用途を説明します。クレームは、アルファベット順に一覧表示されています。

### アプリケーション ID

アプリケーション ID クレームは、トークンを使用してリソースにアクセスするアプリケーションを識別します。アプリケーションとして識別することもできますが、アプリケーションを使用しているユーザーとして識別することもできます。アプリケーション ID は通常、アプリケーション オブジェクトを表しますが、Azure AD 内のサービス プリンシパル オブジェクトを表すこともできます。

Azure AD では、SAML トークン内でアプリケーション ID クレームはサポートされていません。

JWT トークンでは、アプリケーション ID は appid クレームに表示されます。

    "appid":"15CB020F-3984-482A-864D-1D92265E8268"

### 対象ユーザー
トークンの対象ユーザーは、トークンの受信対象者です。トークンを受信するアプリケーションは、対象ユーザーの値が正しいことを検証し、異なる対象ユーザー向けのトークンをすべて拒否する必要があります。

対象ユーザーの値は文字列で、通常は ”https://contoso.com” などのリソースのベース アドレスです。Azure AD トークンでは、対象ユーザーはトークンを要求したアプリケーションのアプリケーション ID URI となります。アプリケーション (対象ユーザー) に 1 つ以上のアプリケーション ID URI がある場合、トークンの Audience クレーム内にあるアプリケーション ID URI は、トークン要求内のアプリケーション ID URI と照合されます。SAML トークンでは、対象ユーザー クレームは AudienceRestriction 要素の Audience 要素内で定義されます。

    <AudienceRestriction>
    <Audience>https://contoso.com</Audience>
    </AudienceRestriction>

JWT トークンでは、対象ユーザーは aud クレームに表示されます。

    "aud":"https://contoso.com"

### アプリケーションの認証コンテキスト クラスの参照

アプリケーションの認証コンテキスト クラスの参照クレームは、クライアントの認証方法を示します。パブリック クライアントの場合、値は 0 です。クライアント ID とクライアント シークレットが使用されている場合、値は 1 です。

JWT トークンでは、認証コンテキスト クラスの参照値は、appidacr (アプリケーション固有の ACR 値) クレームに表示されます。

    "appidacr": "0"

### 認証コンテキスト クラスの参照
認証コンテキスト クラスの参照クレームは、アプリケーションの認証コンテキスト クラスの参照クレームのクライアントとの認証法とは異なる、サブジェクトが認証された方法を示します。値 「0」 は、エンドユーザーの認証が ISO/IEC 29115 の要件を満たしていないことを示します。

- JWT トークンでは、認証コンテキスト クラスの参照クレームは、acr (ユーザー固有の ACR 値) クレームに表示されます。

    "acr": "0"

### 認証のインスタント

認証のインスタント クレームは、認証された日時を記録します。

SAML トークンでは、認証インスタントは、AuthnStatement 要素の AuthnInstant 属性に表示されます。日時は UTC (Z) 時間で表示されます。

    <AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">

Azure AD では、JWT トークン内に同等のクレームはありません。

### 認証方法

認証方法クレームは、トークンのサブジェクトの認証方法を示します。この例 http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password では、ID プロバイダーはパスワードを使用してユーザーを認証しました。

SAML トークンでは、認証方法の値は AuthnContextClassRef 要素に表示されます。

    <AuthnContextClassRef>http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password</AuthnContextClassRef>

JWT トークンでは、認証方法の値は amr クレームに表示されます。

    “amr”: ["pwd"]

###名

名つまり 「姓を含まない」名前クレームでは、Azure AD ユーザー オブジェクトに設定されたユーザーの名、つまり「姓を含まない名前」 を示します。

SAML トークンでは、名 (姓を含まない名前) は givenname SAML Attribute 要素内のクレームに表示されます。

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>
    <AttributeValue>Frank<AttributeValue>

JWT トークンでは、名は given\_name クレームに表示されます。

    "given_name": "Frank"

### グループ

グループ クレームは、サブジェクトのグループ メンバーシップを表すオブジェクト ID です。これらの値は一意 (「オブジェクト ID」を参照) であり、アクセスの管理 (リソースへのアクセスを承認するなど) に安全に使用できます。グループ クレームに含まれるグループは、アプリケーションごとに、アプリケーション マニフェストの ”groupMembershipClaims” プロパティを介して構成されます。値が null の場合はすべてのグループが除外され、値が ”SecurityGroup” の場合は Active Directory セキュリティ グループのメンバーシップのみが含まれ、値が ”All” の場合はセキュリティ グループと Office 365 配布リストの両方が含まれます。どの構成でも、グループ クレームは、サブジェクトの推移的なグループ メンバーシップを表します。

SAML トークンでは、グループ クレームは groups 属性に表示されます。

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
    <AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>

JWT トークンでは、グループ クレームは groups クレームに表示されます。

    “groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]

### ID プロバイダー

ID プロバイダー クレームは、トークンのサブジェクトを認証した ID プロバイダーを記録します。この値は、ユーザー アカウントが発行者とは異なるテナントにある場合を除いて、発行者クレームの値と同じです。

SAML トークンでは、ID プロバイダーは identityprovider SAML Attribute 要素内のクレームに表示されます。

    <Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>
    <AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>

JWT トークンでは、ID プロバイダーは idp クレームに表示されます。

    "idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### IssuedAt

IssuedAt クレームは、トークンが発行された時刻を格納します。このクレームは、トークンの鮮度を測定するためによく使用されます。SAML トークンでは、IssuedAt 値は IssueInstant アサーションに表示されます。

    <Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">

JWT トークンでは、IssuedAt 値は iat クレームに表示されます。値は 1970-01-010:0:0Z 以降、世界協定時刻 (UTC) に基づいて秒数で表されています。

    "iat": 1390234181

### 発行者

発行者クレームは、トークンと Azure AD ディレクトリ テナントを構築して返すセキュリティ トークン サービス (STS) を識別します。Azure AD が返すトークンでは、発行者は sts.windows.net です。発行者クレーム値の GUID は、Azure AD ディレクトリのテナント ID です。テナント ID は、変更不可で信頼性の高いディレクトリの識別子です。

SAML トークンでは、発行者クレームは Issuer 要素に表示されます。

    <Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>

JWT トークンでは、発行者は iss クレームに表示されます。

    "iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### 姓

姓クレームは、Azure AD ユーザー オブジェクトで定義されたユーザーの姓や名字、つまり家名を示します。SAML トークンでは、姓は surname SAML Attribute 要素内のクレームに表示されます。

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>
    <AttributeValue>Miller<AttributeValue>

JWT トークンでは、姓は family\_name クレームに表示されます。

    "family_name": "Miller"

### 名前

名前クレームは、トークンのサブジェクトを識別する、人が認識できる値を示します。この値は、テナント内で一意であるとは限らず、表示目的でのみ使用されます。SAML トークンでは、Name 属性に名前が表示されます。

    <Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>
    <AttributeValue>frankm@contoso.com<AttributeValue>

JWT クレームでは、unique\_name クレームに名前が表示されます。

    "unique_name": "frankm@contoso.com"

### オブジェクト ID

オブジェクト ID クレームには、Azure AD 内のオブジェクトの一意の識別子が含まれています。この値は変更不可で、再割り当ても再利用もできません。したがってこの値を使用すると、トークンを使用してリソースにアクセスする場合などに安全に承認チェックができます。オブジェクト ID を使用すると、Azure AD へのクエリ内のオブジェクトを識別できます。SAML トークンでは、オブジェクト ID は objectidentifier 属性に表示されます。

    <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
    <AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>

JWT トークンでは、オブジェクト ID は oid クレームに表示されます。

    "oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"

### ロール

ロール クレームは、Azure AD におけるサブジェクトのアプリケーション ロール割り当てを表す文字列です。このクレームを使用すると、ロールベースのアクセス制御ができます。アプリケーション ロールは、アプリケーションごとに、アプリケーション マニフェストの ”appRoles” プロパティを介して定義されます。各アプリケーション ロールの ”value” プロパティは、ロール クレーム内に表示される値です。ロール クレームに含まれるロールは、グループ メンバーシップを通して直接的および間接的にサブジェクトに付与されているすべてのアプリケーション ロールを表します。SAML トークンでは、ロール クレームは roles 属性に表示されます。

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">
    <AttributeValue>Admin</AttributeValue>

JWT トークンでは、ロール クレームは roles クレームに表示されます。

    “roles”: ["Admin", … ]

### スコープ

トークンのスコープは、クライアント アプリケーションに権限借用が許可されていることを示します。既定のアクセス許可は user\_impersonation です。保護されたリソースの所有者は、別の値を Azure AD に登録できます。

JWT トークンでは、トークンのスコープは scp クレームで指定できます。

    "scp": "user_impersonation"

### サブジェクト

トークンのサブジェクトは、トークンが情報 (アプリケーションのユーザーなど) をアサートするためのプリンシパルです。この値は変更不可で、再割り当ても再利用もできません。したがってこの値を使用すると、トークンを使用してリソースにアクセスする場合などに安全に承認チェックができます。サブジェクトは、Azure AD が発行するトークン内に常に存在するため、汎用性のある承認システムでこの値を使用することをお勧めします。

SAML トークンでは、トークンのサブジェクトは Subject 要素の NameID 要素で指定できます。NameID はサブジェクトの再利用できない一意の識別子であり、ユーザー、アプリケーション、またはサービスに使用できます。

SubjectConfirmation はクレームではありません。これは、トークンのサブジェクトの検証方法を示します。”bearer” は、トークンを所有していることでサブジェクトが確認されることを示します。

    <Subject>
    <NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>
    <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
    </Subject>

JWT トークンでは、サブジェクトは sub クレームに表示されます。

    "sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"

### テナント ID
テナント ID は、トークンを発行したディレクトリ テナントを識別する変更不可で、再利用できない識別子です。この値を使用すると、マルチ テナント アプリケーションのテナント固有のディレクトリ リソースにアクセスできます。たとえば、この値を使用すると、Graph API への呼び出しでテナントを識別できます。

SAML トークンでは、テナント ID は tenantid SAML Attribute 要素内のクレームに表示されます。

    <Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>
    <AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>

JWT トークンでは、テナント ID は tid クレームに表示されます。

    "tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"

### トークンの有効期間
トークンの有効期間クレームは、トークンが有効である期間を定義します。トークンを検証するサービスは、現在の日付がトークンの有効期間内にあることを確認する必要があります。有効期間内にない場合、サービスはトークンを拒否します。サービスでは、Azure AD とサービスの間のクロック タイムの違い (「時間のずれ」) を考慮して、トークンの有効期間の範囲を最大 5 分まで延長する場合があります。

SAML トークンでは、トークンの有効期間クレームは、NotBefore 属性と NotOnOrAfter 属性を使用して Conditions 要素内で定義されます。

    <Conditions
    NotBefore="2013-03-18T21:32:51.261Z"
    NotOnOrAfter="2013-03-18T22:32:51.261Z"
    >

JWT トークンでは、トークンの有効期間は、nbf (有効期間の開始時刻) クレームと exp (有効期限の終了時刻) クレームによって定義されます。これらのクレームの値は 1970-01-010:0:0Z 以降から、世界協定時刻 (UTC) に基づいて秒数で表されています。詳細については、RFC 3339 を参照してください。

    "nbf":1363289634,
    "exp":1363293234

### ユーザー プリンシパル名
ユーザー プリンシパル名 クレームには、ユーザー プリンシパルのユーザー名が格納されます。

JWT トークンでは、ユーザー プリンシパル名は upn クレームに表示されます。

    "upn": frankm@contoso.com

### バージョン
バージョン クレームには、トークンのバージョン番号が格納されます。JWT トークンでは、ユーザー プリンシパル名は ver クレームに表示されます。

    "ver": "1.0"

## トークンのサンプル

このセクションでは、Azure AD が返す SAML トークンと JWT トークンのサンプルを示します。これらのサンプルでは、コンテキスト内のクレームを確認できます。SAML トークン

以下に示すのは、一般的な SAML トークンのサンプルです。

	<?xml version="1.0" encoding="UTF-8"?>
	<t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
	  <t:Lifetime>
		<wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
		<wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
	  </t:Lifetime>
	  <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
		<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
		  <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
		</EndpointReference>
	  </wsp:AppliesTo>
	  <t:RequestedSecurityToken>
		<Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
		  <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
		  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
			<ds:SignedInfo>
			  <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
			  <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
			  <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
				<ds:Transforms>
				  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
				  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
				</ds:Transforms>
				<ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
				<ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
			  </ds:Reference>
			</ds:SignedInfo>
			<ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
			<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
			  <X509Data>
				<X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
			  </X509Data>
			</KeyInfo>
		  </ds:Signature>
		  <Subject>
			<NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
			<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
		  </Subject>
		  <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
			<AudienceRestriction>
			  <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
			</AudienceRestriction>
		  </Conditions>
		  <AttributeStatement>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
			  <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
			  <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
			  <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
			  <AttributeValue>Admin</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
			  <AttributeValue>Sample</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
			  <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
			  <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
			  <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
			  <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
			  <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
			  <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
			  <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
			  <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
			  <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
			  <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
			  <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
			  <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
			  <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
			  <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
			</Attribute>
		  </AttributeStatement>
		  <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
			<AuthnContext>
			  <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
			</AuthnContext>
		  </AuthnStatement>
		</Assertion>
	  </t:RequestedSecurityToken>
	  <t:RequestedAttachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedAttachedReference>
	  <t:RequestedUnattachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedUnattachedReference>
	  <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
	  <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
	  <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
	</t:RequestSecurityTokenResponse>

### JWT トークン - ユーザーの権限借用

以下に示すのは、ユーザーの権限借用の Web フローで使用される、一般的な JSON Web トークン (JWT) のサンプルです。クレームに加えて、トークンには** ver** と **appidacr **で表されるバージョン番号と認証コンテキスト クラス参照 (クライアントが認証された方法を示す) が含まれます。パブリック クライアントの場合、値は 0 です。クライアント ID またはクライアント シークレットが使用されている場合、値は 1 です。

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

##関連項目

[Azure Active Directory の認証プロトコル](https://msdn.microsoft.com/library/azure/dn151124.aspx)
 

<!---HONumber=August15_HO6-->