---
title: Azure AD MFA と Azure AD ユーザー認証に移行する - Azure Active Directory
description: オンプレミスの Azure MFA Server から、Azure AD MFA と Azure AD ユーザー認証への移行に関するステップ バイ ステップのガイダンス
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f10052f6bcb458b98535d54a9b2fa5deb749c38
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730805"
---
# <a name="migrate-to-azure-ad-mfa-and-azure-ad-user-authentication"></a>Azure AD MFA と Azure AD ユーザー認証に移行する

多要素認証 (MFA) は、インフラストラクチャと資産を悪意のあるアクターからセキュリティ保護するのに役立ちます。 Microsoft の Multi-Factor Authentication Server (MFA Server) は、新しいデプロイでは提供されなくなりました。 MFA Server を使用しているお客様は、Azure AD Multi-Factor Authentication (Azure AD MFA) に移行する必要があります。 

多要素認証 (MFA) を MFA Server から Azure Active Directory (Azure AD) に移行するには、複数のオプションがあります。 次に例を示します。

* 良い: [MFA サービスのみを Azure AD に](how-to-migrate-mfa-server-to-azure-mfa.md)移動。 
* さらに良い: MFA サービスとユーザー認証を Azure ADに移動 (この記事で説明します)。
* 最もよい: すべてのアプリケーション、MFA サービス、ユーザー認証を Azure AD に移動。 この記事で説明するアプリケーションの移動を計画する場合は、この記事の Azure AD へのアプリケーションの移動に関するセクションを参照してください。 

組織に適した MFA 移行オプションを選択するには、[MFA Server から Azure Active Directory MFA への移行](how-to-migrate-mfa-server-to-azure-mfa.md)に関する考慮事項を参照してください。 

次の図は、AD FS 上の一部のアプリケーションを維持しながら、Azure AD MFA とクラウド認証に移行するプロセスを示したものです。 このプロセスを使用すると、グループ メンバーシップに基づいて MFA Server から Azure MFA にユーザーを反復的に移行できます。

この記事の以降のセクションでは、各ステップについて説明します。

>[!NOTE]
>この移行の一環として、Azure Active Directory へのアプリケーションの移行を計画している場合は、MFA の移行の前に行う必要があります。 すべてのアプリを移動する場合は、MFA 移行プロセスのセクションをスキップできます。 この記事の最後にあるアプリケーションの移動に関するセクションを参照してください。

## <a name="process-to-migrate-to-azure-ad-and-user-authentication"></a>Azure AD とユーザー認証に移行するプロセス

![Azure AD とユーザー認証に移行するプロセス。](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/mfa-cloud-authentication-flow.png)

## <a name="prepare-groups-and-conditional-access"></a>グループと条件付きアクセスを準備する

グループは、MFA 移行のために 3 つの容量で使用されます。
* **段階的ロールアウトを使用して Azure AD MFA にユーザーを反復的に移動するため。**
Azure AD で作成されたグループ (クラウド専用グループとも呼ばれます) を使用します。 MFA へのユーザーの移動と、条件付きアクセス ポリシーの両方に、Azure AD セキュリティ グループまたは Microsoft 365 グループを使用できます。  詳細については、Azure AD セキュリティ グループの作成と、管理者向け Microsoft 365 グループの概要に関するページを参照してください。
  >[!IMPORTANT]
  >段階的ロールアウト プロセスでは、入れ子になったグループと動的グループはサポートされていません。 段階的ロールアウト作業には、これらの種類のグループを使しないでください。
* **条件付きアクセス ポリシー**。 条件付きアクセスには、Azure AD またはオンプレミスのグループを使用できます。
* **要求規則で AD FS アプリケーションに対して Azure AD MFA を呼び出すため。**
これは、AD FS にアプリケーションがある場合にのみ適用されます。
これは、オンプレミスの Active Directory セキュリティ グループである必要があります。 Azure AD MFA が追加の認証方法になったら、ユーザーのグループを指定して各証明書利用者の信頼でその方法を使用できます。 たとえば、既に移行したユーザーには Azure AD MFA を呼び出し、まだ移行していないユーザーには MFA Server を呼び出すことができます。 これは、テストと移行中の両方に役立ちます。 

>[!NOTE] 
>セキュリティに使用されているグループを再利用しないことをお勧めします。 セキュリティ グループを使用して、条件付きアクセス ポリシーで高価値アプリのグループをセキュリティ保護するときは、そのグループをそれだけに使用する必要があります。

