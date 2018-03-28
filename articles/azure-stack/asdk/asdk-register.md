---
title: ASDK の Azure への登録 | Microsoft Docs
description: Azure Stack を Azure に登録してマーケットプレース シンジケーションと使用状況レポートを有効にする方法について説明します。
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
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 9e2dbc71f6424b87945e346a42c86d4cde7f740e
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="register-azure-stack-with-azure"></a>Azure を使用した Azure Stack の登録
Azure Stack Development Kit (ASDK) インストールを Azure に登録して Azure からマーケットプレース項目をダウンロードしたり、Microsoft に返送するコマース データを設定したりできます。 登録によって、マーケットプレース シンジケーションや使用状況レポートなどの Azure Stack の重要な機能をテストできるようになるので、登録することをお勧めします。 Azure Stack を登録すると、使用状況が Azure コマースにレポートされます。 使用状況は、登録に使用したサブスクリプションの下に表示されます。 ただし、ASDK のユーザーは、レポートする使用状況に対して課金されることはありません。


## <a name="register-azure-stack-with-azure"></a>Azure を使用した Azure Stack の登録 
次の手順に従って、ASDK を Azure に登録します。

> [!NOTE]
> これらすべての手順は、特権エンドポイントにアクセスできるコンピューターから実行する必要があります。 ASDK の場合は、開発キットのホスト コンピューターです。 

次の手順を使って Azure に ASDK を登録する前に、[デプロイ後の構成](asdk-post-deploy.md)の記事の説明に従って Azure Stack PowerShell をインストールし、Azure Stack ツールをダウンロードしたことを確認します。 

1. 管理者として PowerShell コンソールを開きます。  

2. 次の PowerShell コマンドを実行して ASDK インストールを Azure に登録します (Azure サブスクリプションとローカル ASDK インストールの両方にログインする必要があります)。

    ```PowerShell
    # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
    Set-AzsRegistration `
        -CloudAdminCredential $CloudAdminCred `
        -PrivilegedEndpoint AzS-ERCS01 `
        -BillingModel Development

3. When the script completes, you should see this message: **Your environment is now registered and activated using the provided parameters.**

    ![](media/asdk-register/1.PNG) 

## Verify the registration was successful
Follow these steps to verify that the ASDK registration with Azure was successful.

1. Sign in to the [Azure Stack administration portal](https://adminportal.local.azurestack.external).

2. Click **Marketplace Management** > **Add from Azure**.

    ![](media/asdk-register/2.PNG) 

3. If you see a list of items available from Azure, your activation was successful.

    ![](media/asdk-register/3.PNG) 

## Next steps
[Add an Azure Stack marketplace item](asdk-marketplace-item.md)