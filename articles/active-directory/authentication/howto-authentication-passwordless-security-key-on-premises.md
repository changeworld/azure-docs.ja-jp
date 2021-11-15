---
title: オンプレミスのリソースへのパスワードなしのセキュリティ キー サインイン - Azure Active Directory
description: Azure Active Directory を使用して、オンプレミスのリソースへのパスワードレス セキュリティ キー サインインを有効にする方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce8869a15a1c94ab2f3227780de8eb37bbf52b6f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505424"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-by-using-azure-ad"></a>Azure AD を使用してオンプレミスのリソースへのパスワードレス セキュリティ キー サインインを有効にする 

このドキュメントでは、*Azure Active Directory (Azure AD) 参加済み* と *ハイブリッド Azure AD 参加済み* の両方の Windows 10 デバイスがある環境で、オンプレミスのリソースに対するパスワードレス認証を有効にする方法について説明します。 このパスワードレス認証機能により、Microsoft と互換性のあるセキュリティ キーを使用したときに、オンプレミスのリソースへのシームレスなシングル サインオン (SSO) が提供されます。

## <a name="use-sso-to-sign-in-to-on-premises-resources-by-using-fido2-keys"></a>SSO を使用して FIDO2 キーでオンプレミスのリソースにサインインする

Azure AD では、1 つ以上の Active Directory ドメインの Kerberos Ticket Granting Ticket (TGT) を発行できます。 この機能により、ユーザーは FIDO2 セキュリティ キーなどの最新の資格情報で Windows にサインインし、従来の Active Directory ベースのリソースにアクセスできます。 Kerberos サービス チケットと認可は、引き続きオンプレミスの Active Directory ドメイン コントローラー (DC) によって制御されます。

Azure AD Kerberos サーバー オブジェクトがオンプレミスの Active Directory インスタンスに作成され、Azure Active Directory に安全に公開されます。 このオブジェクトは、どの物理サーバーにも関連付けられていません。 これは、Active Directory ドメインの Kerberos TGT を生成するために Azure Active Directory で使用できるリソースです。

:::image type="Image" source="./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png" alt-text="Azure AD と Active Directory Domain Services から T G T を取得する方法を示す図" lightbox="./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png":::

1. ユーザーは、FIDO2 セキュリティ キーを使用して Windows 10 デバイスにサインインし、Azure AD に対して認証を行います。
1. Azure AD によって、ユーザーのオンプレミスの Active Directory ドメインと一致する Kerberos サーバー キーがディレクトリにあるかどうかが確認されます。

   Azure AD によって、ユーザーのオンプレミスの Active Directory ドメインの Kerberos TGT が生成されます。 TGT にはユーザーの SID のみが含まれ、認可データは含まれていません。

1. ユーザーの Azure AD プライマリ更新トークン (PRT) と共に、TGT がクライアントに返されます。
1. クライアント マシンはオンプレミスの Azure AD DC に接続し、部分的な TGT を完全な形式の TGT と交換します。
1. この時点でクライアント マシンには Azure AD PRT と完全な Active Directory TGT があり、クラウドとオンプレミスの両方のリソースにアクセスできます。

## <a name="prerequisites"></a>前提条件

この記事の手順を開始する前に、組織では、[Windows 10 デバイスへのパスワードレス セキュリティ キー サインインの有効化](howto-authentication-passwordless-security-key.md)に関する記事の手順を完了する必要があります。

また、次のシステム要件を満たしている必要があります。

- デバイスでは、Windows 10 バージョン 2004 以降を実行している必要があります。

- [Azure AD Connect バージョン 1.4.32.0 以降](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)を実行している必要があります。
  - 使用可能な Azure AD ハイブリッド認証オプションの詳細については、次の記事を参照してください。
    - [Azure AD ハイブリッド ID ソリューションの適切な認証方法を選択する](../hybrid/choose-ad-authn.md)
    - [Azure AD Connect で使用するインストールの種類の選択](../hybrid/how-to-connect-install-select-installation.md)

