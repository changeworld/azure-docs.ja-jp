<properties
	pageTitle="Azure Automation での初めてのテキスト形式の Runbook"
	description="PowerShell ワークフローを使用して簡単なテキスト形式の Runbook を作成、テスト、および発行する手順を説明するチュートリアルです。Azure リソースに対する認証、入力パラメーターなど、複数の概念について説明します。"
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
	ms.topic="get-started-article" 
	ms.date="08/13/2015"
	ms.author="bwren"/>


# 初めてのテキスト形式の Runbook

> [AZURE.SELECTOR]
- [Graphical](automation-first-runbook-graphical.md)
- [Textual](automation-first-runbook-textual.md)

このチュートリアルでは、Azure Automation で[テキスト形式の Runbook](automation-powershell-workflow.md) を作成する手順を説明します。簡単な Runbook を作成、テスト、発行しながら、Runbook ジョブの状態を追跡する方法を説明します。その後、実際に Azure リソースを管理するように Runbook を変更し、ここでは Azure Virtual Machine を開始します。そして、Runbook のパラメーターを追加することによって、Runbook をいっそう堅牢にします。

## 前提条件

このチュートリアルを最後まで行うには、以下のものが必要です。

- として機能します。まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、<a href="/pricing/free-trial/" target="_blank">[無料評価版にサインアップ](http://azure.microsoft.com/pricing/free-trial/)してください。
- Runbook を保持するための [Automation アカウント](automation-configuring.md)。
- Azure Virtual Machine。マシンを停止して起動するので、運用環境のものは使用しないでください。
- Azure リソースに対する認証に使用する [Azure Active Directory のユーザーと Automation の資格情報資産](automation-configuring.md)。このユーザーには、仮想マシンを開始および停止するアクセス許可が必要です。

## 手順 1 - 新しい Runbook を作成する

最初に、*Hello World* というテキストを出力する簡単な Runbook を作成します。

1. Azure プレビュー ポータルで、Automation アカウントを開きます。Automation アカウント ページでは、そのアカウントのリソースを簡単に確認できます。既に資産がいくつかあります。これらのほとんどは、新しい Automation アカウントに自動的に含まれるモジュールです。[前提条件](#prerequisites)で説明されている資格情報資産も必要です。
2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。<br> ![Runbook の制御](media/automation-first-runbook-textual/runbooks-control.png)
2. **[Runbook の追加]** ボタンをクリックし、次に **[新しい Runbook の作成]** をクリックして、新しい Runbook を作成します
3. Runbook の名前を *MyFirstRunbook-Textual* に設定します。
4. ここでは、PowerShell ワークフローに基づいた[テキスト形式の Runbook](automation-powershell-workflow.md) を作成するため、**[Runbook type]** として **[Powershell ワークフロー**] を選択します。<br> ![新しい Runbook](media/automation-first-runbook-textual/new-runbook.png)
5. **[作成]** をクリックして Runbook を作成し、テキスト エディターを開きます。

## 手順 2 - コードを Runbook に追加する

Runbook に直接コードを入力するか、ライブラリ コントロールからコマンドレット、Runbook、資産を選択し、関連するパラメーターを使って Runbook に追加できます。このチュートリアルでは、Runbook に直接コードを入力します。

1. Runbook はこの時点で空です (必要な*ワークフロー* キーワード、Runbook の名前、ワークフロー全体を囲む中かっこ以外)。<br> ![Runbook の制御](media/automation-first-runbook-textual/empty-runbook.png)
2. 中かっこに「*Write-Output "Hello World."*」と入力します。<br> ![Hello world](media/automation-first-runbook-textual/hello-world.png)
3.   **[保存]** をクリックして Runbook を保存します。<br> ![Runbook の保存](media/automation-first-runbook-textual/runbook-edit-toolbar-save.png)

## 手順 3 - Runbook をテストする

Runbook を発行して運用環境で使用できるようにする前に、Runbook をテストして正常に動作することを確認します。Runbook をテストするときは、**ドラフト** バージョンを実行し、その出力を対話形式で表示します。
 
2. **[テスト ウィンドウ]** をクリックして、テスト ウィンドウを開きます。<br> ![テスト ウィンドウ](media/automation-first-runbook-textual/runbook-edit-toolbar-test-pane.png)
2. **[開始]** をクリックしてテストを開始します。有効なオプションはこれだけです。
3. [Runbook ジョブ](automation-runbook-execution)が作成され、その状態が表示されます。最初のジョブの状態は*キューに設定*であり、クラウドの Runbook ワーカーが使用できるようになるのを待っていることを示します。その後、ワーカーがジョブを要求すると*開始中*になり、Runbook が実際に実行を開始すると*実行中*になります。  
4. Runbook ジョブが完了すると、その出力が表示されます。この例では、*Hello World* と表示されます。<br>![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)
5. テスト ウィンドウを閉じてキャンバスに戻ります。

## 手順 4 - Runbook を発行して開始する

前の手順で作成した Runbook はまだドラフト モードです。運用環境で実行できるようにするには、発行する必要があります。Runbook を発行するときは、既存の発行済みバージョンをドラフト バージョンで上書きします。この例では、Runbook を作成したばかりなので、発行済みバージョンはまだありません。

1. **[発行]** をクリックして Runbook を発行し、確認を要求されたら **[はい]** をクリックします。<br> ![Publish](media/automation-first-runbook-textual/runbook-edit-toolbar-publish.png)
2. **[Runbook]** ウィンドウで左にスクロールして Runbook を表示すると、**[作成状態]** は **[発行済]** になっています。
3. 右にスクロールして戻り、**MyFirstRunbook-Textual** のウィンドウを表示します。上部のオプションを使用すると、Runbook の開始、将来の開始スケジュールの設定、または HTTP 呼び出しで開始できるようにする [Webhook](automation-webhooks.md) の作成を行うことができます。 
4. ここでは単純に Runbook を開始するので、**[開始]** をクリックし、確認を求められたら **[はい]** をクリックします。<br> ![Runbook の開始](media/automation-first-runbook-textual/runbook-toolbar-start.png)
5. 作成した Runbook ジョブのジョブ ウィンドウが開かれます。このウィンドウは閉じてもかまいませんが、ここではジョブの進行状況を確認できるように開いたままにします。
6.  ジョブの状態が **[ジョブの概要]** に表示され、Runbook をテストしたときに確認した状態と一致しています。<br> ![ジョブの概要](media/automation-first-runbook-textual/job-pane-summary.png)
7.  Runbook の状態が *[完了]* になったら、**[出力]** をクリックします。**[出力]** ウィンドウが開き、*Hello World* と表示されます。<br> ![ジョブの概要](media/automation-first-runbook-textual/job-pane-output.png)  
8.  [出力] ウィンドウを閉じます。
9.  **[ストリーム]** をクリックして、Runbook ジョブのストリーム ウィンドウを開きます。出力ストリームでは *Hello World* だけが表示されますが、Runbook が詳細やエラーに書き込んでいる場合は、これらの Runbook ジョブの他のストリームも表示できます。<br> ![ジョブの概要](media/automation-first-runbook-textual/job-pane-streams.png) 
9. ストリーム ウィンドウを閉じると、ジョブ ウィンドウは MyFirstRunbook ウィンドウに戻ります。
9.  **[ジョブ]** をクリックして、この Runbook のジョブ ウィンドウを開きます。この Runbook によって作成されたジョブの一覧が表示されます。ジョブを 1 回実行しただけなので、一覧に表示されるジョブは 1 つだけです。<br> ![ジョブ](media/automation-first-runbook-textual/runbook-control-jobs.png) 
9. このジョブをクリックすると、Runbook を開始したときに表示されたものと同じジョブ ウィンドウが開きます。これにより前に戻って、特定の Runbook に対して作成されたジョブの詳細を見ることができます。

## 手順 5 - Azure リソースを管理するための認証を追加する

Runbook をテストして発行しましたが、これまでのところ役に立つことは何もしていません。ここでは、Runbook で Azure リソースを管理します。ただし、[前提条件](#prerequisites)で示されている資格情報を使用して認証を行わないと、これを実現することはできません。**Set-AzureAccount** コマンドレットでこれを行います。

1.  MyFirstRunbook-Textual ウィンドウで **[編集]** をクリックして、テキスト エディターを開きます。<br> ![Runbook の編集](media/automation-first-runbook-textual/runbook-toolbar-edit.png) 
2.  **Write-Output** の行は不要になったので削除します。
3.  中かっこ内の空白行にカーソルを置きます。
3.  ライブラリ コントロールで、**[資産]**、**[資格情報]** の順に展開します。
4.  資格情報を右クリックし、**[キャンバスに追加]** をクリックします。これにより、資格情報の **Get AutomationCredential** アクティビティが追加されます。
5.  **Get-AutomationCredential** の前に「*$Credential =*」と入力し、変数に資格情報を割り当てます。 
3.  次の行に「*Add-AzureAccount -Credential $Credential*」と入力します。<br> ![認証](media/automation-first-runbook-textual/authentication.png) 
3. **テスト ウィンドウ**をクリックして、Runbook をテストできるようにします。
10. **[開始]** をクリックしてテストを開始します。完了すると、次のような出力により、資格情報内のユーザーに関する情報が返されます。これは、資格情報が有効であることの確認になります。<br> ![認証](media/automation-first-runbook-textual/authentication-test.png) 

## 手順 6 - 仮想マシンを開始するコードを追加する

これで、Runbook で Azure サブスクリプションに対する認証が行われ、リソースを管理できるようになります。仮想マシンを起動するコマンドを追加します。Azure サブスクリプション内の任意の仮想マシンを選択し、ここではその名前をコマンドレットにハードコーディングします。


1. *Add-AzureAccount* の後に、「*Start-AzureVM -Name 'VMName' -ServiceName 'VMServiceName'*」と入力して起動する仮想マシンの名前とサービス名を指定します。<br> ![認証](media/automation-first-runbook-textual/start-azurevm.png) 
9. Runbook を保存し、**テスト ウィンドウ**をクリックしてテストします。
10. **[開始]** をクリックしてテストを開始します。完了したら、仮想マシンが開始されたことを確認します。


## 手順 7 - Runbook に入力パラメーターを追加する

この Runbook では、Runbook にハードコードした仮想マシンを開始しますが、Runbook を開始するときに仮想マシンを指定できるようにすればもっと便利です。そこで次に、入力パラメーターを Runbook に追加して、その機能を提供します。

1. *VMName* と *VMServiceName* のパラメーターを Runbook に追加して、次の図に示すように、**Start-AzureVM** コマンドレットでこれらの変数を使用します。<br> ![認証](media/automation-first-runbook-textual/params.png) 
9. Runbook を保存してテスト ウィンドウを開きます。テストで使用される 2 つの入力変数の値を指定できることに注意してください。 
11.  テスト ウィンドウを閉じます。
12.  **[発行]** をクリックして、新しいバージョンの Runbook を発行します。
13.  前の手順で開始した仮想マシンを停止します。
13.  **[開始]** をクリックして Runbook を開始します。開始する仮想マシンの **VMName** と **VMServiceName** を入力します。<br> ![Runbook の開始](media/automation-first-runbook-textual/start-runbook-input-params.png) 

14.  Runbook が完了したら、仮想マシンが開始されたことを確認します。


## 関連記事:

- [初めてのグラフィカルな Runbook](automation-first-runbook-graphical.md)

<!---HONumber=August15_HO7-->