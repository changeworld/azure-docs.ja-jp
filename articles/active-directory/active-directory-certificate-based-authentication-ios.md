---
title: "iOS の証明書ベースの認証の使用 | Microsoft Docs"
description: "iOS デバイスでソリューションの証明書ベースの認証を構成する方法について説明します。"
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: 26a6fc54-0153-44fb-b970-9b432c99e9f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: ce9474f2926a856673efbab5103a308d31001343
ms.openlocfilehash: fe00603de855e621b751004fdbd74acafaee017f


---
# <a name="get-started-with-certificate-based-authentication-on-ios"></a>iOS の証明書ベースの認証の使用
> [!div class="op_single_selector"]
> * [iOS](active-directory-certificate-based-authentication-ios.md)
> * [Android](active-directory-certificate-based-authentication-android.md)
> 
> 

このトピックでは、Office 365 Enterprise、Business、および Education プランでのテナントのユーザーの iOS デバイス上で証明書ベースの認証 (CBA) を構成し、使用する方法について説明します。 

CBA では、Exchange online アカウントを次に接続する場合、Azure Active Directory と、Android または iOS デバイス上のクライアント証明書を使用して認証できます。 

* Microsoft Outlook や Microsoft Word などの Office モバイル アプリケーション   
* Exchange ActiveSync (EAS) クライアント 

この機能を構成すると、モバイル デバイスで特定のメールおよび Microsoft Office アプリケーションにユーザー名とパスワードの組み合わせを入力する必要がなくなります。 

## <a name="supported-scenarios-and-requirements"></a>サポートされるシナリオと要件
### <a name="general-requirements"></a>一般的な要件
このトピックのすべてのシナリオでは、次のタスクが必要です。  

