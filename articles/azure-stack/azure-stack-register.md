---
title: "Azure Stack の登録 | Microsoft Docs"
description: "Azure サブスクリプションを使用して Azure Stack を登録します。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2018
ms.author: jeffgilb
ms.openlocfilehash: b58b3fc538d2237c12a860d268d550c4223155ba
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2018
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Azure サブスクリプションを使用した Azure Stack の登録

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

[Azure Stack](azure-stack-poc.md) を Azure に登録して Azure からマーケットプレース項目をダウンロードし、Microsoft への商取引データの事後レポートを設定できます。

> [!NOTE]
>登録によって、マーケットプレース シンジケーションや使用状況レポートなどの Azure Stack の重要な機能をテストできるようになるので、登録することをお勧めします。 Azure Stack を登録すると、使用状況が Azure コマースにレポートされます。 使用状況は、登録に使用したサブスクリプションの下に表示されます。 Azure Stack Development Kit のユーザーは、レポートする使用状況に対して課金されることはありません。


## <a name="get-azure-subscription"></a>Azure サブスクリプションの取得

Azure を使用して Azure Stack を登録する前に、以下のものが必要です。

- Azure サブスクリプションのサブスクリプション ID。 ID を取得する場合は、Azure にサインインし、**[More services]** (その他のサービス) > **[Subscriptions]** (サブスクリプション) をクリックして、使用するサブスクリプションをクリックすると、**[Essentials]** (Essentials) の下に **[Subscription ID]** (サブスクリプション ID) が表示されます。 中国、ドイツ、および米国政府機関向けのクラウド サブスクリプションは、現在サポートされていません。
- サブスクリプションの所有者であるアカウントのユーザー名とパスワード (MSA/2FA アカウントがサポートされます)。
- *Azure Stack 1712 更新バージョン (180106.1) からは必要ありません:* Azure サブスクリプション用の Azure Active Directory。 Azure でこのディレクトリを検索するには、Azure Portal の右上隅にあるアバターにポインターを合わせます。

