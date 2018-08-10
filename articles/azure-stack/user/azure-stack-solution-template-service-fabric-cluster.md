---
title: セキュリティで保護された Service Fabric クラスターを Azure Stack にデプロイする | Microsoft Docs
description: セキュリティで保護された Service Fabric クラスターを Azure Stack にデプロイする方法について説明します
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: mattbriggs
ms.reviewer: shnatara
ms.openlocfilehash: 9feb2e538d3578fe259aa3fbc693a1e953f2f894
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440353"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Service Fabric クラスターを Azure Stack にデプロイする

セキュリティで保護された Service Fabric クラスターを Azure Stack にデプロイするには、Azure Marketplace の **[Service Fabric クラスター]** 項目を使用します。 

Service Fabric を使った作業の詳細については、Azure のドキュメントで「[Azure Service Fabric の概要](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)」および「[Service Fabric クラスターのセキュリティに関するシナリオ](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)」を参照してください。

## <a name="prerequisites"></a>前提条件

Service Fabric クラスターをデプロイするには、次のものが必要です。
1. **クラスター証明書**  
   これは、Service Fabric のデプロイ時に KeyVault に追加する X.509 サーバー証明書です。 
   - この証明書の **CN** が、作成する Service Fabric クラスターの完全修飾ドメイン名 (FQDN) と一致している必要があります。 
   - 公開キーと秘密キーの両方が必要なため、証明書の形式は PFX である必要があります。 
   このサーバー側証明書の作成に関する[要件](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)を参照してください。

    > [!NOTE]  
    > テスト用途であれば、x.509 サーバー証明書の代わりに自己署名証明書を使用することができます。 自己署名証明書は、クラスターの FQDN と一致している必要はありません。

1.  **管理用クライアント証明書** これは、Service Fabric クラスターに対して認証を行うクライアントが使用する証明書で、自己署名されたものでもかまいません。 このクライアント証明書の作成に関する[要件](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)を参照してください。

1.  **Azure Stack Marketplace に次の項目が用意されている必要があります。**
     - **Windows Server 2016** - テンプレートで Windows Server 2016 イメージを使用してクラスターを作成します。  
     - **カスタム スクリプト拡張機能** - Microsoft の仮想マシン拡張機能。  
     - **PowerShell Desired State Configuration** - Microsoft の仮想マシン拡張機能。


## <a name="add-a-secret-to-key-vault"></a>Key Vault にシークレットを追加する
Service Fabric クラスターをデプロイするには、Service Fabric クラスターの正しい KeyVault *Secret Identifier* または URL を指定する必要があります。 Azure Resource Manager テンプレートは、Service Fabric クラスターのインストール時に、KeyVault を入力として受け取ってクラスター証明書を取得します。 

> [!IMPORTANT]  
> Service Fabric で使用するシークレットは、PowerShell を使用して KeyVault に追加する必要があります。 ポータルは使用しないでください。  

