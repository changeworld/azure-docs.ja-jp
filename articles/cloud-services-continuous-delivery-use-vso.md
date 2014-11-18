<properties urlDisplayName="Publishing with Visual Studio Online" pageTitle="Visual Studio Online を使用した Azure への継続的な配信" metaKeywords="" description="Visual Studio Online チーム プロジェクトを自動的にビルドして Azure Web サイトまたはクラウド サービスにデプロイするための構成方法について説明します。" metaCanonical="" services="web-sites" documentationCenter=".NET" title="Continuous delivery to Azure using Visual Studio Online (Visual Studio Online を使用した Azure への継続的な配信)" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen" />

# Continuous delivery to Azure using Visual Studio Online (Visual Studio Online を使用した Azure への継続的な配信)

Visual Studio Online チーム プロジェクトを、自動的にビルドして Azure Web サイトまたはクラウド サービスにデプロイするように構成できます。*内部設置型* Team Foundation Server を使用した継続的なビルドおよびデプロイ システムのセットアップ方法については、「[Continuous Delivery for Cloud Applications in Azure (Azure でのクラウド サービスの継続的な配信)][Continuous Delivery for Cloud Applications in Azure (Azure でのクラウド サービスの継続的な配信)]」を参照してください。

このチュートリアルは、Visual Studio 2013 と Azure SDK がインストール済みであることを前提としています。Visual Studio 2013 がない場合は、[www.visualstudio.com][www.visualstudio.com] で**無料利用の開始**リンクをクリックしてダウンロードします。Azure SDK を[ここ][ここ]からインストールしてください。

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
<h5><a name="note"></a>このチュートリアルを完了するには、Visual Studio Online アカウントが必要です。</h5>
<p><a href="http://go.microsoft.com/fwlink/p/?LinkId=512979">Visual Studio Online アカウントは無料で開く</a>ことができます。</p>
</div>

Visual Studio Online を使用してクラウド サービスを自動的にビルドして Azure にデプロイするようにセットアップするには、次の手順に従います。

-   [手順 1.チーム プロジェクトを作成する。][手順 1.チーム プロジェクトを作成する。]

-   [手順 2.プロジェクトをソース管理にチェックインする。][手順 2.プロジェクトをソース管理にチェックインする。]

-   [手順 3.プロジェクトを Azure に接続する。][手順 3.プロジェクトを Azure に接続する。]

-   [手順 4.変更を行い、リビルドと再デプロイをトリガーする。][手順 4.変更を行い、リビルドと再デプロイをトリガーする。]

-   [手順 5.以前のビルドを再デプロイする (省略可能)][手順 5.以前のビルドを再デプロイする (省略可能)]

-   [手順 6.運用デプロイを変更する (クラウド サービスのみ)][手順 6.運用デプロイを変更する (クラウド サービスのみ)]

-   [手順 7.単体テストを実行する (省略可能)][手順 7.単体テストを実行する (省略可能)]

## <a name="step1"></a><span class="short-header">チーム プロジェクトの作成</span>手順 1: チーム プロジェクトの作成

[この][この]手順に従って、チーム プロジェクトを作成し、Visual Studio にリンクさせます。このチュートリアルでは、ソース管理ソリューションとして Team Foundation バージョン管理 (TFVC) を使用していることを前提としています。バージョン管理に Git を使用する場合は、「[このチュートリアルの Git バージョン][このチュートリアルの Git バージョン]」を参照してください。

## <a name="step2"> </a><span class="short-header">プロジェクトをソース管理にチェックイン</span>手順 2: プロジェクトをソース管理にチェックイン

1.  Visual Studio で、デプロイするソリューションを開くか、新しいソリューションを作成します。
    このチュートリアルの手順に従って、Web サイトまたはクラウド サービス (Azure アプリケーション) をデプロイできます。
    新しいソリューションを作成する場合は、新しい Azure クラウド サービス プロジェクト、
    または新しい ASP.NET MVC プロジェクトを作成します。プロジェクトが .NET Framework 4 または 4.5 をターゲットにしていることを確認し、クラウド サービス プロジェクトを作成している場合は、ASP.NET MVC の Web ロールおよび worker ロールのプロジェクトを追加して、Web ロールに対応するインターネット アプリケーションを選択します。確認メッセージが表示されたら、**[インターネット アプリケーション]** を選択します。
    Web サイトを作成する場合は、ASP.NET Web アプリケーション プロジェクトのテンプレートを選択し、次に MVC を選択します。[Azure と ASP.NET の使用に関するページ][Azure と ASP.NET の使用に関するページ]を参照してください。

