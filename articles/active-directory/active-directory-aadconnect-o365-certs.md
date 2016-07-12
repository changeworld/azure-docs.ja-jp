<properties
	pageTitle="Office 365 と Azure AD のユーザー向けの証明書更新ガイダンス | Microsoft Azure"
	description="この記事では、証明書の更新を通知する電子メールによって生じる問題を解決する方法を Office 365 のユーザー向けに説明します。"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="billmath"/>


# Office 365 および Azure AD 用のフェデレーション証明書の更新

##概要

Azure AD と AD FS とのフェデレーションが正常に機能するためには、AD FS が Azure AD に提示するセキュリティ トークンに署名するときに使う証明書が、Azure AD 側の構成内容と一致している必要があります。完全に一致していないと、AD FS と AAD との間の信頼関係が失われる可能性があります。証明書の情報は、AD FS と (エクストラネット アクセスに使用される) Web アプリケーション プロキシをデプロイするときに Azure AD によって同期されます。

この記事では、トークン署名証明書を管理し、Azure AD との同期状態を維持する方法について詳しく説明します。以下のケースを想定しています。

* Web アプリケーション プロキシをデプロイしていないため、エクストラネットではフェデレーション メタデータが利用できない
* トークン署名証明書に AD FS の既定の構成を使用していない
* サード パーティの ID プロバイダーを使用している

## トークン署名証明書に使用する AD FS の既定の構成

通常、トークン署名証明書とトークン暗号化解除証明書は自己署名証明書であり、有効期間は 1 年です。トークン署名証明書とトークン暗号化解除証明書に関する AD FS の既定の構成には、**AutoCertificateRollover** と呼ばれる自動更新プロセスが存在します。AD FS 2.0 以降を使用している場合、Office 365 または Azure AD では、証明書は期限切れになる前に自動的に更新されます。

### 更新通知 - O365 ポータルと電子メール通知

