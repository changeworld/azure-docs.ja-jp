---
title: Azure Stack 統合システムの Azure 登録 | Microsoft Docs
description: マルチノード Azure Stack の Azure に接続されたデプロイのための Azure 登録プロセスについて説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: jeffgilb
ms.reviewer: brbartle
ms.openlocfilehash: e73cd24064f2b0d6197a69251b55639d41e3212c
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39357750"
---
# <a name="register-azure-stack-with-azure"></a>Azure を使用した Azure Stack の登録

[Azure Stack](azure-stack-poc.md) の Azure への登録により、Azure からマーケットプレース項目をダウンロードしたり、Microsoft に返送するコマース データを設定したりできます。 Azure Stack を登録した後は、Azure コマースに使用状況が報告され、登録に使用したサブスクリプションの下で確認できます。

> [!IMPORTANT]  
> マーケットプレース シンジケーションを含む、Azure Stack のすべての機能をサポートするには、登録が必要です。 さらに、従量制課金モデルを使用している場合、登録しないと、Azure Stack のライセンス条項違反になります。 Azure Stack のライセンス モデルに関する詳細は、[購入方法のページ](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)を参照してください。

## <a name="prerequisites"></a>前提条件

Azure を使用して Azure Stack を登録する前に、以下のものが必要です。

- Azure サブスクリプションのサブスクリプション ID。 ID を取得するには、Azure にサインインし、**[More services] (その他のサービス)** > **[サブスクリプション]** をクリックして、使用するサブスクリプションをクリックすると、**[要点]** の下にサブスクリプション ID が表示されます。

  > [!NOTE]
  > ドイツおよび米国政府機関向けのクラウド サブスクリプションは、現在サポートされていません。

- サブスクリプションの所有者であるアカウントのユーザー名とパスワード (MSA/2FA アカウントがサポートされます)。
- Azure Stack リソース プロバイダーを登録しました (詳細については、下の「Azure Stack リソース プロバイダーを登録する」セクションを参照してください)。

