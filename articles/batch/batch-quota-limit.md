<properties
	pageTitle="Batch サービスのクォータと制限 | Microsoft Azure"
	description="Azure Batch サービスを使用する際のクォータ、制限、制約について説明します"
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
	ms.date="10/26/2015"
	ms.author="danlep"/>



# Azure Batch サービスのクォータと制限

この記事では、Azure Batch サービスで使用できるリソースの既定値と上限値について説明します。このような制限のほとんどは、Azure からユーザーのサブスクリプションまたは Batch アカウントに適用されているクォータです。

実稼働 Batch ワークロードを実行する予定がある場合、状況に応じて既定値を超える 1 つまたは複数のクォータを増やす必要があります。クォータを増やすには、オンライン カスタマー サポートに申請 (無料) してください。

>[AZURE.NOTE]クォータは、容量の保証ではなく、クレジット制限です。大規模な容量が必要な場合は、Azure サポートにお問い合わせください。

## サブスクリプションのクォータ
リソース|既定の制限|上限
---|---|---
サブスクリプションあたりのリージョンごとの Batch アカウント数|1|20

## Batch アカウントのクォータ
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## その他の制限
リソース|上限
---|---
コンピューティング ノードごとのタスク|ノードのコア数 x 4

## Batch クォータの確認

Batch アカウントのクォータは、[Azure プレビュー ポータル](https://portal.azure.com)で確認します。

1. プレビュー ポータルで **Batch アカウント**、Batch アカウント名の順にクリックします。

2. アカウント ブレードで **[設定]**、**[プロパティ]** の順にクリックします。

	![Batch アカウントのクォータ][account_quotas]

3. **[プロパティ]** ブレードで、Batch アカウントに現在適用されているクォータを確認します。

## クォータを増やす

Azure プレビュー ポータルでクォータの増加を要求するには、次の手順を実行します ([Azure ポータル](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)で増加を要求することもできます)。

1. プレビュー ポータルのダッシュボードで、**[ヘルプとサポート]** をクリックします。

2. **[サポート要求の作成]、[Basic]** の順にクリックします。

3. **Basic** ブレードで、次の手順を実行します。

	a.**[問題の種類]** で、**[クォータ]** を選択します。

	b.サブスクリプションを選択します。

	c.**[サービス]** で **[Batch Service]** を選択します。

	d.**[サポート プラン]** で **[Azure のサポート プラン - 開発者]** を選択します。

	**[次へ]** をクリックします。

4. **[問題]** ブレードで、次の手順を実行します。

	a.**[問題の種類]** で、**[Batch]** を選択します。

	b.**[詳細]** に、特定のアカウントの変更するクォータと、新しい制限が表示されます。

	**[次へ]** をクリックします。

5. **[連絡先情報]** ブレードに連絡先の詳細情報を入力し、**[次へ]** をクリックします。

6. **[作成]** をクリックして新しいサポート要求を送信します。

Azure サポートから連絡があります。要求を完了するには最大で 2 営業日かかります。

## 関連トピック

* [Azure Batch アカウントの作成と管理](batch-account-create-portal.md)

* [Azure Batch の API の基本](batch-api-basics.md)

* [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

<!---HONumber=Nov15_HO1-->