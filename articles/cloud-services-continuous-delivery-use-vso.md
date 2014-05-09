<properties linkid="dev-net-common-tasks-publishing-with-vso" urlDisplayName="TFS を使用した発行" pageTitle="Visual Studio Online を使用した Azure への継続的な配信" metaKeywords="" description="Visual Studio Online チーム プロジェクトを、自動的にビルドして Azure の Web サイトまたはクラウド サービスにデプロイするように構成できます。" metaCanonical="" services="web-sites" documentationCenter=".NET" title="Visual Studio Online を使用した Azure への継続的な配信" authors="ghogen" solutions="" manager="" editor="" />




#Visual Studio Online を使用した Azure への継続的な配信

Visual Studio Online (旧 Team Foundation Service) は、Microsoft のよく知られた Team Foundation Server (TFS) ソフトウェアのクラウド ホステッド サービス バージョンで、高度にカスタマイズ可能なソース コードおよびビルド管理、アジャイル開発およびチーム プロセスのワークフロー、問題および作業項目の追跡などの機能を提供します。Visual Studio Online チーム プロジェクトを、自動的にビルドして Azure の Web サイトまたはクラウド サービスにデプロイするように構成できます。内部設置型 Team Foundation Server を使用した継続的なビルドおよびデプロイ システムのセットアップ方法については、「[Azure でのクラウド サービスの継続的な配信に関するページ](../cloud-services-dotnet-continuous-delivery)」 を参照してください。

