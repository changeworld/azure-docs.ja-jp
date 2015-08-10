<properties 
	pageTitle="Office 365 と Azure AD のユーザー向けの証明書更新ガイダンス" 
	description="この記事では、証明書の更新を通知する電子メールによって生じる問題を解決する方法を Office 365 のユーザー向けに説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="billmath"/>


# Office 365 および Azure AD 用のフェデレーション証明書の更新

Office 365 の証明書の更新を求める電子メールやポータルの通知を受信した場合、この記事を利用して、問題を解決し、再発を防ぐことができます。この記事は、AD FS をフェデレーション サーバーとして使用していることを前提としています。

## 対応の必要性を確認する

AD FS 2.0 以降を使用している場合、Office 365 または Azure AD では、証明書は期限切れになる前に自動的に更新されます。手動で手順を実行したり、スケジュール済みのタスクとしてスクリプトを実行したりする必要はありません。これが機能するには、次の AD FS の既定の構成設定が両方とも有効になっている必要があります。

- AD FS の AutoCertificateRollover プロパティが True に設定されている必要があります。これは、有効期限が切れる前に、AD FS が新しいトークン署名とトークン暗号化解除証明書を自動的に生成することを示します。
	- 値が False の場合、カスタムの証明書の設定を使用しています。包括的なガイダンスについては、[こちら](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert)を参照してください。
- フェデレーション メタデータがパブリック インターネットに対して使用可能になっている必要があります。
	
	確認方法を次に示します。

	- プライマリ フェデレーション サーバーの PowerShell コマンド ウィンドウで次のコマンドを実行して、AD FS のインストールで証明書の自動ロールオーバーが使用されていることを確認します。

	`PS C:\> Get-ADFSProperties`

(AD FS 2.0 を使用している場合は、Add-Pssnapin Microsoft.Adfs.Powershell を最初に実行する必要があることに注意してください)

(社内ネットワークの外部の) パブリック インターネット上のコンピューターで次の URL に移動して、フェデレーション メタデータにパブリックにアクセスできることを確認します。


https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

この `(your_FS_name) ` は、fs.contoso.com など、組織で使用しているフェデレーション サービスのホスト名に置き換えます。どちらの設定も適切であることを確認できた場合、他の作業は不要です。

例: https://fs.contos.com/federationmetadata/2007-06/federationmetadata.xml

## AutoCertificateRollover プロパティが False に設定されている場合

AutoCertificateRollover プロパティが False に設定されている場合、既定以外の AD FS の証明書の設定を使用しています。よくある理由に、企業が、組織的な証明機関によって登録された AD FS 証明書を管理していることが挙げられます。この場合、証明書を自分で更新する必要があります。[こちら](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert)のガイダンスに従ってください。

## メタデータにパブリックにアクセスできない場合
AutocertificateRollover の設定は True だが、フェデレーション メタデータにパブリックにアクセスできない場合は、次の手順に従って、オンプレミスとクラウドの両方の証明書が更新されていることを確認します。

### AD FS システムによって新しい証明書が生成されたことを確認します。 

- プライマリ AD FS サーバーにログオンしていることを確認します。
- PowerShell コマンド ウィンドウを開き、次のコマンドを実行して、AD FS の現在の署名証明書を確認します。 

`PS C:\>Get-ADFSCertificate –CertificateType token-signing.`

(AD FS 2.0 を使用している場合は、Add-Pssnapin Microsoft.Adfs.Powershell を最初に実行する必要があることに注意してください)


- コマンドの出力に表示されたすべての証明書を確認します。AD FS によって新しい証明書が生成された場合は、出力に 2 つの証明書が表示されます。1 つは IsPrimary 値が True で NotAfter の日付が 5 日以内であり、もう 1 つは IsPrimary が False で NotAfter の日付が約 1 年後です。
	
- 証明書が 1 つしか表示されず、その NotAfter の日付が 5 日以内の場合は、次の手順を実行して新しい証明書を生成する必要があります。

- 新しい証明書を生成するには、PowerShell コマンド プロンプトで次のコマンドを実行します: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`。

- 次のコマンドを再度実行して、更新内容を確認します。PS C:\>Get-ADFSCertificate –CertificateType token-signing
- 次に、以下の手順に従って、Office 365 フェデレーションの信頼されたプロパティを手動で更新します。

次の 2 つの証明書が表示されます。そのうちの 1 つは、NotAfter の日付が約 1 年後で、IsPrimary の値が False です。


### 以下の手順に従って、Office 365 のフェデレーション信頼プロパティを手動で更新します。

1.	Windows PowerShell 用 Microsoft Azure Active Directory モジュールを開きます。
2.	$cred=Get-Credential を実行します。このコマンドレットで資格情報の入力を求められたら、クラウド サービス管理者アカウントの資格情報を入力します。
3.	Connect-MsolService –Credential $cred を実行します。このコマンドレットでクラウド サービスに接続します。クラウド サービスに接続している状況を作った後で、ツールによってインストールされた追加のコマンドレットを実行する必要があります。
4.	AD FS のプライマリ フェデレーション サーバー以外のコンピューターでこれらのコマンドを実行している場合は、Set-MSOLAdfscontext -Computer <AD FS primary server> を実行します。この <AD FS primary server> は、プライマリ AD FS サーバーの内部 FQDN 名です。このコマンドレットで AD FS に接続している状況を作ります。 
5.	Update-MSOLFederatedDomain –DomainName <domain> を実行します。このコマンドレットで、AD FS の設定でクラウド サービスを更新し、両者の信頼関係を構成します。

>[AZURE.NOTE]contoso.com や fabrikam.com などの複数の最上位のドメインをサポートする必要がある場合は、すべてのコマンドレットで SupportMultipleDomain スイッチを使用する必要があります。詳細については、複数の最上位のドメインのサポートに関するページを参照してください。最後に、すべての Web アプリケーション プロキシ サーバーが[Windows Server 2014 年 5 月](http://support.microsoft.com/kb/2955164)のロールアップで更新されていることを確認します。更新されていない場合は、プロキシが新しい証明書の更新に失敗し、機能が停止する可能性があります。

<!---HONumber=July15_HO5-->