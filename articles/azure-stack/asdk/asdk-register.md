---
title: ASDK の Azure への登録 | Microsoft Docs
description: Azure Stack を Azure に登録してマーケットプレース シンジケーションと使用状況レポートを有効にする方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 00c4d750d0617d36ab476719ce31c8038065511c
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807212"
---
# <a name="azure-stack-registration"></a>Azure Stack の登録
Azure Stack Development Kit (ASDK) インストールを Azure に登録して Azure からマーケットプレース項目をダウンロードしたり、Microsoft に返送するコマース データを設定したりできます。 マーケットプレース シンジケーションを含む、Azure Stack のすべての機能をサポートするには、登録が必要です。 登録によって、マーケットプレース シンジケーションや使用状況レポートなどの Azure Stack の重要な機能をテストできるようになるので、登録することをお勧めします。 Azure Stack を登録すると、使用状況が Azure コマースにレポートされます。 使用状況は、登録に使用したサブスクリプションの下に表示されます。 ただし、ASDK のユーザーは、レポートする使用状況に対して課金されることはありません。

自分の ASDK を登録しない場合、Azure Stack Development Kit を登録するように勧める警告アラート "**アクティブ化が必要**" が表示されます。 これは正しい動作です。

## <a name="prerequisites"></a>前提条件
次の手順を使って Azure に ASDK を登録する前に、[デプロイ後の構成](asdk-post-deploy.md)の記事の説明に従って Azure Stack PowerShell をインストールし、Azure Stack ツールをダウンロードしたことを確認します。

さらに、Azure に ASDK を登録するために使用されるコンピューター上で、PowerShell 言語モードを **FullLanguageMode** に設定する必要があります。 現在の言語モードが完全に設定されていることを確認するには、PowerShell ウィンドウを管理者特権で開き、次の PowerShell コマンドを実行します。

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

出力で **FullLanguageMode** が返されていることを確認します。 その他の言語モードが返されている場合は、別のコンピューター上で再登録を実行するか、言語モードを **FullLanguageMode** に設定してから作業を続行する必要があります。

## <a name="register-azure-stack-with-azure"></a>Azure を使用した Azure Stack の登録
次の手順に従って、ASDK を Azure に登録します。

> [!NOTE]
> これらすべての手順は、特権エンドポイントにアクセスできるコンピューターから実行する必要があります。 ASDK の場合は、開発キットのホスト コンピューターです。

1. 管理者として PowerShell コンソールを開きます。  

