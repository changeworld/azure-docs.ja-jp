---
title: Azure AD Connect:前提条件とハードウェア |Microsoft Docs
description: このトピックでは、Azure AD Connect を使用するための前提条件とハードウェア要件について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f250d4593c8dac8007590245e1b774b95d8fa786
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75767944"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Azure AD Connect の前提条件
このトピックでは、Azure AD Connect を使用するための前提条件とハードウェア要件について説明します。

## <a name="before-you-install-azure-ad-connect"></a>Azure AD Connect をインストールする前に
Azure AD Connect をインストールする前に、いくつか必要な項目があります。

### <a name="azure-ad"></a>Azure AD
* Azure AD テナント。 [Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)に 1 つ付属します。 次のポータルのいずれかを使用して、Azure AD Connect を管理できます。
  * [Azure ポータル](https://portal.azure.com)。
  * [Office ポータル](https://portal.office.com)。  
* [ドメインを追加して検証](../active-directory-domains-add-azure-portal.md) します。 たとえば、ユーザー向けに contoso.com を使用する予定の場合は、そのドメインが検証されていることと、使用しているドメインが既定のドメインである contoso.onmicrosoft.com だけではないことを確認します。
* Azure AD テナントでは、既定では 50,000 個のオブジェクトを使用できます。 ドメインを検証すると、制限が 300,000 個のオブジェクトに増加します。 Azure AD でさらに多くのオブジェクトが必要な場合は、制限を緩和するサポート ケースを開く必要があります。 500,000 個を超えるオブジェクトが必要な場合は、Office 365、Azure AD Basic、Azure AD Premium、Enterprise Mobility and Security などのライセンスが必要です。

### <a name="prepare-your-on-premises-data"></a>オンプレミスのデータの準備
* Azure AD および Office 365 に同期する前に、[IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) を使用して、ディレクトリ内の重複部分や書式の問題などのエラーを特定してください。
* [Azure AD で有効にできるオプションの同期機能](how-to-connect-syncservice-features.md) について確認し、どの機能を有効にする必要があるかを検討してください。

### <a name="on-premises-active-directory"></a>オンプレミスの Active Directory
* AD スキーマのバージョンとフォレストの機能レベルは、Windows Server 2003 以降である必要があります。 ドメイン コントローラーは、スキーマとフォレスト レベルの要件を満たしていれば、任意のバージョンを実行できます。
* **パスワード ライトバック**機能を使用する場合、ドメイン コントローラーが Windows Server 2008 R2 以降にインストールされている必要があります。
* Azure AD で使用されるドメイン コントローラーは、書き込み可能である必要があります。 RODC (読み取り専用ドメイン コントローラー) は**使用できません**。Azure AD Connect では、書き込みのリダイレクトを行いません。
* "ドット形式" (名前にピリオド "." が含まれる) の NetBios 名を使用するオンプレミスのフォレスト/ドメインは**使用できません**。
* [Active Directory のごみ箱を有効にする](how-to-connect-sync-recycle-bin.md)ことをお勧めします。

### <a name="azure-ad-connect-server"></a>Azure AD Connect サーバー
>[!IMPORTANT]
>Azure AD Connect サーバーは、重要な ID データを格納するため、[Active Directory 管理階層モデル](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)の説明に従い、階層 0 のコンポーネントとして取り扱う必要があります。

* Small Business Server または 2019 より前の Windows Server Essentials には、Azure AD Connect をインストールできません (Windows Server Essentials 2019 はサポートされます)。 サーバーは Windows Server Standard 以上を使用する必要があります。
* Azure AD Connect をドメイン コントローラーにインストールすることはお勧めできません。これは、セキュリティの慣例上の理由に加え、設定に通常よりも厳しい制限があるために、Azure AD Connect を正しくインストールできないためです。
* Azure AD Connect サーバーには、完全な GUI がインストールされている必要があります。 サーバー コアにインストールすることは**できません**。
>[!IMPORTANT]
>Small Business Server、Server Essentials、または Server Core への Azure AD Connect のインストールはサポートされていません。

* Azure AD Connect は、Windows Server 2012 以降にインストールする必要があります。 このサーバーは、ドメインに参加させる必要があります。また、ドメイン コントローラーまたはメンバー サーバーにすることができます。
* Azure AD Connect ウィザードを使用して ADFS 構成を管理している場合、Azure AD Connect サーバーで PowerShell トランスクリプション グループ ポリシーを有効にしてはなりません。 Azure AD Connect ウィザードを使用して同期構成を管理している場合は、PowerShell トランスクリプションを有効にすることができます。
* Active Directory Federation Services をデプロイする場合、AD FS または Web アプリケーション プロキシがインストールされるサーバーは、Windows Server 2012 R2 以降である必要があります。 [Windows リモート管理](#windows-remote-management) を有効にする必要があります。
* Active Directory フェデレーション サービスがデプロイされている場合は、 [SSL 証明書](#ssl-certificate-requirements)が必要です。
* Active Directory フェデレーション サービス (AD FS) がデプロイされている場合は、 [名前解決](#name-resolution-for-federation-servers)を構成する必要があります。
* 全体管理者が、MFA を有効にしている場合は、URL **https://secure.aadcdn.microsoftonline-p.com** は信頼済みサイトの一覧になければなりません。 MFA チャレンジを求められたときに、この URL がまだ追加されていない場合は、信頼済みサイトの一覧に追加するように促されます。 信頼済みサイトへの追加には、Internet Explorer を使用できます。
* Microsoft では、Azure AD Connect サーバーを強化して、お客様の IT 環境に含まれるこの重要なコンポーネントに対する、セキュリティ攻撃の対象領域を縮小することをお勧めしています。  以下の推奨事項に従うと、お客様の組織に対するセキュリティ リスクが低下します。

* ドメイン参加しているサーバー上に Azure AD Connect をデプロイし、管理アクセス権を、ドメイン管理者やその他の厳格に管理されたセキュリティ グループに制限します。

詳細については、次を参照してください。 

* [管理者グループのセキュリティ保護](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [ビルトイン Administrator アカウントのセキュリティ保護](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [攻撃対象領域の縮小によるセキュリティの向上と維持](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Active Directory の攻撃対象領域の縮小](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>Azure AD Connect で使用される SQL Server
* Azure AD Connect には、ID データを格納する SQL Server データベースが必要です。 既定では、SQL Server 2012 Express LocalDB (SQL Server Express の簡易バージョン) がインストールされています。 SQL Server Express のサイズ制限は 10 GB で、約 100,000 オブジェクトを管理できます。 さらに多くのディレクトリ オブジェクトを管理する必要がある場合は、インストール ウィザードで別の SQL Server インストール済み環境を指定する必要があります。 SQL Server のインストールの種類により、[Azure AD Connect のパフォーマンス](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors)に影響することがあります。
* SQL Server の別のインストールを使用する場合は、次の要件が適用されます。
  * Azure AD Connect では、Microsoft SQL Server 2012 (最新の Service Pack 付き) から SQL Server 2019 までのすべてのバージョンがサポートされています。 Microsoft Azure SQL Database は、データベースとして **サポートされていません** 。
  * 大文字と小文字が区別されない SQL 照合順序を使用する必要があります。 これらの照合順序は、名前に含まれる \_CI_ で識別します。 大文字と小文字が区別される照合順序 (名前に含まれる \_CS_ で識別) は**サポートされていません**。
  * 1 つの SQL インスタンスにつき保持できる同期エンジンは 1 つだけです。 FIM/MIM Sync、DirSync、または Azure AD Sync との SQL インスタンスの共有は**サポートされていません**。

### <a name="accounts"></a>アカウント
* 統合する Azure AD テナントの Azure AD 全体管理者アカウント。 このアカウントには**学校または組織のアカウント**を使用する必要があり、**Microsoft アカウント**を使用することはできません。
* 簡単設定を使用するか、DirSync からアップグレードする場合は、オンプレミスの Active Directory のエンタープライズ管理者アカウント。
* オンプレミスの Active Directory に対してカスタム設定のインストール パスまたは Enterprise Administrator アカウントを使用する場合は、[Active Directory でのアカウント](reference-connect-accounts-permissions.md)。

### <a name="connectivity"></a>接続
* Azure AD Connect サーバーには、イントラネット用とインターネット用の両方の DNS 解決が必要です。 DNS サーバーは、オンプレミス Active Directory と Azure AD エンドポイントの両方の名前を解決できる必要があります。
* お使いのイントラネット環境でファイアウォールを使用していて、Azure AD Connect サーバーとドメイン コントローラーの間でポートを開く必要がある場合の詳細については、[Azure AD Connect のポート](reference-connect-ports.md)に関する記事を参照してください。
* アクセスできる URL をプロキシまたはファイアウォールが制限している場合は、「[Office 365 URL および IP アドレス範囲](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)」に記載されている URL を開く必要があります。
  * ドイツで Microsoft Cloud を使用する場合、または Microsoft Azure Government クラウドを使用する場合は、 [Azure AD Connect 同期サービス インスタンスの考慮事項](reference-connect-instances.md) に関するページで URL を確認してください。
* Azure AD Connect (バージョン 1.1.614.0 以降) では、同期エンジンと Azure AD との間の通信の暗号化に既定で TLS 1.2 が使用されます。 基盤となるオペレーティング システムで TLS 1.2 が使用できない場合は、1 つ前のプロトコル (TLS 1.1 と TLS 1.0) に段階的にフォールバックされます。
* バージョン 1.1.614.0 未満の Azure AD Connect では、同期エンジンと Azure AD との間の通信の暗号化に既定で TLS 1.0 が使用されます。 TLS 1.2 に変更するには、「[Azure AD Connect 用に TLS 1.2 を有効にする](#enable-tls-12-for-azure-ad-connect)」の手順に従います。
* 送信プロキシを使用してインターネットに接続する場合は、次の設定を **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** ファイルに追加して、インストール ウィザードと Azure AD Connect 同期がインターネットと Azure AD に接続できるようにする必要があります。 このテキストは、ファイルの末尾に入力する必要があります。 このコードの &lt;PROXYADDRESS&gt; は実際のプロキシ IP アドレスまたはホスト名を表します。

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* プロキシ サーバーで認証が必要な場合は、[サービス アカウント](reference-connect-accounts-permissions.md#adsync-service-account)をドメイン内に配置する必要があり、カスタマイズした設定のインストール パスを使用して、[カスタム サービス アカウント](how-to-connect-install-custom.md#install-required-components)を指定する必要があります。 machine.config に別の変更も必要です。この machine.config の変更によって、インストール ウィザードと同期エンジンは、プロキシ サーバーからの認証要求に応答します。 **[構成]** ページを除くインストール ウィザードのすべてのページで、サインインしたユーザーの資格情報を使用します。 インストール ウィザードの最後の **[構成]** ページで、コンテキストが、自分で作成した[サービス アカウント](reference-connect-accounts-permissions.md#adsync-service-account)に切り替わります。 machine.config のセクションは、次のようになるはずです。

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Azure AD Connect がディレクトリ同期の過程で Web 要求を Azure AD に送信するとき、Azure AD から応答が返されるまでに長くて 5 分程度かかる場合があります。 一般に、プロキシ サーバーには、接続アイドル タイムアウトの構成を適用します。 この構成は 6 分以上に設定してください。

詳細については、[既定のプロキシ要素](https://msdn.microsoft.com/library/kd3cf2ex.aspx)に関する MSDN を参照してください。  
接続に問題が発生した場合は、[接続の問題に対するトラブルシューティング](tshoot-connect-connectivity.md)についてのページを参照してください。

### <a name="other"></a>その他
* 省略可能:同期を検証するテスト ユーザー アカウント。

## <a name="component-prerequisites"></a>コンポーネントの前提条件
### <a name="powershell-and-net-framework"></a>PowerShell と .NET Framework
Azure AD Connect は、Microsoft PowerShell と .NET 4.5.1 に依存しています。 これ以降のバージョンがサーバーにインストールされている必要があります。 Windows Server のバージョンに応じて、次の操作を行います。

* Windows Server 2012R2
  * Microsoft PowerShell は既定でインストールされています。 必要な操作はありません。
  * .NET Framework 4.5.1 以降のリリースは、Windows Update によって提供されます。 コントロール パネルで、Windows Server に最新の更新プログラムがインストールされていることを確認します。
* Windows Server 2012
  * Microsoft PowerShell の最新バージョンは、 **Microsoft ダウンロード センター**の [Windows Management Framework 4.0](https://www.microsoft.com/downloads)で入手できます。
  * .NET Framework 4.5.1 以降のリリースは、 [Microsoft ダウンロード センター](https://www.microsoft.com/downloads)で入手できます。


### <a name="enable-tls-12-for-azure-ad-connect"></a>Azure AD Connect 用に TLS 1.2 を有効にする
バージョン 1.1.614.0 未満の Azure AD Connect では、同期エンジン サーバーと Azure AD との間の通信の暗号化に既定で TLS 1.0 が使用されます。 これを変更するには、サーバー上で TLS 1.2 を既定で使用するように .NET アプリケーションを構成します。 TLS 1.2 の詳細については、「[Microsoft セキュリティ アドバイザリ 2960358](https://technet.microsoft.com/security/advisory/2960358)」を参照してください。

1.  ご使用のオペレーティング システムに .NET 4.5.1 修正プログラムがインストールされていることを確認してください。詳細については、「[マイクロソフト セキュリティ アドバイザリ 2960358](https://technet.microsoft.com/security/advisory/2960358)」を参照してください。 既にこの修正プログラムやこれ以降のリリースをサーバーにインストールしている可能性があります。
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319 "SchUseStrongCrypto"=dword:00000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PSH command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PSH command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PSH command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PSH connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PSH session should open to ensure remote PowerShell sessions can be established.

### SSL Certificate Requirements
* It’s strongly recommended to use the same SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
* The certificate must be an X509 certificate.
* You can use a self-signed certificate on federation servers in a test lab environment. However, for a production environment, we recommend that you obtain the certificate from a public CA.
  * If using a certificate that is not publicly trusted, ensure that the certificate installed on each Web Application Proxy server is trusted on both the local server and on all federation servers
* The identity of the certificate must match the federation service name (for example, sts.contoso.com).
  * The identity is either a subject alternative name (SAN) extension of type dNSName or, if there are no SAN entries, the subject name specified as a common name.  
  * Multiple SAN entries can be present in the certificate, provided one of them matches the federation service name.
  * If you are planning to use Workplace Join, an additional SAN is required with the value **enterpriseregistration.** followed by the User Principal Name (UPN) suffix of your organization, for example, **enterpriseregistration.contoso.com**.
* Certificates based on CryptoAPI next generation (CNG) keys and key storage providers are not supported. This means you must use a certificate based on a CSP (cryptographic service provider) and not a KSP (key storage provider).
* Wild-card certificates are supported.

### Name resolution for federation servers
* Set up DNS records for the AD FS federation service name (for example sts.contoso.com) for both the intranet (your internal DNS server) and the extranet (public DNS through your domain registrar). For the intranet DNS record, ensure that you use A records and not CNAME records. This is required for windows authentication to work correctly from your domain joined machine.
* If you are deploying more than one AD FS server or Web Application Proxy server, then ensure that you have configured your load balancer and that the DNS records for the AD FS federation service name (for example sts.contoso.com) point to the load balancer.
* For windows integrated authentication to work for browser applications using Internet Explorer in your intranet, ensure that the AD FS federation service name (for example sts.contoso.com) is added to the intranet zone in IE. This can be controlled via group policy and deployed to all your domain joined computers.

## Azure AD Connect supporting components
The following is a list of components that Azure AD Connect installs on the server where Azure AD Connect is installed. This list is for a basic Express installation. If you choose to use a different SQL Server on the Install synchronization services page, then SQL Express LocalDB is not installed locally.

* Azure AD Connect Health
* Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## Hardware requirements for Azure AD Connect
The table below shows the minimum requirements for the Azure AD Connect sync computer.

| Number of objects in Active Directory | CPU | Memory | Hard drive size |
| --- | --- | --- | --- |
| Fewer than 10,000 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| For 100,000 or more objects the full version of SQL Server is required | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| More than 600,000 |1.6 GHz |32 GB |500 GB |

The minimum requirements for computers running AD FS or Web Application Proxy Servers is the following:

* CPU: Dual core 1.6 GHz or higher
* MEMORY: 2 GB or higher
* Azure VM: A2 configuration or higher

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](whatis-hybrid-identity.md).
