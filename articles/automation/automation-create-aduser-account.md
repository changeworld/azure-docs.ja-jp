---
title: Azure AD ユーザー アカウントを作成する
description: この記事では、Azure Automation の Runbook が Azure で認証を受ける際に使用する Azure AD ユーザー アカウントを作成する方法について説明します。
keywords: Azure Active Directory ユーザー, Azure サービス管理, Azure AD ユーザー アカウント
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 105dcf5564ec5c1d0b3528e9b5667d89d98c6cab
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Azure クラシック デプロイメントと Resource Manager による Runbook の認証
この記事では、Azure クラシック デプロイ モデルまたは Azure Resource Manager のリソースに対して実行する Azure Automation Runbook 用に Azure AD ユーザー アカウントを構成するための手順を説明します。 Azure Resource Manager ベースの Runbook では、このアカウントを引き続き認証 ID として使用できますが、Azure 実行アカウントを使用することが推奨されます。       

## <a name="create-a-new-azure-active-directory-user"></a>新しい Azure Active Directory ユーザーを作成する
1. 管理する Azure サブスクリプションのサービス管理者として Azure Portal にログインします。
2. **[Azure Active Directory]** > **[ユーザーとグループ]** > **[すべてのユーザー]** > **[新しいユーザー]** を選択します。
3. **名前**や**ユーザー名**など、ユーザーの詳細を入力します。  
4. ユーザーの完全な名前と一時的なパスワードを書き留めておきます。
5. **[ディレクトリ ロール]** を選択します。
6. グローバル管理者または制限付き管理者のロールを割り当てます。
7. ログアウトして、先ほど作成したアカウントで再度 Azure にログインします。ユーザーのパスワードを変更するように促されます。

## <a name="create-an-automation-account-in-the-azure-portal"></a>Azure Portal で Automation アカウントを作成する
このセクションでは、以下の手順を実行して、Azure Resource Manager モードでリソースを管理する Runbook で使用される Azure Automation アカウントを Azure Portal で作成します。  

1. 管理する Azure サブスクリプションのサービス管理者として Azure Portal にログインします。
2. **[Automation アカウント]** を選択します。
3. **[追加]** を選択します。<br><br>![[Automation アカウントの追加]](media/automation-create-aduser-account/add-automation-acct-properties.png)
4. **[Automation アカウントの追加]** ブレードの **[名前]** ボックスに、新しい Automation アカウントの名前を入力します。
5. 複数のサブスクリプションがある場合は、新しいアカウントに対していずれかを指定し、新規または既存の**リソース グループ**と、Azure データ センターの**場所**も指定します。
6. **[Azure 実行アカウントの作成]** オプションで **[はい]** を選択し、**[作成]** ボタンをクリックします。  
   
    > [!NOTE]
    > 実行アカウントを作成しなかった場合 (先ほどのオプションで **[いいえ]** を選択した場合)、**[Automation アカウントの追加]** ブレードに警告メッセージが表示されます。 アカウントが作成されてサブスクリプションの**共同作成者**ロールに割り当てられますが、サブスクリプションのディレクトリ サービス内の対応する認証 ID が割り当てられないため、サブスクリプション内のリソースにアクセスすることはできません。 このアカウントを参照する Runbook は認証を通過できず、Azure Resource Manager リソースに対するタスクを実行することができません。
    > 
    >

    <br>![Add Automation Account Warning](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. Azure によって Automation アカウントが作成されている間、メニューの **[通知]** で進行状況を追跡できます。

資格情報の作成が完了したら、資格情報資産を作成し、作成済みの AD ユーザー アカウントに Automation アカウントを関連付ける必要があります。 Automation アカウントは作成しただけで、認証 ID に関連付けられていません。 「[Azure Automation での資格情報資産](automation-credentials.md#creating-a-new-credential-asset)」に説明されている手順を実行し、**ユーザー名**の値を**ドメイン\ユーザー**の形式で入力します。

## <a name="use-the-credential-in-a-runbook"></a>Runbook での資格情報の使用
[Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) アクティビティを使用して Runbook の資格情報を取得した後、それを [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) で使用して Azure サブスクリプションに接続できます。 資格情報が複数の Azure サブスクリプションの管理者のものである場合は、 [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) も使用して正しいものを指定する必要があります。 これについては、ほとんどの Azure Automation Runbook の先頭に一般に出現する以下のサンプル Windows PowerShell で示されています。

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Runbook のすべての [チェックポイント](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) の後で、これらの行を繰り返します。 Runbook が中断された後、別のワーカーで再開した場合は、もう一度認証を実行する必要があります。

## <a name="next-steps"></a>次の手順
* Runbook のさまざまな種類について、また、独自の Runbook を作成する手順について確認します。記事「[Azure Automation の Runbook の種類](automation-runbook-types.md)」を参照してください

