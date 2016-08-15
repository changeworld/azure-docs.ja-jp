<properties 
    pageTitle="iOS の証明書ベースの認証の使用 | Microsoft Azure" 
    description="iOS デバイスでソリューションの証明書ベースの認証を構成する方法について説明します。" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/02/2016" 
    ms.author="markvi" />



# iOS の証明書ベースの認証の使用 - パブリック プレビュー

> [AZURE.SELECTOR]
- [iOS](active-directory-certificate-based-authentication-ios.md)
- [Android](active-directory-certificate-based-authentication-android.md)


このトピックでは、Office 365 Enterprise、Business、および Education プランでのテナントのユーザーの iOS デバイス上で証明書ベースの認証 (CBA) を構成し、使用する方法について説明します。

CBA では、Exchange online アカウントを次に接続する場合、Azure Active Directory と、Android または iOS デバイス上のクライアント証明書を使用して認証できます。

- Microsoft Outlook や Microsoft Word などの Office モバイル アプリケーション
- Exchange ActiveSync (EAS) クライアント

この機能を構成すると、モバイル デバイスで特定のメールおよび Microsoft Office アプリケーションにユーザー名とパスワードの組み合わせを入力する必要がなくなります。
 

## サポートされるシナリオと要件  



### 一般的な要件 


このトピックのすべてのシナリオでは、次のタスクが必要です。

- 証明機関にアクセスし、クライアント証明書を発行します。

