---
title: フェデレーションを使用して Azure AD MFA に移行する - Azure Active Directory
description: オンプレミスの Azure MFA Server から、フェデレーションを使用した Azure AD MFA への移行に関するステップ バイ ステップのガイダンス
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c5d92d16f5ac9fcd8aa69ce9fd71f4844a77d28
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352715"
---
# <a name="migrate-to-azure-ad-mfa-with-federation"></a>フェデレーションを使用して Azure AD MFA に移行する

多要素認証 (MFA) ソリューションを Azure Active Directory (Azure AD) に移行することは、クラウドに移行するための最適な第一歩です。 今後、ユーザー認証のために Azure AD に移行することも検討してください。 詳細については、クラウド認証を使用して Azure AD MFA に移行するためのプロセスを参照してください。

フェデレーションを使用して Azure AD MFA に移行するために、AD FS に Azure MFA 認証プロバイダーがインストールされます。 移行されたユーザーに Azure MFA を使用するように、Azure AD 証明書利用者信頼とその他の証明書利用者信頼が構成されます。

次の図は、この移行のプロセスを示しています。

![プロセスの手順を示すフロー チャート。 これらは、このドキュメントの見出しに同じ順序で配置されます。](./media/how-to-migrate-mfa-server-to-azure-mfa-with-federation/mfa-federation-flow.png)

## <a name="create-migration-groups"></a>移行グループを作成する

新しい条件付きアクセス ポリシーを作成するには、それらのポリシーをグループに割り当てる必要があります。 この目的のために、既存の Azure AD セキュリティ グループまたは Microsoft 365 グループを使用できます。 新しいものを作成または同期することもできます。

また、Azure AD MFA にユーザーを繰り返し移行するための Azure AD セキュリティ グループも必要です。 これらのグループは、要求規則で使用されます。

セキュリティに使用されるグループを再利用しないでください。 セキュリティ グループを使用して、条件付きアクセス ポリシーで高価値アプリのグループをセキュリティで保護する場合、そのグループのみを使用する必要があります。

## <a name="prepare-ad-fs"></a>AD FS を準備する

### <a name="upgrade-ad-fs-server-farm-to-2019-fbl-4"></a>AD FS サーバー ファームを 2019、FBL 4 にアップグレードする

AD FS 2019 では、アプリケーションなど、証明書利用者に対して追加の認証方法を指定できます。 グループ メンバーシップを使用して、認証プロバイダーを決定します。 追加の認証方法を指定すると、移行中に他の認証を維持しながら、Azure AD MFA に切り替えることができます。 詳細については、「[WID データベースを使用した、Windows Server 2016 での AD FS へのアップグレード](/windows-server/identity/ad-fs/deployment/upgrading-to-ad-fs-in-windows-server)」を参照してください。 この記事では、ファームを AD FS 2019 にアップグレードし、FBL を 4 にアップグレードする方法について説明します。

### <a name="configure-claims-rules-to-invoke-azure-ad-mfa"></a>Azure AD MFA を呼び出す要求規則を構成する

MFA は Azure AD MFA の追加の認証方法のため、ユーザーのグループを割り当てて、それを使用できます。 これを行うには、要求規則 (証明書利用者信頼とも呼ばれます) を構成します。 グループを使用すると、グローバルまたはアプリケーションによって、どの認証プロバイダーが呼び出されるかを制御できます。 たとえば、統合されたセキュリティ情報に登録したユーザーまたは自分の電話番号を移行したユーザーに対して Azure AD MFA を呼び出すことができますが、そうしなかったユーザーに対しては MFA Server を呼び出すことができます。

> [!NOTE]
> 要求規則には、オンプレミスのセキュリティ グループが必要です。 要求規則を変更する前に、それらをバックアップします。


#### <a name="back-up-existing-rules"></a>既存のルールをバックアップする

新しい要求規則を構成する前に、既存の規則をバックアップします。 これらのルールは、クリーンアップ手順の一部として復元する必要があります。 