このチュートリアルは、Visual Studio 2013 と Azure SDK がインストール済みであることを前提としています。Visual Studio 2013 がない場合は、[www.visualstudio.com](http://www.visualstudio.com) で**無料利用の開始**リンクをクリックしてダウンロードします。Azure SDK を[ここ](http://go.microsoft.com/fwlink/?LinkId=239540)からインストールしてください。

Visual Studio Online を使用してクラウド サービスを自動的にビルドして Azure にデプロイするようにセットアップするには、次の手順に従います。

-   [手順 1. Visual Studio Online にサインアップする。][]

-   [手順 2. プロジェクトをソース管理にチェックインする。][]

-   [手順 3. プロジェクトを Azure に接続する。][]

-   [手順 4. 変更を行い、リビルドと再デプロイをトリガーする。][]

-   [手順 5. 以前のビルドを再デプロイする (省略可能)][]

-   [手順 6. 運用デプロイを変更する (クラウド サービスのみ)][]

<h2> <a name="step1"></a><span class="short-header">Visual Studio Online へのサインアップ</span>手順 1. Visual Studio Online へのサインアップ</h2>

1. [http://www.visualstudio.com](http://www.visualstudio.com) に移動して Visual Studio Online アカウントを作成します。**[サインイン]** リンクをクリックします。
  Microsoft アカウントを使用してサインインする必要があります。初めてサインインする場合は、名前や電子メール アドレスなど、自分に関する情報の入力を求められます。
![][0]
  
2. 初めてのサインインではない場合は、サインインするときにこの画面が表示されます。**無料アカウントを今すぐ作成** リンクをクリックします。<br/>
![][36]

3. 新しいプロジェクトに対応するアカウント URL を作成します。アカウントは https://&lt;accountname&gt;.visualstudio.com のような形式です。<br/>
![][37]
 
4. これで、最初のプロジェクトを作成できるようになりました。プロジェクトの名前と説明を入力します。次に、使用するバージョン管理システムを選択します。Team Foundation バージョン管理 (TFVC) と Git はいずれもサポートされています。これらのオプションの詳細については、「[バージョン管理の使用](http://go.microsoft.com/fwlink/?LinkId=324037)」を参照してください。このチュートリアルでは、TFVC を使用していることを前提としています。その後、組織で使用するプロセス テンプレートを選択して、**[プロジェクトを作成する]** をクリックします。プロセス テンプレートの詳細については、「[チーム プロジェクト成果物の操作、プロセス テンプレートの選択](http://go.microsoft.com/fwlink/?LinkId=324035)」を参照してください。<br/>
![][1]

5. プロジェクトの作成が完了したら、**[Visual Studio で開いて接続する]** をクリックすると、Visual Studio が自動的に起動し、チーム プロジェクトに接続します。セキュリティ ダイアログ ボックスが表示された場合は、[許可] を選択します。<br/>
![][2]

<h2><a name="step2"> </a><span class="short-header">プロジェクトをソース管理にチェックイン</span>手順 2. プロジェクトをソース管理にチェックイン</h2>

1. Visual Studio で、デプロイするソリューションを開くか、新しいソリューションを作成します。
このチュートリアルの手順に従って、Web サイトまたはクラウド サービス (Azure アプリケーション) をデプロイできます。
新しいソリューションを作成する場合は、新しい Azure クラウド サービス プロジェクト、または新しい ASP.NET MVC プロジェクトを作成します。プロジェクトが .NET Framework 4 または 4.5 をターゲットにしていることを確認し、クラウド サービス プロジェクトを作成している場合は、ASP.NET MVC の Web ロールおよび worker ロールのプロジェクトを追加して、Web ロールに対応するインターネット アプリケーションを選択します。確認メッセージが表示されたら、**[インターネット アプリケーション]** を選択します。
Web サイトを作成する場合は、ASP.NET Web アプリケーション プロジェクトのテンプレートを選択し、次に MVC を選択します。[Azure と ASP.NET の使用に関するページ](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-dotnet-get-started/)を参照してください。

2. ソリューションのコンテキスト メニューを開き、**[ソリューションをソース管理に追加]** を選択します。<br/>
![][5]

3. 既定値を受け入れるか変更し、**[OK]** を選択します。処理が完了すると、ソース管理アイコンがソリューション エクスプローラーに表示されます。<br/>
![][6]

4. ソリューションのショートカット メニューを開き、**[チェックイン]** を選択します。<br/>
![][7]

5. チーム エクスプローラーの [保留中の変更] にチェックインのコメントを入力し、**[チェックイン]** を選択します。<br/>
![][8]

<br/>
チェックイン時に特定の変更を含める、または除外するオプションに注意してください。必要な変更が除外されている場合は、**[すべて含む]** を選択します。<br/>
![][9]

<h2> <a name="step3"> </a><span class="short-header">プロジェクトを Azure に接続</span>手順 3. プロジェクトを Azure に接続</h2>

1. ソース コードが含まれる VSO チーム プロジェクトが完成したので、チーム プロジェクトを Azure に接続することができます。[Azure ポータル](http://manage.windowsazure.com)で、クラウド サービスまたは Web サイトを選択するか、新たに作成します。作成するには、左下にある [+] アイコンを選択し、**[クラウド サービス]** または **[Web サイト]** を選択して、**[簡易作成]** を選択します。**[Visual Studio Online 発行の設定]** を選択します。<br/>
![][10]

2. ウィザードで、テキスト ボックスに Visual Studio Online アカウントの名前を入力し、**[今すぐ承認]** をクリックします。サインインを求められることがあります。<br/>
![][11]

3. [OAuth] ポップアップ ダイアログで、**[承諾]** を選択して Azure が VSO でチーム プロジェクトを構成することを許可します。<br/>
![][12]

4. 承認が成功すると、Visual Studio Online チーム プロジェクトのリストが含まれるドロップダウンが表示されます。前のステップで作成したチーム プロジェクトの名前を選択し、ウィザードのチェック マーク ボタンを選択します。<br/>
![][13]

5. プロジェクトがリンクされると、変更を Visual Studio Online チーム プロジェクトにチェックインするための手順が表示されます。次のチェックイン時に、Visual Studio Online はプロジェクトをビルドして Azure にデプロイします。これを試すには、**[Visual Studio 2012 からチェックインする]** をクリックし、**[Visual Studio 2012 を起動]** をクリックします (または、同等のコマンド バーの **[Visual Studio]** ボタンをクリックします)。<br/>
![][14]

<h2><a name="step4"> </a><span class="short-header">リビルドのトリガー</span>手順 4. リビルドをトリガーし、プロジェクトを再デプロイ</h2>

1. Visual Studio のチーム エクスプローラーで、**[ソース管理エクスプローラー]** をクリックします。<br/>
![][15]

2. ソリューション ファイルの保存場所に移動し、ファイルを開きます。<br/>
![][16]

3. ソリューション エクスプローラーで、ファイルを開き、変更します。たとえば、MVC Web ロールの Views\Shared フォルダーにある _Layout.cshtml ファイルを変更します。<br/>
![][17]

4. サイトのロゴを編集し、Ctrl + S キーを押してファイルを保存します。<br/>
![][18]

5. チーム エクスプローラーで、**[保留中の変更]** を選択します。<br/>
![][19]

6. コメントを入力し、**[チェックイン]** を選択します。<br/>
![][20]

7. [ホーム] ボタンを選択して、チーム エクスプローラーのホーム ページに戻ります。<br/>
![][21]

8. **[ビルド]** を選択して、処理中のビルドを表示します。<br/>
![][22]
<br/>
チーム エクスプローラーに、チェックインのためにビルドが開始されたことが表示されます。<br/>
![][23]

9. 処理中のビルドの名前をダブルクリックして、ビルドの進行に合わせて詳細ログを表示します。<br/>
![][24]

10. ビルドの処理中、ウィザードを使用して TFS を Azure にリンクしたときに作成されたビルド定義を調べてください。ビルド定義のショートカット メニューを開き、**[ビルド定義の編集]** を選択します。<br/>
![][25]
<br/>
**[トリガー]** タブを見ると、既定ではチェックインごとにビルドを行うようにビルド定義が設定されていることがわかります。<br/>
![][26]
<br/>
**[プロセス]** タブを見ると、デプロイ環境がクラウド サービスまたは Web サイトの名前に設定されていることがわかります。Web サイトを操作している場合は、ここで表示されているのとは異なるプロパティが表示されます。<br/>
![][27]
<br/>
既定値と異なる値を使用する場合は、プロパティに対して希望の値を指定します。
次の表に、クラウド サービスに関するプロパティの既定値を示します。
	<table>
<tr><td><b>プロパティ</b></td><td><b>既定値</b></td></tr>
<tr><td>アップグレードの許可</td><td>true</td></tr>
<tr><td>クラウド サービス環境</td><td>ステージング</td></tr>
<tr><td>クラウド サービス名</td><td>接続先のサービスの名前</td></tr>
<tr><td>デプロイ ラベル</td><td>サービス名と同じ</td></tr>
<tr><td>サービス構成</td><td>ServiceConfiguration.Cloud.cscfg</td></tr>
<tr><td>ストレージ アカウント名</td><td>空白 (ストレージ アカウントを検索する)。</td></tr>
<tr><td>発行プロファイル</td><td>.azurePubxml ファイル。チェックインする場合、ここで選択できる。</td></tr>
</table>
<br/>
ストレージ アカウントのプロパティが空白のままの場合は、Azure がアカウントを検索します。クラウド サービスと同じ名前のストレージ アカウントがある場合は、それが使用されます。同じ名前のアカウントがない場合は、別のストレージ アカウントが使用されます。ストレージ アカウントがない場合は、アカウントが作成されます。ストレージ アカウントは、ファイルとその他のデータを Azure 内に格納するための場所を提供します。詳細については、「[ストレージ アカウントに関するページ](http://www.windowsazure.com/ja-jp/documentation/articles/storage-whatis-account)を参照してください。

11. このころまでには、ビルドが正常に完了しています。<br/>
![][28]

12. ビルド名をダブルクリックすると、関連付けられた単体テスト プロジェクトのテスト結果を含む **[ビルドの概要]** が表示されます。<br/>
![][29]

13. [Azure ポータル](http://manage.windowsazure.com) では、ステージング環境が選択されたとき、関連付けられたデプロイが [デプロイ] タブに表示されます。<br/>
![][30]

14.	目的のサイトの URL に移動します。Web サイトの場合は、コマンド バーの参照ボタンをクリックします。クラウド サービスのステージング環境を示す **[ダッシュボード]** ページの **[概要]** セクションで URL を選択します。クラウド サービス向けの継続的な統合からのデプロイは、既定ではステージング環境に発行されます。代替クラウド サービス環境プロパティを [運用] に設定することで、これを変更できます。このスクリーンショットでは、クラウド サービスのダッシュボード ページにサイト URL が表示されています。<br/>
![][31]
<br/>
新しいブラウザー タブが開いて、実行中のサイトが表示されます。<br/>
![][32]

15.	クラウド サービスの場合は、プロジェクトにその他の変更を加えると、さらにビルドが実行され、複数のデプロイが累積されます。最新のデプロイは [アクティブ] とマークされます。<br/>
![][33]

<h2> <a name="step5"> </a><span class="short-header">以前のビルドを再デプロイ</span>手順 5. 以前のビルドを再デプロイ</h2>

この手順の内容は、クラウド サービスのみに適用され、省略可能です。管理ポータルで以前のデプロイを選択し、**[再デプロイ]** をクリックしてサイトを以前のチェックインに戻します。これによって、TFS で新しいビルドが開始され、デプロイ履歴に新しいエントリが作成されます。<br/>
![][34]

<h2> <a name="step6"> </a><span class="short-header">運用デプロイの変更</span>手順 6. 運用デプロイの変更</h2>

この手順の内容は、クラウド サービスのみに適用され、Web サイトには適用されません。準備が整ったら、管理ポータルで [スワップ] を選択してステージング環境を運用環境へ昇格できます。新たにデプロイされたステージング環境は運用に昇格され、以前の運用環境がある場合、運用環境はステージング環境になります。運用環境とステージング環境でアクティブなデプロイは異なることはありますが、最近のビルドのデプロイ履歴は環境にかかわらず同じです。<br/>
![][35]

詳細については、[Visual Studio Online に関するページ](http://go.microsoft.com/fwlink/?LinkId=253861)を参照してください。Git を使用している場合は、「[Git でコードを共有する](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx)」および「[ソース管理から Azure の Web サイトへの発行](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-publish-source-control)」を参照してください。

[手順 1. Visual Studio Online にサインアップする。]: #step1
[手順 2. プロジェクトをソース管理にチェックインする。]: #step2
[手順 3. プロジェクトを Azure に接続する。]: #step3
[手順 4. 変更を行い、リビルドと再デプロイをトリガーする。]: #step4
[手順 5. 以前のビルドを再デプロイする (省略可能)]: #step5
[手順 6. 運用デプロイを変更する (クラウド サービスのみ)]: #step6
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

