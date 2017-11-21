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
ms.date: 10/27/2017
ms.author: erikje
ms.openlocfilehash: 24cde66a132ae2e1ba0eb9b1564915746e5ca448
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2017
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
- [登録されている Azure Stack リソースプロバイダー](#register-azure-stack-resource-provider-in-azure)。

これらの要件を満たす Azure サブスクリプションがない場合は、[ここで無料の Azure アカウントを作成](https://azure.microsoft.com/en-us/free/?b=17.06)できます。 Azure Stack を登録しても、Azure サブスクリプションに課金されることはありません。



## <a name="register-azure-stack-resource-provider-in-azure"></a>Azure での Azure Stack リソース プロバイダーの登録
> [!NOTE] 
> この手順は、Azure Stack 環境で 1 回だけ実行する必要があります。
>

1. PowerShell ISE を管理者として起動します。
2. -EnvironmentName パラメーターを「AzureCloud」に設定して、Azure サブスクリプションの所有者である Azure アカウントにログインします。
3. Azure リソース プロバイダー「Microsoft.AzureStack」を登録します。

例: 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```


## <a name="register-azure-stack-with-azure"></a>Azure を使用した Azure Stack の登録

> [!NOTE]
>これらの手順はすべて、ホスト コンピューターで完了する必要があります。
>

1. [PowerShell for Azure Stack をインストールします](azure-stack-powershell-install.md)。 
2. [RegisterWithAzure.psm1 スクリプト](https://go.microsoft.com/fwlink/?linkid=842959)をフォルダー (C:\Temp など) にコピーします。
3. 管理者として PowerShell ISE を起動し、RegisterWithAzure モジュールをインポートします。    
4. RegisterWithAzure.psm1 スクリプトから Add-AzsRegistration モジュールを実行します。 次のプレースホルダーを置き換えます。 
    - *YourCloudAdminCredential*は domain\cloudadmin (Development Kitでは azurestack\cloudadmin) のローカル ドメインの資格情報を含む PowerShell オブジェクトです。
    - *YourAzureSubscriptionID* は、Azure Stack の登録に使用する Azure サブスクリプション ID です。
    - *YourAzureDirectoryTenantName* は、自分の Azure サブスクリプションに関連付けられている Azure テナント ディレクトリの名前です。 登録リソースは、このディレクトリ テナントに作成されます。 
    - *YourPrivilegedEndpoint* は[特権エンドポイント](azure-stack-privileged-endpoint.md)の名前です。

    ```powershell
    Add-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -AzureDirectoryTenantName $YourAzureDirectoryTenantName  -AzureSubscriptionId $YourAzureSubscriptionId -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Development 
    ```
5. ログインのポップアップ ウィンドウで、Azure サブスクリプションの資格情報を入力します。

## <a name="verify-the-registration"></a>登録の確認

1. 管理者ポータル (https://adminportal.local.azurestack.external) にサインインします。
2. **[More Services]** (その他のサービス) > **[Marketplace Management]** (Marketplace の管理) > **[Add from Azure]** (Azure から追加) をクリックします。
3. Azure (WordPress など) から利用可能な項目のリストが表示される場合は、アクティブ化に成功しました。

> [!NOTE]
> 登録が完了すると、登録されていないことを示すアクティブな警告は表示されなくなります。

## <a name="next-steps"></a>次のステップ

[Azure Stack への接続](azure-stack-connect-azure-stack.md)

