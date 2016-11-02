
<properties
   pageTitle="Azure Resource Manager を使用してセキュリティで保護された Service Fabric クラスターを作成する | Microsoft Azure"
   description="この記事では、Azure Resource Manager、Azure Key Vault、クライアント認証用の Azure Active Directory (AAD) を使用して、セキュリティで保護された Service Fabric クラスターを Azure で設定する方法について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>


# <a name="create-a-service-fabric-cluster-in-azure-using-azure-resource-manager"></a>Azure Resource Manager を使用して Azure で Service Fabric クラスターを作成する

> [AZURE.SELECTOR]
- [Azure リソース マネージャー](service-fabric-cluster-creation-via-arm.md)
- [Azure ポータル](service-fabric-cluster-creation-via-portal.md)

これは、Azure Resource Manager を使用して、セキュリティで保護された Azure Service Fabric クラスターを Azure でセットアップする手順を説明するステップ バイ ステップ ガイドです。 このガイドでは、次の手順について説明します。

 - クラスターとアプリケーションのセキュリティのためのキーを管理する Key Vault を設定します。
 - Azure Resource Manager を使って、セキュリティで保護されたクラスターを Azure で作成します。
 - クラスター管理のため、Azure Active Directory (AAD) でユーザーを認証します。

セキュリティで保護されたクラスターとは、管理操作に対する未承認のアクセスを防止するクラスターで、この操作には、アプリケーション、サービス、また格納されたデータのデプロイ、アップグレード、削除が含まれます。 セキュリティで保護されていないクラスターとは、だれでも管理操作にいつでも接続し、実行できるクラスターを指します。 セキュリティで保護されていないクラスターを作成することもできますが、**セキュリティで保護されたクラスターを作成することを強くお勧めします**。 セキュリティで保護されていないクラスターを**後でセキュリティで保護することはできません**。新しいクラスターを作成する必要があります。

