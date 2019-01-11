---
title: Azure Stack のサービス プリンシパルを管理する | Microsoft Docs
description: Azure Resource Manager でロールベースのアクセス制御と共に使用してリソースへのアクセスを管理できる、新しいサービス プリンシパルを管理する方法について説明します。
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: sethm
ms.openlocfilehash: 50ece9edbc4bee1dea2cc61f2cdd851b278aa7b0
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720443"
---
# <a name="provide-applications-access-to-azure-stack"></a>Azure Stack へのアクセスをアプリケーションに提供する

*適用先:Azure Stack 統合システムと Azure Stack Development Kit*

Azure Stack 内の Azure Resource Manager を通じてリソースをデプロイまたは構成するためのアクセスがアプリケーションに必要な場合は、アプリケーションの資格情報であるサービス プリンシパルを作成します。 サービス プリンシパルには必要な権限のみを委任できます。  

たとえば、Azure Resource Manager を使用して Azure リソースのインベントリを管理する構成管理ツールがあります。 このシナリオでは、サービス プリンシパルを作成し、これに閲覧者のロールを付与して、構成管理ツールのアクセスを読み取り専用に制限します。 

サービス プリンシパルは、お客様自身の資格情報でアプリを実行するよりも推奨されますが、その理由は次のとおりです。

 - お客様自身のアカウントのアクセス許可とは異なるアクセス許可を、サービス プリンシパルに割り当てることができます。 通常、こうしたアクセス許可は、アプリが行う必要があることに制限されます。
 - お客様の責任が変わっても、アプリの資格情報を変更する必要はありません。
 - 無人インストール用スクリプトを実行するときに、証明書を使用して認証を自動化できます。  

## <a name="getting-started"></a>使用の開始

Azure Stack のデプロイ方法に応じて、サービス プリンシパルを作成して開始します。 このドキュメントでは、以下のもののサービス プリンシパルの作成について説明します。

