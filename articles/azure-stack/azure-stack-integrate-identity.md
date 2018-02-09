---
title: "Azure Stack とデータセンターの統合 - ID"
description: "Azure Stack の AD FS をデータセンターの AD FS と統合する方法について説明します。"
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: 
ms.openlocfilehash: 2f15e130859272a729fb0ad6e0b718d4724f2103
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="azure-stack-datacenter-integration---identity"></a>Azure Stack とデータセンターの統合 - ID
Azure Stack は、ID プロバイダーとして Azure Active Directory (Azure AD) または Active Directory フェデレーション サービス (AD FS) のいずれかを使用してデプロイできます。 Azure Stack を展開する前に、選択を行う必要があります。 AD FS を使用したデプロイは、切断モードでの Azure Stack のデプロイとも呼ばれます。

次の表は、2 つの ID の選択間の違いを示します。

||物理的に切断されている|物理的に接続されている|
|---------|---------|---------|
|課金|容量ベース<br> Enterprise Agreement (EA) のみ|容量ベースまたは従量課金制<br>EA または Cloud Solution Provider (CSP)|
|ID|AD FS|Azure AD または AD FS|
|Marketplace シンジケーション|サポートされています<br>ライセンス持ち込み (BYOL)|サポートされています<br>ライセンス持ち込み (BYOL)|
|登録|推奨、リムーバブル メディアと<br> 別途接続されているデバイスが必要。|自動|
|パッチと更新プログラム|必須、リムーバブル メディアと<br> 別途接続されているデバイスが必要。|更新プログラム パッケージはインターネットから<br> Azure Stack に直接ダウンロード可能。|

> [!IMPORTANT]
> Azure Stack ソリューション全体を再デプロイすることなく、ID プロバイダーを切り替えることはできません。

## <a name="active-directory-federation-services-and-graph"></a>Active Directory フェデレーション サービス (AD FS) と Graph

AD FS を使用してデプロイすると、既存の Active Directory フォレスト内の ID を Azure Stack 内のリソースに対して認証できます。 この既存の Active Directory フォレストでは、AD FS フェデレーション信頼の作成を許可するために、AD FS のデプロイが必要です。

認証は ID の一部です。 Azure Stack でロールベースのアクセス制御 (RBAC) を管理するには、Graph コンポーネントを構成する必要があります。 リソースへのアクセスが委任されると、Graph のコンポーネントは LDAP プロトコルを使用して、既存の Active Directory フォレストでユーザー アカウントを検索します。