構成に応じて、既存の規則をコピーし、移行用に作成される新しい規則を追加することが必要になる場合もあります。  

既存のグローバル ルールを表示するには、次のように実行します。  

```powershell
Get-AdfsAdditionalAuthenticationRule
```

既存の証明書利用者信頼を表示するには、次のコマンドを実行して、RPTrustName を証明書利用者信頼の要求規則の名前に置き換えます。 

```powershell
(Get-AdfsRelyingPartyTrust -Name "RPTrustName").AdditionalAuthenticationRules 
```

#### <a name="access-control-policies"></a>アクセス制御ポリシー

> [!NOTE]
> グループ メンバーシップに基づいて特定の認証プロバイダーが呼び出されるように、アクセス制御ポリシーを構成することはできません。 

 
アクセス制御ポリシーから追加の認証規則に切り替えるには、MFA Server 認証プロバイダーを使用して、各証明書利用者信頼に対して次のコマンドを実行します。


```powershell
Set-AdfsRelyingPartyTrust -TargetName AppA -AccessControlPolicyName $Null
```

 

このコマンドでは、現在のアクセス制御ポリシーから追加の認証規則にロジックを移行します。


#### <a name="set-up-the-group-and-find-the-sid"></a>グループを設定して SID を検索する

Azure AD MFA を呼び出すユーザーを配置する特定のグループを用意する必要があります。 そのグループのセキュリティ識別子 (SID) が必要になります。

グループ SID を検索するには、次のコマンドとグループ名を使用します。

`Get-ADGroup "GroupName"`

![Get-ADGroup スクリプトの結果を示すスクリーンショットの画像。](./media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/find-the-sid.png)

#### <a name="setting-the-claims-rules-to-call-azure-mfa"></a>Azure MFA を呼び出すための要求規則を設定する

次の PowerShell コマンドレットでは、企業ネットワークに存在しない場合に、グループのユーザーに対して Azure AD MFA を呼び出します。 "YourGroupSid" を、上記のコマンドレットを実行して検出された SID に置き換えます。

[2019 で追加の認証プロバイダーを選択する方法](/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server)を必ず確認してください。 

 > [!IMPORTANT]
> 既存の要求規則をバックアップする

 

#### <a name="set-global-claims-rule"></a>グローバル要求規則を設定する 

次の PowerShell コマンドレットを実行します。 

```powershell
(Get-AdfsRelyingPartyTrust -Name "RPTrustName").AdditionalAuthenticationRules
```

 

コマンドを実行すると、証明書利用者信頼の現在の追加の認証規則が返されます。 現在の要求規則に次の規則を追加します。

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");'
```

次の例では、ユーザーがネットワークの外部から接続するときに、MFA を要求するように現在の要求規則が構成されていることを前提としています。 この例には、追加する必要がある規則が含まれています。

```PowerShell
Set-AdfsAdditionalAuthenticationRule -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
 c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");'
