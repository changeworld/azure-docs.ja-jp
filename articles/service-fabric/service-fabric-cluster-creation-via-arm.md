---
title: テンプレートからの Azure Service Fabric クラスターの作成 | Microsoft Docs
description: この記事では、Azure Resource Manager、Azure Key Vault、クライアント認証用の Azure Active Directory (Azure AD) を使用して、セキュリティで保護された Service Fabric クラスターを Azure で設定する方法について説明します。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/31/2018
ms.author: aljo
ms.openlocfilehash: ccdea2833a24aa9e2bdf4fadd12b19d78b40f999
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038517"
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Azure Resource Manager を使用して Service Fabric クラスターを作成する 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

このガイドでは、Azure Resource Manager を使用して、セキュリティで保護された Azure Service Fabric クラスターを Azure でセットアップする手順を説明します。 長い記事ですが、 この内容について精通していない場合は、必ず以下の各手順に従って進めてください。

このガイドでは次の手順について説明します。

* Service Fabric クラスターをデプロイする前に、気を付ける必要がある主な概念。
* Service Fabric Resource Manager モジュールを使用して Azure にクラスターを作成する方法。
* クラスター上で管理操作を実行するユーザーを認証するための Azure Active Directory (Azure AD) を設定する方法。
* カスタム Azure Resource Manager テンプレートを作成する方法。

## <a name="key-concepts-to-be-aware-of"></a>気を付ける必要がある主な概念
Azure の Service Fabric では、X509 証明書を使用してクラスターとそのエンドポイントをセキュリティで保護する必要があります。 Service Fabric では証明書を使用して、クラスターとそのアプリケーションのさまざまな側面をセキュリティで保護するための認証および暗号化を指定します。 アプリケーション、サービス、それらに含まれるデータのデプロイ、アップグレード、削除など、クラスターに対するクライアント アクセスや管理操作の実行について、証明書または Azure Active Directory の資格情報を使用できます。 クライアント上の証明書の共有を防ぐことができる唯一の方法なので、Azure Active Directory を使用することが強く勧められます。  Service Fabric での証明書の使用方法の詳細については、「[Service Fabric クラスターのセキュリティに関するシナリオ][service-fabric-cluster-security]」をご覧ください。

Service Fabric では、X.509 証明書を使用してクラスターをセキュリティで保護し、アプリケーションのセキュリティ機能を提供します。 [Key Vault][key-vault-get-started] を使用して Azure の Service Fabric クラスターの証明書を管理します。 


### <a name="cluster-and-server-certificate-required"></a>クラスターとサーバーの証明書 (必須)
これらの証明書 (1 つのプライマリと省略可能なセカンダリ) は、クラスターをセキュリティで保護し、クラスターに対する不正なアクセスを防ぐために必要です。 クラスターのセキュリティは次の 2 つの方法で確保されます。

* **クラスター認証:** クラスター フェデレーション用のノード間通信を認証します。 この証明書で自分の ID を証明できたノードだけがクラスターに参加できます。
* サーバー認証: 管理クライアントに対するクラスター管理エンドポイントを**認証**します。これで、管理クライアントにより、"中間者" ではなく実際のクラスターと通信していることが認識されるようになります。 この証明書は、HTTPS 管理 API および HTTPS 経由の Service Fabric Explorer に対して SSL も提供します。

この目的のため、証明書は次の要件を満たす必要があります。

* 証明書は秘密キーを含む必要があります。 通常、これらの証明書の拡張子は .pfx または .pem です。  
* 証明書はキー交換のために作成される必要があり、Personal Information Exchange (.pfx) ファイルにエクスポートできます。
* **証明書のサブジェクト名は Service Fabric クラスターへのアクセスに使用されるドメインと一致している必要があります**。 この整合性は、HTTPS 管理エンドポイントと Service Fabric Explorer 用の SSL を提供するために必要です。 証明機関 (CA) から *.cloudapp.azure.com ドメインの SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書のサブジェクト名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>クライアント認証用に Azure Active Directory をセットアップする (省略可能ですが推奨されます)

