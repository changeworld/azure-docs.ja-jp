---
title: Active Directory フェデレーション サービス (AD FS) を使用して Azure Stack に Kubernetes をデプロイする | Microsoft Docs
description: Active Directory フェデレーション サービス (AD FS) を使用して Azure Stack に Kubernetes をデプロイする方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: a161d2223dacb618f1e2eeb6443fffae18cc7259
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354870"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>Active Directory フェデレーション サービスを使用して Azure Stack に Kubernetes をデプロイする

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

> [!Note]  
> Azure Stack 上の Kubernetes はプレビュー段階にあります。

この記事の手順に従えば、Kubernetes のリソースをデプロイおよび設定することができます。 これらの手順は、Active Directory フェデレーション サービス (AD FS) が ID 管理サービスである場合に使用します。

## <a name="prerequisites"></a>前提条件 

使用を開始するには、次の手順に従って、適切なアクセス許可を保持し、Azure Stack の準備が整っていることを確認します。

1. SSH 公開および秘密キー ペアを生成して Azure Stack 上の Linux VM にサインインします。 クラスターを作成するときは、公開キーが必要です。

    キーを生成する手順については、[SSH キーの生成](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation)に関するページを参照してください。

1. Azure Stack テナント ポータルに有効なサブスクリプションがあること、また、新しいアプリケーションの追加に使用できる十分なパブリック IP アドレスがあることを確認します。

    Azure Stack **Administrator** サブスクリプションにクラスターを展開することはできません。 **User** サブスクリプションを使用する必要があります。 

1. マーケットプレースに Kubernetes クラスターがない場合は、Azure Stack 管理者に連絡してください。

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

AD FS を ID ソリューションとして使用する場合は、Azure Stack 管理者と協力してサービス プリンシパルを設定する必要があります。 サービス プリンシパルは、アプリケーションに Azure Stack リソースへのアクセス権を付与します。

1. Azure Stack の管理者は、証明書とサービス プリンシパルの情報を提供します。 この情報は次のようになります。

    ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
    ```

2. 新しいサービス プリンシパルにサブスクリプションの共同作成者としてのロールを割り当てます。 手順については、「[ロールの割り当て](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal#assign-role-to-service-principal)」を参照してください。

3. デプロイの証明書を格納するためのキー コンテナーを作成します。

    - 次の情報が必要です。

        | 値 | 説明 |
        | ---   | ---         |
        | Azure Resource Manager エンドポイント | Microsoft Azure Resource Manager は、管理者が Azure リソースのデプロイ、管理、監視を行えるようにするための管理フレームワークです。 Azure Resource Manager では、これらのタスクを個別に処理するのではなく、グループとして単一の操作で処理することができます。<br>Azure Stack Development Kit (ASDK) のエンドポイント: `https://management.local.azurestack.external/`<br>統合システムのエンドポイント: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
        | サブスクリプション ID | [サブスクリプション ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) は Azure Stack 内のオファーにアクセスするために必要です。 |
        | ユーザー名 | ユーザー名。 |
        | リソース グループ名  | 新しいリソース グループの名前。または、既存のリソース グループを選択します。 リソース名は、英数字かつ小文字にする必要があります。 |
        | KeyVault 名 | コンテナーの名前。<br> 正規表現パターン: `^[a-zA-Z0-9-]{3,24}$` |
        | リソース グループの場所 | リソース グループの場所。 これは、Azure Stack のインストールに対して選択するリージョンです。 |

    - 管理者特権のプロンプトで PowerShell を開きます。 実際の値に更新されたパラメーターを使用して、次のスクリプトを実行します。

    ```PowerShell  
        $armEndpoint="<Azure Resource Manager Endpoint>"
        $subscriptionId="<Your Subscription ID>"
        $username="<your user name >"
        $resource_group_name = "<the resource group name >"
        $key_vault_name = "<keyvault name>"
        $resource_group_location="<resource group location>"
        
        # Login Azure Stack Environment
        Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
        $mycreds = Get-Credential
        Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
        
        # Create new Resource group and key vault
        New-AzureRmResourceGroup -Name $resource_group_name -Location $resource_group_location -Force
        
        # Note, Do not omit -EnabledForTemplateDeployment flag
        New-AzureRmKeyVault -VaultName $key_vault_name -ResourceGroupName $resource_group_name -Location local -EnabledForTemplateDeployment
        
        # Obtain the security identifier(SID) of the active directory user
        $adUser = Get-ADUser -Filter "Name -eq '$username'" -Credential $mycreds
        $objectSID = $adUser.SID.Value
        # Set the key vault access policy
        Set-AzureRmKeyVaultAccessPolicy -VaultName $key_vault_name -ResourceGroupName $resource_group_name -ObjectId $objectSID -BypassObjectIdValidation -PermissionsToKeys all -PermissionsToSecrets all
    ```