```


#### <a name="set-per-application-claims-rule"></a>アプリケーションごとの要求規則を設定する

この例では、特定の証明書利用者信頼 (アプリケーション) の要求規則を変更し、追加する必要がある情報が含まれています。

```PowerShell
Set-AdfsRelyingPartyTrust -TargetName AppA -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");'
```


### <a name="configure-azure-ad-mfa-as-an-authentication-provider-in-ad-fs"></a>AD FS で認証プロバイダーとして Azure AD MFA を構成する

AD FS に Azure AD MFA を構成するには、各 AD FS サーバーを構成する必要があります。 ファームに複数の AD FS サーバーがある場合は、Azure AD PowerShell を使用してリモートで構成できます。

このプロセスのステップ バイ ステップの指示については、「[AD FS を使用して Azure MFA を認証プロバイダーとして構成する](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa)」の記事の「[AD FS サーバーを構成する](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa)」を参照してください。

サーバーを構成したら、追加の認証方法として Azure AD MFA を追加できます。 

![Azure MFA と Azure Multi-Factor Authentication Server が選択された [認証方法の編集] 画面を示すスクリーンショット](./media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/edit-authentication-methods.png)

## <a name="prepare-azure-ad-and-implement"></a>Azure AD を準備して実装する

### <a name="ensure-supportsmfa-is-set-to-true"></a>SupportsMFA が True に設定されていることを確認する

フェデレーション ドメインの場合は、Azure AD 条件付きアクセスまたはオンプレミス フェデレーション プロバイダーによって MFA が適用されることがあります。 Azure AD 内の各フェデレーション ドメインには、SupportsMFA フラグがあります。 SupportsMFA フラグが True に設定されている場合、Azure AD によって、AD FS または他のフェデレーション プロバイダーで MFA がユーザーリダイレクトされます。 たとえば、MFA を必要とする条件付きアクセス ポリシーが構成されているアプリケーションにユーザーがアクセスしている場合、そのユーザーは AD FS にリダイレクトされます。 AD FS で認証方法として Azure AD MFA を追加すると、構成が完了した後で Azure AD MFA を呼び出すことができます。

SupportsMFA フラグが False に設定されている場合、Azure MFA を使用していない可能性があります。おそらく、AD FS 証明書利用者に対して要求規則を使用して MFA を呼び出しています。

次の [Windows PowerShell コマンドレット](/powershell/module/msonline/get-msoldomainfederationsettings)を使用して、SupportsMFA フラグの状態を確認できます。

```powershell
Get-MsolDomainFederationSettings –DomainName yourdomain.com
```

フェデレーション ドメインで SupportsMFA フラグが false に設定されている場合、または空白になっている場合は、次の Windows PowerShell コマンドレットを使用して true に設定します。

```powershell
Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true
```

この構成により、AD FS で MFA Server または Azure MFA を使用することを決定できます。

### <a name="configure-conditional-access-policies-if-needed"></a>必要に応じて条件付きアクセス ポリシーを構成する

条件付きアクセスを使用して、ユーザーに MFA をいつ要求するかを決定する場合は、ポリシーを変更する必要はありません。

フェデレーション ドメインで SupportsMFA が false に設定されている場合は、Azure AD 証明書利用者信頼の要求規則を分析し、同じセキュリティ目標をサポートする条件付きアクセス ポリシーを作成します。

AD FS と同じ制御を適用する条件付きアクセス ポリシーを作成すると、Azure AD 証明書利用者の要求規則のカスタマイズをバックアップおよび削除できます。

詳細については、次のリソースを参照してください。

* [条件付きアクセスのデプロイを計画する](../conditional-access/plan-conditional-access.md)

* [一般的な条件付きアクセス ポリシー](../conditional-access/concept-conditional-access-policy-common.md)


## <a name="register-users-for-azure-mfa"></a>ユーザーを Azure MFA に登録する

Azure MFA のユーザーを登録するには、次の 2 つの方法があります。 

* 統合されたセキュリティに登録する (MFA とセルフサービス パスワード リセット) 

* MFA Server から電話番号を移行する

Microsoft Authenticator アプリは、パスワードレス モードとして使用できます。 また、いずれかの登録方法を使用して MFA の 2 番目の要素として使用することもできます。

### <a name="register-for-combined-security-registration-recommended"></a>統合されたセキュリティ登録に登録する (推奨)

統合されたセキュリティ情報をユーザーに登録してもらいます。これは、MFA と SSPR の両方に認証方法とデバイスを登録するための単一の場所です。 MFA Server から Azure AD MFA にデータを移行できますが、次のような課題があります。

* 移行できるのは電話番号のみです。

* Authenticator アプリを再登録する必要があります。

* 古いデータが移行される場合があります。

Microsoft では、統合された登録プロセスを進めるためにユーザーに提供できる通信テンプレートを用意しています。 これには、メール、ポスター、テーブル テント、その他のアセットのテンプレートなどがあります。 ユーザーは、[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) で各自の情報を登録します。これにより、統合されたセキュリティ登録の画面に移動します。 

信頼できるデバイスまたは場所から登録する必要がある[条件付きアクセスを使用して、セキュリティ登録プロセスをセキュリティで保護する](../conditional-access/howto-conditional-access-policy-registration.md)ことをお勧めします。

> [!NOTE]
> 信頼されていない場所またはデバイスから、統合されたセキュリティ情報を登録する必要があるユーザーについては、一時アクセス パスを発行したり、ポリシーから一時的に除外したりできます。

### <a name="migrate-phone-numbers-from-mfa-server"></a>MFA Server から電話番号を移行する

電話番号を移行すると、古い番号が移行される可能性があり、[Microsoft Authenticator アプリ を使用したパスワードなしのサインイン](howto-authentication-passwordless-phone.md)など、安全な方法を設定する代わりに、ユーザーが電話ベースの MFA を使用し続ける可能性があります。 Microsoft Authenticator アプリの設定など、デバイス登録を移行できません。 [統合されたセキュリティ情報](howto-registration-mfa-sspr-combined.md)をすべてのユーザーに登録してもらうことをお勧めします。 統合されたセキュリティ情報には、セルフサービス パスワード リセット用のユーザーも登録されます。

統合されたセキュリティ情報をユーザーに登録してもらうことが全くできない場合は、MFA Server からユーザーとその電話番号をエクスポートし、電話番号を Azure AD にインポートできます。
 

#### <a name="export-user-phone-numbers-from-mfa-server"></a>MFA Server からユーザーの電話番号をエクスポートする 

1. MFA Server で Multi-Factor Authentication Server 管理コンソールを開きます。 

1. **[ファイル]** を選択し、 **[ユーザーのエクスポート]** を選択します。

1. .CSV ファイルを **保存** します。 既定の名前は Multi-Factor Authentication Users.csv です。

#### <a name="interpret-and-format-the-csv--file"></a>.csv 解釈ファイルを解釈して書式設定する

.csv ファイルには、移行に必要ない多くのフィールドが含まれています。 電話番号を Azure AD にインポートする前に、編集して書式設定します。 

.csv ファイルを開くと、関心のある列には次のものが含まれます。

* ユーザー名
* メインの電話番号
* メインの電話番号の国番号
* 予備の国コード
* 予備の電話番号
* 予備の内線

データを解釈、クリーンアップ、書式設定する必要があります。

#### <a name="tips-to-avoid-errors-during-import"></a>インポート中のエラーを回避するためのヒント

* 認証方法 API を使用して電話番号を Azure AD にインポートする前に、CSV ファイルを変更します。 

* .csv を、UPN、PhoneType、PhoneNumber の 3 つの列に簡略化します。 

* エクスポートされた MFA Server のユーザー名が Azure AD UserPrincipalName に一致していることを確認します。 そうでない場合は、Azure AD 内のユーザー名と一致するように、CSV ファイル内のユーザー名を更新します。そうしないと、ユーザーは見つかりません。

ユーザーが既に電話番号を Azure AD に登録している可能性があります。 認証方法 API を使用して電話番号をインポートする場合は、既存の電話番号を上書きするか、インポートした番号を代替の電話番号として追加するかを決定する必要があります。

次の PowerShell コマンドレットでは、指定した CSV ファイルを取得し、認証方法 API を使用して、エクスポートされた電話番号を各 UPN の電話番号として追加します。 "myPhones" を、CSV ファイルの名前に置き換えます。

```powershell