2.  ソリューションのコンテキスト メニューを開き、**[ソリューションをソース管理に追加]** を選択します。
    ![][0]

3.  既定値を受け入れるか変更し、**[OK]** を選択します。処理が完了すると、ソース管理アイコンがソリューション エクスプローラーに表示されます。
    ![][1]

4.  ソリューションのショートカット メニューを開き、**[チェックイン]** を選択します。
    ![][2]

5.  チーム エクスプローラーの [保留中の変更] にチェックインのコメントを入力し、**[チェックイン]** を選択します。
    ![][3]

チェックイン時に特定の変更を含める、または除外するオプションに注意してください。必要な変更が除外されている場合は、**[すべて含む]** を選択します。
![][4]

## <a name="step3"> </a><span class="short-header">プロジェクトを Azure に接続する</span>手順 3. プロジェクトを Azure に接続する

1.  ソース コードが含まれる VSO チーム プロジェクトが完成したので、チーム プロジェクトを Azure に接続することができます。[Azure ポータル][Azure ポータル]で、クラウド サービスまたは Web サイトを選択するか、新たに作成します。作成するには、左下にある [+] アイコンを選択し、**[クラウド サービス]** または **[Web サイト]** を選択して、**[簡易作成]** を選択します。**[Visual Studio Online 発行の設定]** を選択します。
    ![][5]

2.  ウィザードで、テキスト ボックスに Visual Studio Online アカウントの名前を入力し、**[今すぐ承認]** をクリックします。サインインを求められることがあります。
    ![][6]

3.  [OAuth] ポップアップ ダイアログで、**[承諾]** を選択して Azure が VSO でチーム プロジェクトを構成することを許可します。
    ![][7]

4.  承認が成功すると、Visual Studio Online チーム プロジェクトのリストが含まれるドロップダウンが表示されます。前のステップで作成したチーム プロジェクトの名前を選択し、ウィザードのチェック マーク ボタンを選択します。
    ![][8]

5.  プロジェクトがリンクされると、変更を Visual Studio Online チーム プロジェクトにチェックインするための手順が表示されます。次のチェックイン時に、Visual Studio Online はプロジェクトをビルドして Azure にデプロイします。これを試すには、**[Visual Studio 2012 からチェックインする]** をクリックし、**[Visual Studio 2012 を起動]** をクリックします (または、同等のコマンド バーの **[Visual Studio]** ボタンをクリックします)。
    ![][9]

## <a name="step4"> </a><span class="short-header">リビルドのトリガー</span>手順 4: リビルドをトリガーし、プロジェクトを再デプロイする

1.  Visual Studio のチーム エクスプローラーで、**[ソース管理エクスプローラー]** をクリックします。
    ![][10]

2.  ソリューション ファイルの保存場所に移動し、ファイルを開きます。
    ![][11]

3.  ソリューション エクスプローラーで、ファイルを開き、変更します。たとえば、MVC Web ロールの Views\\Shared フォルダーにある \_Layout.cshtml ファイルを変更します。
    ![][12]

4.  サイトのロゴを編集し、Ctrl + S キーを押してファイルを保存します。
    ![][13]

5.  チーム エクスプローラーで、**[保留中の変更]** を選択します。
    ![][14]

6.  コメントを入力し、**[チェックイン]** を選択します。
    ![][15]

7.  [ホーム] ボタンを選択して、チーム エクスプローラーのホーム ページに戻ります。
    ![][16]

8.  **[ビルド]** リンクを選択して処理中のビルドを表示します。
    ![][17]
    チーム エクスプローラーに、チェックインのためにビルドが開始されたことが表示されます。
    ![][18]

9.  処理中のビルドの名前をダブルクリックして、ビルドの進行に合わせて詳細ログを表示します。
    ![][19]

