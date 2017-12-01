---
title: "Azure Stack の登録 | Microsoft Docs"
description: "Azure サブスクリプションを使用して Azure Stack を登録します。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: erikje
ms.openlocfilehash: 977630741b8424c4c6bd5f5d492e33b9981b9cb5
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2017
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Azure サブスクリプションを使用した Azure Stack の登録

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

[Azure Stack](azure-stack-poc.md) を Azure に登録して Azure からマーケットプレース項目をダウンロードし、Microsoft への商取引データの事後レポートを設定できます。 

> [!NOTE]
>登録によって、マーケットプレース シンジケーションや使用状況レポートなどの Azure Stack の重要な機能をテストできるようになるので、登録することをお勧めします。 Azure Stack を登録すると、使用状況が Azure コマースにレポートされます。 使用状況は、登録に使用したサブスクリプションの下に表示されます。 Azure Stack Development Kit のユーザーは、レポートする使用状況に対して課金されることはありません。
>


## <a name="get-azure-subscription"></a>Azure サブスクリプションの取得

Azure を使用して Azure Stack を登録する前に、以下のものが必要です。

- Azure サブスクリプションのサブスクリプション ID。 ID を取得する場合は、Azure にサインインし、**[More services]** (その他のサービス) > **[Subscriptions]** (サブスクリプション) をクリックして、使用するサブスクリプションをクリックすると、**[Essentials]** (Essentials) の下に **[Subscription ID]** (サブスクリプション ID) が表示されます。 中国、ドイツ、および米国政府機関向けのクラウド サブスクリプションは、現在サポートされていません。
- サブスクリプションの所有者であるアカウントのユーザー名とパスワード (MSA/2FA アカウントがサポートされます)。
- Azure サブスクリプションの Azure Active Directory。 Azure でこのディレクトリを検索するには、Azure Portal の右上隅にあるアバターにポインターを合わせます。 

これらの要件を満たす Azure サブスクリプションがない場合は、[ここで無料の Azure アカウントを作成](https://azure.microsoft.com/en-us/free/?b=17.06)できます。 Azure Stack を登録しても、Azure サブスクリプションに課金されることはありません。



## <a name="register-azure-stack-resource-provider-in-azure"></a>Azure での Azure Stack リソース プロバイダーの登録
> [!NOTE] 
> この手順は、Azure Stack 環境で 1 回だけ実行する必要があります。
>

1. PowerShell セッションを管理者として開始します。
2. Azure サブスクリプションの所有者である Azure アカウントにログインします (Login-AzureRmAccount コマンドレットを使用してログインできますが、サインイン時に -EnvironmentName パラメーターを "AzureCloud" に設定してください)。
3. Azure リソースプロバイダー "Microsoft.AzureStack" を登録します。

**例:** 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```

## <a name="register-azure-stack-with-azure"></a>Azure を使用した Azure Stack の登録

> [!NOTE]
> これらすべての手順は、特権エンドポイントにアクセスできるコンピューターから実行する必要があります。 Azure Stack Development Kit では、ホスト コンピューターがそれにあたります。 統合システムを使用している場合は、Azure Stack のオペレーターに問い合わせてください。
>

1. PowerShell コンソールを管理者として開き、[PowerShell for Azure Stack をインストール](azure-stack-powershell-install.md)します。  

2. Azure Stack の登録に使用する Azure アカウントを追加します。 これを行うには、`Add-AzureRmAccount` コマンドレットをパラメーターなしで実行します。 Azure アカウント資格情報の入力を求められ、お使いのアカウントの構成によっては 2 要素認証を使用する必要があります。  

3. 複数のサブスクリプションがある場合は、次のコマンドを実行して、使用するものを選択します。  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. 登録に対応する既存バージョンの Powershell モジュールをすべて削除し、[GitHub から最新バージョンをダウンロード](azure-stack-powershell-download.md)します。  

5. 前の手順で作成された "AzureStack-Tools-master" ディレクトリから、"Registration" フォルダーに移動し、".\RegisterWithAzure.psm1" モジュールをインポートします。  

   ```powershell 
   Import-Module .\RegisterWithAzure.psm1 
   ```

6. 同じ PowerShell セッションで、次のスクリプトを実行します。 資格情報の入力を求められたら、`azurestack\cloudadmin` をユーザーとして指定すると、パスワードはデプロイ時にローカル管理者用に使用したものと同じです。  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Add-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -AzureSubscriptionId $AzureContext.Subscription.Id `
       -AzureDirectoryTenantName $AzureContext.Tenant.TenantId `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development 
   ```

   | パラメーター | 説明 |
   | -------- | ------------- |
   | CloudAdminCredential | [特権エンドポイントへのアクセス](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)に使用するクラウド ドメイン資格情報。 ユーザー名の形式は "\<Azure Stack ドメイン\>\cloudadmin" です。 開発キットのユーザー名は "azurestack\cloudadmin" に設定されます。 統合システムを使用している場合は、Azure Stack のオペレーターに問い合わせてこの値を入手してください。|
   | AzureSubscriptionId | Azure Stack の登録に使用する Azure サブスクリプション。|
   | AzureDirectoryTenantName | Azure サブスクリプションと関連付けられている Azure テナント ディレクトリの名前。 登録リソースは、このディレクトリ テナントに作成されます。 |
   | PrivilegedEndpoint | ログ収集およびその他のデプロイ後タスクのような機能を提供する、あらかじめ構成されたリモート PowerShell コンソール。 開発キットでは、特権エンドポイントは "AzS-ERCS01" 仮想マシン上にホストされます。 統合システムを使用している場合は、Azure Stack のオペレーターに問い合わせてこの値を入手してください。 詳細については、[特権エンドポイントの使用](azure-stack-privileged-endpoint.md)に関するトピックをご覧ください。|
   | BillingModel | 自分のサブスクリプションで使用する請求モデル。 このパラメーターに使用できる値は、"Capacity"、"PayAsYouUse" および "Development" です。 開発キットでは、この値は "Development" に設定されています。 統合システムを使用している場合は、Azure Stack のオペレーターに問い合わせてこの値を入手してください。 |

7. スクリプトが完了したら、"Azure Stack をアクティブ化しています (この手順は完了までに最大 10 分ほどかかります)" というメッセージが表示されます。 

## <a name="verify-the-registration"></a>登録の確認

1. 管理者ポータル (https://adminportal.local.azurestack.external) にサインインします。
2. **[More Services]** (その他のサービス) > **[Marketplace Management]** (Marketplace の管理) > **[Add from Azure]** (Azure から追加) をクリックします。
3. Azure (WordPress など) から利用可能な項目のリストが表示される場合は、アクティブ化に成功しました。

> [!NOTE]
> 登録が完了すると、登録されていないことを示すアクティブな警告は表示されなくなります。

## <a name="next-steps"></a>次のステップ

[Azure Stack への接続](azure-stack-connect-azure-stack.md)

