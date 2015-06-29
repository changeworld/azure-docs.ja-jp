<properties
   pageTitle="Azure Automation の構成"
   description="Azure Automation を初めて使用するときに必要な構成手順について説明します。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/13/2015"
   ms.author="bwren" />

# Azure Automation の構成

この記事では、Azure Automation を使い始めるときに最初に行う必要があることについて説明します。

## Automation アカウント

Azure Automation を初めて開始するときに、少なくとも 1 つのAutomation アカウントを作成する必要があります。Automation アカウントを使用すると、アカウントに含まれる Automation のリソース (Runbook、資産) をアカウント間で分離できます。Automation アカウントを使用して、Automation のリソースを異なる論理環境に分けることができます。たとえば、開発用と運用用に異なるアカウントを使用できます。

各 Automation アカウントの Automation リソースは 1 つの Azure リージョンに関連付けられますが、Automation アカウントは任意のリージョンの Azure サービスを管理できます。異なるリージョンに Automation アカウントを作成する主な理由は、データとリソースを特定のリージョンに分離する必要があるポリシーがある場合です。

>[AZURE.NOTE]Azure プレビュー ポータルで作成した Automation アカウントと、そこに含まれるリソースは、Azure ポータルからアクセスすることはできません。これらのアカウントまたはリソースを Windows PowerShell で管理する場合は、Azure リソース マネージャー モジュールを使用する必要があります。
>
>Azure ポータルで作成した Automation アカウントは、どちらのポータルでも、またどちらのコマンドレットでも管理できます。いったんアカウントが作成されると、アカウント内のリソースを作成および管理する方法に違いはありません。引き続き Azure ポータルを使用する場合は、Azure プレビュー ポータルではなく Azure ポータルを使用して Automation アカウントを作成してください。


支払い期限超過などの問題が Azure アカウントにある場合、Automation アカウントが中断される可能性があります。このような場合、アカウントにはアクセスできず、実行中のジョブは停止され、すべてのスケジュールは無効になります。アカウントを表示することはできますが、その中のリソースは表示できません。問題を解決して Automation アカウントが有効になった後は、スケジュールを有効にして、中断された Runbook を再開する必要があります。


## Azure リソースへの認証の構成

[Azure コマンドレット](http://msdn.microsoft.com/library/azure/jj554330.aspx)を使用して Azure リソースにアクセスするときは、Azure サブスクリプションに認証を提供する必要があります。Azure Automation では、これは、サブスクリプションの管理者が構成する Azure Active Directory の組織アカウントで行います。その後、このユーザー アカウントに対する[資格情報](http://msdn.microsoft.com/library/dn940015.aspx)を作成し、Runbook の [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) でそれを使用できます。

>[AZURE.NOTE]Microsoft アカウント (旧称 LiveID) を Azure Automation で使用することはできません。

## Azure サブスクリプションを管理するための新しい Azure Active Directory ユーザーの作成

1. 管理する Azure サブスクリプションのサービス管理者として Azure ポータルにログインします。
2. **[Active Directory]** を選択します。
3. Azure サブスクリプションと関連付けられているディレクトリ名を選択します。必要であれば、**[設定]、[サブスクリプション]、[ディレクトリの編集]** で関連付けを変更できます。
4. [新しい Active Directory ユーザーを作成します](http://msdn.microsoft.com/library/azure/hh967632.aspx)。**[ユーザーの種類]** では、**[多要素認証の有効化]** ではなく **[組織内の新しいユーザー]** を選択します。
5. ユーザーの完全な名前と一時的なパスワードを書き留めておきます。
7. **[設定]、[管理者]、[追加]** の順に選択します。
8. 作成したユーザーの完全なユーザー名を入力します。
9. そのユーザーが管理するサブスクリプションを選択します。
10. Azure からログアウトし、作成したアカウントで再度ログインします。ユーザーのパスワードを変更するように促されます。
11. 作成したユーザー アカウント用に新しい [Azure Automation 資格情報資産](http://msdn.microsoft.com/library/dn940015.aspx)を作成します。**[資格情報の種類]** は、**[Windows PowerShell 資格情報]** にする必要があります。


## Runbook での資格情報の使用

[Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) アクティビティを使用して Runbook の資格情報を取得した後、それを [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) で使用して Azure サブスクリプションに接続できます。資格情報が複数の Azure サブスクリプションの管理者のものである場合は、[Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) も使用して正しいものを指定する必要があります。これについては、ほとんどの Azure Automation Runbook の先頭に一般に出現する以下のサンプル Windows PowerShell で示されています。

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
	Add-AzureAccount –Credential $cred
	Select-AzureSubscription –SubscriptionName "My Subscription"

Runbook のすべての[チェックポイント](automation-runbook-execution/#checkpoints)の後で、これらの行を繰り返す必要があります。Runbook が中断された後、別のワーカーで再開した場合は、もう一度認証を実行する必要があります。

## 関連記事:
- [Azure Automation: Azure Active Directory を使用して Azure を認証する](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)
 

<!---HONumber=58_postMigration-->