10. ビルドの処理中、ウィザードを使用して TFS を Azure にリンクしたときに作成されたビルド定義を調べてください。ビルド定義のショートカット メニューを開き、**[ビルド定義の編集]** を選択します。
    ![][20]
    **[トリガー]** タブで、チェックインごとにビルドするようにビルド定義が既定で設定されていることを確認します。
    ![][21]
    **[プロセス]** タブを見ると、デプロイ環境がクラウド サービスまたは Web サイトの名前に設定されていることがわかります。Web サイトを操作している場合は、ここでの表示とは異なるプロパティが表示されます。
    ![][22]
    既定値と異なる値を使用する場合は、プロパティに対して希望の値を指定します。Azure 発行のプロパティは [展開] セクションにあります。
    次の表は、[展開] セクションで使用可能なプロパティを示しています。

    <table>

    <tr>
    <td>
    **プロパティ**

    </td>
    <td>
    **既定値**

    </td>
    </tr>
    </p>
    > <tr>
    > <td>
    > 信頼されていない証明書を許可
    >
    > </td>
    > <td>
    > false の場合、ルート証明機関により SSL 証明書に署名する必要があります。
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > アップグレードの許可
    >
    > </td>
    > <td>
    > 新規作成の代わりに、既存のデプロイを更新するデプロイを許可します。IP アドレスを保持します。
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > 削除しない
    >
    > </td>
    > <td>
    > true の場合は、既存の無関係のデプロイを上書きしません (アップグレードは許可されます)。
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > 配置設定へのパス
    >
    > </td>
    > <td>
    > リポジトリのルート フォルダーを基準にした .pubxml ファイルまたは Web サイトへのパス。クラウド サービスの場合は無視されます。
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > SharePoint 配置環境
    >
    > </td>
    > <td>
    > サービス名と同じ
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Windows Azure 配置環境
    >
    > </td>
    > <td>
    > Web サイトまたはクラウド サービスの名前
    >
    > </td>
    > </tr>
    > </table>

複数のサービス構成 (.cscfg ファイル) を使用している場合は、**[Build、Advanced、MSBuild 引数]** 設定で目的のサービス構成を指定できます。たとえば、ServiceConfiguration.Test.cscfg を使用するには、MSBuild 引数ライン オプション /p:TargetProfile=Test を設定します。
![][23]

1.  この時点で、ビルドは正常に完了しています。
    ![][24]

2.  ビルド名をダブルクリックすると、関連付けられた単体テスト プロジェクトのテスト結果を含む **[ビルドの概要]** が表示されます。
    ![][25]

3.  [Azure ポータル][Azure ポータル]では、ステージング環境が選択されると、関連付けられたデプロイが [デプロイ] タブに表示されます。
    ![][26]

4.  目的のサイトの URL に移動します。Web サイトの場合は、コマンド バーの参照ボタンをクリックします。クラウド サービスのステージング環境を示す **[ダッシュボード]** ページの **[概要]** セクションで URL を選択します。クラウド サービス向けの継続的な統合からのデプロイは、既定ではステージング環境に発行されます。代替クラウド サービス環境プロパティを [運用] に設定することで、これを変更できます。このスクリーンショットでは、クラウド サービスのダッシュボード ページにサイト URL が表示されています。
    ![][27]
    新しいブラウザー タブが開いて、実行中のサイトが表示されます。
    ![][28]

5.  クラウド サービスの場合は、プロジェクトにその他の変更を加えると、さらにビルドが実行され、複数のデプロイが累積されます。最新のデプロイは [アクティブ] とマークされます。
    ![][29]

## <a name="step5"> </a><span class="short-header">以前のビルドの再デプロイ</span>手順 5: 以前のビルドを再デプロイする

この手順の内容は、クラウド サービスのみに適用され、省略可能です。管理ポータルで以前のデプロイを選択し、**[再デプロイ]** をクリックしてサイトを以前のチェックインに戻します。これによって、TFS で新しいビルドが開始され、デプロイ履歴に新しいエントリが作成されます。
![][30]

## <a name="step6"> </a><span class="short-header">運用デプロイの変更</span>手順 6: 運用デプロイを変更する