- Windows Server ドメイン コントローラーには、次のサーバーのパッチがインストールされている必要があります。
    - [Windows Server 2016](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
    - [Windows Server 2019](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="supported-scenarios"></a>サポートされるシナリオ

この記事のシナリオでは、次の両方のインスタンスで SSO がサポートされています。

- Microsoft 365 や他の Security Assertion Markup Language (SAML) 対応アプリケーションなどのクラウド リソース。
- オンプレミスのリソースと、Web サイトに対する Windows 統合認証。 リソースには、IIS 認証を必要とする Web サイトおよび SharePoint サイトや、NTLM 認証を使用するリソースを含めることができます。

### <a name="unsupported-scenarios"></a>サポートされていないシナリオ

以下のシナリオはサポートされていません。

- Windows Server Active Directory Domain Services (AD DS) 参加済みの (オンプレミス専用デバイスの) デプロイ。
- セキュリティ キーを使用したリモート デスクトップ プロトコル (RDP)、仮想デスクトップ インフラストラクチャ (VDI)、Citrix のシナリオ。
- セキュリティ キーを使用した S/MIME。
- セキュリティ キーを使用した *[別のユーザーとして実行]* 。
- セキュリティ キーを使用したサーバーへのログイン。

## <a name="install-the-azure-ad-kerberos-powershell-module"></a>Azure AD Kerberos PowerShell モジュールをインストールする

[Azure AD Kerberos PowerShell モジュール](https://www.powershellgallery.com/packages/AzureADHybridAuthenticationManagement)は、管理者に FIDO2 管理機能を提供します。

1. [管理者として実行] オプションを使用して、PowerShell プロンプトを開きます。
1. Azure AD Kerberos PowerShell モジュールをインストールします。

   ```powershell
   # First, ensure TLS 1.2 for PowerShell gallery access.
   [Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12

   # Install the Azure AD Kerberos PowerShell Module.
   Install-Module -Name AzureADHybridAuthenticationManagement -AllowClobber
   ```

> [!NOTE]
> - Azure AD Kerberos PowerShell モジュールでは、[AzureADPreview PowerShell モジュール](https://www.powershellgallery.com/packages/AzureADPreview)を使用して高度な Azure Active Directory 管理機能を提供します。 [AzureAD PowerShell モジュール](https://www.powershellgallery.com/packages/AzureAD)がローカル コンピューターに既にインストールされている場合、競合が原因で、ここで説明するインストールが失敗する可能性があります。 インストール時の競合を防ぐために、"-AllowClobber" オプション フラグを必ず含めてください。
> - Azure AD Kerberos PowerShell モジュールは、Azure AD Connect ソリューションに依存せずに、オンプレミスの Active Directory ドメイン コントローラーにアクセスできる任意のコンピューターにインストールできます。
> - Azure AD Kerberos PowerShell モジュールは、[PowerShell ギャラリー](https://www.powershellgallery.com/)を介して配布されます。 PowerShell ギャラリーは、PowerShell コンテンツの中央リポジトリです。 ここには、PowerShell コマンドと Desired State Configuration (DSC) リソースを含む、便利な PowerShell モジュールがあります。

## <a name="create-a-kerberos-server-object"></a>Kerberos サーバー オブジェクトを作成する

管理者は Azure AD Kerberos PowerShell モジュールを使用して、オンプレミスのディレクトリに Azure AD Kerberos サーバー オブジェクトを作成します。

Azure AD ユーザーを含む組織内の各ドメインおよびフォレストで、次の手順を実行してください。

1. [管理者として実行] オプションを使用して、PowerShell プロンプトを開きます。
1. 次の PowerShell コマンドを実行して、オンプレミスの Active Directory ドメインと Azure Active Directory テナントの両方に新しい Azure AD Kerberos サーバー オブジェクトを作成します。

   > [!NOTE]
   > 次の例の `contoso.corp.com` を、オンプレミスの Active Directory ドメイン名に置き換えます。

   ```powershell
   # Specify the on-premises Active Directory domain. A new Azure AD
   # Kerberos Server object will be created in this Active Directory domain.
   $domain = "contoso.corp.com"

   # Enter an Azure Active Directory global administrator username and password.
   $cloudCred = Get-Credential

   # Enter a domain administrator username and password.
   $domainCred = Get-Credential

   # Create the new Azure AD Kerberos Server object in Active Directory
   # and then publish it to Azure Active Directory.
   Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
   ```

   > [!NOTE]
   > ドメイン管理者特権があるアカウントでドメイン参加済みマシンを操作している場合は、"-DomainCredential" パラメーターを省略できます。 "-DomainCredential" パラメーターが指定されていない場合、オンプレミスの Active Directory ドメイン コントローラーにアクセスする際に、現在の Windows ログイン資格情報が使用されます。

   ```powershell
   # Specify the on-premises Active Directory domain. A new Azure AD
   # Kerberos Server object will be created in this Active Directory domain.
   $domain = "contoso.corp.com"

   # Enter an Azure Active Directory global administrator username and password.
   $cloudCred = Get-Credential

   # Create the new Azure AD Kerberos Server object in Active Directory
   # and then publish it to Azure Active Directory.
   # Use the current windows login credential to access the on-prem AD.
   Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred
   ```

   > [!NOTE]
   > 組織でパスワードベースのサインインを保護し、多要素認証、FIDO2、スマート カード テクノロジなどの最新の認証方法を適用している場合は、`-UserPrincipalName` パラメーターに全体管理者のユーザー プリンシパル名 (UPN) を指定する必要があります。
   > - 次の例の `contoso.corp.com` を、オンプレミスの Active Directory ドメイン名に置き換えます。
   > - 次の例の `administrator@contoso.onmicrosoft.com` を、全体管理者の UPN に置き換えます。

   ```powershell
   # Specify the on-premises Active Directory domain. A new Azure AD
   # Kerberos Server object will be created in this Active Directory domain.
   $domain = "contoso.corp.com"

   # Enter a UPN of an Azure Active Directory global administrator
   $userPrincipalName = "administrator@contoso.onmicrosoft.com"

   # Enter a domain administrator username and password.
   $domainCred = Get-Credential

   # Create the new Azure AD Kerberos Server object in Active Directory
   # and then publish it to Azure Active Directory.
   # Open an interactive sign-in prompt with given username to access the Azure AD.
   Set-AzureADKerberosServer -Domain $domain -UserPrincipalName $userPrincipalName -DomainCredential $domainCred
   ```

### <a name="view-and-verify-the-azure-ad-kerberos-server"></a>Azure AD Kerberos サーバーを表示して確認する

新しく作成された Azure AD Kerberos サーバーは、次のコマンドを使用して表示および確認できます。

```powershell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

このコマンドで、Azure AD Kerberos サーバーのプロパティが出力されます。 プロパティを確認して、すべてが適切な順序であることを確認できます。

> [!NOTE]
> 資格情報を指定して別のドメインに対して実行すると、NTLM を使用して接続され、失敗します。 ユーザーが Active Directory の Protected Users セキュリティ グループに属している場合は、これらの手順を実行して問題を解決します: **ADConnect** で別のドメイン ユーザーとしてサインインします。"-domainCredential" を指定しないでください。 現在サインインしているユーザーの Kereberos チケットが使用されます。 `whoami /groups` を実行することで、前のコマンドを実行するために必要な Active Directory のアクセス許可がユーザーにあるかどうかを検証できます。
 
| プロパティ | 説明 |
| --- | --- |
| id | AD DS DC オブジェクトの一意の ID。 この ID は、"*スロット*" または "*ブランチ ID*" と呼ばれることもあります。 |
| DomainDnsName | Active Directory ドメインの DNS ドメイン名。 |
| ComputerAccount | Azure AD Kerberos サーバー オブジェクト (DC) のコンピューター アカウント オブジェクト。 |
| UserAccount | Azure AD Kerberos サーバーの TGT 暗号化キーを保持する、無効なユーザー アカウント オブジェクト。 このアカウントのドメイン名は `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` です。 |
| KeyVersion | Azure AD Kerberos サーバーの TGT 暗号化キーのキー バージョン。 バージョンは、キーの作成時に割り当てられます。 バージョンは、キーがローテーションされるたびに増やされます。 増分はレプリケーション メタデータに基づいており、ほとんどの場合、1 より大きい値です。 たとえば、初期の *KeyVersion* が *192272* だったとします。 キーが最初にローテーションされると、バージョンは *212621* に進む可能性があります。 確認すべき重要な点は、オンプレミスのオブジェクトの *KeyVersion* とクラウド オブジェクトの *CloudKeyVersion* が同じであることです。 |
| KeyUpdatedOn | Azure AD Kerberos サーバーの TGT 暗号化キーが更新または作成された日時。 |
| KeyUpdatedFrom | Azure AD Kerberos サーバーの TGT 暗号化キーが最後に更新された DC。 |
| CloudId | Azure AD オブジェクトの ID。 この表の最初の行の ID と一致している必要があります。 |
| CloudDomainDnsName | Azure AD オブジェクトの *DomainDnsName*。 この表の 2 番目の行の *DomainDnsName* と一致している必要があります。 |
| CloudKeyVersion | Azure AD オブジェクトの *KeyVersion*。 この表の 5 番目の行の *KeyVersion* と一致している必要があります。 |
| CloudKeyUpdatedOn | Azure AD オブジェクトの *KeyUpdatedOn*。 この表の 6 番目の行の *KeyUpdatedOn* と一致している必要があります。 |
| | |

### <a name="rotate-the-azure-ad-kerberos-server-key"></a>Azure AD Kerberos サーバー キーをローテーションする

Azure AD Kerberos サーバー暗号化の *krbtgt* キーは、定期的にローテーションする必要があります。 Active Directory DC の他のすべての *krbtgt* キーのローテーションに使用するものと同じスケジュールに従うことをお勧めします。

> [!WARNING]
> *krbtgt* キーをローテーションできるツールはほかにもあります。 ただし、Azure AD Kerberos サーバーの *krbtgt* キーのローテーションには、このドキュメントに記載されているツールを使用する必要があります。 これにより、オンプレミスの Active Directory と Azure AD の両方でキーが確実に更新されます。

```powershell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="remove-the-azure-ad-kerberos-server"></a>Azure AD Kerberos サーバーを削除する

シナリオを元に戻し、オンプレミスの Active Directory と Azure AD の両方から Azure AD Kerberos サーバーを削除する場合は、次のコマンドを実行します。

```powershell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multiforest-and-multidomain-scenarios"></a>マルチフォレストとマルチドメインのシナリオ

Azure AD Kerberos サーバー オブジェクトは、Azure AD 内では *KerberosDomain* オブジェクトとして表されます。 オンプレミスの各 Active Directory ドメインは、Azure AD 内では 1 つの *KerberosDomain* オブジェクトとして表されます。

たとえば、`contoso.com` と `fabrikam.com` の 2 つのドメインを含む Active Directory フォレストが組織にあるとします。 Azure AD でフォレスト全体の Kerberos TGT を発行することを許可した場合は、Azure AD 内に 2 つの *KerberosDomain* オブジェクトがあります。1 つは `contoso.com` 用の *KerberosDomain* オブジェクトで、もう 1 つは `fabrikam.com` 用です。 複数の Active Directory フォレストがある場合は、各フォレスト内のドメインごとに 1 つの *KerberosDomain* オブジェクトがあります。

Azure AD ユーザーを含む組織内の各ドメインとフォレストで、「[Kerberos サーバー オブジェクトを作成する](#create-a-kerberos-server-object)」の手順に従います。

## <a name="known-behavior"></a>既知の動作

パスワードの有効期限が切れている場合、FIDO を使用したサインインはブロックされます。 ユーザーは、FIDO を使用してログインする前に、パスワードをリセットしておく必要があります。

## <a name="troubleshooting-and-feedback"></a>トラブルシューティングとフィードバック

このパスワードレス セキュリティ キー サインイン機能について問題が発生した場合や、フィードバックを共有したい場合は、次の手順を実行して、Windows フィードバック Hub アプリ経由で共有します。

1. **フィードバック Hub** を開き、サインインしていることを確認します。
1. 次のカテゴリを選択してフィードバックを送信します。
   - カテゴリ:セキュリティとプライバシー
   - サブカテゴリ: FIDO
1. ログをキャプチャするには、 **[Recreate my Problem]\(問題の再現\)** オプションを使用します。

## <a name="passwordless-security-key-sign-in-faq"></a>パスワードレス セキュリティ キー サインインに関する FAQ

パスワードレス サインインについてよく寄せられる質問とその回答を次に示します。

### <a name="does-passwordless-security-key-sign-in-work-in-my-on-premises-environment"></a>パスワードレス セキュリティ キー サインインはオンプレミス環境で機能しますか?

この機能は、純粋なオンプレミス AD DS 環境では機能しません。

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>私の組織では、リソースにアクセスするために 2 要素認証が必要です。 この要件をサポートするにはどうすればよいですか。

セキュリティ キーは、さまざまなフォーム ファクターで提供されています。 2 番目の要素として PIN または生体認証を使用してデバイスを有効にする方法については、デバイスの製造元にお問い合わせください。

### <a name="can-administrators-set-up-security-keys"></a>管理者はセキュリティ キーを設定できますか?

Microsoft は、この機能の一般提供 (GA) リリースに向けてこの機能に取り組んでいます。

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>準拠しているセキュリティ キーはどこで見つけることができますか。

準拠しているセキュリティ キーについては、「[FIDO2 セキュリティ キー](concept-authentication-passwordless.md#fido2-security-keys)」を参照してください。

### <a name="what-can-i-do-if-i-lose-my-security-key"></a>セキュリティ キーを紛失した場合はどうすればよいですか?

セキュリティ キーを取得するには、Azure portal にサインインし、 **[セキュリティ情報]** ページに移動します。

### <a name="what-can-i-do-if-im-unable-to-use-the-fido-security-key-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>ハイブリッド Azure AD 参加済みマシンを作成した直後に FIDO セキュリティ キーを使用できない場合はどうすればよいですか?

ハイブリッド Azure AD 参加済みマシンをクリーンインストールした場合、ドメイン参加および再起動プロセスの後、FIDO セキュリティ キーを使用してサインインするには、パスワードを使用してサインインし、ポリシーが同期されるまで待つ必要があります。

- コマンド プロンプト ウィンドウで `dsregcmd /status` を実行して現在の状態を確認し、**AzureAdJoined** と **DomainJoined** の両方の状態に *[YES]* と表示されていることを確認します。
- 同期のこの遅延は、ドメイン参加済みデバイスの既知の制限であり、FIDO に固有のものではありません。

### <a name="what-if-im-unable-to-get-single-sign-on-to-my-ntlm-network-resource-after-i-sign-in-with-fido-and-get-a-credential-prompt"></a>FIDO を使用してサインインし、資格情報プロンプトが表示された後、NTLM ネットワーク リソースにシングル サインオンできない場合はどうなりますか?

時間内に応答してリソース要求を処理できるように、十分な数の DC に修正プログラムが適用されていることを確認してください。 DC で機能が実行されているかどうかを確認するには、`nltest /dsgetdc:contoso /keylist /kdc` を実行し、出力を確認します。

> [!NOTE]
> `nltest` コマンドの `/keylist` スイッチは、Windows 10 v2004 以降のクライアントで使用できます。


### <a name="do-fido2-security-keys-work-in-a-windows-login-with-rodc-present-in-the-hybrid-environment"></a>ハイブリッド環境に RODC が存在する Windows ログインで FIDO2 セキュリティ キーは機能しますか?

FIDO2 Windows ログインでは、ユーザー TGT を交換するために書き込み可能 DC が検索されます。 サイトごとに少なくとも 1 つの書き込み可能 DC があれば、ログインは正常に機能します。 

## <a name="next-steps"></a>次の手順

[パスワードレス認証の詳細を確認する](concept-authentication-passwordless.md)
