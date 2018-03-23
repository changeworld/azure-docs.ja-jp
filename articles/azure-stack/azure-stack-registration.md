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
ms.date: 02/27/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 27bd44f936e19890526c0834e14084647dcec086
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2018
---
# <a name="register-azure-stack-with-azure"></a>Azure を使用した Azure Stack の登録
Azure Stack を Azure に登録して Azure からマーケットプレース項目をダウンロードしたり、Microsoft に返送するコマース データを設定したりできます。 Azure Stack を登録すると、使用状況が Azure コマースにレポートされます。 使用状況は、登録に使用したサブスクリプションの下に表示されます。

> [!IMPORTANT]
> 従量制課金モデルを選択した場合は、登録が必須です。 そうしないと、使用状況はそれ以外の方法で報告されないため、Azure Stack デプロイのライセンス条項に違反します。

## <a name="before-you-register-azure-stack-with-azure"></a>Azure Stack を Azure に登録する前に
Azure を使用して Azure Stack を登録する前に、以下のものが必要です。

- Azure サブスクリプションのサブスクリプション ID。 ID を取得するには、Azure にサインインし、**[More services] (その他のサービス)** > **[サブスクリプション]** をクリックして、使用するサブスクリプションをクリックすると、**[要点]** の下にサブスクリプション ID が表示されます。 

  > [!NOTE]
  > 中国、ドイツ、および米国政府機関向けのクラウド サブスクリプションは、現在サポートされていません。 

- サブスクリプションの所有者であるアカウントのユーザー名とパスワード (MSA/2FA アカウントがサポートされます)
- *Azure Stack 1712 更新バージョン (180106.1) からは必要ありません*: Azure サブスクリプション用の Azure AD。 Azure でこのディレクトリを検索するには、Azure Portal の右上隅にあるアバターにポインターを合わせます。 
- Azure Stack リソース プロバイダーを登録しました (詳細については、下の「Azure Stack リソース プロバイダーを登録する」セクションを参照してください)