### <a name="configure-conditional-access-policies"></a>条件付きアクセス ポリシーを構成する

ユーザーに MFA をいつ要求するかを決定するために、条件付きアクセスを既に使用している場合は、ポリシーを変更する必要はありません。 ユーザーがクラウド認証に移行されると、既存の条件付きアクセス ポリシーで定義されているように、Azure AD MFA を使い始めるようになります。 AD FS と MFA Server にはリダイレクトされなくなります。

フェデレーション ドメインで SupportsMFA が false に設定されている場合は、要求規則を使用して AD FS で MFA を適用している可能性があります。 この場合は、Azure AD の証明書利用者の信頼での要求規則を分析し、同じセキュリティ目標をサポートする条件付きアクセス ポリシーを作成する必要があります。

条件付きアクセス ポリシーを構成する必要がある場合は、段階的ロールアウトを有効にする前に構成する必要があります。 詳細については、次のリソースを参照してください。
* [条件付きアクセスのデプロイを計画する](../conditional-access/plan-conditional-access.md)
* [一般的な条件付きアクセス ポリシー](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="prepare-ad-fs"></a>AD FS を準備する 

AD FS に MFA を必要とするアプリケーションがない場合は、このセクションをスキップし、「段階的ロールアウトを準備する」セクションに進んでかまいません。

### <a name="upgrade-ad-fs-server-farm-to-2019-fbl-4"></a>AD FS サーバー ファームを 2019 FBL 4 にアップグレードする

AD FS 2019 において、Microsoft は、アプリケーションなどの証明書利用者に対して追加の認証方法を指定できるようにする新しい機能をリリースしました。 これは、グループ メンバーシップを使用して認証プロバイダーを決定することにより行われます。 追加の認証方法を指定すると、切り替えの間に他の認証をそのまま維持しながら、Azure AD MFA に切り替えることができます。 

詳細については、「[Windows Server 2016 での WID データベースを使用した AD FS へのアップグレード](/windows-server/identity/ad-fs/deployment/upgrading-to-ad-fs-in-windows-server)」を参照してください。 この記事では、AD FS 2019 へのファームのアップグレードと、FBL の 4 へのアップグレードの両方について説明します。

### <a name="configure-claims-rules-to-invoke-azure-ad-mfa"></a>Azure AD MFA を呼び出すための要求規則を構成する

追加の認証方法として Azure AD MFA を設定したので、Azure AD MFA を使用するためのユーザーのグループを割り当てることができます。 これを行うには、要求規則 ("*証明書利用者の信頼*" とも呼ばれます) を構成します。 グループを使用すると、グローバルに、またはアプリケーションによって、どの認証プロバイダーが呼び出されるかを制御できます。 たとえば、統合されたセキュリティ情報を登録しているユーザーまたは自分の電話番号の移行が済んだユーザーに対しては Azure AD MFA を呼び出し、そうでないユーザーには MFA Server を呼び出すことができます。 

>[!NOTE]
>要求規則には、オンプレミスのセキュリティ グループが必要です。 

#### <a name="back-up-existing-rules"></a>既存の規則をバックアップする 

新しい要求規則を構成する前に、既存の規則をバックアップします。 クリーンアップ手順の一環として、これらを復元する必要があります。 

構成によっては、既存の規則をコピーし、移行用に作成している新しい規則を追加することが必要になる場合もあります。

既存のグローバル規則を表示するには、次のように実行します。  
```powershell
Get-AdfsAdditionalAuthenticationRule
```

既存の証明書利用者の信頼を表示するには、次のコマンドを実行し、RPTrustName を証明書利用者の信頼の要求規則の名前に置き換えます。 

```powershell
(Get-AdfsRelyingPartyTrust -Name “RPTrustName”).AdditionalAuthenticationRules
```

#### <a name="access-control-policies"></a>アクセス制御ポリシー

>[!NOTE]
>グループ メンバーシップに基づいて特定の認証プロバイダーが呼び出されるように、アクセス制御ポリシーを構成することはできません。 

アクセス制御ポリシーから追加の認証規則に切り替えるには、MFA Server 認証プロバイダーを使用して、証明書利用者の信頼ごとに次のコマンドを実行します。

```powershell
Set-AdfsRelyingPartyTrust -**TargetName AppA -AccessControlPolicyName $Null**
```

このコマンドにより、現在のアクセス制御ポリシーから追加の認証規則にロジックが移動されます。

#### <a name="set-up-the-group-and-find-the-sid"></a>グループを設定して SID を検索する

Azure AD MFA を呼び出すユーザーを配置する特定のグループを用意する必要があります。 そのグループのセキュリティ識別子 (SID) を見つける必要があります。
グループ SID を見つけるには、グループ名を指定して `Get-ADGroup “GroupName”` コマンドを使用します

![グループ SID を取得するための PowerShell コマンド。](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/find-the-sid.png)

#### <a name="setting-the-claims-rules-to-call-azure-mfa"></a>Azure MFA を呼び出すための要求規則の設定

次の PowerShell コマンドレットを使用すると、グループのユーザーが企業ネットワーク上に存在しない場合は、Azure AD MFA が呼び出されます。 "YourGroupSid" は、前のコマンドレットを実行して見つかった SID に置き換える必要があります。

「[2019 での他の認証プロバイダーの選択方法](/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server#how-to-choose-additional-auth-providers-in-2019)」を必ず確認してください。 

>[!IMPORTANT] 
>次に進む前に、既存の要求規則をバックアップします。

##### <a name="set-global-claims-rule"></a>グローバル要求規則を設定する 

次のコマンドを実行し、RPTrustName を証明書利用者の信頼の要求規則の名前に置き換えます。 

```powershell
(Get-AdfsRelyingPartyTrust -Name “RPTrustName”).AdditionalAuthenticationRules
```

このコマンドからは、証明書利用者の信頼に対する現在の追加の認証規則が返されます。  
現在の要求規則に次の規則を追加する必要があります。

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

次の例では、ユーザーがネットワークの外部から接続しているときは MFA を要求するように、現在の要求規則が構成されているものとします。 この例には、追加する必要のある追加規則が含まれています。

```PowerShell
Set-AdfsAdditionalAuthenticationRule -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
 c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
“YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value==“YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

##### <a name="set-per-application-claims-rule"></a>アプリケーションごとの要求規則を設定する

この例では、特定の証明書利用者の信頼 (アプリケーション) に対する要求規則を変更します。 それには、追加する必要のある追加規則が含まれています。

```PowerShell
Set-AdfsRelyingPartyTrust -TargetName AppA -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
“YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value==“YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

### <a name="configure-azure-ad-mfa-as-an-authentication-provider-in-ad-fs"></a>AD FS での認証プロバイダーとして Azure AD MFA を構成する

AD FS 用に Azure AD MFA を構成するには、各 AD FS サーバーを構成する必要があります。 ファームに複数の AD FS サーバーがある場合は、Azure AD PowerShell を使用してリモートで構成できます。

このプロセスの詳細な手順については、「[AD FS サーバーを構成する](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)」を参照してください。

サーバーを構成したら、追加の認証方法として Azure AD MFA を追加できます。 

![追加の認証方法として Azure AD MFA を追加する方法のスクリーンショット。](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/edit-authentication-methods.png)


## <a name="prepare-staged-rollout"></a>段階的ロールアウトを準備する 

これで、段階的ロールアウト機能を有効にする準備ができました。 段階的ロールアウトは、ユーザーを PHS または PTA に反復的に移動するのに役立ちます。 

* [サポートされているシナリオ](../hybrid/how-to-connect-staged-rollout.md#supported-scenarios)を必ず確認してください。 
* 最初に、[PHS に関する事前作業](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-password-hash-sync)または [PTA に関する事前作業](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-pass-through-authentication)を行う必要があります。 PHS をお勧めします。 
* 次に、[シームレス SSO に関する事前作業](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-seamless-sso)を行います。 
* 選択した認証方法について、[クラウド認証の段階的ロールアウトを有効にします](../hybrid/how-to-connect-staged-rollout.md#enable-a-staged-rollout-of-a-specific-feature-on-your-tenant)。 
* 段階的ロールアウト用に作成したグループを追加します。 ユーザーをグループに反復的に追加すること、および動的グループまたは入れ子になったグループは使用できないことを思い出してください。 

## <a name="register-users-for-azure-mfa"></a>ユーザーを Azure MFA に登録する

ユーザーを Azure MFA に登録するには、2 つの方法があります。 

* 統合されたセキュリティに登録する (MFA とセルフサービス パスワード リセット) 
* MFA Server から電話番号を移行する

どちらの方法でも MFA の 2 番目の要素としてであれば、Microsoft Authenticator アプリをパスワードレス サインイン方法として使用できます。

### <a name="register-for-combined-security-registration-recommended"></a>統合されたセキュリティ登録に登録する (推奨)

ユーザーに統合されたセキュリティ情報に登録してもらうことをお勧めします。これは、MFA と SSPR の両方に認証方法とデバイスを登録するための単一の場所です。 MFA Server から Azure AD MFA にデータを移行することはできますが、次のような課題が発生します。

* 移行できるのは電話番号のみです。
* Authenticator アプリを再登録する必要があります。
* 古いデータが移行される場合があります。

Microsoft では、統合された登録プロセスを進めるためにユーザーに提供できる通信テンプレートを用意しています。 これには、メール、ポスター、テーブル テント、その他のさまざまな資産のためのテンプレートが含まれます。 ユーザーは、`https://aka.ms/mysecurityinfo` で各自の情報を登録します。これにより、統合されたセキュリティ登録の画面に移動します。 

信頼できるデバイスまたは場所から登録する必要がある[条件付きアクセスを使用して、セキュリティ登録プロセスを保護する](../conditional-access/howto-conditional-access-policy-registration.md)ことをお勧めします。 登録の状態の追跡については、[Azure Active Directory の認証方法のアクティビティ](howto-authentication-methods-activity.md)に関する記事を参照してください。
> [!NOTE]
> 信頼されていない場所またはデバイスから、統合されたセキュリティ情報を登録する必要があるユーザーについては、一時アクセス パスを発行したり、代わりにポリシーから一時的に除外したりできます。

### <a name="migrate-phone-numbers-from-mfa-server"></a>MFA Server から電話番号を移行する

ユーザーの登録された MFA 電話番号とハードウェア トークンを移行することはできますが、Microsoft Authenticator アプリの設定などのデバイスの登録を移行することはできません。 電話番号を移行すると、古い番号が移行される可能性があり、[Microsoft Authenticator アプリ を使用したパスワードなしのサインイン](howto-authentication-passwordless-phone.md)など、安全な方法を設定する代わりに、ユーザーが電話ベースの MFA を使用し続ける可能性があります。 したがって、選択した移行パスに関係なく、[統合されたセキュリティ情報](howto-registration-mfa-sspr-combined.md)をすべてのユーザーに登録してもらうことをお勧めします。 統合されたセキュリティ情報を使用すると、ユーザーはセルフサービス パスワード リセットに登録することもできます。

統合されたセキュリティ情報をユーザーに登録してもらうことができない場合は、MFA Server からユーザーとその電話番号をエクスポートし、電話番号を Azure AD にインポートできます。 

#### <a name="export-user-phone-numbers-from-mfa-server"></a>MFA Server からユーザーの電話番号をエクスポートする 

1. MFA Server で Multi-Factor Authentication Server 管理コンソールを開きます。 
1. **[ファイル]**  >  **[ユーザーのエクスポート]** を選択します。
3)  CSV ファイルを保存します。 既定の名前は Multi-Factor Authentication Users.csv です。

#### <a name="interpret-and-format-the-csv-file"></a>.csv ファイルを解釈して書式設定する

.csv ファイルには、移行に必要のないフィールドが多数含まれているので、電話番号を Azure AD にインポートする前に、編集して書式設定する必要があります。 

.csv ファイルを開いたとき、関心のある列は、Username、Primary Phone、Primary Country Code、Backup Country Code、Backup Phone、Backup Extension などです。 このデータを解釈し、必要に応じて書式設定する必要があります。

#### <a name="tips-to-avoid-errors-during-import"></a>インポートでのエラーを回避するためのヒント

* Authentication Methods API を使用して電話番号を Azure AD にインポートする前に、CSV ファイルを変更する必要があります。 
* .csv を UPN、PhoneType、PhoneNumber の 3 つの列に簡略化することをお勧めします。 

  ![csv の例のスクリーンショット。](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/csv-example.png)

* エクスポートされた MFA Server のユーザー名が Azure AD の UserPrincipalName と一致していることを確認します。 そうでない場合は、Azure AD 内のユーザー名と一致するように、CSV ファイル内のユーザー名を更新します。そうしないと、ユーザーは見つかりません。

ユーザーが既に電話番号を Azure AD に登録している可能性があります。 Authentication Methods API を使用して電話番号をインポートするときに、既存の電話番号を上書きするか、インポートした番号を代替の電話番号として追加するかを決定する必要があります。

次の PowerShell コマンドレットでは、指定した CSV ファイルが取得され、Authentication Methods API を使用して、エクスポートした電話番号が各 UPN の電話番号として追加されます。 "myPhones" を、CSV ファイルの名前に置き換える必要があります。


```powershell
$csv = import-csv myPhones.csv
$csv|% { New-MgUserAuthenticationPhoneMethod -UserId $_.UPN -phoneType $_.PhoneType -phoneNumber $_.PhoneNumber} 
```

ユーザーの認証方法の管理の詳細については、[Azure AD Multi-Factor Authentication の認証方法の管理](howto-mfa-userdevicesettings.md)に関する記事を参照してください。

### <a name="add-users-to-the-appropriate-groups"></a>ユーザーを適切なグループに追加する 

* 新しい条件付きアクセス ポリシーを作成した場合は、それらのグループに適切なユーザーを追加します。 
* 要求規則用にオンプレミスのセキュリティ グループを作成した場合は、それらのグループに適切なユーザーを追加します。 
* 適切な条件付きアクセス規則にユーザーを追加した後でのみ、段階的ロールアウト用に作成したグループにユーザーを追加します。 完了すると、多要素認証を実行する必要があるときに、選択した Azure 認証方法 (PHS または PTA) と Azure AD MFA の使用が開始されます。

> [!IMPORTANT] 
> 段階的ロールアウト プロセスでは、入れ子になったグループと動的グループはサポートされていません。 これらの種類のグループは使用しないでください。 

セキュリティに使用されているグループを再利用しないことをお勧めします。 したがって、セキュリティ グループを使用して、条件付きアクセス ポリシーで高価値アプリのグループを保護する場合、そのグループのみを使用する必要があります。

## <a name="monitoring"></a>監視

いくつかの [Azure Monitor ブック](../reports-monitoring/howto-use-azure-monitor-workbooks.md)と使用および分析情報レポートを使用して、デプロイを監視できます。 これらは、Azure AD のナビゲーション ウィンドウの **[監視]** にあります。 

### <a name="monitoring-staged-rollout"></a>段階的ロールアウトの監視

[[Workbooks]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Workbooks) セクションで、 **[Public Templates]\(パブリック テンプレート\)** を選択します。 **[Hybrid Auth]\(ハイブリッド認証\)** セクションで、 **[Groups, Users and Sign-ins in Staged Rollout]\(段階的ロールアウトでのグループ、ユーザー、サインイン\)** ブックを選択します。

このブックを使用すると、以下のことを監視できます。 
* 段階的ロールアウトに追加されたユーザーとグループ。
* 段階的ロールアウトから削除されたユーザーとグループ。
* 段階的ロールアウトでのユーザーのサインイン失敗と、失敗の理由。

### <a name="monitoring-azure-mfa-registration"></a>Azure MFA の登録の監視
Azure MFA の登録は、[認証方法の使用状況と分析情報に関するレポート](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthenticationMethodsMenuBlade/AuthMethodsActivity/menuId/AuthMethodsActivity)を使用して監視できます。 このレポートは、Azure AD で確認できます。 **[監視]** を選択し、 **[使用状況と分析情報]** を選択します。 

![使用状況と分析情報レポートを見つける方法のスクリーンショット。](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/usage-report.png)

[使用状況と分析情報] で、 **[認証方法]** を選択します。 

詳細な Azure MFA 登録情報は、[登録] タブで確認できます。 **[Users registered for Azure multi-factor authentication]\(Azure Multi-Factor Authentication に登録されているユーザー\)** ハイパーリンクを選択し、ドリルダウンして、登録済みユーザーの一覧を表示できます。

![[登録] タブのスクリーン ショット。](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/registration-tab.png)

### <a name="monitoring-app-sign-in-health"></a>アプリのサインインの正常性の監視

アプリ サインイン正常性ブックまたはアプリケーション アクティビティ使用状況レポートを使用して、Azure AD に移動したアプリケーションを監視します。

* **アプリ サインイン正常性ブック**。 このブックの使用に関する詳細なガイダンスについては、「[回復性のためにアプリケーションのサインインの正常性を監視する](../fundamentals/monitor-sign-in-health-for-resilience.md)」を参照してください。
* **Azure AD アプリケーション アクティビティ使用状況レポート**。 この[レポート](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsageAndInsightsMenuBlade/Azure%20AD%20application%20activity)を使用すると、個々のアプリケーションのサインインの成功と失敗を表示できるだけでなく、特定のアプリケーションのサインイン アクティビティにドリルダウンして表示することもできます。 

## <a name="clean-up-tasks"></a>クリーンアップのタスク

すべてのユーザーを Azure AD クラウド認証と Azure MFA に移動したら、MFA Server の使用を停止できる状態になります。 サーバーを削除する前に、MFA Server のログを調べて、ユーザーまたはアプリケーションによってそれが使用されていないのを確認することをお勧めします。

### <a name="convert-your-domains-to-managed-authentication"></a>ドメインをマネージド認証に変換する

次に、[Azure AD のフェデレーション ドメインをマネージドに変換](../hybrid/migrate-from-federation-to-cloud-authentication.md#convert-domains-from-federated-to-managed)し、段階的ロールアウトの構成を削除する必要があります。 これにより、新しいユーザーは移行グループに追加されなくてもクラウド認証を使用できます。

### <a name="revert-claims-rules-on-ad-fs-and-remove-mfa-server-authentication-provider"></a>AD FS の要求規則を元に戻し、MFA Server 認証プロバイダーを削除する

「[Azure AD MFA を呼び出すための要求規則を構成する](#configure-claims-rules-to-invoke-azure-ad-mfa)」の手順に従って、バックアップした要求規則に戻し、AzureMFAServerAuthentication 要求規則を削除します。 

たとえば、規則から次を削除します。 

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value ==
“**YourGroupSID**"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders",
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
Value=="YourGroupSid"]) => issue(Type =
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value =
"AzureMfaServerAuthentication");’
```


### <a name="disable-mfa-server-as-an-authentication-provider-in-ad-fs"></a>AD FS での認証プロバイダーとして MFA Server を無効にする

この変更により、Azure MFA のみが認証プロバイダーとして使用されるようになります。

1. **AD FS 管理コンソール** を開きます。
1. **[サービス]** で、 **[認証方法]** を右クリックし、 **[多要素認証方法の編集]** を選択します。 
1. **[Azure Multi-Factor Authentication Server]** チェック ボックスをオフにします。 

### <a name="decommission-the-mfa-server"></a>MFA Server の使用を停止する

エンタープライズ サーバーの使用停止プロセスに従って、環境内の MFA Server を削除します。

MFA Server の使用停止時に考えられる考慮事項は次のとおりです。 

* サーバーを削除する前に、MFA Server のログを調べて、ユーザーまたはアプリケーションによってそれが使用されていないのを確認することをお勧めします。
* サーバーの [コントロール パネル] から、Multi-Factor Authentication Server をアンインストールします。
* 必要に応じて、最初のバックアップ後に残っているログとデータ ディレクトリをクリーンアップします。 
* Multi-Factor Authentication Web Server SDK をアンインストールします (該当する場合は、inetpub\wwwroot\MultiFactorAuthWebServiceSdk や MultiFactorAuth ディレクトリに残っているファイルも)。
* 8\.0.x より前のバージョンの MFA Server の場合は、Multi-Factor Auth Phone App Web Service の削除が必要になる場合もあります。

## <a name="move-application-authentication-to-azure-active-directory"></a>アプリケーション認証を Azure Active Directory に移動する

MFA およびユーザー認証と共にすべてのアプリケーション認証を移行する場合は、オンプレミス インフラストラクチャのかなりの部分を削除して、コストとリスクを減らすことができます。 すべてのアプリケーション認証を移動する場合は、「[AD FS を準備する](#prepare-ad-fs)」ステージをスキップして、MFA の移行を簡略化できます。

すべてのアプリケーション認証を移動するプロセスを次の図に示します。

![アプリケーションを Azure AD MFA に移行するプロセス。](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/mfa-app-migration-flow.png)

移行前にすべてのアプリケーションを移動できない場合は、開始する前に可能なアプリケーションを移動します。
Azure へのアプリケーションの移行に関する詳細については、「[アプリケーションを Azure Active Directory に移行するためのリソース](../manage-apps/migration-resources.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Microsoft MFA Server から Azure Multi-Factor Authentication に移行する (概要)](how-to-migrate-mfa-server-to-azure-mfa.md)
- [Windows Active Directory から Azure Active Directory にアプリケーションを移行する](../manage-apps/migrate-application-authentication-to-azure-active-directory.md)
- [クラウド認証戦略を計画する](../fundamentals/active-directory-deployment-plans.md)