これらの要件を満たす Azure サブスクリプションがない場合は、[ここで無料の Azure アカウントを作成](https://azure.microsoft.com/free/?b=17.06)できます。 Azure Stack を登録しても、Azure サブスクリプションに課金されることはありません。

### <a name="powershell-language-mode"></a>PowerShell 言語モード

Azure Stack を正常に登録するには、PowerShell 言語モードを **FullLanguageMode** に設定する必要があります。  現在の言語モードが完全に設定されていることを確認するには、PowerShell ウィンドウを管理者特権で開き、次の PowerShell コマンドを実行します。

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

出力で **FullLanguageMode** が返されていることを確認します。 その他の言語モードが返されている場合は、別のマシン上で再登録を実行するか、言語モードを **FullLanguageMode** に設定してから作業を続行する必要があります。

### <a name="bkmk_powershell"></a>PowerShell for Azure Stack をインストールする

Azure に登録するには、最新の PowerShell for Azure Stack を使用する必要があります。

まだインストールされていない場合は、[PowerShell for Azure Stack をインストールします](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)。

### <a name="bkmk_tools"></a>Azure Stack ツールをダウンロードする

Azure Stack ツールの GitHub リポジトリには、Azure Stack 機能 (登録機能を含む) をサポートする PowerShell モジュールが含まれています。 登録プロセス中に、Azure Stack ツール リポジトリにある RegisterWithAzure.psm1 PowerShell モジュールをインポートおよび利用して、使用する Azure Stack インスタンスを Azure に登録する必要があります。

最新バージョンを使用していることを確認するには、Azure に登録する前に、既存のバージョンの Azure Stack ツールをすべて削除し、[GitHub から最新バージョンをダウンロードする](azure-stack-powershell-download.md)必要があります。

## <a name="register-azure-stack-in-connected-environments"></a>接続された環境で Azure Stack を登録する

接続された環境では、インターネットと Azure にアクセスできます。 これらの環境では、Azure Stack リソース プロバイダーを Azure に登録してから、課金モデルを構成する必要があります。

> [!NOTE]
> これらすべての手順は、特権エンドポイントにアクセスできるコンピューターから実行する必要があります。

### <a name="register-the-azure-stack-resource-provider"></a>Azure Stack リソース プロバイダーを登録する

Azure Stack リソース プロバイダーを Azure に登録するには、PowerShell ISE を管理者として起動し、**EnvironmentName** パラメーターに適切な Azure サブスクリプション タイプ (以下のパラメーターを参照) に設定して、次の PowerShell コマンドを使用します。

1. Azure Stack を登録するために使用する Azure アカウントを追加します。 アカウントを追加するには、**Add-AzureRmAccount** コマンドレットを実行します。 Azure グローバル管理者アカウント資格情報の入力を求められ、お使いのアカウントの構成によっては 2 要素認証を使用する必要があります。

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | パラメーター | [説明] |  
   |-----|-----|
   | EnvironmentName | Azure クラウド サブスクリプション環境名。 サポートされている環境名は **AzureCloud**、または中国の Azure サブスクリプションを使用している場合は **AzureChinaCloud** です。  |
   |  |  |

2. 複数のサブスクリプションがある場合は、次のコマンドを実行して、使用するものを選択します。  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Azure サブスクリプションで Azure Stack リソース プロバイダーを登録するには、次のコマンドを実行します。

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>従量制課金モデルを使用して Azure Stack を Azure に登録する

従量制課金モデルを使用して Azure Stack を Azure に登録するには、次の手順を使用します。

1. 管理者として PowerShell ISE を起動し、[Azure Stack ツールをダウンロードした](#bkmk_tools)ときに作成された **AzureStack-Tools-master** ディレクトリ内の **Registration** フォルダーに移動します。 PowerShell を使用して **RegisterWithAzure.psm1** モジュールをインポートします。

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. 次に、同じ PowerShell セッションで、正しい Azure PowerShell コンテキストにログインしていることを確認します。 これは、上記の Azure Stack リソース プロバイダーの登録に使用された Azure アカウントです。 実行する PowerShell:

   ```PowerShell  
   Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>"
   ```

3. 同じ PowerShell セッションで **Set-AzsRegistration** コマンドレットを実行します。 実行する PowerShell:  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```

  |パラメーター|[説明]|
  |-----|-----|
  |PrivilegedEndpointCredential|[特権エンドポイントへのアクセス](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)に使用する資格情報。 ユーザー名は、**AzureStackDomain\CloudAdmin** の形式です。|
  |PrivilegedEndpoint|ログ収集およびその他のデプロイ後タスクのような機能を提供する、あらかじめ構成されたリモート PowerShell コンソール。 詳細については、[特権エンドポイントの使用](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)の記事を参照してください。|
  |BillingModel|自分のサブスクリプションで使用する請求モデル。 このパラメーターに使用できる値は、Capacity、PayAsYouUse、および Development です。|
  | RegistrationName | 同じ Azure Subscription ID を利用し、複数の Azure Stack インスタンスで登録スクリプトを実行している場合、登録に一意の名前を設定します。 このパラメーターの既定値は **AzureStackRegistration** です。 ただし、複数の Azure Stack インスタンスに同じ名前を使用すると、スクリプトは失敗します。 |

  このプロセスには 10 - 15 分かかります。 コマンドが完了すると、**「Your environment is now registered and activated using the provided parameters. (提供されたパラメーターを使用して環境が登録され、アクティブ化されました。)」** というメッセージが表示されます。

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>容量課金モデルを使用して Azure Stack を Azure に登録する

従量制課金モデルを使用した登録に使用したのと同じ手順に従いますが、容量を購入したときの契約番号を追加し、**BillingModel** パラメーターを **Capacity** に変更します。 その他のすべてのパラメーターは変更ありません。

実行する PowerShell:

```PowerShell  
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
$RegistrationName = "<unique-registration-name>"
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity `
    -RegistrationName $RegistrationName
```

## <a name="register-azure-stack-in-disconnected-environments"></a>切断された環境で Azure Stack を登録する
切断された (インターネット接続のない) 環境で Azure Stack を登録している場合は、Azure Stack 環境から登録トークンを取得してから、Azure に接続でき、かつ [PowerShell for Azure Stack がインストールされた](#bkmk_powershell)コンピューター上でそのトークンを使用する必要があります。  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Azure Stack 環境から登録トークンを取得する

1. 管理者として PowerShell ISE を起動し、[Azure Stack ツールをダウンロードした](#bkmk_tools)ときに作成された **AzureStack-Tools-master** ディレクトリ内の **Registration** フォルダーに移動します。 **RegisterWithAzure.psm1** モジュールをインポートします。  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. 登録トークンを取得するには、次の PowerShell コマンドを実行します。  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!TIP]  
   > 登録トークンは、*$FilePathForRegistrationToken* に指定されたファイルに保存されます。 ファイル パスまたはファイル名は任意に変更できます。

3. 接続された Azure マシンで使用するためにこの登録トークンを保存します。 $FilePathForRegistrationToken からファイルまたはテキストをコピーできます。

### <a name="connect-to-azure-and-register"></a>Azure に接続して登録する

インターネットに接続されたコンピューターで同じ手順を実行し、RegisterWithAzure.psm1 モジュールをインポートし、正しい Azure PowerShell コンテキストにサインインします。 次に、Register-AzsEnvironment を呼び出します。 Azure に登録する登録トークンを指定します。 同じ Azure サブスクリプション ID を利用して複数の Azure Stack インスタンスを登録している場合、一意の登録名を指定します。 次のコマンドレットを実行します。

  ```PowerShell  
  $registrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  -RegistrationName $RegistrationName
  ```

オプションで、Get-Content コマンドレットを使用して、登録トークンが含まれているファイルを示すことができます。

  ```PowerShell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken -RegistrationName $RegistrationName
  ```

  > [!NOTE]  
  > 後で参照できるように登録リソース名および登録トークンを保存します。

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Azure の登録リソースからアクティブ化キーを取得する

次に、Register-AzsEnvironment 中に Azure で作成された登録リソースからアクティブ化キーを取得する必要があります。

アクティブ化キーを取得するには、次の PowerShell コマンドを実行します。  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!TIP]  
  > アクティブ化キーは *$KeyOutputFilePath*　に指定されたファイルに保存されます。 ファイル パスまたはファイル名は任意に変更できます。

### <a name="create-an-activation-resource-in-azure-stack"></a>Azure Stack にアクティブ化リソースを作成する

Get-AzsActivationKey から作成されたアクティブ化キーのファイルまたはテキストを使用して Azure Stack 環境に戻ります。 次に、アクティブ化キーを使用して Azure Stack にアクティブ化リソースを作成します。 アクティブ化リソースを作成するには、次の PowerShell コマンドを実行します。  

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

オプションで、Get-Content コマンドレットを使用して、登録トークンが含まれているファイルを示すことができます。

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Azure Stack の登録を検証する

Azure Stack が Azure に正常に登録されたことを検証するには、次の手順を使用します。

1. Azure Stack [管理者ポータル](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;//adminportal.*&lt;region>.&lt;fqdn>* にサインインします。
2. **[その他のサービス]** > **[Marketplace Management]\(Marketplace の管理\)** > **[Add from Azure]\(Azure から追加\)** の順に選択します。

Azure (WordPress など) から利用可能な項目のリストが表示される場合は、アクティブ化に成功しました。 ただし、切断された環境では、Azure Stack Marketplace に Azure Marketplace 項目は表示されません。

> [!NOTE]
> 登録が完了すると、登録されていないことを示すアクティブな警告は表示されなくなります。

## <a name="renew-or-change-registration"></a>登録を更新または変更する

### <a name="renew-or-change-registration-in-connected-environments"></a>接続された環境で登録を更新または変更する

次の状況では、登録を更新または変更する必要があります。

- 容量ベースの年単位サブスクリプションを更新した後。
- 課金モデルを変更したとき。
- 容量ベースの課金のために変更を調整したとき (ノードの追加/削除)。

#### <a name="change-the-subscription-you-use"></a>使用するサブスクリプションを変更する

使用するサブスクリプションを変更する場合は、まず **Remove-AzsRegistration** コマンドレットを実行してから、正しい Azure PowerShell コンテキストにログインしていることを確認し、最後に変更されたパラメーターを指定して **Set-AzsRegistration** を実行する必要があります。

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>課金モデルまたはシンジケーション機能を変更する

インストールの課金モデルまたはシンジケーション機能を変更する場合は、登録機能を呼び出して新しい値を設定できます。 最初に現在の登録を削除する必要はありません。

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>切断された環境で登録を更新または変更する

次の状況では、登録を更新または変更する必要があります。

- 容量ベースの年単位サブスクリプションを更新した後。
- 課金モデルを変更したとき。
- 容量ベースの課金のために変更を調整したとき (ノードの追加/削除)。

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Azure Stack からアクティブ化リソースを削除する

最初に Azure Stack からアクティブ化リソースを削除してから、Azure の登録リソースを削除する必要があります。  

Azure Stack のアクティブ化リソースを削除するには、Azure Stack 環境で次の PowerShell コマンドを実行します。  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

次に、Azure の登録リソースを削除するには、Azure に接続されたコンピューターを使用していることを確認して、正しい Azure PowerShell コンテキストにサインインし、以下の説明に従って、適切な PowerShell コマンドを実行します。

リソースの作成に使用した登録トークンを使用できます。  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

または登録名を使用することもできます。

  ```Powershell
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>切り離された手順を使用して再登録する

これで、接続が切断されたシナリオで登録が完全に解除されました。接続が切断されたシナリオで Azure Stack 環境を登録する手順を繰り返す必要があります。

## <a name="next-steps"></a>次の手順

[Azure から Marketplace の項目をダウンロードする](azure-stack-download-azure-marketplace-item.md)
