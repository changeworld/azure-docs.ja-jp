<properties 
	pageTitle="Azure オートメーションの使用" 
	description="Azure でオートメーション ジョブをインポートして実行する方法について説明します。" 
	services="automation" 
	documentationCenter="" 
	authors="bwren" 
	manager="stevenka" 
	editor=""/>

<tags 
	ms.service="automation" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article" 
	ms.date="2/20/2015" 
	ms.author="bwren"/>


# Azure オートメーションの使用

## Azure Automation とは

ユーザーは Microsoft Azure オートメーションを使用すると、クラウド環境で一般的に実行される、手動で実行時間が長く、エラーが起こりやすく、頻繁に繰り返されるタスクを自動化する手段を入手できます。Runbook を使用して、Azure 環境内でリソースを作成、監視、管理、およびデプロイすることができます。Runbook は、Windows PowerShell のワークフローに基づいています。このガイドでは、チュートリアルを通じて、簡単なサンプルの Runbook を実行します。次に、サービスのより高度な機能を探るためのリソースを紹介します。

## チュートリアル
このチュートリアルでは、オートメーション アカウントを作成し、Azure Automation にサンプルの「Hello World」Runbook をインポートし、その Runbook を実行して、出力を表示する手順について説明します。

このチュートリアルを完了するには、Azure サブスクリプションが必要です。またお持ちでない場合は、<a href="/pricing/member-offers/msdn-benefits-details/" target="_blank">MSDN サブスクライバーの特典を有効にする</a>か、<a href="/pricing/free-trial/" target="_blank">無料評価版にサインアップ</a>してください。

[AZURE.INCLUDE [automation-note-authentication](../includes/automation-note-authentication.md)]

## <a name="automationaccount"></a>Automation アカウントの作成