![Azure Stack AD FS のアーキテクチャ](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

既存の AD FS はアカウント セキュリティ トークン サービス (STS) で、Azure Stack AD FS (リソース STS) に要求を送信します。 Azure Stack で、自動化によりクレーム プロバイダー信頼とメタデータ エンドポイントを、既存の AD FS に対して作成します。

既存の AD FS で、証明書利用者信頼を構成する必要があります。 この手順は自動化によっては実行されず、オペレーターによって構成される必要があります。 Azure Stack のメタデータ エンドポイントは AzureStackStampDeploymentInfo.JSON ファイルに、または特権エンドポイントを介してコマンド `Get-AzureStackInfo` を実行することで記載されます。

また、証明書利用者信頼構成には、マイクロソフトが提供する要求変換ルールを構成する必要があります。

Graph の構成には、既存の Active Directory に対する読み取りアクセス許可が付与されたサービス アカウントが提供されている必要があります。 このアカウントは RBAC シナリオを有効にする自動化の入力として必要です。

最後の手順では、既定のプロバイダー サブスクリプションに対して新しい所有者を構成する必要があります。 このアカウントで Azure Stack 管理ポータルにサインインすると、すべてのリソースに対してフル アクセスが付与されます。

要件:


|コンポーネント|要件|
|---------|---------|
|Graph|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Graph の統合を設定する

自動化パラメーターの入力として、次の情報が必要です。


|パラメーター|[説明]|例|
|---------|---------|---------|
|CustomADGlobalCatalog|統合する対象の Active Directory<br>フォレストの FQDN|Contoso.com|
|CustomADAdminCredentials|LDAP の読み取りアクセス許可を持つユーザー|YOURDOMAIN\graphservice|

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>既存の Active Directory でユーザー アカウントを作成する (省略可能)

必要に応じて、既存の Active Directory で Graph サービスのアカウントを作成できます。 まだ使用するアカウントがない場合はこの手順を実行してください。

1. 既存の Active Directory で、次のユーザー アカウントを作成します (推奨)。
   - **ユーザー名**: graphservice
   - **パスワード**: 強力なパスワードを使用<br>パスワードを無期限に設定します。

   特殊なアクセス許可やメンバーシップは不要

#### <a name="trigger-automation-to-configure-graph"></a>トリガーを自動化して Graph を構成する

この手順では、データセンター ネットワーク内の、Azure Stack の特権エンドポイントと通信できるコンピューターを使用します。

2. 管理者特権での Windows PowerShell セッション (管理者として実行) を開き、特権エンドポイントの IP アドレスに接続します。 **CloudAdmin** の資格情報を使用して認証します。

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

3. これで特権エンドポイントに接続されました。次のコマンドを実行します。 

   ```powershell
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   メッセージが表示されたら、Graph サービスに使用するユーザー アカウントの資格情報を指定します (graphservice など)。

   > [!IMPORTANT]
   > 資格情報のポップアップ (Get-Credential は特権エンドポイントではサポートされていません)が表示されるのを待ち、Graph のサービス アカウントの資格情報を入力します。

#### <a name="graph-protocols-and-ports"></a>Graph のプロトコルとポート

Azure Stack の Graph サービスは、次のプロトコルとポートを使用して、対象の Active Directory と通信します。

|type|Port|プロトコル|
|---------|---------|---------|
|LDAP|389|TCP と UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>フェデレーション メタデータをダウンロードして AD FS の統合を設定する

自動化パラメーターの入力として、次の情報が必要です。

|パラメーター|[説明]|例|
|---------|---------|---------|
|CustomAdfsName|クレーム プロバイダーの名前。<cr>AD FS のランディング ページにそのように表示されます。|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|フェデレーション メタデータのリンク|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Azure Stack で自動化をトリガーしてクレーム プロバイダー信頼を構成する

この手順では、Azure Stack の特権エンドポイントと通信できるコンピューターを使用します。 アカウント **STS AD FS** で使用される証明書が Azure Stack によって信頼されている必要があります。

1. Windows PowerShell セッションを管理者特権で開き、特権エンドポイントに接続します。

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. これで特権エンドポイントに接続されました。お使いの環境に合わせてパラメーターを変更し、次のコマンドを実行します。

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. お使いの環境に合わせてパラメーターを変更して次のコマンドを実行し、既定のプロバイダー サブスクリプションの所有者を更新します。

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>フェデレーション メタデータ ファイルを指定して AD FS の統合を設定する

このメソッドは、次の条件のいずれかに該当する場合に使用します。

- AD FS の証明書チェーンが Azure Stack の他のすべてのエンドポイントと比較して異なる。
- Azure Stack の AD FS インスタンスと既存の AD FS サーバーがネットワークで接続されていない。

自動化パラメーターの入力として、次の情報が必要です。


|パラメーター|[説明]|例|
|---------|---------|---------|
|CustomAdfsName|クレーム プロバイダーの名前。 AD FS のランディング ページにそのように表示されます。|Contoso|
|CustomADFSFederationMetadataFile|フェデレーション メタデータ ファイル|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|

### <a name="create-federation-metadata-file"></a>フェデレーション メタデータ ファイルを作成する

次の手順では、アカウント STS になる既存の AD FS デプロイとネットワークで接続されているコンピューターを使用する必要があります。 また、必要な証明書がインストールされている必要があります。

1. Windows PowerShell セッションを管理者特権で開き、お使いの環境に合わせてパラメーターを変更し、次のコマンドを実行します。

   ```powershell
   [XML]$Metadata = Invoke-WebRequest -URI https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml -UseBasicParsing

   $Metadata.outerxml|out-file c:\metadata.xml
   ```

2. 特権エンドポイントからアクセスできる共有にメタデータ ファイルをコピーします。


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Azure Stack で自動化をトリガーしてクレーム プロバイダー信頼を構成する

この手順では、Azure Stack の特権エンドポイントと通信できるコンピューターを使用します。

1. Windows PowerShell セッションを管理者特権で開き、特権エンドポイントに接続します。

   ```powershell
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. これで特権エンドポイントに接続されました。お使いの環境に合わせてパラメーターを変更し、次のコマンドを実行します。

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso – CustomADFSFederationMetadataFile \\share\metadataexample.xml
   ```

3. お使いの環境に合わせてパラメーターを変更して次のコマンドを実行し、既定のプロバイダー サブスクリプションの所有者を更新します。

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>既存の AD FS デプロイ (アカウント STS) の証明書利用者を構成する

マイクロソフトは、要求変換ルールなど、証明書利用者信頼を構成するスクリプトを用意しています。 コマンドは手動で実行できるため、スクリプトの使用は任意です。

へルパー スクリプトは、Github の [Azure Stack ツール](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity)からダウンロードできます。

コマンドを手動で実行する場合は、次の手順に従います。

1. データセンターの AD FS インスタンスまたはファーム メンバーの .txt ファイル (例: c:\ClaimRules.txt) に次のコンテンツをコピーします。

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname]
   => issue(claim = c);
   ```

2. Windows フォーム ベースの認証を有効にするには、Windows PowerShell セッションを管理者特権で開き、次のコマンドを実行します。

   ```powershell
   Set-AdfsProperties -WIASupportedUserAgents @("MSAuthHost/1.0/In-Domain","MSIPC","Windows Rights Management Client","Kloud")
   ```

3. 証明書利用者信頼を追加するには、AD FS インスタンスまたはファーム メンバーで、次の Windows PowerShell コマンドを実行します。 AD FS エンドポイントを更新し、手順 1 で作成されたファイルを指定します。

   **AD FS 2016 の場合**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone"
   ```

   **AD FS 2012/2012 R2 の場合**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true
   ```

   > [!IMPORTANT]
   > Windows Server 2012 または 2012 R2 AD FS を使用している場合は、AD FS MMC スナップインを使用して発行承認規則を構成する必要があります。

4. Internet Explorer または Edge ブラウザーを使用して Azure Stack にアクセスするには、トークンのバインドを無視する必要があります。 無視しないと、サインインの試行が失敗します。 AD FS インスタンスまたはファーム メンバーで、次のコマンドを実行します。

   ```powershell
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

5. トークンの更新を有効にするには、Windows PowerShell セッションを管理者特権で開き、次のコマンドを実行します。

   ```powershell
   Set-ADFSRelyingPartyTrust -TargetName AzureStack -TokenLifeTime 1440
   ```

## <a name="spn-creation"></a>SPN の作成

多くのシナリオで、認証にサービス プリンシパル名 (SPN) の使用が要求されます。 次はその例の一部です。

- Azure Stack での AD FS デプロイにおける CLI の使用
- AD FS でデプロイされるときの Azure Stack の System Center 管理パック
- AD FS でデプロイされるときの Azure Stack のリソース プロバイダー
- 各種アプリケーション
- 非対話型ログオンを要求する

SPN の作成について詳しくは、「[AD FS のサービス プリンシパルを作成する](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs)」をご覧ください。


## <a name="troubleshooting"></a>トラブルシューティング

### <a name="configuration-rollback"></a>構成のロールバック

エラーが発生し、環境が認証できない状態になった場合は、ロールバック オプションを使用できます。

1. Windows PowerShell セッションを管理者特権で開き、次のコマンドを実行します。

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. その後次のコマンドレットを実行します。

   ```powershell
   Reset-DatacenterIntegationConfiguration
   ```

   ロールバック アクションを実行すると、すべての構成の変更がロールバックされます。 組み込みの **CloudAdmin** ユーザーによる認証のみが有効です。

   > [!IMPORTANT]
   > 既定のプロバイダー サブスクリプションの元の所有者を構成する必要があります

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>追加のログを収集する

コマンドレットの実行が失敗した場合、`Get-Azurestacklogs` コマンドレットを使用して追加のログを収集できます。

1. Windows PowerShell セッションを管理者特権で開き、次のコマンドを実行します。

   ```powershell
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. その後次のコマンドレットを実行します。

   ```powershell
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>次の手順

[Azure Stack の登録](azure-stack-registration.md)