Azure AD は組織 (テナントと呼ばれます) を有効にしてアプリケーションに対するユーザー アクセスを管理します。 アプリケーションは、Web ベースのサインイン UI を持つアプリケーションと、ネイティブ クライアントのエクスペリエンスを持つアプリケーションに分けられます。 この記事では、既にテナントを作成していることを前提としています。 まだ作成していない場合は、まず「[Azure Active Directory テナントを取得する方法][active-directory-howto-tenant]」をお読みください。

Service Fabric クラスターでは、Web ベースの [Service Fabric Explorer][service-fabric-visualizing-your-cluster] や [Visual Studio][service-fabric-manage-application-in-visual-studio] など、いくつかのエントリ ポイントから管理機能にアクセスできます。 このため、クラスターへのアクセスを制御するには、2 つの Azure AD アプリケーション (Web アプリケーションとネイティブ アプリケーション) を作成します。

詳細な設定方法については後述します。

### <a name="application-certificates-optional"></a>アプリケーション証明書 (省略可能)
アプリケーション セキュリティの目的で、任意の数の追加の証明書をクラスターにインストールできます。 クラスターを作成する前に、ノードにインストールする証明書を必要とするアプリケーション セキュリティ シナリオについて考慮します。これには次のようなものがあります。

* アプリケーション構成値の暗号化と復号化。
* レプリケーション中のノード間のデータの暗号化。

セキュリティで保護されたクラスターを作成する概念は、Linux のクラスターでも Windows のクラスターでも同じです。 

### <a name="client-authentication-certificates-optional"></a>クライアント認証証明書 (省略可能)
管理者またはユーザーのクライアント操作用に、任意の数の証明書を追加で指定できます。 既定では、クラスター証明書には管理者クライアント特権があります。 このような追加のクライアント証明書は、クラスター構成で許可と指定する必要があるだけで、クラスターにインストールする必要はありません。ただし、クラスターに接続し、何らかの管理操作を実行するには、クライアント コンピューターにインストールする必要があります。


## <a name="prerequisites"></a>前提条件 
セキュリティで保護されたクラスターを作成する概念は、Linux のクラスターでも Windows のクラスターでも同じです。 このガイドでは、Azure PowerShell または Azure CLI を使用して新しいクラスターを作成する方法について説明します。 つぎのいずれかが前提条件です。

-  [Azure PowerShell 4.1 以降][azure-powershell]または [Azure CLI 2.0 以降][azure-CLI]。
-  Service Fabric モジュールの詳細については、[AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) と [az SF CLI モジュール](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)に関するページを参照してください。


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Service Fabric RM モジュールを使用してクラスターをデプロイする

このドキュメントでは、Service Fabric RM PowerShell と CLI モジュールを使用してクラスターをデプロイします。PowerShell または CLI コマンドには複数のシナリオがあります。 その各シナリオについて説明します。 ニーズに最も近いシナリオを参考にしてください。 

- 新しいクラスターを作成する 
    - システムで生成された自己署名証明書を使用する
    - 既に所有している証明書を使用する

既定のクラスター テンプレートまたは既に持っているテンプレートを使用することができます

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>新しいクラスターを作成する - システムで生成された自己署名証明書を使用する

システムで自己署名証明書を生成し、クラスターのセキュリティ保護に使用する場合は、次のコマンドを使用してクラスターを作成します。 このコマンドで、クラスターのセキュリティに使用されるプライマリ クラスターの証明書が設定されます。また、その証明書を使用して管理操作を実行する管理者アクセス権が設定されます。

### <a name="login-to-azure"></a>Azure にログインする

```PowerShell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
```

```CLI
azure login
az account set --subscription $subscriptionId
```
#### <a name="use-the-default-5-node-1-node-type-template-that-ships-in-the-module-to-set-up-the-cluster"></a>モジュールに含まれる既定の 5 Node 1 ノード タイプ テンプレートを使用してクラスターを設定する