セキュリティで保護されたクラスターの作成については、Linux クラスターであれ Windows クラスターであれ、考え方は同じです。 セキュリティで保護された Linux クラスターの作成に関する詳しい情報とヘルパー スクリプトについては、「 [セキュリティで保護されたクラスターを Linux 上に作成する](#secure-linux-clusters)

## <a name="log-in-to-azure"></a>Azure へのログイン
このガイドでは [Azure PowerShell][azure-powershell] を使用します。 新しい PowerShell セッションを開始した場合、Azure アカウントにログインし、Azure のコマンドを実行する前にサブスクリプションを選択します。

Azure アカウントにログインします。

```powershell
Login-AzureRmAccount
```

サブスクリプションを選択します。

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>Key Vault の設定

このセクションでは、Azure Service Fabric クラスターと Service Fabric アプリケーション用の Key Vault を作成する手順を説明します。 Key Vault の完全なガイドについては、[「Azure Key Vault の概要」][key-vault-get-started] を参照してください。

Service Fabric では、X.509 証明書を使用してクラスターをセキュリティで保護し、アプリケーションのセキュリティ機能を提供します。 Azure で Service Fabric クラスター用の証明書を管理するために、Azure Key Vault が使用されます。 Azure にクラスターがデプロイされると、Service Fabric クラスターの作成担当 Azure リソース プロバイダーにより Key Vault から証明書が取得されてクラスター VM にインストールされます。

次の図は、Key Vault、Service Fabric クラスター、そしてクラスターの作成時に Key Vault に格納された証明書を使用する Azure リソース プロバイダー間の関係を示しています。

![証明書のインストール][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>リソース グループの作成

最初の手順として、Key Vault 専用のリソース グループを作成します。 Key Vault は専用のリソース グループに配置することをお勧めします。 そうすることで、必要なキーとシークレットを失うことなく、コンピューティング リソース グループやストレージ リソース グループを削除することができます (Service Fabric クラスターのあるリソース グループを含む)。 Key Vault を持つリソース グループは、それを使用するクラスターと同じリージョンにある必要があります。

```powershell

    New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    
    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>Key Vault の作成 

新しいリソース グループに、Key Vault を作成します。 Key Vault を **デプロイ用に有効にして** 、Service Fabric リソース プロバイダーがそこから証明書を取得し、クラスター ノードにインストールできるようにする必要があります。

```powershell

    New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment
    
    
    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all
    
    
    Tags                             :
```

Key Vault が既にある場合は、Azure CLI を使用してそれをデプロイ用に有効にできます。

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```

<a id="add-certificate-to-key-vault"></a>
## <a name="add-certificates-to-key-vault"></a>証明書の Key Vault への追加

Service Fabric では証明書を使用して、クラスターとそのアプリケーションのさまざまな側面をセキュリティで保護するための認証および暗号化を指定します。 Service Fabric での証明書の使用方法については、[「Service Fabric クラスターのセキュリティに関するシナリオ」][service-fabric-cluster-security] をご覧ください。

### <a name="cluster-and-server-certificate-(required)"></a>クラスターとサーバーの証明書 (必須) 

この証明書はクラスターをセキュリティで保護し、クラスターに対する未承認のアクセスを防ぐために必要です。 証明書により、クラスター セキュリティが次のような方法で提供されます。
 
 - **クラスター認証:** クラスター フェデレーション用のノード間通信を認証します。 この証明書で自分の ID を証明できたノードだけがクラスターに参加できます。
 - **サーバー認証:** 管理クライアントに対するクラスター管理エンドポイントを認証します。これで、管理クライアントにより、実際のクラスターと通信していることが認識されるようになります。 この証明書は、HTTPS 管理 API および HTTPS 経由の Service Fabric Explorer に対して SSL も提供します。

この目的のため、証明書は次の要件を満たす必要があります。

 - 証明書は秘密キーを含む必要があります。
 - 証明書はキー交換のために作成され、Personal Information Exchange (.pfx) ファイルにエクスポートできる必要があります。
 - 証明書の件名は Service Fabric クラスターへのアクセスに使用されるドメインと一致する必要があります。 この整合性は、HTTPS 管理エンドポイントと Service Fabric Explorer 用の SSL を提供するために必要です。 証明機関 (CA) から `.cloudapp.azure.com` ドメインの SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書の件名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

### <a name="application-certificates-(optional)"></a>アプリケーション証明書 (省略可能)

アプリケーション セキュリティの目的で、任意の数の追加の証明書をクラスターにインストールできます。 クラスターを作成する前に、ノードにインストールする証明書を必要とするアプリケーション セキュリティ シナリオについて考慮します。これには次のようなものがあります。

 - アプリケーション構成値の暗号化と復号化
 - レプリケーション中のノード間のデータの暗号化 

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Azure リソース プロバイダー用の証明書の書式設定

秘密キー ファイル (.pfx) を追加し、Key Vault を使用して直接使用できます。 ただし、Azure リソース プロバイダーは、base-64 でエンコードされた文字列としての .pfx と、秘密キーのパスワードを含む特別な JSON 形式にキーを格納する必要があります。 これらの要件に対応するには、キーを JSON 文字列に配置して、 *シークレット* として Key Vault に格納する必要があります。

このプロセスをわかりやすくするために、PowerShell モジュールを [GitHub から入手できます][service-fabric-rp-helpers]。 モジュールを使用するには、次の手順を実行します。

  1. リポジトリの内容全体をローカル ディレクトリにダウンロードします。 
  2. モジュールを PowerShell ウィンドウにインポートします。

  ```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
  ```
     
この PowerShell モジュールの `Invoke-AddCertToKeyVault` コマンドは、証明書の秘密キーを JSON 文字列に自動的にフォーマットし、Key Vault にアップロードします。 これを使用してクラスター証明書と追加のアプリケーション証明書 (ある場合) を Key Vault に追加します。 必要に応じて、クラスターにインストールする追加の証明書についてこの手順を繰り返します。

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"
    
    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault
    
    
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

前掲の文字列はいずれも、ノードの認証、管理エンドポイントのセキュリティおよび認証、X.509 証明書を使用する追加のアプリケーション セキュリティ機能用の証明書をインストールする Service Fabric クラスターの Resource Manager テンプレートを構成するための Key Vault 前提条件です。 この時点で、Azure で以下の設定が完了しています。

 - Key Vault リソース グループ
   - Key Vault
     - クラスター サーバー認証証明書
     - アプリケーション証明書

## <a name="set-up-azure-active-directory-for-client-authentication"></a>クライアント認証用に Azure Active Directory をセットアップする

組織 (テナントと呼ばれます) は、AAD を使用してアプリケーションへのユーザー アクセスを管理できます。アプリケーションは、Web ベースのログイン UI を持つものとネイティブ クライアント エクスペリエンスを提供するものに分類されます。 このドキュメントでは、すでにテナントを作成していることを前提としています。 作成していない場合は、まず [「Azure Active Directory テナントを取得する方法」][active-directory-howto-tenant]をお読みください。

Service Fabric クラスターでは、Web ベースの [Service Fabric Explorer][service-fabric-visualizing-your-cluster] や [Visual Studio][service-fabric-manage-application-in-visual-studio] など、いくつかのエントリ ポイントから管理機能にアクセスできます。 このため、クラスターへのアクセスを制御するには、2 つの AAD アプリケーション (Web アプリケーションとネイティブ アプリケーション) を作成します。

AAD の Service Fabric クラスターでの構成に関する手順の一部を簡略化するため、一連の Windows PowerShell スクリプトが作成されています。

>[AZURE.NOTE] これらの手順は、クラスターを作成する*前に*実行する必要があります。これは、スクリプトでクラスター名とエンドポイントの使用が発生した場合に、既に作成した値ではなく、予定された値にする必要があるためです。

1. コンピューターに [スクリプトをダウンロード][sf-aad-ps-script-download]します。

2. zip ファイルを右クリックして、**[プロパティ]** を選択し、**[ブロックの解除]** チェック ボックスをオンにして適用します。

3. zip ファイルを解凍します。

4. `SetupApplications.ps1` を実行します。パラメーターとして、TenantId、ClusterName、WebApplicationReplyUrl を指定します。 次に例を示します。

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    **TenantId** は、Azure クラシック ポータルでテナントの URL を見るとわかります。 その URL に埋め込まれている GUID が TenantId です。 次に例を示します。

    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/**690ec069-8200-4068-9d01-5aaf188e557a**/users

    **ClusterName** は、スクリプトによって作成される AAD アプリケーションのプレフィックスとして使用されます。 それは AAD アーティファクトとそれらが使用される Service Fabric クラスターとのマッピングを容易にすることのみを目的としているため、実際のクラスター名と完全に一致させる必要はありません。

    **WebApplicationReplyUrl** は、サインイン プロセスの完了後に AAD がユーザーに返す既定のエンドポイントです。 これには、クラスターの Service Fabric Explorer エンドポイントを設定します。既定値は次のとおりです。

    https://&lt;cluster_domain&gt;:19080/Explorer

    AAD テナント用の管理特権を持っているアカウントにサインインすることを求められます。 これを実行すると、スクリプトは、Service Fabric クラスターを表す Web アプリケーションとネイティブ アプリケーションの作成に進みます。 [Azure クラシック ポータル][azure-classic-portal]でテナントのアプリケーションを調べると、次の 2 つの新しいエントリがあることがわかります。

    - *ClusterName*\_Cluster
    - *ClusterName*\_Client

    次のセクションでクラスターを作成するとき、スクリプトによって、Azure Resource Manager テンプレートが必要とする JSON が出力されます。このため、PowerShell ウィンドウは開いたままにしてください。

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Service Fabric クラスターの Resource Manager テンプレートを作成する

このセクションでは、前述の PowerShell コマンドの出力が、Service Fabric クラスターの Resource Manager テンプレートで使用されます。

サンプル Resource Manager テンプレートは、[GitHub の Azure クイック スタート テンプレート ギャラリー][azure-quickstart-templates]で入手できます。 これらのテンプレートは、クラスター テンプレートの作成を始める際に使用できます。 

### <a name="create-the-resource-manager-template"></a>Resource Manager テンプレートの作成

このガイドでは、セキュリティで保護された [5 ノード クラスター][service-fabric-secure-cluster-5-node-1-nodetype-wad]のテンプレート例とパラメーター例を使用します。 `azuredeploy.json` と `azuredeploy.parameters.json` をコンピューターにダウンロードして、任意のテキスト エディターで両方のファイルを開きます。

### <a name="add-certificates"></a>証明書の追加

証明書は、証明書キーを含む Key Vault を参照することによって、クラスターの Resource Manager テンプレートに追加されます。 Resource Manager テンプレート ファイルを再利用可能にし、デプロイ固有の値がない状態にするため、これらの Key Vault の値は Resource Manager テンプレートのパラメーター ファイルに配置することをお勧めします。

#### <a name="add-all-certificates-to-the-vmss-osprofile"></a>VMSS osProfile にすべての証明書を追加する

クラスターにインストールする必要があるすべての証明書は、VMSS リソース (Microsoft.Compute/virtualMachineScaleSets) の [osProfile] セクションで構成する必要があります。 これにより、リソース プロバイダーに対して VM に証明書をインストールするよう指示されます。 これには、クラスター証明書だけでなく、アプリケーションで使用する予定のあらゆるアプリケーションのセキュリティ証明書が含まれます。

```json
{
  "apiVersion": "2016-03-30",
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

#### <a name="configure-service-fabric-cluster-certificate"></a>Service Fabric クラスターの証明書を構成する

クラスターの認証証明書は、Service Fabric クラスター リソース (Microsoft.ServiceFabric/clusters) と、VMSS リソースの VMSS 用 Service Fabric 拡張機能でも構成する必要があります。 これにより、Service Fabric のリソース プロバイダーを使用してクラスターの認証および管理エンドポイントのサーバー認証用に構成できます。

##### <a name="vmss-resource:"></a>VMSS リソース:

```json
{
  "apiVersion": "2016-03-30",
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
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
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

##### <a name="service-fabric-resource:"></a>Service Fabric リソース:

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-aad-config"></a>AAD 構成の挿入

以前作成した AAD 構成は、直接 Resource Manager テンプレートに挿入できますが、Resource Manager テンプレートを再利用可能にし、デプロイ固有の値がない状態にするため、まず値をパラメーター ファイルに抽出することをお勧めします。

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
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

### <a name="<a-"configure-arm"-></a>configure-resource-manager-template-parameters"></a><a "configure-arm" ></a>Resource Manager テンプレート パラメーターの構成

最後に、Key Vault と AAD PowerShell コマンドからの出力値を使用してパラメーター ファイルを作成します。

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
この時点で、以下の設定が完了しています。

 - Key Vault リソース グループ
    - Key Vault
    - クラスター サーバー認証証明書
    - データ暗号化証明書
 - Azure Active Directory テナント 
    - Web ベースの管理用の AAD アプリケーションと Service Fabric Explorer
    - ネイティブ クライアント管理用の AAD アプリケーション
    - 役割を割り当て済みのユーザー 
 - Service Fabric クラスター Resource Manager テンプレート
    - Key Vault で構成された証明書
    - 構成済みの Azure Active Directory 

次の図は、Key Vault と AAD の構成を Resource Manager テンプレートに適合させる場所を示しています。

![Resource Manager の依存関係マップ][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>クラスターを作成する

これで、[ARM デプロイ][resource-group-template-deploy]を使用してクラスターを作成する準備ができました。

#### <a name="test-it"></a>テストする

次の PowerShell コマンドを使用して、パラメーター ファイルで Resource Manager テンプレートをテストします。

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>デプロイする

Resource Manager テンプレートのテストに合格した場合は、次の PowerShell コマンドを使用して、パラメーター ファイルで Resource Manager テンプレートをデプロイします。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>
## <a name="assign-users-to-roles"></a>ユーザーをロールに割り当てる

クラスターを表すアプリケーションを作成したら、Service Fabric によってサポートされるロール (read-only と admin) にユーザーを割り当てる必要があります。 これは、[Azure クラシック ポータル][azure-classic-portal]で実行できます。

1. テナントに移動し、[アプリケーション] を選択します。
2. `myTestCluster_Cluster`のような名前を持つ Web アプリケーションを選択します。
3. [ユーザー] タブをクリックします。
4. 割り当てるユーザーを選択し、画面の下部にある **[割り当て]** ボタンをクリックします。

    ![ユーザーをロールに割り当てるためのボタン][assign-users-to-roles-button]

5. ユーザーに割り当てるロールを選択します。

    ![ユーザーをロールに割り当てる][assign-users-to-roles-dialog]

>[AZURE.NOTE] Service Fabric でのロールの詳細については、「 [ロール ベースのアクセス制御 (Service Fabric クライアント用)](service-fabric-cluster-security-roles.md)」を参照してください。

 <a name="secure-linux-cluster"></a> 
##  <a name="create-secure-clusters-on-linux"></a>セキュリティで保護されたクラスターを Linux 上に作成する

必要なプロセスがわかりやすいように、ヘルパー スクリプトを [こちら](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux)に用意しました。 このヘルパー スクリプトを使用するためには、あらかじめ Azure CLI をインストールし、パスを通しておく必要があります。 ダウンロード後、必ず `chmod +x cert_helper.py` を実行して、スクリプトに実行権限を割り当ててください。 最初に、CLI から `azure login` コマンドを実行して Azure アカウントにログインします。 Azure アカウントへのログイン後、次のコマンドに示したように、証明機関の署名入りの証明書と一緒にヘルパー スクリプトを指定します。

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]

The -ifile parameter can take a .pfx or a .pem file as input, with the certificate type (pfx or pem, or ss if it is a self-signed cert).
The parameter -h prints out the help text.
```

コマンドの出力として次の 3 つの文字列が返されます。 

1. SourceVaultID: 作成された新しい KeyVault ResourceGroup の ID です。 

2. 証明書にアクセスするための CertificateUrl。

3. CertificateThumbprint: 認証に使用されます。


次の例は、コマンドの使用方法を示しています。

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
先ほどのコマンドを実行すると、次の 3 つの文字列が返されます。

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

 証明書の件名は Service Fabric クラスターへのアクセスに使用されるドメインと一致する必要があります。 これは、HTTPS 管理エンドポイントと Service Fabric Explorer 用の SSL を提供するために必要です。 証明機関 (CA) から `.cloudapp.azure.com` ドメインの SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書の件名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

「 [Resource Manager テンプレート パラメーターの構成](#configure-arm)」で取り上げた、セキュリティで保護された Service Fabric クラスターを (AAD なしで) 作成するためには、これらのエントリが必要となります。 セキュリティで保護されたクラスターには、 [クラスターに対するクライアント アクセスの認証](service-fabric-connect-to-secure-cluster.md)に関するページの手順に従って接続できます。 Linux プレビュー クラスターでは、AAD 認証がサポートされません。 「 [ユーザーをロールに割り当てる](#assign-roles)」セクションの説明に従って管理者ロールとクライアント ロールを割り当ててください。 Linux プレビュー クラスターの管理者ロールとクライアント ロールを指定するときは、サブジェクト名ではなく、認証に使用する証明書の拇印を指定する必要があります (このプレビュー リリースではチェーンの検証や失効が実行されないため)。


テスト用に自己署名証明書を使用したい場合は、同じスクリプトを使用して自己署名証明書を生成し、KeyVault にアップロードすることができます。その場合は、証明書パスと証明書名の代わりにフラグ `ss` を指定してください。 たとえば自己署名証明書を作成してアップロードするには、次のコマンドを使用します。

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net" 
```

このコマンドからは、先ほどと同じ 3 つの文字列 (SourceVault、CertificateUrl、CertificateThumbprint) が返されます。自己署名証明書が置かれている場所と共に、これらの文字列を使用することで、セキュリティで保護された Linux クラスターを作成します。 クラスターに接続するためには、自己署名証明書が必要となります。  セキュリティで保護されたクラスターには、 [クラスターに対するクライアント アクセスの認証](service-fabric-connect-to-secure-cluster.md)に関するページの手順に従って接続できます。 証明書の件名は Service Fabric クラスターへのアクセスに使用されるドメインと一致する必要があります。 これは、HTTPS 管理エンドポイントと Service Fabric Explorer 用の SSL を提供するために必要です。 証明機関 (CA) から `.cloudapp.azure.com` ドメインの SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書の件名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

ヘルパー スクリプトから得られるパラメーターは、「 [Azure Portal でのクラスターの作成](service-fabric-cluster-creation-via-portal.md#create-cluster-portal)」セクションの説明に従ってポータルに入力することができます。

## <a name="next-steps"></a>次のステップ

この時点で、管理認証を提供する Azure Active Directory で、セキュリティで保護されたクラスターの準備ができています。 次に、[クラスターに接続](service-fabric-connect-to-secure-cluster.md)して、[アプリケーション シークレットを管理](service-fabric-application-secret-management.md)する方法を説明します。

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>クライアント認証用の Azure Active Directory のセットアップのトラブルシューティングを行う

クライアント認証のために Azure Active Directory をセットアップしているときに問題が発生した場合、考えられる解決策として次の推奨事項を確認してください。

### <a name="service-fabric-explorer-prompts-for-selecting-certificate"></a>Service Fabric エクスプローラーに、証明書の選択を求めるメッセージが表示される

#### <a name="problem"></a>問題点

Service Fabric エクスプローラーで AAD ログイン ページへのログインに成功すると、ブラウザーはホーム ページに戻りますが、証明書の選択を求めるダイアログを表示します。

![SFX の証明書を選択するためのダイアログ][sfx-select-certificate-dialog]

#### <a name="reason"></a>理由

ユーザーが AAD クラスター アプリケーション内でロールを割り当てていません。 このため、Service Fabric クラスターで AAD 認証は失敗します。 Service Fabric エクスプローラーは、証明書認証にフォールバックします。

#### <a name="solution"></a>解決策

AAD のセットアップの手順に従って、ユーザー ロールを割り当てます。 また、`SetupApplications.ps1` の場合と同様に、“USER ASSIGNMENT REQUIRED TO ACCESS APP” をオンにすることをお勧めします。

### <a name="connect-with-powershell-fails-with-error:-the-specified-credentials-are-invalid"></a>PowerShell を使用した接続が失敗し、"指定した資格情報が無効です" というエラーが返される

#### <a name="problem"></a>問題点

"AzureActiveDirectory" セキュリティ モードで PowerShell を使用してクラスターに接続すると、AAD ログイン ページに正常にログインした後で接続に失敗し、"指定した資格情報が無効です" というエラーが返されます。

#### <a name="solution"></a>解決策

上記と同じです。

### <a name="service-fabric-explorer-signing-in-return-failure:-aadsts50011"></a>Service Fabric エクスプローラーのサインインでエラー (AADSTS50011) が返される

#### <a name="problem"></a>問題点

Service Fabric エクスプローラーで AAD サインイン ページにログオンした後で、サイン エラー (AADSTS50011) が返されます。応答アドレス &lt;url&gt; が、アプリケーションに対して構成された応答アドレス &lt;guid&gt; と一致していません。 

![SFX の応答アドレスが一致していない][sfx-reply-address-not-match]

#### <a name="reason"></a>理由

Service Fabric エクスプローラーに相当するクラスター (Web) アプリケーションは AAD に対する認証を試み、要求の一部として、戻り先のリダイレクト URL を指定しています。 しかし、AAD アプリケーションの "応答 URL" 一覧に表示されません。

#### <a name="solution"></a>解決策

Service Fabric エクスプローラーの URL をクラスター (Web) アプリケーションの [構成] タブの "応答 URL" に追加するか、またはリスト内の項目のいずれかを置換します。 保存します。

![Web アプリケーションの応答 URL][web-application-reply-url]

### <a name="can-i-reuse-the-same-aad-tenant-for-multiple-clusters?"></a>複数のクラスターに同じ AAD テナントを再利用できますか?

#### <a name="answer"></a>リダイレクト先

はい。 ただし、Service Fabric エクスプローラーの　URL をクラスター (Web) アプリケーションに追加することを忘れないでください。そうしないと、Service Fabric エクスプローラーは動作しません。

### <a name="why-do-i-still-need-server-certificate-while-aad-enabled?"></a>AAD が有効になっているときもサーバーの証明書が必要なのはどうしてですか?

#### <a name="answer"></a>リダイレクト先

FabricClient と FabricGateway では、相互認証が実行されます。 AAD 認証の場合、AAD 統合がクライアント ID をサーバーに提供し、サーバー証明書を使用してサーバー ID の確認が行われます。 Service Fabric での証明書のしくみの詳細については、[「X.509 証明書と Service Fabric」][x509-certificates-and-service-fabric] を参照してください。

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png


<!--HONumber=Oct16_HO2-->