KeyVault を作成してそこに "*クラスター証明書*" を追加するには、以下のスクリプトを使用します  (「[前提条件](#prerequisites)」を参照してください)。このスクリプトを実行する前に、サンプル スクリプトを確認し、前述のパラメーターを実際の環境に合わせて更新してください。 また、このスクリプトでは、Azure Resource Manager テンプレートに渡す必要のある値が出力されます。 

> [!TIP]  
> このスクリプトを正常に実行するためには、Compute、Network、Storage、KeyVault の各サービスを含んだパブリック オファーが必要です。 

  ```PowerShell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


詳細については、[PowerShell を使用した Azure Stack での KeyVault の管理](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-kv-manage-powershell)に関するページを参照してください。

## <a name="deploy-the-marketplace-item"></a>Marketplace 項目のデプロイ

1. ユーザー ポータルで **[新規]** > **[Compute]** > **[Service Fabric クラスター]** の順に移動します。 

   ![[Service Fabric クラスター] を選択](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

1. *[基本]* をはじめとする各ページのデプロイ フォームに必要事項を入力します。 指定すべき値がわからない場合は、既定値を使用してください。 **[OK]** を選択して次のページに進みます。

   ![基本](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

1. *[ネットワーク設定]* ページでは、アプリケーション用に開放するポートを具体的に指定できます。

   ![ネットワーク設定](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

1. *[セキュリティ]* ページには、[Azure KeyVault の作成](#add-a-secret-to-key-vault)とシークレットのアップロードによって得られた値を追加します。

   *[Admin Client Certificate Thumbprint]\(管理用クライアント証明書の拇印\)* には、"*管理用クライアント証明書*" の拇印を入力します  ([前提条件](#prerequisites)を参照してください)。
   
   - [ソース Key Vault]: スクリプトの実行結果から得られる *keyVault id* 文字列全体を指定します。 
   - (クラスター証明書 URL\): スクリプトの実行結果から得られる *Secret Id* の URL 全体を指定します。 
   - [クラスター証明書の拇印]: スクリプトの実行結果から得られる "*クラスター証明書の拇印*" を指定します。
   - [Admin Client Certificate Thumbprints]\(管理用クライアント証明書の拇印\): 「前提条件」で作成した "*管理用クライアント証明書の拇印*" を指定します。 

   ![スクリプト出力](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![セキュリティ](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

1. ウィザードを完了し、**[作成]** を選択して Service Fabric クラスターをデプロイします。



## <a name="access-the-service-fabric-cluster"></a>Service Fabric クラスターへのアクセス
Service Fabric クラスターには、Service Fabric Explorer または Service Fabric PowerShell を使用してアクセスできます。


### <a name="use-service-fabric-explorer"></a>Service Fabric Explorer の使用
1.  Web ブラウザーが管理用クライアント証明書にアクセスできること、また Service Fabric クラスターに対して認証を行えることを確認します。  

    a. Internet Explorer を開いて **[インターネット オプション]** > **[コンテンツ]** > **[証明書]** の順に移動します。
  
    b. [証明書] の **[インポート]** を選択して "*証明書のインポート ウィザード*" を開始し、**[次へ]** をクリックします。 *[インポートするファイル]* ページの **[参照]** をクリックして、Azure Resource Manager テンプレートに指定した**管理用クライアント証明書**を選択します。
        
       > [!NOTE]  
       > この証明書は、前もって KeyVault に追加しておいたクラスター証明書とは異なります。  

    c. エクスプローラー ウィンドウの拡張子ドロップダウンで [個人情報の交換] が選択されていることを確認してください。  

       ![個人情報の交換](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. *[証明書ストア]* ページの **[個人用]** を選択してウィザードを完了します。  
       ![証明書ストア](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. Service Fabric クラスターの FQDN を特定するには、次の手順を実行します。  

    a. Service Fabric クラスターに関連付けられているリソース グループに移動し、*[パブリック IP アドレス]* リソースを探します。 パブリック IP アドレスに関連付けられているオブジェクトを選択し、*[パブリック IP アドレス]* ブレードを開きます。  

      ![パブリック IP アドレス](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. [パブリック IP アドレス] ブレードに *[DNS 名]* として FQDN が表示されます。  

      ![DNS name](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. Service Fabric Explorer の URL とクライアント接続のエンドポイントを見つけるには、Template deployment の結果を確認します。

1. お使いのブラウザーで https://*FQDN*:19080 にアクセスします。 *FQDN* の部分は、手順 2. で調べた実際の Service Fabric クラスターの FQDN に置き換えてください。   
   自己署名証明書を使用した場合は、接続のセキュリティが確保されていないという警告が表示されます。 Web サイトに進むには、**[詳細表示]** を選択して **[Go on to the webpage]\(Web ページに進む\)** を選択してください。 

1. サイトに対する認証を行うには、使用する証明書を選択する必要があります。 **[More choices]\(その他の選択肢\)** を選択して適切な証明書を選び、**[OK]** をクリックして Service Fabric Explorer に接続します。 

   ![認証](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Service Fabric PowerShell の使用

1. Azure Service Fabric ドキュメントの「[Windows で開発環境を準備する](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools)」から *Microsoft Azure Service Fabric SDK* をインストールします。  

1. インストールが完了したら、Service Fabric のコマンドレットに PowerShell から確実にアクセスできるようシステム環境変数を構成します。  
    
    a. **[コントロール パネル]** > **[システムとセキュリティ]** > **[システム]** に移動し、**[システムの詳細設定]** を選択します。  
    
      ![コントロール パネル](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. *[システムのプロパティ]* の **[詳細設定]** タブで **[環境変数]** を選択します。  

    c. *[システム環境変数]* の **[Path]** を編集し、**C:\\Program Files\\Microsoft Service Fabric\\bin\\Fabric\\Fabric.Code** が一連の環境変数の先頭にあることを確認します。  

      ![環境変数リスト](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. 環境変数の順序を変更したら、PowerShell を再起動し、次の PowerShell スクリプトを実行して Service Fabric クラスターにアクセスします。

   ````PowerShell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ````
   
   > [!NOTE]  
   > スクリプトでは、クラスター名の前に *https://* を付けません。 ポート 19000 は必須です。
 
