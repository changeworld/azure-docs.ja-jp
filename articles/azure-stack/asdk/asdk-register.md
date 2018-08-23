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
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 95ab06685452f647884bf92f110e3ab56f3c2714
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "41954854"
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

2. 次の PowerShell コマンドを実行し、Azure に ASDK インストールを登録します。 Azure サブスクリプションとローカル ASDK インストールの両方にサインインする必要があります。 Azure サブスクリプションをまだお持ちでない場合は、[こちらから無料の Azure アカウントを作成](https://azure.microsoft.com/free/?b=17.06)できます。 Azure Stack を登録しても、Azure サブスクリプションに課金されることはありません。  

同じ Azure Subscription ID を利用し、複数の Azure Stack インスタンスで登録スクリプトを実行している場合、**Set-AzsRegistration** コマンドレットを実行するとき、登録に一意の名前を設定します。 **RegistrationName** パラメーターの既定値は **AzureStackRegistration** です。 ただし、複数の Azure Stack インスタンスに同じ名前を使用すると、スクリプトは失敗します。

  ```PowerShell  
    # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName "<Unique-name>"
  ```
3. スクリプトが完了すると、"**Your environment is now registered and activated using the provided parameters** " (提供されたパラメーターを使用して環境が登録され、アクティブ化されました) というメッセージが表示されます。

    ![ご利用の環境がこれで登録されました](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>登録が成功したことを確認する
次の手順に従って、Azure への ASDK の登録が成功したことを確認します。

1. [Azure Stack 管理ポータル](https://adminportal.local.azurestack.external)にサインインします。

2. **[Marketplace Management]** > **[Azure から追加]** の順にクリックします。

    ![](media/asdk-register/2.PNG)

3. Azure から利用可能な項目のリストが表示される場合は、アクティブ化に成功しました。

    ![](media/asdk-register/3.PNG)

## <a name="next-steps"></a>次の手順
[Azure Stack マーケットプレース項目を追加する](.\.\azure-stack-marketplace.md)