4. 証明書を Key Vault にアップロードします。

    - 次の情報が必要です。

        | 値 | 説明 |
        | ---   | ---         |
        | 証明書パス | FQDN または証明書へのファイル パス。 |
        | Certificate password | 証明書のパスワード。 |
        | シークレット名 | 前の手順で生成したシークレット。 |
        | KeyVault 名 | 前の手順で作成した KeyVault の名前。 |
        | Azure Resource Manager エンドポイント | Azure Stack Development Kit (ASDK) のエンドポイント: `https://management.local.azurestack.external/`<br>統合システムのエンドポイント: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
        | サブスクリプション ID | [サブスクリプション ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) は Azure Stack 内のオファーにアクセスするために必要です。 |

    - 管理者特権のプロンプトで PowerShell を開きます。 実際の値に更新されたパラメーターを使用して、次のスクリプトを実行します。

    ```PowerShell  
        
    # upload the pfx to key vault
    $tempPFXFilePath = "<certificate path>"
    $password = "<certificate password>"
    $keyVaultSecretName = "<secret name>"
    $keyVaultName = "<keyvault name>"
    $armEndpoint="<Azure Resource Manager Endpoint>"
    $subscriptionId="<Your Subscription ID>"
    # Login Azure Stack Environment
    Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
    $mycreds = Get-Credential
    Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
    
    $certContentInBytes = [io.file]::ReadAllBytes($tempPFXFilePath)
    $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    $jsonObject = @"
    {
    "data": "$pfxAsBase64EncodedString",
    "dataType" :"pfx",
    "password": "$password"
    }
    "@
    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
    ```

## <a name="deploy-kubernetes"></a>Kubernetes のデプロイ

1. [Azure Stack ポータル](https://portal.local.azurestack.external)を開きます。

1. **[+ リソースの作成]** > **[コンピューティング]** > **[Kubernetes クラスター]** を選択します。 **Create** をクリックしてください。

    ![ソリューション テンプレートのデプロイ](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1.基本

1. [Kubernetes クラスターを作成] で **[基本]** を選びます。

    ![ソリューション テンプレートのデプロイ](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. **[サブスクリプション]** を選択します。

1. 新しいリソース グループの名前を入力するか、既存のリソース グループを選択します。 リソース名は、英数字かつ小文字にする必要があります。

1. リソース グループの **[場所]** を選択します。 これは、Azure Stack のインストールに対して選択するリージョンです。

### <a name="2-kubernetes-cluster-settings"></a>2.Kubernetes クラスターの設定

1. [Kubernetes クラスターを作成] で **[Kubernetes Cluster Settings] (Kubernetes クラスターの設定)** を選択します。

    ![ソリューション テンプレートのデプロイ](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. **[Linux VM Admin Username]\(Linux VM 管理者ユーザー名\)** を入力します。 Kubernetes クラスターと DVM の一部である Linux Virtual Machines のユーザー名

1. Kubernetes クラスターと DVM の一部として作成されたすべての Linux マシンに対する承認に使用される **[SSH 公開キー]** を入力します。

1. リージョンで一意の **[Master Profile DNS Prefix]\(マスター プロファイル DNS プレフィックス\)** を入力します。 これは、`k8s-12345` など、リージョンで一意の名前になっている必要があります。 ベスト プラクティスとして、リソース グループ名と同じ名前を選択してみてください。

    > [!Note]  
    > 各クラスターに対して、新しい一意のマスター プロファイル DNS プレフィックスを使用してください。

1. **[Kubernetes Master Pool Profile Count] (Kubernetes マスター プール プロファイル数)** を選択します。 この数には、マスター プール内のノードの数が含まれます。 1 ～ 7 を使用できます。 この値は奇数である必要があります。

1. **[The VMSize of the Kubernetes master VMs] (Kubernetes マスター VM の VMSize)** を選択します。

1. **[Kubernetes Node Pool Profile Count] (Kubernetes ノード プール プロファイル数)** を選択します。 値には、クラスター内のエージェントの数が含まれます。 

1. **[Storage Profile] (ストレージ プロファイル)** を選択します。 **[Blob Disk] (Blob ディスク)** または **[Managed Disk] (マネージド ディスク)** を選択できます。 これにより、Kubernetes ノード VM の VM サイズを指定します。 

1. Azure Stack のインストールに対して、**Azure Stack の ID システム**用の **ADFS** を選択します。

1. **[Service Principal ClientId]\(サービス プリンシパル クライアント ID\)** を入力します。これは、Kubernetes Azure クラウド プロバイダーによって使用されます。 Azure Stack 管理者がサービス プリンシパルを作成したときにアプリケーション ID として識別されたクライアント ID。

1. **Key Vault リソース グループ**を入力します。 

1. **Key Vault 名**を入力します。

1. **Key Vault シークレット**を入力します。

1. **Kubernetes Azure クラウド プロバイダーのバージョン**を入力します。 これは、Kubernetes Azure プロバイダーのバージョンです。 Azure Stack は、各 Azure Stack バージョンに対してカスタムの Kubernetes ビルドをリリースします。

### <a name="3-summary"></a>手順 3.まとめ

1. [Summary] (概要) を選択します。 ブレードには、Kubernetes クラスターの構成設定の検証メッセージが表示されます。

    ![ソリューション テンプレートのデプロイ](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. 設定を確認します。

3. **[OK]** を選択してクラスターをデプロイします。

> [!TIP]  
>  デプロイに関して質問がある場合は、[Azure Stack フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)で質問を投稿するか、他の人が既に回答を受け取っていないか確認することができます。 

## <a name="next-steps"></a>次の手順

[クラスターへの接続](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)