Automation アカウントは、Azure Automation リソースのコンテナーで、環境の分離や、ワークフローの詳細な整理のための方法を提供します。詳細については、Automation ライブラリの「[Automation アカウント](http://aka.ms/runbookauthor/azure/automationaccounts)」を参照してください。オートメーション アカウントを既に作成している場合は、次の手順を省略できます。

1.	[Azure 管理ポータル](http://manage.windowsazure.com)にログインします。

2.	管理ポータルで、**\[オートメーション アカウントの作成\]** をクリックします。

	![アカウントの作成](./media/automation/automation_01_CreateAccount.png)

3.	**\[新しいオートメーション アカウントの追加\]** ページで、名前を入力し、アカウントのリージョンを選択します。リージョンでは、アカウントの Automation リソースが格納される場所を指定します。これは、アカウントの機能には影響しませんが、アカウントのリージョンがご使用のその他の Azure リソースが格納された場所に近い場合、Runbook をより素早く実行できます。準備ができたら、チェック マークをクリックします。

	![新しいアカウントの追加](./media/automation/automation_02_addnewautoacct.png)

## <a name="importrunbook"></a>Runbook ギャラリーからの Runbook のインポート

[Runbook ギャラリー](http://aka.ms/runbookgallery)には、Azure Automation アカウントに直接インポートできるサンプルの Runbook が含まれており、これによって、他の Azure Automation および PowerShell ユーザーの作業を活用できます。この手順では、ギャラリーを使用して「Hello World」サンプル Runbook をインポートします。

4.	**\[オートメーション\]** ページで、作成した新しいアカウントをクリックします。
 
	![新しいアカウント](./media/automation/automation_03_NewAutoAcct.png)

5.	**\[Runbook\]** をクリックします。

	![\[Runbook\] タブ](./media/automation/automation_04_RunbooksTab.png)
  
6.	**\[新規\]** \> **\[Runbook\]** \> **\[ギャラリーから\]** の順にクリックします。

	![Runbook Gallery](./media/automation/automation_05_ImportGallery.png)

7.  **\[チュートリアル\]** カテゴリを選択し、**\[Hello World for Azure Automation\]** を選択します。右矢印ボタンをクリックします。

	![Import Runbook](./media/automation/automation_06_ImportRunbook.png)

8.  Runbook の内容を確認し、右矢印ボタンをクリックします。

	![Runbook Definition](./media/automation/automation_07_RunbookDefinition.png)

8.	Runbook の詳細を確認し、チェック マーク ボタンをクリックします。

	![Runbook Details](./media/automation/automation_08_RunbookDetails.png)

## <a name="publishrunbook"></a>Runbook の発行 

Runbook は、まずドラフト モードでインポートされます。つまりこれは、承認前に実行可能な新しいバージョンとして Runbook で作業を継続できることを意味します。このサンプルの Runbook は追加の構成を必要としないため、そのまますぐに発行できます。詳細については、「[Runbook の発行](http://aka.ms/runbookauthor/azure/publishrunbook)」を参照してください。

9.	Runbook のインポートが完了したら、**\[Write-HelloWorld\]** をクリックします。

	![インポートした Runbook](./media/automation/automation_07_ImportedRunbook.png)

9.	**\[作成者\]** をクリックし、**\[ドラフト\]** をクリックします。

	ドラフト モードでは、Runbook の内容を変更できます。この Runbook に対して、どのような変更も加える必要はありません。

	![作成者のドラフト](./media/automation/automation_08_AuthorDraft.png)
 
10.	**\[発行\]** をクリックして Runbook を昇格し、運用環境で使用する準備をします。

	![Publish](./media/automation/automation_085_Publish.png)
   
11.	確認を求められたら、**\[はい\]** をクリックします。
 
	![保存して発行するためのメッセージ](./media/automation/automation_09_SavePubPrompt.png)

## <a name="startrunbook"></a>Runbook の開始

Runbook をインポートし、発行したら、それを実行して、出力を調べます。詳細については、「[Runbook の開始](http://aka.ms/runbookauthor/azure/startrunbook)」および「[Runbook の出力とメッセージ](http://aka.ms/runbookauthor/azure/runbookoutput)」を参照してください。

12.	**\[Write-HelloWorld\]** の Runbook が開いた状態で **\[開始\]** をクリックします。

	![公開済み](./media/automation/automation_10_PublishStart.png)
 
13.	**\[Runbook パラメーターの値を指定します\]**ページで、Write-HelloWorld.ps1 スクリプトの入力パラメーターとして使用される **\[名前\]** を入力し、チェック マークをクリックします。

	![Runbook のパラメーター](./media/automation/automation_11_RunbookParams.png)
  
14.	**\[ジョブ\]** をクリックして、開始した Runbook ジョブの状態を確認し、**\[ジョブ開始\]** 列にあるタイムスタンプをクリックしてジョブの概要を表示します。

	![Runbook のステータス](./media/automation/automation_12_RunbookStatus.png)

15.	**\[概要\]** ページで、ジョブの概要、入力パラメーター、出力を表示できます。
 
	![Runbook の概要](./media/automation/automation_13_RunbookSummary_callouts.png)

ご利用ありがとうございます。 チュートリアルが完了しました。

## <a name="nextsteps"></a>次のステップ 
1. このチュートリアルの単純な Runbook では、**Azure サービスを管理できません**。ほとんどの Runbook では [Azure コマンドレット](http://msdn.microsoft.com/library/jj156055.aspx)を使ってこれを行います。このためには、Azure サブスクリプションに対する認証が必要です。「[Runbook での管理用の Azure の構成](http://aka.ms/azureautomationauthentication)」の手順に従って、Azure サブスクリプションをこれらのコマンドレットを操作するように構成します。  
2. Azure Automation の機能の詳細については、以下に示された[リソース](#resources)を参照してください。
3. [Azure Automation ブログ](http://azure.microsoft.com/blog/tag/azure-automation)をサブスクライブすると、Azure Automation チームにより常に最新の情報を把握できます。

## <a name="resources"></a>リソース

その他にも、Azure Automation や、独自の Runbook の作成方法の詳細を確認できる多数のリソースが用意されています。

- [Azure Automation ライブラリ](http://go.microsoft.com/fwlink/p/?LinkId=392860)では、Azure Automation の構成や管理、また独自の Runbook の作成に関する完全なドキュメントもご利用いただけます。 
- [Azure PowerShell コマンドレット](http://msdn.microsoft.com/library/jj156055.aspx)では、Windows PowerShell を使用した Azure のオートメーション操作に関する情報が提供されます。Runbook ではこれらのコマンドレットを使用して Azure リソースを操作します。
- [Azure Automation ブログ](http://azure.microsoft.com/blog/tag/azure-automation)には、マイクロソフトからの Azure Automation に関する最新の情報が掲載されています。
- [Automation フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=390561)では Azure Automation に関する質問を投稿でき、マイクロソフトや Automation コミュニティから回答を取得できます。


## サンプル Runbook とユーティリティ Runbook

マイクロソフトおよび Azure Automation コミュニティが提供するサンプル Runbook を使うと、独自のソリューションを作成できます。また、ユーティリティ Runbook は大きめのオートメーション タスクの構成ブロックとして使用できます。これらの Runbook は[スクリプト センター](http://go.microsoft.com/fwlink/p/?LinkId=393029)からダウンロードするか、[Runbook ギャラリー](http://aka.ms/runbookgallery)を使用して Azure Automation に直接インポートできます。
  

## フィードバック

<strong>フィードバックの送信</strong> Azure Automation の Runbook ソリューションや統合モジュールを探索している場合は、スクリプト センターにスクリプトの要求を投稿することができます。Azure Automation に関するフィードバックや機能に関するご要望は、[User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback) にお寄せください。よろしくお願いいたします。

<!--HONumber=52-->