この手順の内容は、クラウド サービスのみに適用され、Web サイトには適用されません。準備が整ったら、管理ポータルで [スワップ] を選択してステージング環境を運用環境へ昇格できます。新たにデプロイされたステージング環境は運用に昇格され、以前の運用環境がある場合、運用環境はステージング環境になります。運用環境とステージング環境でアクティブなデプロイは異なることはありますが、最近のビルドのデプロイ履歴は環境にかかわらず同じです。
![][31]

## <a name="step7"> </a><span class="short-header">単体テストの実行</span>手順 7: 単体テストの実行

ライブまたはステージング環境の展開にクオリティ ゲートを配置するには、単体テストを実行できます。失敗した場合は、展開を停止できます。

1.  Visual Studio で、単体テスト プロジェクトを追加します。
    ![][32]

2.  テストするプロジェクトにプロジェクト参照を追加します。
    ![][33]

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

5.  **[テスト失敗時にビルドを失敗させる]** を True に設定します。これは、テストに合格しない限り、展開が行われないことを意味します。
    ![][34]

6.  新しいビルドがキューに追加されます。
    ![][35]
    ![][36]

7.  ビルドの進行中は、進行状況を確認します。
    ![][37]
    ![][38]

8.  ビルドが完了したら、テスト結果を確認します。
    ![][39]
    ![][40]

9.  失敗するテストを作成してみます。最初のテストをコピーして新しいテストを追加し、その名前を変更して、NotImplementedException をスローするコード行をコメントアウトします。

        [TestMethod]
        [ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            //throw new NotImplementedException();
        }

10. 変更をチェックインし、新しいビルドをキューに追加します。
    ![][41]

11. テスト結果を表示して失敗の詳細を確認します。
    ![][42]
    ![][43]

Visual Studio Online での単位テストの詳細については、[「ビルド プロセスでのテストの実行」][「ビルド プロセスでのテストの実行」] を参照してください。

詳細については、[Visual Studio Online に関するページ][Visual Studio Online に関するページ]を参照してください。Git を使用している場合は、「[Git でコードを共有する][Git でコードを共有する]」および「[ソース管理から Windows Azure の Web サイトへの発行][ソース管理から Windows Azure の Web サイトへの発行]」を参照してください。

  [Continuous Delivery for Cloud Applications in Azure (Azure でのクラウド サービスの継続的な配信)]: ../cloud-services-dotnet-continuous-delivery
  [www.visualstudio.com]: http://www.visualstudio.com
  [ここ]: http://go.microsoft.com/fwlink/?LinkId=239540
  [手順 1.チーム プロジェクトを作成する。]: #step1
  [手順 2.プロジェクトをソース管理にチェックインする。]: #step2
  [手順 3.プロジェクトを Azure に接続する。]: #step3
  [手順 4.変更を行い、リビルドと再デプロイをトリガーする。]: #step4
  [手順 5.以前のビルドを再デプロイする (省略可能)]: #step5
  [手順 6.運用デプロイを変更する (クラウド サービスのみ)]: #step6
  [手順 7.単体テストを実行する (省略可能)]: #step7
  [この]: http://go.microsoft.com/fwlink/?LinkId=512980
  [このチュートリアルの Git バージョン]: http://go.microsoft.com/fwlink/p/?LinkId=397358
  [Azure と ASP.NET の使用に関するページ]: http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-dotnet-get-started/
  [0]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
  [1]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
  [2]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
  [3]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
  [4]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
  [Azure ポータル]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
  [6]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
  [7]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
  [8]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
  [9]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
  [10]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
  [11]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
  [12]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
  [13]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
  [14]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
  [15]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
  [16]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
  [17]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
  [18]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
  [19]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
  [20]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
  [21]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
  [22]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
  [23]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
  [24]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
  [25]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
  [26]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
  [27]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
  [28]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
  [29]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
  [30]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
  [31]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
  [32]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
  [33]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
  [34]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
  [35]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
  [36]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
  [37]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
  [38]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
  [39]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
  [40]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
  [41]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
  [42]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
  [43]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
  [「ビルド プロセスでのテストの実行」]: http://go.microsoft.com/fwlink/p/?LinkId=510474
  [Visual Studio Online に関するページ]: http://go.microsoft.com/fwlink/?LinkId=253861
  [Git でコードを共有する]: http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx
  [ソース管理から Windows Azure の Web サイトへの発行]: http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-publish-source-control