次のコマンドを使用し、最小限のパラメーターを指定して、簡単にクラスターを作成します。

ここで使用されているテンプレートは、[Azure Service Fabric テンプレート サンプル: Windows テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG)と [Ubuntu テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)のページで入手できます。

次のコマンドは、Windows および Linux クラスターを作成する場合に利用できます。必要な変更は、使用する OS の指定のみです。 次の PowerShell/CLI コマンドから、CertificateOutputFolder に指定されている証明書も出力されますが、証明書フォルダーはあらかじめ作成しておく必要があります。 このコマンドは、VM SKU など他のパラメーターも受け取ります。

> [!NOTE]
> 次の PowerShell コマンドは、バージョン 6.1 より新しい Azure Resource Manager PowerShell でのみ動作します。 Azure Resource Manager PowerShell の現在のバージョンを確認するには、PowerShell コマンド "Get-Module AzureRM" を実行します。 Azure Resource Manager PowerShell のバージョンをアップグレードするには、次のリンクに従ってください。 https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-6.3.0
>
>
```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

```CLI
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

#### <a name="use-the-custom-template-that-you-already-have"></a>既存のカスタム テンプレートを使用する 

ニーズに合わせてカスタム テンプレートを作成する必要がある場合は、[Azure Service Fabric テンプレート サンプル](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)で入手できるテンプレートのいずれかから始めることを強くお勧めします。 ガイダンスと説明については、後述する[クラスター テンプレートのカスタマイズ][customize-your-cluster-template]に関するセクションを参照してください。

カスタム テンプレートを既に持っている場合は、3 つの証明書すべてがテンプレートのパラメーターに関連付けられ、パラメーターのファイル名がテンプレートで指定され、値がテンプレートで null であることを慎重に確認してください。

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

この処理と同等の処理を行う CLI コマンドを次に示します。 宣言ステートメントの値は適切な値に変更してください。 CLI は、上記の PowerShell コマンドがサポートする他のパラメーターをすべてサポートしています。

```CLI
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath
```


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>新しいクラスターを作成する - CA から購入した証明書または既に所有している証明書を使用する

クラスターのセキュリティ保護に使用する証明書を持っている場合は、次のコマンドを使用してクラスターを作成します。

CA で署名された証明書で、他の用途にも使用する予定がある場合は、Key Vault 専用のリソース グループを用意することをお勧めします。 Key Vault は専用のリソース グループに配置することをお勧めします。 そうすることで、必要なキーとシークレットを失うことなく、コンピューティング リソース グループやストレージ リソース グループを削除することができます (Service Fabric クラスターのあるリソース グループを含む)。 **Key Vault を持つリソース グループは、それを使用するクラスターと_同じリージョンにある必要があります_。**


#### <a name="use-the-default-5-node-1-node-type-template-that-ships-in-the-module"></a>モジュールに含まれる既定の 5 Node 1 ノード タイプ テンプレートを使用する
ここで使用されているテンプレートは、[Azure サンプル: Windows テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG)と [Ubuntu テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)のページで入手できます。

```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

```CLI
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

#### <a name="use-the-custom-template-that-you-have"></a>既存のカスタム テンプレートを使用する 
ニーズに合わせてカスタム テンプレートを作成する必要がある場合は、[Azure Service Fabric テンプレート サンプル](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)で入手できるテンプレートのいずれかから始めることを強くお勧めします。 ガイダンスと説明については、後述する[クラスター テンプレートのカスタマイズ][customize-your-cluster-template]に関するセクションを参照してください。

カスタム テンプレートを既に持っている場合は、3 つの証明書すべてがテンプレートのパラメーターに関連付けられ、パラメーターのファイル名がテンプレートで指定され、値がテンプレートで null であることを慎重に確認してください。

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

この処理と同等の処理を行う CLI コマンドを次に示します。 宣言ステートメントの値は適切な値に変更してください。