>[AZURE.NOTE] Office の証明書を更新するよう求める電子メールまたはポータル通知が届いた場合に、何らかの対処が必要かどうかは、[トークン署名証明書に対する変更の管理](#managecerts)について記載した後述の手順に従って判断してください。実際には対処が不要であるにもかかわらず証明書の更新を求める通知がユーザーに送信される問題が確認されています。

Azure AD は、フェデレーション メタデータを監視し、その結果に応じてトークン署名証明書の更新を試みます。トークン署名証明書の有効期限が切れる 30 日前に、Azure AD がフェデレーション メタデータをポーリングして新しい証明書の存在をチェックします。

* フェデレーション メタデータをポーリングして新しい証明書を取得できた場合は、電子メール通知も O365 ポータルの警告もユーザーには送信されません。
* フェデレーション メタデータにアクセスできなかったり、証明書の自動ロールオーバーが有効になっていなかったりしたために、新しいトークン署名証明書を取得できなかった場合は、電子メール通知と警告が O365 ポータルに表示されます。

![O365 portal notification](./media/active-directory-aadconnect-o365-certs/notification.png)

>[AZURE.IMPORTANT] AD FS を使用している場合、ビジネス継続性を確保するために、既知の問題による認証エラーを防止する修正プログラムがサーバーに適用されていることを確認してください。該当する修正プログラムは次のとおりです。以後、更新期間における AD FS プロキシ サーバーの既知の問題が緩和されます。
>
>Server 2012 R2 - [Windows Server 2014 年 5 月の更新プログラム ロールアップ](http://support.microsoft.com/kb/2955164)
>
>Server 2008 R2 および 2012 - [Windows Server 2012 または Windows 2008 R2 SP1 で、プロキシ経由の認証に失敗する](http://support.microsoft.com/kb/3094446)

## 証明書の更新が必要かどうかの確認 <a name="managecerts"></a>

### 手順 1: AutoCertificateRollover 状態を確認する

AD FS サーバーで Microsoft Powershell を開きます。AutoCertRollover 値が TRUE に設定されていることを確認します。

	Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

[AZURE.NOTE] AD FS 2.0 を使用している場合は、Add-Pssnapin Microsoft.Adfs.Powershell を最初に実行する必要があります。

### 手順 2: AD FS と Azure AD が同期されていることを確認する

AD FS Server で Azure AD Powershell プロンプトを開き、Azure AD に接続します。

>[AZURE.NOTE] Azure AD PowerShell をまだインストールしていない場合は、[こちら](https://technet.microsoft.com/library/jj151815.aspx)からダウンロードできます。

	Connect-MsolService

AD FS と Azure AD との間の信頼関係のプロパティで証明書が構成されていることを特定のドメインを指定して確認します。

	Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

両方の出力結果において拇印が一致した場合、Azure AD 側と証明書が同期されています。

### 手順 3: 証明書の有効期限が迫っているかどうかを確認する

Get-MsolFederationProperty または Get-AdfsCertificate の出力結果で、"有効期間の終了時刻" の日付を確認します。今日の日付から 30 日未満である場合、期限切れに対処する必要があります。

### 次のステップ

| AutoCertificateRollover | Azure AD 側と証明書が同期されている | フェデレーション メタデータにパブリックにアクセス可能 | 有効期限までの日数 | アクション |
|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|
| はい | はい | はい | - | 対処不要「[トークン署名証明書を自動的に更新する](#autorenew)」を参照してください。|
| はい | いいえ | - | 15 日未満 | 直ちに更新する。「[トークン署名証明書を手動で更新する](#manualrenew)」を参照してください。|
| いいえ | - | - | 30 日未満 | 直ちに更新する。「[トークン署名証明書を手動で更新する](#manualrenew)」を参照してください。|

[-] 該当せず

## トークン署名証明書を自動的に更新する (推奨) <a name="autorenew"></a>

エクストラネットからフェデレーション メタデータへのアクセスを可能にする Web アプリケーション プロキシをデプロイ済みで、かつ AD FS の既定の構成を使用している (つまり AutoCertificateRollover が有効である) 場合、**手動による手順は一切必要ありません**。 以下の点をチェックして、証明書の自動更新が実行できる状態にあることを確認してください。

**1. AD FS の AutoCertificateRollover プロパティが True に設定されていること**

これは、有効期限が切れる前に、AD FS が新しいトークン署名証明書とトークン暗号化解除証明書を自動的に生成することを示します。

**2. AD FS のフェデレーション メタデータへのパブリック アクセスが確保されていること**

(社内ネットワークの外部の) パブリック インターネット上のコンピューターで次の URL に移動して、フェデレーション メタデータにパブリックにアクセスできることを確認します。


https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

この `(your_FS_name) ` は、fs.contoso.com など、組織で使用しているフェデレーション サービスのホスト名に置き換えます。どちらの設定も適切であることを確認できた場合、他の作業は不要です。

例: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## トークン署名証明書を手動で更新する <a name="manualrenew"></a>

トークン署名証明書を手動で更新しなければならない場合があります。トークン署名証明書の手動更新が必要になる一般的な状況として、
* トークン署名証明書が自己署名証明書でないケースが考えられます。よくある理由に、企業が、組織的な証明機関によって登録された AD FS 証明書を管理していることが挙げられます。 
* また、フェデレーション メタデータのパブリック アクセスがネットワーク セキュリティで禁止されているケースもあります。

このような状況では、トークン署名証明書を更新するときに都度、PowerShell コマンド Update-MsolFederatedDomain を使って Office 365 ドメインを更新する必要もあります。

### トークン署名証明書の更新手順と O365 フェデレーション信頼の更新手順

**手順 1: AD FS のトークン署名証明書を更新する**

### 既定の構成が変更されている場合
AD FS の既定の構成が変更され **AutoCertificateRollover** が **False** に設定されている場合、(自己署名ではない) カスタムの証明書が使用されていると考えられます。「[AD FS 自己署名証明書を使用しないお客様へのガイダンス](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert)」を読んで、AD FS のトークン署名証明書を更新する方法についての包括的な指針を確認してください。

### フェデレーション メタデータへのパブリック アクセスができない場合
**AutoCertificateRollover** が **True** に設定されているにもかかわらず、フェデレーション メタデータに対してパブリックにアクセスできない場合は、まず新しいトークン署名証明書が AD FS によって生成されていることを確認します。以下の手順に従って、新しいトークン署名証明書があることを確認してください。

1. プライマリ AD FS サーバーにログオンしていることを確認します。
2. PowerShell コマンド ウィンドウを開き、次のコマンドを実行して、AD FS の現在の署名証明書を確認します。

	PS C:\>Get-ADFSCertificate –CertificateType token-signing

	>[AZURE.NOTE] AD FS 2.0 を使用している場合は、Add-Pssnapin Microsoft.Adfs.Powershell を最初に実行する必要があります。

3. コマンドの出力に表示されたすべての証明書を確認します。AD FS によって新しい証明書が生成された場合は、出力に 2 つの証明書が表示されます。1 つは IsPrimary 値が True で NotAfter の日付が 5 日以内であり、もう 1 つは IsPrimary が False で NotAfter の日付が約 1 年後です。

4. 証明書が 1 つしか表示されず、その NotAfter の日付が 5 日以内の場合は、次の手順を実行して新しい証明書を生成する必要があります。

5. 新しい証明書を生成するには、PowerShell コマンド プロンプトで次のコマンドを実行します: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`。

6. 次のコマンドを再度実行して、更新内容を確認します。PS C:\>Get-ADFSCertificate –CertificateType token-signing

次の 2 つの証明書が表示されます。そのうちの 1 つは、NotAfter の日付が約 1 年後で、IsPrimary の値が False です。

**手順 2: 新しいトークン署名証明書で O365 のフェデレーション信頼を更新する**

以下の手順に従い、新しいトークン署名証明書で O365 のフェデレーション信頼を更新します。

1.	Windows PowerShell 用 Microsoft Azure Active Directory モジュールを開きます。
2.	$cred=Get-Credential を実行します。このコマンドレットで資格情報の入力を求められたら、クラウド サービス管理者アカウントの資格情報を入力します。
3.	Connect-MsolService –Credential $cred を実行します。このコマンドレットでクラウド サービスに接続します。クラウド サービスに接続している状況を作った後で、ツールによってインストールされた追加のコマンドレットを実行する必要があります。
4.	AD FS のプライマリ フェデレーション サーバー以外のコンピューターでこれらのコマンドを実行している場合は、Set-MSOLAdfscontext -Computer <AD FS primary server> を実行します。この <AD FS primary server> は、プライマリ AD FS サーバーの内部 FQDN 名です。このコマンドレットで AD FS に接続している状況を作ります。
5.	Update-MSOLFederatedDomain –DomainName <domain> を実行します。このコマンドレットで、AD FS の設定でクラウド サービスを更新し、両者の信頼関係を構成します。


>[AZURE.NOTE] contoso.com や fabrikam.com などの複数の最上位のドメインをサポートする必要がある場合は、すべてのコマンドレットで SupportMultipleDomain スイッチを使用する必要があります。詳細については、[複数の最上位のドメインのサポート](active-directory-aadconnect-multiple-domains.md)に関するページを参照してください。

## AAD Connect を使用して Azure AD の信頼を修復する <a name="connectrenew"></a>

AD FS ファーム / Azure AD 信頼のインストールと構成に Azure AD Connect を使用した場合、トークン署名証明書に関して何らかの対処が必要かどうかは、Azure AD Connect を使用して検出できます。証明書を更新する必要がある場合、Azure AD Connect を使用すると、必要な作業を数回のクリック操作で遂行できます。

詳細については、「[信頼の修復](./active-directory-aadconnect-federation-management.md#repairing-the-trust)」を参照してください。

<!---HONumber=AcomDC_0622_2016-->