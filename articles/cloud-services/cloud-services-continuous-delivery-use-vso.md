<properties 
	pageTitle="Visual Studio Online を使用した Azure への継続的な配信" 
	description="Visual Studio Online チーム プロジェクトを自動的にビルドして Azure App Service の Web アプリ機能またはクラウド サービスにデプロイするための構成方法について説明します。" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/08/2015" 
	ms.author="kempb"/>


# Continuous delivery to Azure using Visual Studio Online (Visual Studio Online を使用した Azure への継続的な配信)

  Visual Studio Online チーム プロジェクトを、自動的にビルドして Azure の Web アプリまたはクラウド サービスにデプロイするように構成できます*内部設置型* Team Foundation Server を使用した継続的なビルドおよびデプロイ システムのセットアップ方法については、「[Continuous Delivery for Cloud Applications in Azure (Azure でのクラウド サービスの継続的な配信)](cloud-services-dotnet-continuous-delivery.md)」を参照してください。

このチュートリアルは、Visual Studio 2013 と Azure SDK がインストール済みであることを前提としています。Visual Studio 2013 がない場合は、**www.visualstudio.com** で[無料利用の開始](http://www.visualstudio.com)リンクをクリックしてダウンロードします。Azure SDK を[ここ](http://go.microsoft.com/fwlink/?LinkId=239540)からインストールしてください。

> [AZURE.NOTE]このチュートリアルを完了するには、Visual Studio Online のアカウントが必要です。[Visual Studio Online アカウントは無料で開く](http://go.microsoft.com/fwlink/p/?LinkId=512979)ことができます。

Visual Studio Online を使用してクラウド サービスを自動的にビルドして Azure にデプロイするようにセットアップするには、次の手順に従います。

-   [手順 1. チーム プロジェクトを作成する。][]

-   [手順 2. プロジェクトをソース管理にチェックインする。][]

-   [手順 3. プロジェクトを Azure に接続する。][]

-   [手順 4. 変更を行い、リビルドと再デプロイをトリガーする。][]

-   [手順 5. 以前のビルドを再デプロイする (省略可能)][]

-   [手順 6. 運用デプロイを変更する (クラウド サービスのみ)][]

-	[手順 7. 単体テストを実行する (省略可能)][]

## <a name="step1"></a>手順 1: チーム プロジェクトを作成する

[この](http://go.microsoft.com/fwlink/?LinkId=512980)手順に従って、チーム プロジェクトを作成し、Visual Studio にリンクさせます。このチュートリアルでは、ソース管理ソリューションとして Team Foundation バージョン管理 (TFVC) を使用していることを前提としています。バージョン管理に Git を使用する場合は、「[このチュートリアルの Git バージョン](http://go.microsoft.com/fwlink/p/?LinkId=397358)」を参照してください。

## <a name="step2"></a>手順 2: プロジェクトをソース管理にチェックインする

1. Visual Studio で、デプロイするソリューションを開くか、新しいソリューションを作成します。このチュートリアルの手順に従って、Web アプリまたはクラウド サービス (Azure アプリケーション) をデプロイできます。新しいソリューションを作成する場合は、新しい Azure クラウド サービス プロジェクト、または新しい ASP.NET MVC プロジェクトを作成します。プロジェクトが .NET Framework 4 または 4.5 をターゲットにしていることを確認し、クラウド サービス プロジェクトを作成している場合は、ASP.NET MVC の Web ロールおよび worker ロールのプロジェクトを追加して、Web ロールに対応するインターネット アプリケーションを選択します。確認メッセージが表示されたら、**[インターネット アプリケーション]** を選択します。Web アプリを作成する場合は、ASP.NET Web アプリケーション プロジェクトのテンプレートを選択し、次に MVC を選択します。「[Azure アプリ サービスでの ASP.NET Web アプリの作成](../web-sites-dotnet-get-started.md)」を参照してください。

> [AZURE.NOTE]Visual Studio Online では、現在、Visual Studio Web アプリケーションの CI デプロイメントのみをサポートしています。Web サイト プロジェクトはサポート対象外です。


2. ソリューションのコンテキスト メニューを開き、**[ソリューションをソース管理に追加]** を選択します。<br/> ![][5]

3. 既定値を受け入れるか変更し、**[OK]** を選択します。処理が完了すると、ソース管理アイコンがソリューション エクスプローラーに表示されます。<br/> ![][6]

4. ソリューションのショートカット メニューを開き、**[チェックイン]** を選択します。<br/> ![][7]

5. チーム エクスプローラーの [保留中の変更] にチェックインのコメントを入力し、**[チェックイン]** を選択します。<br/> ![][8]

<br/> チェックインをするときは、特定の変更を含むまたは除外するためのオプションに注意してください。必要な変更が除外されている場合は、**[すべて含む]** を選択します。<br/> ![][9]

## <a name="step3"></a>手順 3: プロジェクトを Azure に接続する

1. ソース コードが含まれる VSO チーム プロジェクトが完成したので、チーム プロジェクトを Azure に接続することができます。[Azure ポータル](http://manage.windowsazure.com)で、クラウド サービスまたは Web アプリを選択するか、新たに作成します。作成するには、左下にある [+] アイコンを選択し、[**クラウド サービス**] または [**Web アプリ**] を選択して、[**簡易作成**] を選択します。**[Visual Studio Online 発行の設定]** を選択します。<br/> ![][10]

2. ウィザードで、テキスト ボックスに Visual Studio Online アカウントの名前を入力し、**[今すぐ承認]** をクリックします。サインインを求められることがあります。<br/> ![][11]

3. [OAuth] ポップアップ ダイアログで、**[承諾]** を選択して Azure が VSO でチーム プロジェクトを構成することを許可します。<br/> ![][12]

4. 承認が成功すると、Visual Studio Online チーム プロジェクトのリストが含まれるドロップダウンが表示されます。前のステップで作成したチーム プロジェクトの名前を選択し、ウィザードのチェック マーク ボタンを選択します。<br/> ![][13]

5. プロジェクトがリンクされると、変更を Visual Studio Online チーム プロジェクトにチェックインするための手順が表示されます。次のチェックイン時に、Visual Studio Online はプロジェクトをビルドして Azure にデプロイします。これを試すには、**[Visual Studio 2012 からチェックインする]** をクリックし、**[Visual Studio 2012 を起動]** をクリックします (またはこれと同等の、ポータル画面下部にある **[Visual Studio]** をクリックします)。<br/> ![][14]

## <a name="step4"></a>手順 4: リビルドをトリガーし、プロジェクトを再デプロイする

1. Visual Studio のチーム エクスプローラーで、**[ソース管理エクスプローラー]** をクリックします。<br/> ![][15]

2. ソリューション ファイルの保存場所に移動し、ファイルを開きます。<br/> ![][16]

3. ソリューション エクスプローラーで、ファイルを開き、変更します。たとえば、MVC Web ロールの Views\Shared フォルダーにある _Layout.cshtml ファイルを変更します。<br/> ![][17]

4. サイトのロゴを編集し、Ctrl + S キーを押してファイルを保存します。<br/> ![][18]

5. チーム エクスプローラーで、**[保留中の変更]** を選択します。<br/> ![][19]

6. コメントを入力し、**[チェックイン]** を選択します。<br/> ![][20]

7. [ホーム] を選択して、チーム エクスプローラーのホーム ページに戻ります。<br/> ![][21]

8. **[ビルド]** を選択して、処理中のビルドを表示します。<br/> ![][22] <br/> チーム エクスプローラーに、チェックインのためにビルドが開始されたことが表示されます。<br/> ![][23]

9. 処理中のビルドの名前をダブルクリックして、ビルドの進行に合わせて詳細ログを表示します。<br/> ![][24]

10. ビルドの処理中、ウィザードを使用して TFS を Azure にリンクしたときに作成されたビルド定義を調べてください。ビルド定義のショートカット メニューを開き、**[ビルド定義の編集]** を選択します。<br/> ![][25] <br/> **[トリガー]** タブを見ると、既定ではチェックインごとにビルドを行うようにビルド定義が設定されていることがわかります。<br/> ![][26]<br/>[**プロセス**] タブを見ると、デプロイ環境がクラウド サービスまたは Web アプリの名前に設定されていることがわかります。Web アプリを操作している場合は、ここで表示されているのとは異なるプロパティが表示されます。<br/> ![][27] <br/> 既定値と異なる値を使用する場合は、プロパティに対して希望の値を指定します。Azure 発行のプロパティは [展開] セクションにあります。次の表は、[展開] セクションで使用可能なプロパティを示しています。
	<table>
<tr><td><b>プロパティ</b></td><td><b>既定値</b></td></tr>
><tr><td>信頼されていない証明書を許可</td><td>false の場合、SSL 証明書はルート証明機関によって署名される必要があります。</td></tr>
<tr><td>アップグレードの許可</td><td>新しい展開を作成せずに既存の展開を更新することを許可します。IP アドレスを保持します。</td></tr><tr><td>削除しない</td><td>true の場合、既存の関連のない展開を上書きしません (アップグレードは許可)。</td></tr>
<tr><td>展開設定へのパス</td><td>リポジトリのルート フォルダーを基準とした Web アプリの .pubxml ファイルへのパス。クラウド サービスでは無視されます。</td></tr>
<tr><td>Sharepoint 展開環境</td><td>サービス名と同じ</td></tr>
<tr><td>Azure 配置環境</td><td>Web アプリ名またはクラウド サービス名</td></tr>
</table> <br/>

複数のサービス構成 (.cscfg ファイル) を使用している場合は、**[Build、Advanced、MSBuild 引数]** 設定で目的のサービス構成を指定できます。たとえば、ServiceConfiguration.Test.cscfg を使用するには、MSBuild 引数ライン オプション /p:TargetProfile=Test を設定します。<br/> ![][38]

11. この時点で、ビルドは正常に完了しています。<br/> ![][28]

12. ビルド名をダブルクリックすると、関連付けられた単体テスト プロジェクトのテスト結果を含む **[ビルドの概要]** が表示されます。<br/> ![][29]

13. [Azure ポータル](http://manage.windowsazure.com)では、ステージング環境が選択されると、関連付けられたデプロイが [デプロイ] タブに表示されます。<br/> ![][30]

14.	目的のサイトの URL に移動します。Web アプリの場合は、コマンド バーの参照ボタンをクリックします。クラウド サービスのステージング環境を示す **[ダッシュボード]** ページの **[概要]** セクションで URL を選択します。クラウド サービス向けの継続的な統合からのデプロイは、既定ではステージング環境に発行されます。代替クラウド サービス環境プロパティを [運用] に設定することで、これを変更できます。このスクリーンショットでは、クラウド サービスのダッシュボード ページにサイト URL が表示されています。<br/> ![][31] <br/> 新しいブラウザー タブが開いて、実行中のサイトが表示されます。<br/> ![][32]

15.	クラウド サービスの場合は、プロジェクトにその他の変更を加えると、さらにビルドが実行され、複数のデプロイが累積されます。最新のデプロイは [アクティブ] とマークされます。<br/> ![][33]

## <a name="step5"></a>手順 5: 以前のビルドを再デプロイする

この手順の内容は、クラウド サービスのみに適用され、省略可能です。管理ポータルで以前のデプロイを選択し、**[再デプロイ]** をクリックしてサイトを以前のチェックインに戻します。これによって、TFS で新しいビルドが開始され、デプロイ履歴に新しいエントリが作成されます。<br/> ![][34]

## <a name="step6"></a>手順 6: 運用デプロイを変更する

この手順の内容は、クラウド サービスのみに適用され、Web アプリには適用されません。準備が整ったら、管理ポータルで [スワップ] を選択してステージング環境を運用環境へ昇格できます。新たにデプロイされたステージング環境は運用に昇格され、以前の運用環境がある場合、運用環境はステージング環境になります。運用環境とステージング環境でアクティブなデプロイが異なることはありますが、最近のビルドのデプロイ履歴は環境にかかわらず同じです。<br/> ![][35]

## <a name="step7"></a>手順 7: 単体テストの実行

この手順の内容は、Web アプリのみに適用され、クラウド サービスには適用されません。デプロイにクオリティ ゲートを配置するには、単体テストを実行できます。失敗した場合は、デプロイを停止できます。

1.  Visual Studio で、単体テスト プロジェクトを追加します。<br/> ![][39]

2.  テストするプロジェクトにプロジェクト参照を追加します。<br/> ![][40]

3.  複数の単体テストを追加します。開始するには、常に合格するダミーのテストを試してみてください。

		using System;
		using Microsoft.VisualStudio.TestTools.UnitTesting;
		
		namespace UnitTestProject1
		{
		    [TestClass]
		    public class UnitTest1
		    {
		        [TestMethod]
		        [ExpectedException(typeof(NotImplementedException))]
		        public void TestMethod1()
		        {
		            throw new NotImplementedException();
		        }
		    }
		}

4.  ビルド定義を編集し、[プロセス] タブを選択し、テスト·ノードを展開します。


5.  **[テスト失敗時にビルドを失敗させる]** を True に設定します。これは、テストに合格しない限り、デプロイが行われないことを意味します。<br/> ![][41]

6.  新しいビルドがキューに追加されます。<br/> ![][42] <br/> ![][43]

7. ビルドの進行中は、進行状況を確認します。<br/> ![][44] <br/> ![][45]

7. ビルドが完了したら、テスト結果を確認します。<br/> ![][46] <br/> ![][47]

8.  失敗するテストを作成してみます。最初のテストをコピーして新しいテストを追加し、その名前を変更して、NotImplementedException が予期される例外であるコード行をコメントアウトします。

		[TestMethod]
		//[ExpectedException(typeof(NotImplementedException))]
		public void TestMethod2()
		{
		    throw new NotImplementedException();
		}

9. 変更をチェックインし、新しいビルドをキューに追加します。<br/> ![][48]

10. テスト結果を表示して失敗の詳細を確認します。<br/> ![][49] <br/> ![][50]

Visual Studio Online での単位テストの詳細については、[「ビルド プロセスでのテストの実行」](http://go.microsoft.com/fwlink/p/?LinkId=510474) を参照してください。

詳細については、[Visual Studio Online に関するページ](http://go.microsoft.com/fwlink/?LinkId=253861)を参照してください。Git を使用している場合は、「[Git でコードを共有する](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx)」および「[Azure App Service での GIT による継続的なデプロイ](../web-sites-publish-source-control.md)」を参照してください。

[手順 1. チーム プロジェクトを作成する。]: #step1
[手順 2. プロジェクトをソース管理にチェックインする。]: #step2
[手順 3. プロジェクトを Azure に接続する。]: #step3
[手順 4. 変更を行い、リビルドと再デプロイをトリガーする。]: #step4
[手順 5. 以前のビルドを再デプロイする (省略可能)]: #step5
[手順 6. 運用デプロイを変更する (クラウド サービスのみ)]: #step6
[手順 7. 単体テストを実行する (省略可能)]: #step7
[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png


[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
 

<!---HONumber=July15_HO4-->