```CLI
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-key-vault"></a>Key Vault に既にアップロードしてあるシークレットへのポインターを使用します。

既存の Key Vault を使用するには、コンピューティング リソース プロバイダーがそこから証明書を取得し、クラスター ノードにインストールできるように、_その Key Vault をデプロイ用に有効にする必要があります_。

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```
この処理と同等の処理を行う CLI コマンドを次に示します。 宣言ステートメントの値は適切な値に変更してください。

```CLI
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>クライアント認証用に Azure Active Directory をセットアップする

Azure AD は組織 (テナントと呼ばれます) を有効にしてアプリケーションに対するユーザー アクセスを管理します。 アプリケーションは、Web ベースのサインイン UI を持つアプリケーションと、ネイティブ クライアントのエクスペリエンスを持つアプリケーションに分けられます。 この記事では、既にテナントを作成していることを前提としています。 まだ作成していない場合は、まず「[Azure Active Directory テナントを取得する方法][active-directory-howto-tenant]」をお読みください。

Service Fabric クラスターでは、Web ベースの [Service Fabric Explorer][service-fabric-visualizing-your-cluster] や [Visual Studio][service-fabric-manage-application-in-visual-studio] など、いくつかのエントリ ポイントから管理機能にアクセスできます。 このため、クラスターへのアクセスを制御するには、2 つの Azure AD アプリケーション (Web アプリケーションとネイティブ アプリケーション) を作成します。

Azure AD の Service Fabric クラスターでの構成に関する手順の一部を簡略化するため、一連の Windows PowerShell スクリプトが作成されています。

> [!NOTE]
> クラスターを作成する前に、次の手順を完了する必要があります。 スクリプトはクラスター名とエンドポイントを想定しているため、値は作成済みのものではなく、計画する必要があります。

1. コンピューターに[スクリプトをダウンロード][sf-aad-ps-script-download]します。
2. zip ファイルを右クリックし、**[プロパティ]** を選択して **[ブロック解除]** チェックボックスをオンにし、**[適用]** をクリックします。
3. zip ファイルを解凍します。
4. `SetupApplications.ps1` を実行します。パラメーターとして、TenantId、ClusterName、WebApplicationReplyUrl を指定します。 例: 

```PowerShell
.\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
```

> [!NOTE]
> 国内のクラウド (Azure Government、Azure China、Azure Germany) については、`-Location` パラメーターを指定する必要もあります。

TenantId は、PowerShell コマンド `Get-AzureSubscription` を実行することで検索できます。 このコマンドを実行すると、すべてのサブスクリプションの TenantId が表示されます。

ClusterName は、スクリプトによって作成される Azure AD アプリケーションのプレフィックスとして使用されます。 実際のクラスター名と完全に一致している必要はありません。 これは、一緒に使用される Service Fabric クラスターに対して、Azure AD のアーティファクトのマッピングを容易にするためだけに使用します。

WebApplicationReplyUrl は、サインインの完了後に Azure AD がユーザーに返す既定のエンドポイントです。 このエンドポイントをクラスターの Service Fabric Explorer エンドポイントして設定します。既定値は次のとおりです。

https://&lt;cluster_domain&gt;:19080/Explorer

Azure AD テナント用の管理特権を持っているアカウントにサインインすることを求められます。 サインインすると、スクリプトは、Service Fabric クラスターを表す Web アプリケーションとネイティブ アプリケーションを作成します。 [Azure Portal][azure-portal] でテナントのアプリケーションを調べると、次の 2 つの新しいエントリがあることがわかります。

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

次のセクションでクラスターを作成するとき、スクリプトによって、Azure Resource Manager テンプレートが必要とする JSON が出力されます。このため、PowerShell ウィンドウは開いたままにしてください。

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Service Fabric クラスターの Resource Manager テンプレートを作成する
このセクションは、カスタムの Service Fabric クラスター Resource Manager テンプレートを作成するユーザー向けです。 テンプレートを持っている場合は、戻って PowerShell または CLI モジュールを使用してデプロイすることもできます。 

サンプルの Resource Manager テンプレートは、[GitHub の Azure サンプル](https://github.com/Azure-Samples/service-fabric-cluster-templates)で入手できます。 これらのテンプレートは、クラスター テンプレートの作成を始める際に使用できます。

> [!NOTE]
> また、国内のクラウド (Azure Government、Azure China、Azure Germany) については、ARM テンプレート: `AADLoginEndpoint`、`AADTokenEndpointFormat`、`AADCertEndpointFormat` に以下の `fabricSettings` を追加する必要もあります。

### <a name="create-the-resource-manager-template"></a>Resource Manager テンプレートの作成
このガイドでは、[セキュリティで保護された 5 ノード クラスター][service-fabric-secure-cluster-5-node-1-nodetype]の例にあるテンプレートとテンプレート パラメーターを使用します。 `azuredeploy.json` と `azuredeploy.parameters.json` をコンピューターにダウンロードして、任意のテキスト エディターで両方のファイルを開きます。

### <a name="add-certificates"></a>証明書の追加
証明書は、証明書キーを含む Key Vault を参照することによって、Cluster Resource Manager テンプレートに追加されます。 Resource Manager テンプレート パラメーター ファイル (azuredeploy.parameters.json) にこれらの Key Vault パラメーターと値を追加します。 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>仮想マシン スケール セットの osProfile にすべての証明書を追加する
クラスターにインストールするすべての証明書は、スケール セット リソース (Microsoft.Compute/virtualMachineScaleSets) の [osProfile] セクションで構成する必要があります。 このアクションにより、リソース プロバイダーに対して VM に証明書をインストールするよう指示されます。 このインストールには、クラスター証明書とアプリケーションで使用する予定のあらゆるアプリケーションのセキュリティ証明書が含まれます。

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Service Fabric クラスターの証明書を構成する
クラスターの認証証明書は、Service Fabric クラスター リソース (Microsoft.ServiceFabric/clusters) と、仮想マシン スケール セット リソースの仮想マシン スケール セット用 Service Fabric 拡張機能で構成する必要があります。 これにより、Service Fabric のリソース プロバイダーを使用してクラスターの認証および管理エンドポイントのサーバー認証用に構成できます。

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>仮想マシン スケール セット リソースの証明書情報を追加します。
```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Service Fabric クラスター リソースの証明書情報を追加します。
```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>クライアント アクセスに Azure AD を使用する Azure AD の構成を追加する

Azure AD 構成は、証明書キーを含む Key Vault を参照することによって、Cluster Resource Manager テンプレートに追加されます。 Resource Manager テンプレート パラメーター ファイル (azuredeploy.parameters.json) にこれらの Azure AD パラメーターと値を追加します。

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a name="populate-the-parameter-file-with-the-values"></a>パラメーター ファイルに値を設定する
最後に、Key Vault と Azure AD PowerShell コマンドからの出力値を使用してパラメーター ファイルを作成します。

Azure Service Fabric RM PowerShell モジュールを使用する予定がある場合は、クラスター証明書情報を設定する必要はありません。 クラスター セキュリティの自己署名証明書を自動的に生成するには、それらの情報を null のままにしておきます。 

> [!NOTE]
> RM モジュールでこれらの空のパラメーター値を取得して設定するには、パラメーター名が以下の名前と一致する必要があります。

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

アプリケーション証明書を使用している場合、または Key Vault にアップロードした既存のクラスターを使用している場合は、その情報を取得して設定する必要があります。

RM モジュールには、Azure AD 構成を生成する機能がありません。そのため、Azure AD をクライアント アクセスに使用する場合は、それを設定する必要があります。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

### <a name="test-your-template"></a>テンプレートのテスト  
次の PowerShell コマンドを使用して、パラメーター ファイルで Resource Manager テンプレートをテストします。

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

問題が発生し、不明なメッセージを受け取った場合は、オプションに "-Debug" を使用します。

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

次の図は、Key Vault と Azure AD の構成を Resource Manager テンプレートに適合させる場所を示しています。

![Resource Manager の依存関係マップ][cluster-security-arm-dependency-map]


## <a name="encrypting-the-disks-attached-to-your-windows-cluster-nodevirtual-machine-instances"></a>Windows クラスター ノード/仮想マシン インスタンスにアタッチされているディスクの暗号化

ノードにアタッチされているディスク (OS ドライブやその他のマネージド ディスク) を暗号化するには、Azure Disk Encryption を利用します。 Azure Disk Encryption は、[Windows 仮想マシン ディスクの暗号化](service-fabric-enable-azure-disk-encryption-windows.md)に役立つ新機能です。 Azure Disk Encryption は、業界標準である Windows の [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 機能を利用して OS ボリュームを暗号化します。 このソリューションは [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) と統合されており、ディスクの暗号化キーとシークレットは Key Vault サブスクリプションで制御および管理できます。 またこのソリューションでは、仮想マシン ディスク上のすべてのデータが、Azure Storage での保存時に暗号化されます。 

## <a name="encrypting-the-disks-attached-to-your-linux-cluster-nodevirtual-machine-instances"></a>Linux クラスター ノード/仮想マシン インスタンスにアタッチされているディスクの暗号化

ノードにアタッチされているディスク (データ ドライブやその他のマネージド ディスク) を暗号化するには、Azure Disk Encryption を利用します。 Azure Disk Encryption は、[Linux 仮想マシン ディスクの暗号化](service-fabric-enable-azure-disk-encryption-linux.md)に役立つ新機能です。 Azure Disk Encryption は、業界標準である Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を利用してデータ ディスクのボリュームを暗号化します。 このソリューションは [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) と統合されており、ディスクの暗号化キーとシークレットは Key Vault サブスクリプションで制御および管理できます。 またこのソリューションでは、仮想マシン ディスク上のすべてのデータが、Azure Storage での保存時に暗号化されます。 


## <a name="create-the-cluster-using-azure-resource-template"></a>Azure リソース テンプレートを使用してクラスターを作成する 

このドキュメントで概要を説明した手順で、クラスターをデプロイできるようになりました。パラメーター ファイルに値があり、設定した場合は、[Azure リソース テンプレートのデプロイ][resource-group-template-deploy]を使用してクラスターを直接作成することもできます。

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

問題が発生し、不明なメッセージを受け取った場合は、オプションに "-Debug" を使用します。


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>ユーザーをロールに割り当てる
クラスターを表すアプリケーションを作成したら、Service Fabric によってサポートされるロール (read-only と admin) にユーザーを割り当てます。[Azure Portal][azure-portal] を使って、ロールを割り当てることができます。

1. Azure Portal の右上隅でテナントを選びます。

    ![テナント ボタンを選ぶ][select-tenant-button]
2. 左のタブで **[Azure Active Directory]** を選び、[エンタープライズ アプリケーション] を選びます。
3. [すべてのアプリケーション] を選び、`myTestCluster_Cluster` のような名前を持つ Web アプリケーションを探して選びます。
4. **[ユーザーとグループ]** タブをクリックします。

    ![[ユーザーとグループ] タブ][users-and-groups-tab]
5. 新しいページの **[ユーザーの追加]** ボタンをクリックして、ユーザーと割り当てるロールを選び、ページの下部にある **[選択]** ボタンをクリックします。

    ![ロールへのユーザーの割り当てページ][assign-users-to-roles-page]
6. ページの下部にある **[割り当て]** ボタンをクリックします。

    ![割り当ての追加の確認][assign-users-to-roles-confirm]

> [!NOTE]
> Service Fabric でのロールの詳細については、「 [ロール ベースのアクセス制御 (Service Fabric クライアント用)](service-fabric-cluster-security-roles.md)」を参照してください。
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Azure Active Directory の設定に関するトラブルシューティング
Azure AD の設定時や使用時には問題が発生することがあります。そこで、問題のデバッグに役立つポインターをいくつか紹介します。

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer に、証明書の選択を求めるメッセージが表示される
#### <a name="problem"></a>問題点
Service Fabric Explorer で Azure AD に正常にサインインすると、ブラウザーはホームページに戻りますが、証明書の選択を求めるメッセージが表示されます。

![SFX 証明書ダイアログ][sfx-select-certificate-dialog]

#### <a name="reason"></a>理由
ユーザーに Azure AD クラスター アプリケーション内でロールが割り当てられていません。 このため、Service Fabric クラスターで Azure AD の認証が失敗します。 Service Fabric エクスプローラーは、証明書認証にフォールバックします。

#### <a name="solution"></a>解決策
次の説明に従って Azure AD をセットアップしてユーザー ロールを割り当てます。 また、`SetupApplications.ps1` のように [アプリにアクセスするにはユーザー割り当てが必要] をオンにすることをお勧めします。

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>PowerShell を使用した接続が失敗し、"指定した資格情報が無効です" というエラーが返される
#### <a name="problem"></a>問題点
"AzureActiveDirectory" セキュリティ モードで PowerShell を使用してクラスターに接続すると、Azure AD に正常にサインインした後で接続に失敗し、"指定した資格情報が無効です" というエラーが返されます。

#### <a name="solution"></a>解決策
前の問題の解決策と同じです。

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>サインインするときに Service Fabric Explorer で "AADSTS50011" エラーが返される
#### <a name="problem"></a>問題点
Service Fabric Explorer で Azure AD へのサインインを試行すると、"AADSTS50011: 応答アドレス &lt;url&gt; が、アプリケーションに対して構成された応答アドレス &lt;guid&gt; と一致していません" というエラーが返されます。

![SFX の応答アドレスが一致しない][sfx-reply-address-not-match]

#### <a name="reason"></a>理由
Service Fabric Explorer に相当するクラスター (Web) アプリケーションは Azure AD に対する認証を試み、要求の一部として、戻り先のリダイレクト URL を指定しています。 しかし、その URL が Azure AD アプリケーションの **[応答 URL]** のリストに表示されません。

#### <a name="solution"></a>解決策
AAD ページで [アプリの登録] を選び、クラスター アプリケーションを選んで、**[応答 URL]** ボタンを選びます。 [応答 URL] ページで、Service Fabric Explorer の URL をリストに追加するか、リスト内の項目のいずれかと置き換えます。 完了したら、変更を保存します。

![Web アプリケーションの応答 URL][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>PowerShell で Azure AD 認証を使用してクラスターに接続する
Service Fabric クラスターに接続するには、次の PowerShell コマンド例を使用します。

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Connect-ServiceFabricCluster コマンドレットについて詳しくは、「[Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster)」をご覧ください。

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>複数のクラスターで同じ Azure AD テナントを再利用できますか?
はい。 ただし、Service Fabric Explorer の URL をクラスター (Web) アプリケーションに追加することを忘れないでください。 そうしないと、Service Fabric Explorer は動作しません。

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Azure AD が有効になっているときもサーバーの証明書が必要なのはどうしてですか?
FabricClient と FabricGateway では、相互認証が実行されます。 Azure AD 認証中、Azure AD 統合がクライアント ID をサーバーに提供し、サーバー証明書を使用してサーバー ID の確認が行われます。 Service Fabric の証明書について詳しくは、「[X.509 証明書と Service Fabric][x509-certificates-and-service-fabric]」をご覧ください。

## <a name="next-steps"></a>次の手順
この時点で、管理認証を提供する Azure Active Directory で、セキュリティで保護されたクラスターの準備ができています。 次に、[クラスターに接続](service-fabric-connect-to-secure-cluster.md)して、[アプリケーション シークレットを管理](service-fabric-application-secret-management.md)する方法を説明します。


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[select-tenant-button]: ./media/service-fabric-cluster-creation-via-arm/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-via-arm/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png