これらの要件を満たす Azure サブスクリプションがない場合は、[ここで無料の Azure アカウントを作成](https://azure.microsoft.com/en-us/free/?b=17.06)できます。 Azure Stack を登録しても、Azure サブスクリプションに課金されることはありません。

## <a name="register-azure-stack-with-azure"></a>Azure を使用した Azure Stack の登録  
> [!NOTE]
> これらすべての手順は、特権エンドポイントにアクセスできるコンピューターから実行する必要があります。 Azure Stack Development Kit では、ホスト コンピューターがそれにあたります。 統合システムを使用している場合は、Azure Stack のオペレーターに問い合わせてください。
>

1. PowerShell コンソールを管理者として開き、[PowerShell for Azure Stack をインストール](azure-stack-powershell-install.md)します。  

2. Azure Stack を登録するために使用する Azure アカウントを追加します。 アカウントを追加するには、EnvironmentName パラメーターを **AzureCloud** に設定して `Add-AzureRmAccount` コマンドレットを実行します。 Azure アカウント資格情報の入力を求められ、お使いのアカウントの構成によっては 2 要素認証を使用する必要があります。

   ```Powershell
   Add-AzureRmAccount -EnvironmentName "AzureCloud"
   ```

3. 複数のサブスクリプションがある場合は、次のコマンドを実行して、使用するものを選択します。  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Azure サブスクリプションで Azure Stack リソース プロバイダーを登録するには、次のコマンドを実行します。

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 登録に対応する PowerShell モジュールの既存のバージョンをすべて削除し、[GitHub からその最新バージョンをダウンロードします](azure-stack-powershell-download.md)。  

6. 前の手順で作成された "AzureStack-Tools-master" ディレクトリから、"Registration" フォルダーに移動し、".\RegisterWithAzure.psm1" モジュールをインポートします。  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

7. 同じ PowerShell セッションで、次のスクリプトを実行します。資格情報の入力を求められたら、`azurestack\cloudadmin` をユーザーとして指定します。パスワードは、デプロイ中にローカル管理者用に使用したものと同じです。  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Set-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development
   ```

   | パラメーター | [説明] |  
   |--------|-------------|
   | CloudAdminCredential | [特権エンドポイントへのアクセス](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)に使用するクラウド ドメイン資格情報。 ユーザー名は、**\<Azure Stack ドメイン\>\cloudadmin** の形式です。 開発キットの場合、ユーザー名は **azurestack\cloudadmin** に設定されます。 統合システムを使用している場合は、Azure Stack のオペレーターに問い合わせてこの値を入手してください。|  
   | PrivilegedEndpoint | ログ収集およびその他のデプロイ後タスクのような機能を提供する、あらかじめ構成されたリモート PowerShell コンソール。 開発キットでは、特権エンドポイントは "AzS-ERCS01" 仮想マシン上にホストされます。 統合システムを使用している場合は、Azure Stack のオペレーターに問い合わせてこの値を入手してください。 詳細については、[特権エンドポイントの使用](azure-stack-privileged-endpoint.md)の記事を参照してください。|  
   | BillingModel | 自分のサブスクリプションで使用する請求モデル。 このパラメーターに使用できる値は、**Capacity**、**PayAsYouUse**、および **Development** です。 開発キットの場合、この値は **Development** に設定されます。 統合システムを使用している場合は、Azure Stack のオペレーターに問い合わせてこの値を入手してください。 |

8. スクリプトが完了したら、"Azure Stack をアクティブ化しています (この手順は完了までに最大 10 分ほどかかります)" というメッセージが表示されます。




## <a name="verify-the-registration"></a>登録の確認  

1. 管理者ポータル (https://adminportal.local.azurestack.external) にサインインします。

2. **[More Services]** (その他のサービス) > **[Marketplace Management]** (Marketplace の管理) > **[Add from Azure]** (Azure から追加) をクリックします。

3. Azure (WordPress など) から利用可能な項目のリストが表示される場合は、アクティブ化に成功しました。

> [!NOTE]
> 登録が完了すると、登録されていないことを示すアクティブな警告は表示されなくなります。


## <a name="modify-the-registration"></a>登録を変更する

### <a name="change-the-subscription-you-use"></a>使用するサブスクリプションを変更する
使用するサブスクリプションを変更する場合は、まず Remove-AzsRegistration を実行し、正しい Azure PowerShell コンテキストにログインしていることを確認してから、変更されたパラメーターを指定して Set-AzsRegistration を実行する必要があります。

  ```Powershell   
  Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```
### <a name="change-the-billing-model-or-syndication-features"></a>課金モデルまたはシンジケーション機能を変更する
インストールの課金モデルまたはシンジケーション機能を変更する場合は、登録機能を呼び出して新しい値を設定できます。 最初に現在の登録を削除する必要はありません。  

  ```Powershell
     Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```


## <a name="disconnected-registration"></a>切断された登録
*このセクションの情報は Azure Stack 1712 更新バージョン (180106.1) 以降に適用され、それ以前のバージョンではサポートされていません。*

切断された環境で Azure Stack を登録している場合は、Azure Stack 環境から登録トークンを取得してから、登録のために Azure に接続できるマシン上でそのトークンを使用する必要があります。  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Azure Stack 環境から登録トークンを取得する
  1. 登録トークンを取得するには、次を実行します。  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > 登録トークンは、*$env:SystemDrive\RegistrationToken.txt* として指定されたファイルに保存されます。

  2. 接続された Azure マシンで使用するためにこの登録トークンを保存します。


### <a name="connect-to-azure-and-register"></a>Azure に接続して登録する
この手順は、Azure に接続できるマシン上で実行します。

  1. [GitHub から登録に対応する最新の PowerShell モジュールをダウンロードします](azure-stack-powershell-download.md)。  

  2. 前の手順で作成された "AzureStack-Tools-master" ディレクトリから、"Registration" フォルダーに移動し、".\RegisterWithAzure.psm1" モジュールをインポートします。  

     ```powershell
     Import-Module .\RegisterWithAzure.psm1
     ```

  3. [RegisterWithAzure.psm1](https://go.microsoft.com/fwlink/?linkid=842959) をフォルダー (*C:\Temp* など) にコピーします。

  4. 管理者として PowerShell ISE を起動してから、RegisterWithAzure モジュールをインポートします。  

  5. Azure Stack 環境を登録するために使用する正しい Azure PowerShell コンテキストにログインしていることを確認します。  

     ```Powershell
        Set-AzureRmContext -SubscriptionId $YourAzureSubscriptionId   
     ```
  6. Azure に登録する登録トークンを指定します。

     ```Powershell  
       $registrationToken = "*Your Registration Token*"
       Register-AzsEnvironment -RegistrationToken $registrationToken  
     ```
    オプションで、Get-Content コマンドレットを使用して、登録トークンが含まれているファイルを示すことができます。

    ```Powershell  
       $registrationToken = Get-Content -Path 'C:\Temp\<Registration Token File>'
       Register-AzsEnvironment -RegistrationToken $registrationToken  
    ```
> [!NOTE]  
> 将来の参照のために登録リソース名または登録トークンを保存します。

### <a name="remove-a-registered-resource"></a>登録されたリソースを削除する
登録リソースを削除する場合は、UnRegister-AzsEnvironment を使用し、Register-AzsEnvironment に使用した登録リソース名または登録トークンのどちらかを渡す必要があります。
- **登録リソース名:**

  ```Powershell    
     UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
  ```
- **登録トークン:**    

  ```Powershell
     $registrationToken = "*Your copied registration token*"
     UnRegister-AzsEnvironment -RegistrationToken $registrationToken
   ```


## <a name="next-steps"></a>次の手順
[Azure Stack への接続](azure-stack-connect-azure-stack.md)