これらの要件を満たす Azure サブスクリプションがない場合は、[ここで無料の Azure アカウントを作成](https://azure.microsoft.com/free/?b=17.06)できます。 Azure Stack を登録しても、Azure サブスクリプションに課金されることはありません。

### <a name="bkmk_powershell"></a>PowerShell for Azure Stack をインストールする
システムを Azure に登録するには、最新の PowerShell for Azure Stack を使用する必要があります。

まだインストールされていない場合は、[PowerShell for Azure Stack をインストールします](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)。 

### <a name="bkmk_tools"></a>Azure Stack ツールをダウンロードする
Azure Stack ツールの GitHub リポジトリには、Azure Stack 機能 (登録機能を含む) をサポートする PowerShell モジュールが含まれています。 登録プロセス中に、Azure Stack ツール リポジトリにある RegisterWithAzure.psm1 PowerShell モジュールをインポートおよび使用して、Azure Stack インスタンスを Azure に登録する必要があります。 

```powershell
# Change directory to the root directory. 
cd \

# Download the tools archive.
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
  expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
  cd AzureStack-Tools-master
```

## <a name="register-azure-stack-in-connected-environments"></a>接続された環境で Azure Stack を登録する
接続された環境では、インターネットと Azure にアクセスできます。 これらの環境では、Azure Stack リソース プロバイダーを Azure に登録してから、課金モデルを構成する必要があります。

### <a name="register-the-azure-stack-resource-provider"></a>Azure Stack リソース プロバイダーを登録する
Azure Stack リソース プロバイダーを Azure に登録するには、管理者として Powershell ISE を起動し、次の PowerShell コマンドを使用します。 これらのコマンドは、次のことを行います。
- ユーザーに、使用する Azure サブスクリプションの所有者としてログインし、`EnvironmentName` パラメーターを **AzureCloud** に設定するよう求めます。
- Azure リソース プロバイダー **Microsoft.AzureStack** を登録します。

実行する PowerShell:

```powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack 
```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>従量制課金モデルを使用して Azure Stack を Azure に登録する
従量制課金モデルを使用して Azure Stack を Azure に登録するには、次の手順を使用します。

管理者として PowerShell ISE を起動し、[Azure Stack ツールをダウンロードした](#bkmk_tools)ときに作成された **AzureStack-Tools-master** ディレクトリ内の **Registration** フォルダーに移動します。 PowerShell を使用して **RegisterWithAzure.psm1** モジュールをインポートします。 

実行する PowerShell:

```powershell
Import-Module .\RegisterWithAzure.psm1
```
次に、同じ PowerShell セッションで **Set-AzsRegistration** コマンドレットを実行します。 資格情報の入力を求められたら、Azure サブスクリプションの所有者を指定します。  

実行する PowerShell:

```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -BillingModel PayAsYouUse
```

|パラメーター|[説明]|
|-----|-----|
|CloudAdminCredential|Azure サブスクリプションの所有者の資格情報 (ユーザー名とパスワード) を含む PowerShell オブジェクト。|
|PrivilegedEndpoint|ログ収集およびその他のデプロイ後タスクのような機能を提供する、あらかじめ構成されたリモート PowerShell コンソール。 詳細については、[特権エンドポイントの使用](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint#access-the-privileged-endpoint)の記事を参照してください。|
|BillingModel|自分のサブスクリプションで使用する請求モデル。 このパラメーターに使用できる値は、Capacity、PayAsYouUse、および Development です。|

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>容量課金モデルを使用して Azure Stack を Azure に登録する
従量制課金モデルを使用した登録に使用したのと同じ手順に従いますが、容量を購入したときの契約番号を追加し、`BillingModel` パラメーターを **Capacity** に変更します。 その他のすべてのパラメーターは変更ありません。

実行する PowerShell:
```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>切断された環境で Azure Stack を登録する 
*このセクションの情報は Azure Stack 1712 更新バージョン (180106.1) 以降に適用され、それ以前のバージョンではサポートされていません。*

切断された (インターネット接続のない) 環境で Azure Stack を登録している場合は、Azure Stack 環境から登録トークンを取得してから、Azure に接続でき、かつ [PowerShell for Azure Stack がインストールされた](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)コンピューター上でそのトークンを使用する必要があります。  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Azure Stack 環境から登録トークンを取得する
  1. 登録トークンを取得するには、次の PowerShell コマンドを実行します。  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > 登録トークンは、*$env:SystemDrive\RegistrationToken.txt* として指定されたファイルに保存されます。

  2. 接続された Azure マシンで使用するためにこの登録トークンを保存します。


### <a name="connect-to-azure-and-register"></a>Azure に接続して登録する
管理者として PowerShell ISE を起動し、[Azure Stack ツールをダウンロードした](#bkmk_tools)ときに作成された **AzureStack-Tools-master** ディレクトリ内の **Registration** フォルダーに移動します。 **RegisterWithAzure.psm1** モジュールをインポートします。 

実行する PowerShell:
```powershell
Import-Module .\RegisterWithAzure.psm1
```
次に、同じ PowerShell セッションで、Azure に登録する登録トークンを指定します。

```Powershell  
$registrationToken = "<Your Registration Token>"
Register-AzsEnvironment -RegistrationToken $registrationToken  
```
オプションで、Get-Content コマンドレットを使用して、登録トークンが含まれているファイルを示すことができます。

 ```Powershell  
 $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
 Register-AzsEnvironment -RegistrationToken $registrationToken  
 ```
> [!NOTE]  
> 将来の参照のために登録リソース名または登録トークンを保存します。

## <a name="verify-azure-stack-registration"></a>Azure Stack の登録を検証する
Azure Stack が Azure に正常に登録されたことを検証するには、次の手順を使用します。
1. Azure Stack [管理者ポータル](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;//adminportal.*&lt;region>.&lt;fqdn>* にサインインします。
2. **[More Services]** (その他のサービス) > **[Marketplace Management]** (Marketplace の管理) > **[Add from Azure]** (Azure から追加) をクリックします。

Azure (WordPress など) から利用可能な項目のリストが表示される場合は、アクティブ化に成功しました。

> [!NOTE]
> 登録が完了すると、登録されていないことを示すアクティブな警告は表示されなくなります。

## <a name="renew-or-change-registration"></a>登録を更新または変更する
次の状況では、登録を更新または変更する必要があります。
- 容量ベースの年単位サブスクリプションを更新した後。
- 課金モデルを変更したとき。
- 容量ベースの課金のために変更を調整したとき (ノードの追加/削除)。

### <a name="change-the-subscription-you-use"></a>使用するサブスクリプションを変更する
使用するサブスクリプションを変更する場合は、まず **Remove-AzsRegistration** コマンドレットを実行してから、正しい Azure PowerShell コンテキストにログインしていることを確認し、最後に変更されたパラメーターを指定して **Set-AzsRegistration** を実行する必要があります。

```powershell
Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
Set-AzureRmContext -SubscriptionId $NewSubscriptionId
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

### <a name="change-the-billing-model-or-syndication-features"></a>課金モデルまたはシンジケーション機能を変更する
インストールの課金モデルまたはシンジケーション機能を変更する場合は、登録機能を呼び出して新しい値を設定できます。 最初に現在の登録を削除する必要はありません。 

```powershell
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

## <a name="remove-a-registered-resource"></a>登録されたリソースを削除する
登録を削除する場合は、**UnRegister-AzsEnvironment** コマンドレットを使用し、**Register-AzsEnvironment** に使用した登録リソース名または登録トークンのどちらかを渡す必要があります。

リソース名を使用して登録を削除するには:

```Powershell    
UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
```
登録トークンを使用して登録を削除するには:

```Powershell
$registrationToken = "*Your copied registration token*"
UnRegister-AzsEnvironment -RegistrationToken $registrationToken
```

## <a name="next-steps"></a>次の手順

[外部の監視の統合](azure-stack-integrate-monitor.md)