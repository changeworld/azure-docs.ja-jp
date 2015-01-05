<properties urlDisplayName="Publishing with Visual Studio Online" pageTitle="Visual Studio Online を使用した Azure への継続的な配信" metaKeywords="" description="Learn how to configure your Visual Studio Online team projects to automatically build and deploy to Azure websites or cloud services." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Continuous delivery to Azure using Visual Studio Online and Git" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen" />




# Visual Studio Online と Git を使用した Azure への継続的な配信

Visual Studio Online チーム プロジェクトを使用してソース コードの Git リポジトリをホストし、コミットをリポジトリにプッシュするたびに自動的にビルドして Azure の Web サイトまたはクラウド サービスにデプロイすることができます。

Visual Studio 2013 および Azure SDK をインストールする必要があります。Visual Studio 2013 がない場合は、[www.visualstudio.com]** で無料利用の開始**リンクをクリックしてダウンロードします(http://www.visualstudio.com)。Azure SDK を[ここ]からインストールしてください(http://go.microsoft.com/fwlink/?LinkId=239540)。


<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>このチュートリアルを完了するには、Visual Studio Online アカウントが必要です。</h5>
<p>Visual Studio Online アカウントは <a href="http://go.microsoft.com/fwlink/p/?LinkId=512979">無料で開くことができます</a>。</p>
</div>

Visual Studio Online を使用してクラウド サービスを自動的にビルドして Azure にデプロイするようにセットアップするには、次の手順に従います。

-   [手順 1: Git リポジトリを作成する。][]

-   [手順 2: プロジェクトを作成して Git リポジトリにプッシュする。][]

-   [手順 3: プロジェクトを Azure に接続する。][]

-   [手順 4: 変更を行い、リビルドと再デプロイをトリガーする。][]

-   [手順 5: 以前のビルドを再デプロイする (省略可能)][]

-   [手順 6: 運用デプロイを変更する][]

-	[手順 7: working 分岐からデプロイする][]

<h2> <a name="step1"></a>手順 1.Git リポジトリを作成する</h2>


1. まだ Visual Studio Online アカウントを持っていない場合は、[こちら]の指示に従ってください(http://go.microsoft.com/fwlink/?LinkId=397665)。チーム プロジェクトを作成するときに、ソース管理システムとして Git を選択します。指示に従い、Visual Studio をチーム プロジェクトに接続します。

2. チーム エクスプローラーで、**[このリポジトリを複製]** リンクを選択します。 
![][3]

3. ローカル コピーの場所を指定し、**[複製]** を選択します。
 
<h2><a name="step2"> </a>手順 2.プロジェクトを作成してリポジトリにコミットする</h2>

1. チーム エクスプローラーの [ソリューション] セクションで、[新規] リンクを選択して新しいプロジェクトをローカル リポジトリに作成します。<br/>
![][4]

2. このチュートリアルの手順に従って、Web サイトまたはクラウド サービス (Azure アプリケーション) をデプロイできます。
新しい Microsoft Azure Cloud Service プロジェクトまたは新しい ASP.NET MVC プロジェクトを作成します。プロジェクトの対象が .NET Framework 4 または 4.5 であることを確認してください。クラウド サービス プロジェクトを作成する場合は、ASP.NET MVC Web ロールと worker ロールを追加します。
Web サイトを作成する場合は、ASP.NET Web アプリケーション プロジェクトのテンプレートを選択し、次に MVC を選択します。「[Azure Websites と ASP.NET を使用する](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-dotnet-get-started/)。

3. ソリューションのショートカット メニューを開き、**[コミット]** を選択します。<br/>
![][7]

4. Visual Studio Online で初めて Git を使用する場合は、Git で自分自身が識別されるように情報を提供する必要があります。チーム エクスプローラーの **[保留中の変更]** 領域で、ユーザー名と電子メール アドレスを入力します。コミットのコメントを入力し、**[コミット]** を選択します。<br/>
![][8]

5. チェックイン時に特定の変更を含める、または除外するオプションに注意してください。必要な変更が除外されている場合は、**[すべて含む]** を選択します。<br/>

6. これで、リポジトリのローカル コピーに変更をコミットできました。次に、これらの変更をサーバーと同期させます。**[同期]** リンクを選択します。

<h2> <a name="step3"> </a>手順 3.プロジェクトを Azure に接続する</h2>

1. ソース コードを含む Git リポジトリが Visual Studio Online に保持され、Git リポジトリを Azure に接続する準備が整いました。[Azure ポータル](http://manage.windowsazure.com)で、クラウド サービスまたは Web サイトを選択するか、新たに作成します。作成するには、左下にある [+] アイコンを選択し、**[クラウド サービス]** または **[Web サイト]**を選択して、**[簡易作成]** を選択します。<br.>
![][9]

3. クラウド サービスの場合は、**[Visual Studio Online を使用した発行の設定]** リンクを選択します。Web サイトの場合は、**[ソース管理からのデプロイの設定]** リンクを選択します。<br/>
![][10]

2. ウィザードで、テキスト ボックスに Visual Studio Online アカウントの名前を入力し、**[今すぐ承認]** リンクを選択します。サインインを求められることがあります。<br/>
![][11]

3. [OAuth] ポップアップ ダイアログで、**[承認]** を選択して、Azure で Visual Studio Online のチーム プロジェクトを構成することを許可します。<br/>
![][12]

4. 承認が成功すると、Visual Studio Online チーム プロジェクトが含まれるドロップダウン リストが表示されます。前のステップで作成したチーム プロジェクトの名前を選択し、ウィザードのチェック マーク ボタンを選択します。<br/>
![][13]

次にコミットをリポジトリにプッシュするときに、Visual Studio Online はプロジェクトをビルドして Azure にデプロイします。<br/>


<h2><a name="step4"> </a>手順 4.リビルドをトリガーし、プロジェクトを再デプロイする</h2>

1. Visual Studio で、ファイルを開き、変更します。たとえば、MVC Web ロールの Views\Shared フォルダーにある _Layout.cshtml ファイルを変更します。<br/>
![][17]

2. サイトのフッター テキストを編集し、そのファイルを保存します。<br/>
![][18]

3. ソリューション エクスプローラーで、ソリューション ノード、プロジェクト ノード、または変更したファイルのショートカット メニューを開き、**[コミット]** を選択します。<br/>

4. コメントを入力し、**[コミット]** を選択します。<br/>
![][20]

5. **[同期]** リンクを選択します。<br/>
![][38]

6. **[プッシュ]** リンクを選択してコミットを Visual Studio Online のリポジトリにプッシュします。(また、**[同期]** を使用してコミットをリポジトリにコピーすることもできますその違いは、**[同期]** ではリポジトリから最新の変更が取得されることです)。<br/>
![][39]

7. [ホーム] ボタンを選択して、チーム エクスプローラーのホーム ページに戻ります。<br/>
![][21]

8. **[ビルド]** を選択して処理中のビルドを表示します。<br/>
![][22]
<br/>
チーム エクスプローラーに、チェックインのためにビルドが開始されたことが表示されます。<br/>
![][23]

9. ビルドの処理に応じて詳細なログを表示するには、処理中のビルドの名前をダブルクリックします。<br/>

10. ビルドの処理中に、Azure にリンクするためのウィザードを使用したときに作成されたビルド定義を調べます。ビルド定義のショートカット メニューを開き、**[ビルド定義の編集]** を選択します。<br/>
![][25]
<br/>
**[トリガー]** タブを見ると、既定ではチェックインごとにビルドを行うようにビルド定義が設定されていることがわかります。クラウド サービスの場合は、Visual Studio Online により master 分岐がビルドされてステージング環境に自動的にデプロイされます。その場合も、ライブ サイトにデプロイするための手動による手順を実行する必要があります。ステージング環境がない Web サイトの場合は、master 分岐が直接ライブ サイトにデプロイされます。<br/>
![][26]
<br/>
**[プロセス]** タブを見ると、デプロイ環境がクラウド サービスまたは Web サイトの名前に設定されていることがわかります。<br/>
![][27]
<br/>
既定値と異なる値を使用する場合は、プロパティに対して希望の値を指定します。Azure の発行のプロパティは [デプロイ] セクションにあり、MSBuild パラメーターの設定が必要な場合もあります。たとえば、クラウド サービス プロジェクトで "クラウド" 以外のサービス構成を指定する場合は、MSbuild パラメーターに /p:TargetProfile=*YourProfile* を設定します。ここで *YourProfile* は ServiceConfiguration.*YourProfile*.cscfg のような名前のサービス構成ファイルに一致します。
次の表は、[展開] セクションで使用可能なプロパティを示しています。
	<table>
<tr><td><b>プロパティ</b></td><td><b>既定値</b></td></tr>
><tr><td>信頼されていない証明書を許可</td><td>false の場合、ルート証明機関により SSL 証明書に署名する必要があります。</td></tr>
<tr><td>アップグレードの許可</td><td>新規作成の代わりに、既存のデプロイを更新するデプロイを許可します。IP アドレスを保持します。</td></tr>
><tr><td>削除しない</td><td>true の場合は、既存の無関係のデプロイを上書きしません (アップグレードは許可されます)。</td></tr>
<tr><td>配置設定へのパス</td><td>リポジトリのルート フォルダーを基準にした .pubxml ファイルまたは Web サイトへのパス。クラウド サービスの場合は無視されます。</td></tr>
<tr><td>SharePoint 配置環境</td><td>サービス名と同じ</td></tr>
<tr><td>Windows Azure 配置環境</td><td>Web サイトまたはクラウド サービスの名前</td></tr>
</table>
<br/>

11. このころまでには、ビルドが正常に完了しています。<br/>
![][28]

12. ビルド名をダブルクリックすると、関連付けられた単体テスト プロジェクトのテスト結果を含む **[ビルドの概要]** が表示されます。<br/>
![][29]

13. [Azure ポータル](http://manage.windowsazure.com)では、ステージング環境が選択されると、関連付けられたデプロイが [デプロイ] タブに表示されます。<br/>
![][30]

14.	目的のサイトの URL に移動します。Web サイトの場合は、ポータルの **[参照]** をクリックします。クラウド サービスの場合は、ステージング環境が表示される **[ダッシュボード]** ページの **[概要]** セクションで URL を選択します。クラウド サービス向けの継続的な統合からのデプロイは、既定ではステージング環境に発行されます。代替クラウド サービス環境プロパティを [運用] に設定することで、これを変更できます。ここでは、クラウド サービスのダッシュボード ページにサイト URL が表示されます。 <br/>
![][31]
<br/>
新しいブラウザー タブが開いて、実行中のサイトが表示されます。<br/>
![][32]

15.	プロジェクトにその他の変更を加えると、さらにビルドが実行され、複数のデプロイが累積されます。最新のデプロイは [アクティブ] とマークされます。<br/>
![][33]

<h2> <a name="step5"> </a>手順 5.以前のビルドを再デプロイする</h2>

この手順は省略可能です。管理ポータルで、以前のデプロイを選択し、**[再デプロイ]** をクリックしてサイトを以前のチェックインに戻します。これによって、TFS で新しいビルドが開始され、デプロイ履歴に新しいエントリが作成されます。<br/>
![][34]

<h2> <a name="step6"> </a>手順 6.運用デプロイを変更する</h2>

 準備が整ったら、管理ポータルで **[スワップ]** を選択してステージング環境を運用環境へ昇格できます。新たにデプロイされたステージング環境は運用に昇格され、以前の運用環境がある場合、運用環境はステージング環境になります。運用環境とステージング環境でアクティブなデプロイは異なることはありますが、最近のビルドのデプロイ履歴は環境にかかわらず同じです。<br/>
![][35]

<h2> <a name="step7"> </a>手順 6.working 分岐からデプロイする</h2>

Git を使用する場合は、通常、working 分岐で変更を行い、開発が完了状態に到達したときに master 分岐に統合します。プロジェクトの開発フェーズの間に、この working 分岐をビルドして Azure にデプロイします。

1. チーム エクスプローラーで、**[ホーム]** ボタンを選択し、**[分岐]** を選択します。<br/>
![][40]

2. **[新しい分岐]** リンクを選択します。<br/>
![][41]

3. "working" など分岐の名前を入力し、**[分岐の作成]** をクリックします。これにより、新しいローカル分岐が作成されます。<br/>
![][42]

4. 分岐を発行します。**[発行されていない分岐]** で分岐名を選択し、**[発行]** をクリックします。<br/>
![][44]

6. 既定では、master 分岐が変更された場合にのみ、継続的なビルドがトリガーされます。working 分岐に対して継続的なビルドを設定するには、チーム エクスプローラーの [ビルド] ページで **[ビルド定義の編集]** をクリックします。

7. **[ソースの設定]** タブをクリックします。**[継続的インテグレーションとビルドの監視された分岐]** で、**[新しい行を追加するにはここをクリックしてください]** をクリックします。<br/>
![][47]

8. refs/heads/working など作成した分岐を指定します。
![][48]

9. コードに変更を加え、変更したファイルのショートカット メニューを開き、**[コミット]** をクリックします。<br/>
![][43]

10. **[同期されていないコミット]** リンクを選択し、**[同期]** を選択するか、**[プッシュ]** リンクを選択して、Visual Studio Online の working 分岐のコピーに変更をコピーします。
![][45]

11. **[ビルド]** ビューに移動して、working 分岐に対してトリガーされたビルドを見つけます。

詳細については、[Visual Studio Online に関するページ](http://go.microsoft.com/fwlink/?LinkId=253861)。Visual Studio Online で Git を使用する場合のその他のヒントについては、「[Git でコードを共有する](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) 」を参照してください。Visual Studio Online によって管理されていない Git リポジトリを使用した Azure への発行の詳細については、「[Publishing to Azure Websites with Git (Git を使用した Azure Websites への発行)](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-publish-source-control)」を参照してください。

[手順 1: Git リポジトリを作成する。]: #step1
[手順 2: プロジェクトを作成して Git リポジトリにプッシュする。]: #step2
[手順 3: プロジェクトを Azure に接続する。]: #step3
[手順 4: 変更を行い、リビルドと再デプロイをトリガーする。]: #step4
[手順 5: 以前のビルドを再デプロイする (省略可能)]: #step5
[手順 6: 運用デプロイを変更する]: #step6
[手順 7: working 分岐からデプロイする]: #step7
[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG

<!--HONumber=35.1-->