$csv = import-csv myPhones.csv

$csv|% { New-MgUserAuthenticationPhoneMethod -UserId $_.UPN -phoneType $_.PhoneType -phoneNumber $_.PhoneNumber} 

```

### <a name="add-users-to-the-appropriate-groups"></a>ユーザーを適切なグループに追加する

* 新しい条件付きアクセス ポリシーを作成した場合は、それらのグループに適切なユーザーを追加します。 

* 要求規則にオンプレミスのセキュリティ グループを作成した場合は、それらのグループに適切なユーザーを追加します。

セキュリティに使用されるグループを再利用しないことをお勧めします。 したがって、セキュリティ グループを使用して、条件付きアクセス ポリシーで高価値アプリのグループをセキュリティで保護する場合、そのグループのみを使用する必要があります。

## <a name="monitoring"></a>監視

Azure MFA の登録は、[認証方法の使用状況と分析情報に関するレポート](https://portal.azure.com/)を使用して監視できます。 このレポートは、Azure AD で確認できます。 **[監視]** を選択し、 **[使用状況と分析情報]** を選択します。 

[使用状況と分析情報] で、 **[認証方法]** を選択します。 

詳細な Azure MFA 登録情報は、[登録] タブで確認できます。 **[Azure Multi-Factor Authentication 対応ユーザー]** ハイパーリンクを選択すると、ドリルダウンして、登録済みユーザーの一覧を表示できます。

![MFA へのユーザー登録を示す認証方法アクティビティ画面の画像](./media/how-to-migrate-mfa-server-to-azure-mfa-with-federation/authentication-methods.png)

   

## <a name="clean-up-steps&quot;></a>クリーンアップの手順

Azure MFA への移行が完了し、MFA Server の使用を停止する準備ができたら、次の 3 つの操作を行います。 

1. AD FS の要求規則を移行前の構成に戻し、MFA Server 認証プロバイダーを削除します。

1. AD FS の認証プロバイダーとして MFA Server を 削除します。 これにより、すべてのユーザーが Azure MFA を使用できます。これは、有効な唯一の追加の認証方法になります。 

1. MFA Server の使用を停止します。

### <a name=&quot;revert-claims-rules-on-ad-fs-and-remove-mfa-server-authentication-provider&quot;></a>AD FS の要求規則を元に戻し、MFA Server 認証プロバイダーを削除する

要求規則の構成の手順に従い、Azure AD MFA を呼び出して、バックアップした要求規則に戻し、AzureMFAServerAuthentication 要求規則を削除します。 

たとえば、ルールから次を削除します。 

 
```console
c:[Type == &quot;https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid&quot;, Value ==
&quot;**YourGroupSID**&quot;] => issue(Type = &quot;https://schemas.microsoft.com/claims/authnmethodsproviders&quot;,
Value = &quot;AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
Value=="YourGroupSid"]) => issue(Type =
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value =
"AzureMfaServerAuthentication");'
```

### <a name="disable-mfa-server-as-an-authentication-provider-in-ad-fs"></a>MFA Server を AD FS の認証プロバイダーとして無効にする

この変更により、Azure MFA のみが認証プロバイダーとして使用されます。

1. **AD FS 管理コンソール** を開きます。

1. **[サービス]** で、 **[認証方法]** を右クリックし、 **[多要素認証方法の編集]** を選択します。 

1. **[Azure Multi-Factor Authentication Server]** の横のチェック ボックスをオフにします。 

### <a name="decommission-the-mfa-server"></a>MFA Server の使用を停止する

エンタープライズ サーバーの使用停止プロセスに従って、環境内の MFA Server を削除します。

MFA Server の使用停止時に考えられる考慮事項は次のとおりです。 

* サーバーを削除する前に、MFA Server のログを確認し、ユーザーまたはアプリケーションで MFA Server が使用されていないことを確認します。

* サーバーの [コントロール パネル] から、Multi-Factor Authentication Server をアンインストールします

* 必要に応じて、最初のバックアップ後に残っているログとデータ ディレクトリをクリーンアップします。 

* 該当する場合は、Multi-Factor Authentication Web Server SDK をアンインストールします (etpub\wwwroot\MultiFactorAuthWebServiceSdk または MultiFactorAuth ディレクトリに残っているファイルを含む)。

* 8\.0 より前の MFA Server バージョンの場合は、Multi-Factor Auth Phone App Web Service の削除が必要になる場合もあります。

## <a name="next-steps"></a>次の手順

- [パスワード ハッシュ同期を展開する](../hybrid/whatis-phs.md)
- [条件付きアクセスの詳細](../conditional-access/overview.md)
- [アプリケーションを Azure AD に移行する](../manage-apps/migrate-application-authentication-to-azure-active-directory.md)

 

 

 