* 証明機関にアクセスし、クライアント証明書を発行します。  
* 証明機関は、Azure Active Directory で構成する必要があります。 「 [使用の開始](#getting-started) 」セクションで構成を完了する方法の詳細な手順を確認できます。  
* ルート証明機関および中間証明機関は、Azure Active Directory で構成する必要があります。  
* 各証明機関には、インターネットに接続する URL から参照できる証明書失効リスト (CRL) が必要です。  
* クライアント証明書は、クライアント認証に対して発行する必要があります。  
* Exchange ActiveSync クライアントの場合に限り、クライアント証明書では、サブジェクト代替名フィールドのプリンシパル名または RFC822 名の値のいずれかで、Exchange online のユーザーのルーティング可能な電子メール アドレスが必要になります。 Azure Active Directory は、ディレクトリ内のプロキシ アドレス属性に RFC822 値をマップします。  

### <a name="office-mobile-applications-support"></a>Office モバイル アプリケーションのサポート
| アプリケーション | サポート |
| --- | --- |
| Word/Excel/PowerPoint |![○][1] |
| OneNote |![○][1] |
| OneDrive |![○][1] |
| Outlook |![○][1] |
| Yammer |![○][1] |
| Skype for Business |近日対応予定 |

### <a name="requirements"></a>必要条件
デバイスの OS バージョンは、iOS 9 以上である必要があります。 

フェデレーション サーバーを構成する必要があります。  

Azure Authenticator は、iOS の Office アプリケーションに必要です。  

Azure Active Directory でクライアント証明書を失効させるには、ADFS トークンに次の要求が必要です。  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
   (クライアント証明書のシリアル番号) 
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
   (クライアント証明書の発行者の文字列) 

Azure Active Directory は、ADFS トークン (またはその他の SAML トークン) で利用できる場合に、これらの要求を更新トークンに追加します。 更新トークンを検証する必要がある場合、この情報を使用して失効を確認します。 

ベスト プラクティスとして、次を参照して、ADFS エラー ページを更新することをお勧めします。

* iOS に Azure Authenticator をインストールするための要件
* ユーザー証明書を取得する手順 

詳細については、「 [AD FS サインイン ページのカスタマイズ](https://technet.microsoft.com/library/dn280950.aspx)」を参照してください。

(先進認証が有効になった) 一部の Office アプリは、要求で *prompt=login* を Azure AD に送信します。 既定では、Azure AD は、ADFS への要求でこれを *wauth=usernamepassworduri* (ADFS に U/P 認証を実行するように要求) と *wfresh=0* (ADFS に SSO 状態を無視して、新しい認証を実行するように要求) に変換します。 これらのアプリに対して証明書ベースの認証を有効にするには、既定の Azure AD の動作を変更する必要があります。 フェデレーション ドメイン設定の *PromptLoginBehavior* を '*無効*' に設定するだけです。 このタスクを実行するには、[MSOLDomainFederationSettings](https://docs.microsoft.com/en-us/powershell/msonline/v1/set-msoldomainfederationsettings) コマンドレットを使用します。

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`
  

### <a name="exchange-activesync-clients-support"></a>Exchange ActiveSync クライアントのサポート
iOS 9 以降では、ネイティブの iOS メール クライアントがサポートされます。 その他の Exchange ActiveSync アプリケーションについては、この機能のサポート状況をアプリケーションの開発者にお問い合わせください。  

## <a name="getting-started"></a>使用の開始
開始するには、Azure Active Directory で証明機関を構成する必要があります。 各証明機関については、次をアップロードします。 

* 証明書の公開部分 ( *.cer* 形式) 
* 証明書失効リスト (CRL) が存在する、インターネットに接続する URL

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


情報をアップロードするには、Windows PowerShell から Azure AD モジュールを使用します。  
証明機関を追加、削除、または変更するための例を次に示します。 

### <a name="configuring-your-azure-ad-tenant-for-certificate-based-authentication"></a>証明書ベースの認証に使用する Azure AD テナントの構成
1. Windows PowerShell を管理者特権で起動します。 
2. Azure AD モジュールをインストールします。 バージョン [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) 以降をインストールする必要があります。  
   
        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33 
3. ターゲット テナントに接続します。 
   
        Connect-AzureAD 

### <a name="adding-a-new-certificate-authority"></a>新しい証明機関の追加
1. 証明機関のさまざまなプロパティを設定して、Azure Active Directory に追加します。 
   
        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 
2. 証明機関を取得します。 
   
        Get-AzureADTrustedCertificateAuthority 

### <a name="retrieving-the-list-certificate-authorities"></a>証明機関の一覧の取得
テナントの Azure Active Directory に現時点で格納されている証明機関を取得します。 

        Get-AzureADTrustedCertificateAuthority 


### <a name="removing-a-certificate-authority"></a>証明機関の削除
1. 証明機関を取得します。 
   
     $c=Get-AzureADTrustedCertificateAuthority 
2. 証明機関の証明書を削除します。 
   
        Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 

### <a name="modfiying-a-certificate-authority"></a>証明機関の変更
1. 証明機関を取得します。 
   
     $c=Get-AzureADTrustedCertificateAuthority 
2. 証明機関のプロパティを変更します。 
   
        $c[0].AuthorityType=1 
3. **証明機関**を設定します。 
   
        Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 

## <a name="testing-office-mobile-applications"></a>Office モバイル アプリケーションのテスト
モバイルの Office アプリケーションの証明書認証をテストするには、次の手順に従います。 

1. テスト デバイスでは、アプリ ストアから Office モバイル アプリケーション (例: OneDrive) をインストールします。
2. ユーザー証明書がテスト デバイスにプロビジョニングされていることを確認します。 
3. アプリケーションを起動します。 
4. ユーザー名を入力し、使用するユーザー証明書を選択します。 

正常にサインインします。 

## <a name="testing-exchange-activesync-client-applications"></a>Exchange ActiveSync クライアント アプリケーションのテスト
証明書ベースの認証を使用した Exchange ActiveSync にアクセスするには、クライアント証明書を含む EAS プロファイルがアプリケーションに対して使用可能になっている必要があります。 EAS プロファイルには次の情報が表示されます。

* 認証に使用するユーザー証明書 
* EAS エンドポイントは、outlook.office365.com にする必要があります (現時点でこの機能は、Exchange online マルチテナント環境でのみサポート)。

EAS プロファイルをデバイス上に構成して配置するには、Intune などの MDM を使用するか、EAS プロファイルの証明書をデバイス上に手動で配置します。  

### <a name="testing-eas-client-applications-on-ios"></a>iOS の EAS クライアント アプリケーションのテスト
iOS 9 以降のネイティブ メール アプリケーションで証明書認証をテストするには、次の手順に従います。 

1. 上記の要件を満たす EAS プロファイルを構成します。 
2. (Intune などの MDM または Apple Configurator アプリケーションのいずれかを使用して) iOS デバイス上にプロファイルをインストールします。
3. プロファイルが正しくインストールされたら、ネイティブ メール アプリケーションを開き、メールが同期していることを確認します。

## <a name="revocation"></a>無効化
クライアント証明書を失効させるために、Azure Active Directory は、証明機関の情報の一部としてアップロードされた URL から証明書失効リスト (CRL) をフェッチし、キャッシュします。 CRL の最後の発行タイムスタンプ (**発効日** プロパティ) を使用し、CRL がまだ有効であることを確認します。 CRL は定期的に参照されて、リストに含まれる証明書へのアクセスは無効になります。

即時の失効が必要な場合 (たとえば、ユーザーがデバイスを紛失した場合) は、ユーザーの認証トークンを無効にできます。 認証トークンを無効にするには、Windows PowerShell を使用してこの特定のユーザーの **StsRefreshTokenValidFrom** フィールドを設定します。 アクセスを無効にする各ユーザーの **StsRefreshTokenValidFrom** フィールドを更新する必要があります。

失効状態が継続していることを確認するには、CRL の**発効日**を **StsRefreshTokenValidFrom** で設定した値より後の日付に設定し、対象の証明書が CRL にあることを確認する必要があります。

次の手順は、 **StsRefreshTokenValidFrom** フィールドを設定することで認証トークンを更新し、無効にするプロセスを簡単に示したものです。 

1. 管理者の資格情報で MSOL サービスに接続します。 
   
        $msolcred = get-credential 
        connect-msolservice -credential $msolcred 
2. ユーザーの現在の StsRefreshTokensValidFrom 値を取得します。 
   
     $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`   $user.StsRefreshTokensValidFrom 
3. 現在のタイムスタンプと等しいユーザーの新しい StsRefreshTokensValidFrom 値を構成します。 
   
     Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

設定する日付は、現在より後の日付にする必要があります。 日付を現在より後の日付にしないと、 **StsRefreshTokensValidFrom** プロパティは設定されません。 日付を現在より後の日付にすると、 **StsRefreshTokensValidFrom** は、現在の時刻に設定されます (Set-MsolUser コマンドで指定した日付ではありません)。 

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png



<!--HONumber=Dec16_HO5-->