- [Azure Active Directory (Azure AD)](#create-service-principal-for-azure-ad)。 Azure AD は、マルチテナントに対応したクラウドベースのディレクトリおよび ID の管理サービスです。 Azure AD は、接続された Azure Stack で使用できます。
- [Active Directory フェデレーション サービス (AD FS)](#create-service-principal-for-ad-fs)。 AD FS は、シンプルかつ安全な ID フェデレーションと Web シングル サインオン (SSO) 機能を実現します。 AD FS は、接続された Azure Stack インスタンスでも、切断された Azure Stack インスタンスでも使用できます。

サービス プリンシパルを作成したら、AD FS と Azure Active Directory の両方に共通する一連の手順を実行して、ロールに[アクセス許可を委任](#assign-role-to-service-principal)します。

## <a name="manage-service-principal-for-azure-ad"></a>Azure AD のサービス プリンシパルの管理

ID 管理サービスとして Azure Active Directory (Azure AD) を使用して Azure Stack をデプロイしてある場合は、Azure の場合と同様にサービス プリンシパルを作成できます。 このセクションでは、それらの手順をポータルで行う方法について説明します。 始める前に、[Azure AD で必要なアクセス許可](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)があることを確認してください。

### <a name="create-service-principal"></a>サービス プリンシパルの作成

このセクションでは、アプリケーションが表示される Azure AD にアプリケーション (サービス プリンシパル) を作成します。

1. [Azure Portal](https://portal.azure.com) で Azure アカウントにサインインします。
2. **[Azure Active Directory]** > **[アプリの登録]** > **[新しいアプリケーションの登録]** を選択します
3. アプリケーションの名前と URL を指定します。 作成するアプリケーションの種類として、**[Web アプリ/API]** または **[ネイティブ]** を選択します。 値を設定したら、**[作成]** をクリックします。

アプリケーションのサービス プリンシパルが作成されました。

### <a name="get-credentials"></a>資格情報の取得

プログラムでログインする場合、アプリケーションに対しては ID を、Web アプリ/API に対しては認証キーを使用します。 これらの値を取得するには、次の手順に従います。

1. Active Directory の **[アプリの登録]** で、アプリケーションを選択します。

2. **アプリケーション ID** をコピーし、アプリケーション コードに保存します。 「[サンプル アプリケーション](#sample-applications)」の各アプリケーションでは、この値をクライアント ID と呼んでいます。

     ![[クライアント ID]](./media/azure-stack-create-service-principal/image12.png)
3. Web アプリ/API に対して認証キーを生成するには、**[設定]** > **[キー]** の順に選択します。 

4. キーの説明を入力し、キーの期間を指定します。 操作が完了したら、**[保存]** をクリックします。

キーを保存すると、キーの値が表示されます。 キーは後から取得できないため、この値をメモ帳などの一時的な場所にコピーします。 キー値は、アプリケーションとしてサインインする際にアプリケーション ID と共に入力します。 アプリケーションが取得できる場所にキー値を保存します。

![保存されたキー](./media/azure-stack-create-service-principal/image15.png)

完了したら、[アプリケーションにロールを割り当てる](#assign-role-to-service-principal)ことができます。

## <a name="manage-service-principal-for-ad-fs"></a>AD FS のサービス プリンシパルの管理

ID 管理サービスとして Active Directory Federation Services (AD FS) を使用して Azure Stack をデプロイしてある場合は、PowerShell を使用してサービス プリンシパルを作成し、アクセスのためのロールを割り当て、その ID でサインインします。

AD FS を使用してサービス プリンシパルを作成する場合は、次の 2 つの方法のいずれかを使用できます。 次のようにすることができます。
 - [証明書を使用したサービス プリンシパルの作成](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate)
 - [クライアント シークレットを使用したサービス プリンシパルの作成](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret)

AD FS のサービス プリンシパルを管理するタスクは以下のとおりです。

| type | Action |
| --- | --- |
| AD FS 証明書 | [作成](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate) |
| AD FS 証明書 | [Update](azure-stack-create-service-principals.md#update-certificate-for-service-principal-for-AD-FS) |
| AD FS 証明書 | [Remove](azure-stack-create-service-principals.md#remove-a-service-principal-for-AD-FS) |
| AD FS クライアント シークレット | [作成](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| AD FS クライアント シークレット | [Update](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| AD FS クライアント シークレット | [Remove](azure-stack-create-service-principals.md##remove-a-service-principal-for-AD-FS) |

### <a name="create-a-service-principal-using-a-certificate"></a>証明書を使用したサービス プリンシパルの作成

ID のために AD FS を使用した状態でサービス プリンシパルを作成するときは、証明書を使用できます。

#### <a name="certificate"></a>証明書

証明書が必要です。

**証明書の要件**

 - 暗号化サービス プロバイダー (CSP) は、従来のキー プロバイダーである必要があります。
 - 公開キーと秘密キーの両方が必要なため、証明書は PFX ファイル形式である必要があります。 Windows サーバーでは、公開キー ファイル (SSL 証明書ファイル) と関連付けられている秘密キー ファイルが含まれている .pfx ファイルが使用されます。
 - 運用環境では、証明書は、内部の証明機関または公的証明機関のどちらかから発行されている必要があります。 公的証明機関を使用している場合は、Microsoft の信頼されたルート機関プログラムの一部としてその機関を基本オペレーティング システム イメージに含める必要があります。 [Microsoft の信頼されたルート証明書プログラム: 参加者](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)に関するページに完全な一覧があります。
 - お使いの Azure Stack インフラストラクチャは、証明書において公開されている証明機関の証明書失効リスト (CRL) の場所にネットワーク アクセスできる必要があります。 この CRL は、HTTP エンドポイントである必要があります。

#### <a name="parameters"></a>parameters

自動化パラメーターの入力として、次の情報が必要です。

|パラメーター|説明|例|
|---------|---------|---------|
|Name|SPN アカウントの名前|MyAPP|
|ClientCertificates|証明書オブジェクトの配列|X509 証明書|
|ClientRedirectUris<br>(省略可能)|アプリケーションのリダイレクト URI|-|

#### <a name="use-powershell-to-create-a-service-principal"></a>PowerShell を使用したサービス プリンシパルの作成

1. Windows PowerShell セッションを管理者特権で開き、次のコマンドレットを実行します。

   ```PowerShell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

    # If you have a managed certificate use the Get-Item command to retrieve your certificate from your certificate location.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
    $cert = Get-Item "<yourcertificatelocation>"
    
    $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name '<yourappname>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }
    $session|remove-pssession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "azurestackuser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```
   > [!Note]  
   > 検証を目的として、次の例を使用して自己署名証明書を作成できます。

   ```PowerShell  
   $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
   ```


2. 自動化が完了すると、SPN を使用するために必要な詳細が表示されます。 後で使用できるように、出力を保存することをお勧めします。

   例: 

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="update-certificate-for-service-principal-for-ad-fs"></a>AD FS のサービス プリンシパルの証明書の更新

AD FS を使用して Azure Stack をデプロイしてある場合は、PowerShell を使用してサービス プリンシパルのシークレットを更新できます。

ERCS 仮想マシン上で、特権エンドポイントからスクリプトが実行されます。

#### <a name="parameters"></a>parameters

自動化パラメーターの入力として、次の情報が必要です。

|パラメーター|説明|例|
|---------|---------|---------|
|Name|SPN アカウントの名前|MyAPP|
|ApplicationIdentifier|一意識別子|S-1-5-21-1634563105-1224503876-2692824315-2119|
|ClientCertificate|証明書オブジェクトの配列|X509 証明書|

#### <a name="example-of-updating-service-principal-for-ad-fs"></a>AD FS のサービス プリンシパルの更新の例

この例では、自己署名証明書を作成します。 運用環境のデプロイでこれらのコマンドレットを実行する場合、[Get-Item](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Management/Get-Item) を利用して、使用する証明書の証明書オブジェクトを取得します。

1. Windows PowerShell セッションを管理者特権で開き、次のコマンドレットを実行します。

     ```powershell
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $Newcert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange

          $RemoveServicePrincipal = Invoke-Command -Session $session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ClientCertificates $Newcert}

          $session|remove-pssession
     ```

2. 自動化が完了したら、SPN の認証に必要な、更新済みの拇印の値が表示されます。

     ```Shell  
          ClientId              : 
          Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
          ApplicationName       : Azurestack-ThomasAPP-3e5dc4d2-d286-481c-89ba-57aa290a4818
          ClientSecret          : 
          RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-using-a-client-secret"></a>クライアント シークレットを使用したサービス プリンシパルの作成

ID のために AD FS を使用した状態でサービス プリンシパルを作成するときは、証明書を使用できます。 コマンドレットを実行するためには特権エンドポイントを使用します。

ERCS 仮想マシン上で、特権エンドポイントからこれらのスクリプトが実行されます。 特権エンドポイントの詳細については、「[Azure Stack での特権エンドポイントの使用](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)」を参照してください。

#### <a name="parameters"></a>parameters

自動化パラメーターの入力として、次の情報が必要です。

| パラメーター | 説明 | 例 |
|----------------------|--------------------------|---------|
| Name | SPN アカウントの名前 | MyAPP |
| GenerateClientSecret | シークレットの作成 |  |

#### <a name="use-the-ercs-privilegedendpoint-to-create-the-service-principal"></a>ERCS PrivilegedEndpoint を使用 したサービス プリンシパルの作成

1. Windows PowerShell セッションを管理者特権で開き、次のコマンドレットを実行します。

     ```PowerShell  
      # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

     # Creating a SPN with a secre
     $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name '<yourappname>' -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }
     $session|remove-pssession

     # Output the SPN details
     $ServicePrincipal
     ```

2. コマンドレットを実行すると、SPN を使用するために必要な情報がシェルに表示されます。 クライアント シークレットは必ず保存してください。

     ```PowerShell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUZLRoBw3EebMDgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : 192.168.200.224
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

#### <a name="update-client-secret-for-a-service-principal-for-ad-fs"></a>AD FS のサービス プリンシパルのクライアント シークレットの更新

新しいクライアント シークレットは、PowerShell コマンドレットによって自動的に生成されます。

ERCS 仮想マシン上で、特権エンドポイントからスクリプトが実行されます。

##### <a name="parameters"></a>parameters

自動化パラメーターの入力として、次の情報が必要です。

| パラメーター | 説明 | 例 |
|-----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------|
| ApplicationIdentifier | 一意識別子。 | S-1-5-21-1634563105-1224503876-2692824315-2119 |
| ChangeClientSecret | 古いシークレットがまだ有効であり続ける 2,880 分間のロールオーバー期間を設けてクライアント シークレットを変更します。 |  |
| ResetClientSecret | クライアント シークレットをすぐに変更します |  |

##### <a name="example-of-updating-a-client-secret-for-ad-fs"></a>AD FS のクライアント シークレットの更新の例

この例では **resetclientsecret** パラメーターを使用します。これにより、クライアント シークレットはすぐに変更されます。

1. Windows PowerShell セッションを管理者特権で開き、次のコマンドレットを実行します。

     ```PowerShell  
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $Newcert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange

          $UpdateServicePrincipal = Invoke-Command -Session $session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ResetClientSecret}

          $session|remove-pssession
     ```

2. 自動化が完了したら、SPN の認証に必要な、新しく生成されたシークレットが表示されます。 新しいクライアント シークレットは必ず保存してください。

     ```PowerShell  
          ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2120
          ClientId              :  
          Thumbprint            : 
          ApplicationName       : Azurestack-Yourapp-6967581b-497e-4f5a-87b5-0c8d01a9f146
          ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
          RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal-for-ad-fs"></a>AD FS のサービス プリンシパルの削除

AD FS を使用して Azure Stack をデプロイしてある場合は、PowerShell を使用してサービス プリンシパルを削除できます。

ERCS 仮想マシン上で、特権エンドポイントからスクリプトが実行されます。

#### <a name="parameters"></a>parameters

自動化パラメーターの入力として、次の情報が必要です。

|パラメーター|説明|例|
|---------|---------|---------|
| パラメーター | 説明 | 例 |
| ApplicationIdentifier | 一意識別子 | S-1-5-21-1634563105-1224503876-2692824315-2119 |

> [!Note]  
> 既存のすべてのサービス プリンシパルと、そのアプリケーション識別子の一覧を表示するには、get-graphapplication コマンドを使用できます。

#### <a name="example-of-removing-the-service-principal-for-ad-fs"></a>AD FS のサービス プリンシパルの削除の例

```powershell  
     Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

     $UpdateServicePrincipal = Invoke-Command -Session $session -ScriptBlock { Remove-GraphApplication -ApplicationIdentifier S-1-5-21-1634563105-1224503876-2692824315-2119}

     $session|remove-pssession
```

## <a name="assign-a-role"></a>ロールの割り当て

サブスクリプション内のリソースにアクセスするには、アプリケーションをロールに割り当てる必要があります。 アプリケーションにとって適切なアクセス許可を表すのはどのロールであるかを判断します。 利用可能なロールについては、[RBAC: 組み込みロール](../role-based-access-control/built-in-roles.md)に関する記事を参照してください。

スコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。 アクセス許可は、スコープの下位レベルに継承されます。 たとえば、アプリケーションをリソース グループの閲覧者ロールに追加すると、アプリケーションではリソース グループとそれに含まれているすべてのリソースを読み取ることができます。

1. Azure Stack ポータルで、アプリケーションを割り当てるスコープのレベルに移動します。 たとえば、サブスクリプション スコープでロールを割り当てるには、**[サブスクリプション]** を選択します。 リソース グループまたはリソースを選択することもできます。

2. アプリケーションを割り当てる特定のサブスクリプション (リソース グループまたはリソース) を選択します。

     ![割り当てのためのサブスクリプションの選択](./media/azure-stack-create-service-principal/image16.png)

3. **[アクセス制御 (IAM)]** を選択します。

     ![アクセスの選択](./media/azure-stack-create-service-principal/image17.png)

4. **[ロールの割り当ての追加]** を選択します。

5. アプリケーションに割り当てるロールを選択します。

6. アプリケーションを検索して選択します。

7. **[OK]** をクリックして、ロールの割り当てを完了します。 該当のスコープのロールに割り当てられたユーザーの一覧にアプリケーションが表示されます。

サービス プリンシパルを作成してロールを割り当てたら、アプリケーション内でこれを使用して、Azure Stack リソースにアクセスできます。  

## <a name="next-steps"></a>次の手順

[AD FS のユーザーの追加](azure-stack-add-users-adfs.md)  
[ユーザー アクセス許可の管理](azure-stack-manage-permissions.md)  
[Azure Active Directory のドキュメント](https://docs.microsoft.com/azure/active-directory)  
[Active Directory フェデレーション サービス (AD FS)](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
