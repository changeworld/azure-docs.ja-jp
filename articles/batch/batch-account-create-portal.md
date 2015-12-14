<properties
	pageTitle="Azure Batch アカウントの作成 | Microsoft Azure"
	description="Azure ポータルで、クラウド内で大規模な並列ワークロードを実行する Azure Batch アカウントを作成する方法について説明します"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="danlep"/>



# Azure ポータルで Azure Batch アカウントを作成して管理する

> [AZURE.SELECTOR]
- [Azure portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

[Azure ポータル](https://portal.azure.com)を使用して Azure Batch アカウントを作成し、その設定 (アカウント キーなど) を管理する方法について説明します。すべての Batch API 要求を認証するには、Batch アカウント URL と関連するアクセス キーが必要です。また、Batch ワークロードのすべての Batch リソース (プール、ジョブ、タスクなど) を特定の Batch アカウントと関連付けます。

>[AZURE.NOTE]現在、プレビュー ポータルでは、Batch アカウント管理と一部のアカウント リソースの表示の機能をサポートしています。開発時は、Batch API を介して Batch の全機能を使用できます。

## Batch アカウントを作成する

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. **[新規]**、**[Compute]**、**[Batch Service]** の順にクリックします。

	![Marketplace での Batch][marketplace_portal]

3. 情報を確認し、**[作成]** をクリックします。

4. **[新しい Batch アカウント]** ブレードで、次の情報を入力します。

	a.**[アカウント名]** に、Batch アカウントの URL に使用する一意の名前を入力します。

	>[AZURE.NOTE]Batch アカウント名は Azure に一意である必要があります。3 ～ 24 文字で小文字と数字のみを使用します。

	b.複数のサブスクリプションがある場合は、**[サブスクリプション]** をクリックし、アカウントが作成される使用可能なサブスクリプションを選択します。

	c.**[リソース グループ]** をクリックして、アカウント用に既存のリソース グループを選択するか、新しいリソース グループを作成します。

	d.**[場所]** で、Batch が使用可能な Azure リージョンを選択します。

	![Batch アカウントを作成する][account_portal]

5. **[作成]** をクリックして、アカウントの作成を完了します。

## アクセス キーとアカウント設定の管理
アカウントの作成後は、ポータルでアカウントを見つけて、アクセス キー、正規ユーザーなどの設定を管理できます。

Batch アカウント URL は、**[Essentials]** に表示されます。`https://<account_name>.<region>.batch.azure.com` という形式の URL です。

アクセス キーの表示と管理には、キー アイコンをクリックします。

![Batch アカウント キー][account_keys]

## Batch アカウントに関するその他の注意事項

* Batch アカウントの作成と管理の方法として、他に [Batch PowerShell コマンドレット](batch-powershell-cmdlets-get-started.md)や [Batch Management .NET ライブラリ](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/)があります。


* Azure Batch アカウントの取得は無料です。ワークロードの実行時に Azure コンピューティング リソースなどのサービスを使用する場合にのみ、料金がかかります ([Batch の価格](https://azure.microsoft.com/pricing/details/batch/)を参照してください)。

* 1 つの Batch アカウントで複数の Batch ワークロードを実行したり、異なる Azure リージョンの複数の Batch アカウントにワークロードを分散したりすることができます。

* いくつかの大規模な Batch ワークロードを実行する場合は、Azure サブスクリプションと各 Batch アカウントに適用する [Batch サービス クォータと制限](batch-quota-limit.md)について気を付けてください。Batch アカウントの現在のクォータは、アカウント プロパティのプレビュー ポータルに表示されます。

## 次のステップ

* Batch の概念の詳細については、「[Azure Batch 機能の概要](batch-api-basics.md)」をご覧ください。

* [Batch .NET クライアント ライブラリ](batch-dotnet-get-started.md)を使用して最初のアプリケーション開発を始める

[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=AcomDC_1203_2015-->