<properties urlDisplayName="Get Started with Azure Automation" pageTitle="Azure オートメーションの使用" metaKeywords="" description="Azure でオートメーション ジョブをインポートして実行する方法について説明します。" metaCanonical="" services="automation" documentationCenter="" title="Get Started with Azure Automation" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/21/2014" ms.author="bwren" />


# Azure オートメーションの使用

開発者は Microsoft Azure オートメーションを使用すると、クラウド環境で一般的に実行される、手動で実行時間が長く、エラーが起こりやすく、頻繁に繰り返されるタスクを自動化する手段を入手できます。Runbook を使用して、Azure 環境内でリソースを作成、監視、管理、およびデプロイすることができます。Runbook は、内部で Windows PowerShell のワークフローを使用しています。オートメーションの詳細については、[オートメーション概要ガイド](http://go.microsoft.com/fwlink/p/?LinkId=392861)に関するページを参照してください。 

このチュートリアルでは、Azure オートメーションにサンプルの「Hello World」Runbook をインポートし、その Runbook を実行して、出力を表示する手順について説明します。

>[WACOM.NOTE] [Azure PowerShell コマンドレット](http://msdn.microsoft.com/ja-jp/library/jj156055.aspx)を使用する Azure の操作を自動化する方法については、 <a href="http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/">「Azure Automation:Authenticating to Azure using Azure Active Directory (Azure オートメーション: Azure Active Directory を使用して Azure を認証する)」を参照してください</a>。

## サンプル Runbook とユーティリティ Runbook

Azure オートメーション チームはさまざまな Runbook サンプルを作成し、オートメーションに関連する作業を開始できるようにしました。これらのサンプルはオートメーションの基本的な概念を取り扱っており、独自の Runbook を作成する方法を学習するのに役立ちます。  

また、オートメーション チームは、より大きいオートメーション タスクの構成要素として使用できるユーティリティ Runbook も作成しました。  

>[WACOM.NOTE] 小規模で、モジュール構造を採用した、再利用可能な Runbook を作成することをお勧めします。また、オートメーションに慣れた後は、一般的に使用されるシナリオで役立つ独自のユーティリティ Runbook を作成することも強くお勧めします。  

オートメーション チームが作成したサンプル Runbook とユーティリティ Runbook を[スクリプト センター](http://go.microsoft.com/fwlink/p/?LinkId=393029)で閲覧、ダウンロードしたり、[Runbook](http://aka.ms/runbookgallery) ギャラリーからそれらをインポートしたりすることができます。 

## オートメーション関連のコミュニティとフィードバック

コミュニティや Microsoft の他のチームから提供された Runbook も、[スクリプト センター](http://go.microsoft.com/fwlink/?LinkID=391681)および [Runbook ギャラリー](http://aka.ms/runbookgallery)で公開されています。 

<strong>フィードバックの送信</strong>  オートメーション Runbook ソリューションや統合モジュールを探索している場合は、スクリプト センターにスクリプトの要求を投稿することができます。オートメーションに関連する新機能のご提案がある場合は、[User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback) に投稿してください。

[WACOM.INCLUDE [アカウント作成メモ](../includes/create-account-note.md)]

## このチュートリアルの手順概要

1. [オートメーション アカウントの作成](#automationaccount)
2. [Runbook ギャラリーからの Runbook のインポート](#importrunbook)
3. [Runbook の発行](#publishrunbook)
4. [Runbook の開始](#startrunbook)


## <a name="automationaccount"></a>オートメーション アカウントの作成

1.	[Azure 管理ポータル](http://manage.windowsazure.com)にログインします。

2.	管理ポータルで、**[オートメーション アカウントの作成]** をクリックします。

	>[WACOM.NOTE] オートメーション アカウントを既に作成した場合は、手順 4 に進むことができます。

	![Create Account](./media/automation/automation_01_CreateAccount.png)

3.	**[新しいオートメーション アカウントの追加]** ページで、アカウントの名前を入力し、チェック マークをクリックします。

	![Add New Account](./media/automation/automation_02_addnewautoacct.png)

## <a name="importrunbook"></a>Runbook ギャラリーからの Runbook のインポート
 
4.	**[オートメーション]** ページで、作成した新しいアカウントをクリックします。
 
	![New Account](./media/automation/automation_03_NewAutoAcct.png)

5.	**[Runbook]** をクリックします。

	![Runbooks Tab](./media/automation/automation_04_RunbooksTab.png)
  
6.	**[新規]** > **[Runbook]** > **[ギャラリーから]** の順にクリックします。

	![Runbook Gallery](./media/automation/automation_05_ImportGallery.png)

7.  **[チュートリアル]** カテゴリを選択し、**[Hello World for Azure Automation]** を選択します。右矢印ボタンをクリックします。

	![Import Runbook](./media/automation/automation_06_ImportRunbook.png)

8.  Runbook の内容を確認し、右矢印ボタンをクリックします。

	![Runbook Definition](./media/automation/automation_07_RunbookDefinition.png)

8.	Runbook の詳細を確認し、チェック マーク ボタンをクリックします。

	![Runbook Details](./media/automation/automation_08_RunbookDetails.png)

## <a name="publishrunbook"></a>Runbook の発行 

9.	Runbook のインポートが完了したら、**[Write-HelloWorld]** をクリックします。

	![Imported Runbook](./media/automation/automation_07_ImportedRunbook.png)

9.	**[作成者]** をクリックし、**[ドラフト]** をクリックします。  

	ドラフト モードでは、Runbook の内容を変更できます。この Runbook に対して、どのような変更も加える必要はありません。

	![Author Draft](./media/automation/automation_08_AuthorDraft.png)  
 
10.	**[発行]** をクリックして Runbook を昇格し、運用環境で使用する準備をします。

	![Publish](./media/automation/automation_085_Publish.png)
   
11.	Runbook を保存して発行するように求めるメッセージが表示された場合は、**[はい]** をクリックします。
 
	![Save and Pub prompt](./media/automation/automation_09_SavePubPrompt.png)

## <a name="startrunbook"></a>Runbook の開始

12.	**[Write-HelloWorld]** の Runbook が開いた状態で **[開始]** をクリックします。

	![Published](./media/automation/automation_10_PublishStart.png)
 
13.	**[Runbook パラメーターの値を指定します]** ページで、Write-HelloWorld.ps1 スクリプトの入力パラメーターとして使用される **[名前]** を入力し、チェック マークをクリックします。

	![Runbook Parameters](./media/automation/automation_11_RunbookParams.png)
  
14.	**[ジョブ]** をクリックして、開始した Runbook ジョブの状態を確認し、**[ジョブ開始]** 列にあるタイムスタンプをクリックしてジョブの概要を表示します。

	![Runbook Status](./media/automation/automation_12_RunbookStatus.png)

15.	**[概要]** ページで、ジョブの概要、入力パラメーター、出力を表示できます。
 
	![Runbook Summary](./media/automation/automation_13_RunbookSummary_callouts.png)


# Runbook からの Azure サービスの管理 
この例では、Azure サービスを管理しない簡単な Runbook を示しています。[Azure コマンドレット](http://msdn.microsoft.com/ja-jp/library/jj156055.aspx)は Azure の認証を要求します。Azure オートメーションによる管理のために Azure サブスクリプションを構成するには、「[Azure Automation:Authenticating to Azure using Azure Active Directory (Azure オートメーション: Azure Active Directory を使用して Azure を認証する)]」(http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)の指示に従います。

# 関連項目

- [オートメーションの概要](http://go.microsoft.com/fwlink/p/?LinkId=392860)
- [Runbook オーサリング ガイド](http://go.microsoft.com/fwlink/p/?LinkID=301740)
- [オートメーション フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=390561)
- [Azure Automation:Authenticating to Azure using Azure Active Directory (Azure オートメーション: Azure Active Directory を使用して Azure を認証する)](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)

<!--HONumber=35.2-->