2. 次の PowerShell コマンドを実行し、Azure に ASDK インストールを登録します。 Azure サブスクリプションとローカル ASDK インストールの両方にサインインする必要があります。 Azure サブスクリプションをまだお持ちでない場合は、[こちらから無料の Azure アカウントを作成](https://azure.microsoft.com/free/?b=17.06)できます。 Azure Stack を登録しても、Azure サブスクリプションに課金されることはありません。<br><br>同じ Azure Subscription ID を利用し、複数の Azure Stack インスタンスで登録スクリプトを実行している場合、**Set-AzsRegistration** コマンドレットを実行するとき、登録に一意の名前を設定します。 **RegistrationName** パラメーターの既定値は **AzureStackRegistration** です。 ただし、複数の Azure Stack インスタンスに同じ名前を使用すると、スクリプトは失敗します。

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    # Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```
3. スクリプトが完了すると、**「Your environment is now registered and activated using the provided parameters. (提供されたパラメーターを使用して環境が登録され、アクティブ化されました。)」** というメッセージが表示されます。

    ![ご利用の環境がこれで登録されました](media/asdk-register/1.PNG)


## <a name="register-in-disconnected-environments"></a>切断された環境での登録
切断された (インターネット接続のない) 環境で Azure Stack を登録している場合は、Azure Stack 環境から登録トークンを取得してから、Azure に接続し、ASDK 環境のアクティベーション リソースを作成できるコンピューターにそのトークンを使用する必要があります。
 
 > [!IMPORTANT]
 > このような手順を使用して Azure Stack を登録する前に、[展開後の構成](asdk-post-deploy.md)の記事に従って、ASDK ホスト コンピューターと、Azure とレジスタへの接続に使用されるインターネット アクセスを備えたコンピューターの両方に PowerShell for Azure Stack をインストールし、Azure Stack ツールをダウンロードしておきます。

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Azure Stack 環境から登録トークンを取得する
ASDK ホスト コンピューター上で、管理者として PowerShell を起動し、Azure Stack ツールをダウンロードしたときに作成された **AzureStack-Tools-master** ディレクトリ内の **Registration** フォルダーに移動します。 次の PowerShell コマンドを使用して **RegisterWithAzure.psm1** モジュールをインポートし、**Get-AzsRegistrationToken** コマンドレットを使用して登録トークンを取得します。  

   ```PowerShell  
   # Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Create registration token
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   # File path to save the token. This example saves the file as C:\RegistrationToken.txt.
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```

インターネット接続されたコンピューターで使用するためにこの登録トークンを保存します。 $FilePathForRegistrationToken パラメーターによって作成されたファイルからファイルまたはテキストをコピーできます。

### <a name="connect-to-azure-and-register"></a>Azure に接続して登録する
インターネット接続されたコンピューターで、次の PowerShell コマンドを使用して **RegisterWithAzure.psm1** モジュールをインポートしてから、作成した登録トークンと一意の登録名で **Register-AzsEnvironment** コマンドレットを使用して Azure に登録します。  

  ```PowerShell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

または、**Get-Content** コマンドレットを使用して、登録トークンが含まれているファイルを示すことができます。

  ```PowerShell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

登録が完了したら、**「Your Azure Stack environment is now registered with Azure. (Azure Stack 環境が Azure に登録されました。)」** といったメッセージが表示されます。

> [!IMPORTANT]
> PowerShell ウィンドウを閉じないでください。 

今後参照できるように登録トークンと登録リソース名を保存します。

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Azure の登録リソースからアクティブ化キーを取得する

この場合もインターネット接続されたコンピューター、**および同じ PowerShell コンソール ウィンドウ**を使用して、Azure に登録したときに作成された登録リソースからアクティブ化キーを取得します。

アクティブ化キーを取得するには、次の PowerShell コマンドを実行し、前の手順で Azure に登録するときに指定したものと同じ一意の登録名の値を使用します。  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```
### <a name="create-an-activation-resource-in-azure-stack"></a>Azure Stack にアクティブ化リソースを作成する

**Get-AzsActivationKey** から作成されたアクティブ化キーのファイルまたはテキストを使用して Azure Stack 環境に戻ります。 次の PowerShell コマンドを実行して、そのアクティブ化キーを使用して Azure Stack でアクティブ化リソースを作成します。   

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

または、**Get-Content** コマンドレットを使用して、登録トークンが含まれているファイルを示すことができます。

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

アクティブ化が完了したら、**「Your environment has finished the registration and activation process. (環境で登録とアクティブ化プロセスが終了しました。)」** といったメッセージが表示されます。

## <a name="verify-the-registration-was-successful"></a>登録が成功したことを確認する

Azure Stack の登録に成功したことは、**[Region management]\(リージョン管理\)** タイルを使用して確認できます。 このタイルは、管理者ポータルの既定のダッシュボードにあります。

1. [Azure Stack 管理ポータル](https://adminportal.local.azurestack.external)にサインインします。

2. ダッシュボードで、**[Region management]\(リージョン管理\)** を選択します。

    [ ![[Region management]\(リージョン管理\) タイル](media/asdk-register/admin1sm.png "[Region management]\(リージョン管理\) タイル") ](media/asdk-register/admin1.png#lightbox)

3. **[プロパティ]** を選択します。 このブレードには、環境の状態と詳細が表示されます。 **[登録済み]** 状態と **[未登録]** 状態とがあります。 登録済みである場合は、Azure Stack の登録に使用した Azure サブスクリプション ID が、登録のリソース グループおよび名前と共に表示されます。

## <a name="move-a-registration-resource"></a>登録リソースを移動する
同じサブスクリプションのリソース グループ間で登録リソースを移動する操作は、**サポートされています**。 新しいリソース グループへのリソースの移動については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)」を参照してください。


## <a name="next-steps"></a>次の手順

- [Azure Stack マーケットプレース項目を追加する](../azure-stack-marketplace.md)