- 証明機関は、Azure Active Directory で構成する必要があります。「[使用の開始](#getting-started)」セクションで構成を完了する方法の詳細な手順を確認できます。

- ルート証明機関および中間証明機関は、Azure Active Directory で構成する必要があります。

- 各証明機関には、インターネットに接続する URL から参照できる証明書失効リスト (CRL) が必要です。

- クライアント証明書は、クライアント認証に対して発行する必要があります。


- Exchange ActiveSync クライアントの場合に限り、クライアント証明書では、サブジェクト代替名フィールドのプリンシパル名または RFC822 名の値のいずれかで、Exchange online のユーザーのルーティング可能な電子メール アドレスが必要になります。Azure Active Directory は、ディレクトリ内のプロキシ アドレス属性に RFC822 値をマップします。



### Office モバイル アプリケーションのサポート 

| アプリケーション | サポート |
| ---                       | ---          |
| Word/Excel/PowerPoint | ![○][1] |
| OneNote | ![○][1] |
| OneDrive | ![○][1] |
| Outlook | 近日対応予定 |
| Yammer | ![○][1] |
| Skype for Business | 近日対応予定 |


### 必要条件  

デバイスの OS バージョンは、iOS 9 以上である必要があります。

フェデレーション サーバーを構成する必要があります。

Azure Authenticator は、iOS の Office アプリケーションに必要です。

Azure Active Directory でクライアント証明書を失効させるには、ADFS トークンに次の要求が必要です。

  - `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (クライアント証明書のシリアル番号)

  - `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (クライアント証明書の発行者の文字列)

Azure Active Directory は、ADFS トークン (またはその他の SAML トークン) で利用できる場合に、これらの要求を更新トークンに追加します。更新トークンを検証する必要がある場合、この情報を使用して失効を確認します。

ベスト プラクティスとして、次を参照して、ADFS エラー ページを更新することをお勧めします。

- iOS に Azure Authenticator をインストールするための要件

- ユーザー証明書を取得する手順

詳細については、「[AD FS サインイン ページのカスタマイズ](https://technet.microsoft.com/library/dn280950.aspx)」を参照してください。



### Exchange ActiveSync クライアントのサポート 


iOS 9 以降では、ネイティブの iOS メール クライアントがサポートされます。その他の Exchange ActiveSync アプリケーションについては、この機能のサポート状況をアプリケーションの開発者にお問い合わせください。



## 使用の開始 


開始するには、Azure Active Directory で証明機関を構成する必要があります。各証明機関については、次をアップロードします。

- 証明書の公開部分 (*.cer* 形式)

- 証明書失効リスト (CRL) が存在する、インターネットに接続する URL
 

証明機関のスキーマを次に示します。

    class TrustedCAsForPasswordlessAuth 
    { 
       CertificateAuthorityInformation[] certificateAuthorities;    
    } 

    class CertificateAuthorityInformation 

    { 
        CertAuthorityType authorityType; 
        X509Certificate trustedCertificate; 
        string crlDistributionPoint; 
        string deltaCrlDistributionPoint; 
        string trustedIssuer; 
        string trustedIssuerSKI; 
    }                

    enum CertAuthorityType 
    { 
        RootAuthority = 0, 
        IntermediateAuthority = 1 
    } 


情報をアップロードするには、Windows PowerShell から Azure AD モジュールを使用します。証明機関を追加、削除、または変更するための例を次に示します。



### 証明書ベースの認証に使用する Azure AD テナントの構成 

1. Windows PowerShell を管理者特権で起動します。

2. Azure AD モジュールをインストールします。バージョン [1\.1.143.0](http://www.powershellgallery.com/packages/AzureADPreview/1.1.143.0) 以降をインストールする必要があります。

        Install-Module -Name AzureADPreview –RequiredVersion 1.1.143.0 

3. ターゲット テナントに接続します。

        Connect-AzureAD 

### 新しい証明機関の追加

1. 証明機関のさまざまなプロパティを設定して、Azure Active Directory に追加します。

        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 

5. 証明機関を取得します。

        Get-AzureADTrustedCertificateAuthority 


### 証明機関の一覧の取得

テナントの Azure Active Directory に現時点で格納されている証明機関を取得します。

        Get-AzureADTrustedCertificateAuthority 


### 証明機関の削除

1.	証明機関を取得します。

		$c=Get-AzureADTrustedCertificateAuthority 


2. 証明機関の証明書を削除します。

		Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### 証明機関の変更 

1.	証明機関を取得します。

		$c=Get-AzureADTrustedCertificateAuthority 


2. 証明機関のプロパティを変更します。

		$c[0].AuthorityType=1 

3. **証明機関**を設定します。

		Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 




## Office モバイル アプリケーションのテスト  

モバイルの Office アプリケーションの証明書認証をテストするには、次の手順に従います。

1.	テスト デバイスでは、アプリ ストアから Office モバイル アプリケーション (例: OneDrive) をインストールします。

2.	ユーザー証明書がテスト デバイスにプロビジョニングされていることを確認します。

3.	アプリケーションを起動します。

4.	ユーザー名を入力し、使用するユーザー証明書を選択します。

正常にサインインします。





## Exchange ActiveSync クライアント アプリケーションのテスト

証明書ベースの認証を使用した Exchange ActiveSync にアクセスするには、クライアント証明書を含む EAS プロファイルがアプリケーションに対して使用可能になっている必要があります。EAS プロファイルには次の情報が表示されます。

- 認証に使用するユーザー証明書

- EAS エンドポイントは、outlook.office365.com にする必要があります (現時点でこの機能は、Exchange online マルチテナント環境でのみサポート)。

EAS プロファイルをデバイス上に構成して配置するには、Intune などの MDM を使用するか、EAS プロファイルの証明書をデバイス上に手動で配置します。

### iOS の EAS クライアント アプリケーションのテスト 

iOS 9 以降のネイティブ メール アプリケーションで証明書認証をテストするには、次の手順に従います。

1.	上記の要件を満たす EAS プロファイルを構成します。

2.	(Intune などの MDM または Apple Configurator アプリケーションのいずれかを使用して) iOS デバイス上にプロファイルをインストールします。

3.	プロファイルが正しくインストールされたら、ネイティブ メール アプリケーションを開き、メールが同期していることを確認します。



## 無効化

クライアント証明書を失効させるために、Azure Active Directory は、証明機関の情報の一部としてアップロードされた URL から証明書失効リスト (CRL) をフェッチし、キャッシュします。CRL の最後の発行タイムスタンプ (**発効日**プロパティ) を使用し、CRL がまだ有効であることを確認します。CRL は定期的に参照されて、リストに含まれる証明書へのアクセスは無効になります。

即時の失効が必要な場合 (たとえば、ユーザーがデバイスを紛失した場合) は、ユーザーの認証トークンを無効にできます。認証トークンを無効にするには、Windows PowerShell を使用してこの特定のユーザーの **StsRefreshTokenValidFrom** フィールドを設定します。アクセスを無効にする各ユーザーの **StsRefreshTokenValidFrom** フィールドを更新する必要があります。
 
失効状態が継続していることを確認するには、CRL の**発効日**を **StsRefreshTokenValidFrom** で設定した値より後の日付に設定し、対象の証明書が CRL にあることを確認する必要があります。
 
次の手順は、**StsRefreshTokenValidFrom** フィールドを設定することで認証トークンを更新し、無効にするプロセスを簡単に示したものです。

1. 管理者の資格情報で MSOL サービスに接続します。

		$msolcred = get-credential 
		connect-msolservice -credential $msolcred 

1.	ユーザーの現在の StsRefreshTokensValidFrom 値を取得します。

		$user = Get-MsolUser -UserPrincipalName test@yourdomain.com` 
		$user.StsRefreshTokensValidFrom 


1.	現在のタイムスタンプと等しいユーザーの新しい StsRefreshTokensValidFrom 値を構成します。

		Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")


設定する日付は、現在より後の日付にする必要があります。日付を現在より後の日付にしないと、**StsRefreshTokensValidFrom** プロパティは設定されません。日付を現在より後の日付にすると、**StsRefreshTokensValidFrom** は、現在の時刻に設定されます (Set-MsolUser コマンドで指定した日付ではありません)。



<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png

<!---HONumber=AcomDC_0803_2016-->