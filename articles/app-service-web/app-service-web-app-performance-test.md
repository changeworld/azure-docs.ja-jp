<properties
   pageTitle="Azure Web アプリのパフォーマンスをテストする | Microsoft Azure"
   description="Azure Web アプリのパフォーマンスを実行して、アプリでユーザーの負荷がどのように処理されるかを確認します。応答時間を測定し、問題を示す可能性があるエラーを見つけます。"
   services="app-service\web"
   documentationCenter=""
   authors="ecfan"
   manager="douge"
   editor="jimbe"/>

<tags
   ms.service="app-service-web"
   ms.workload="web"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="05/25/2016"
   ms.author="estfan; manasma; ahomer"/>

# 負荷をかけた状態での Azure Web アプリのパフォーマンス テスト

Web アプリを起動する前または更新プログラムを運用環境にデプロイする前に、パフォーマンスを確認しましょう。これにより、アプリをリリースする準備が整っているかどうかをより適切に評価できます。アプリの使用がピーク状態にあるときや次のマーケティング プッシュ時にアプリがトラフィックを処理できることを確認しましょう。

パブリック プレビューの段階では、Azure ポータルでアプリのパフォーマンスを無料でテストできます。これらのテストでは、一定期間にわたってアプリのユーザー負荷をシミュレートし、アプリの応答を測定します。たとえば、一定の数のユーザーに対してアプリがどれだけすばやく応答したかがテスト結果に示されます。また、アプリに存在する問題を示す可能性がある失敗した要求の数も示されます。

![Web アプリのパフォーマンスの問題を見つける](./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png)

## 開始する前に

* [Azure サブスクリプション](https://account.windowsazure.com/subscriptions)が必要です (まだ取得していない場合)。[無料で Azure アカウントを開く方法については、このページを参照してください](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)。

* パフォーマンス テストの履歴を保存するには、[Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) アカウントが必要です。パフォーマンス テストを設定すると、適切なアカウントが自動的に作成されます。また、自分で新しいアカウントを作成することも、アカウント所有者である場合は既存のアカウントを使用することもできます。

* 非運用環境にテストするアプリをデプロイします。運用環境で使用するプラン以外の App Service プランをアプリで使用するように設定します。これで、既存の顧客に影響が及んだり、運用環境のアプリの速度が低下したりすることがなくなります。

## パフォーマンス テストの設定と実行

0.  [Azure ポータル](https://portal.azure.com)にサインインします。所有している Visual Studio Team Services アカウントを使用するには、アカウント所有者としてサインインします。

0.  Web アプリに移動します。

    ![[すべてを参照] > [Web アプリ] > アプリを選択](./media/app-service-web-app-performance-test/azure-np-web-apps.png)

0.  **[パフォーマンス テスト]** に移動します。

    ![[ツール] > [パフォーマンス テスト] を選択](./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png)
 
0. 次に、パフォーマンス テストの履歴を保存するために、[Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) アカウントをリンクします。

    使用できる Team Services アカウントがある場合は、そのアカウントを選択します。VSO アカウントを持っていない場合は、新しいアカウントを作成します。

    ![既存の Team Services アカウントを選択するか、新しいアカウントを作成します](./media/app-service-web-app-performance-test/azure-np-no-vso-account.png)

0.  パフォーマンス テストを作成します。詳細を設定し、テストを実行します。

テストの実行中、リアルタイムで結果を確認できます。

たとえば、昨年の休日セールでクーポンを配布したアプリがあるとします。このイベントは、100 人の同時接続顧客数をピーク負荷として 15 分間にわたって続きました。今年は、顧客の数を 2 倍にしたいと考えています。さらに、ページの読み込み時間を 5 秒から 2 秒に短縮することにより、顧客満足度を高めたいと考えています。そこで、更新したアプリのパフォーマンスを 250 人のユーザーを想定して 15 分間にわたってテストします。

Web サイトを同時に訪れる仮想ユーザー (顧客) を生成して、アプリに対する負荷をシミュレートします。これで、失敗した要求や応答が遅い要求の数がわかります。

  ![パフォーマンス テストの作成、設定、実行](./media/app-service-web-app-performance-test/azure-np-new-performance-test.png)

   *  Web アプリの既定の URL は自動的に追加されます。URL を変更して他のページ (HTTP GET 要求のみ) をテストすることができます。

   *  ローカル状態をシミュレートし、待機時間を短縮するには、最も近い場所のユーザーを選択して負荷を生成します。

  テストが実行されているときのようすを次に示します。最初の 1 分間は、ページの読み込みに予定よりも時間がかかっています。

  ![実行中のパフォーマンス テストとリアルタイムのデータ](./media/app-service-web-app-performance-test/azure-np-running-perf-test.png)

  テストが終了すると、最初の 1 分が経過した後はページの読み込みが高速化されていることがわかります。これは、問題のトラブルシューティングを始める場所を特定するのに役立ちます。

  ![パフォーマンス テスト完了、失敗したリクエストなどの結果がわかる](./media/app-service-web-app-performance-test/azure-np-perf-test-done.png)

## 複数の URL のテスト

Visual Studio Web テスト ファイルをアップロードして、エンド ツー エンドのユーザー シナリオを表す複数の URL を組み込んだパフォーマンス テストを実行することもできます。Visual Studio Web テスト ファイルを作成する方法のいくつかを次に示します。

* [Fiddler を使用してトラフィックをキャプチャし、Visual Studio Web テスト ファイルとしてエクスポートする](http://docs.telerik.com/fiddler/Save-And-Load-Traffic/Tasks/VSWebTest)
* [Visual Studio でロード テスト ファイルを作成する](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release)

Visual Studio Web テスト ファイルをアップロードして実行するには、次の手順に従います。
 
0. 上記の手順に従って、**[新しいパフォーマンス テスト]** ブレードを開きます。このブレードで [CONFIGFURE TEST USING] (テストの構成に使用する項目) オプションを選択して、**[Configure test using]** (テストの構成に使用する項目) ブレードを開きます。  

    ![Opening the Configure load testing blade](./media/app-service-web-app-performance-test/multiple-01-authoring-blade.png)

0. [TEST TYPE] (テストの種類) が **[Visual Studio Web Test]** (Visual Studio Web テスト) に設定されていることを確認し、HTTP アーカイブ ファイルを選択します。"フォルダー" アイコンを使用してファイル選択ダイアログを開きます。

    ![Uploading a multiple URL Visual Studio Web Test file](./media/app-service-web-app-performance-test/multiple-01-authoring-blade2.png)

    ファイルがアップロードされると、テストで使用する URL の一覧が [URL DETAILS] (URL の詳細) セクションに表示されます。
 
0. ユーザー ロードとテスト期間を指定し、**[Run test]** (テストの実行) を選択します。

    ![Selecting the user load and duration](./media/app-service-web-app-performance-test/multiple-01-authoring-blade3.png)

    テストが完了すると、2 つのウィンドウに結果が表示されます。左側のウィンドウには、パフォーマンス情報が一連のグラフとして表示されます。

    ![The performance results pane](./media/app-service-web-app-performance-test/multiple-01a-results.png)

    右側のウィンドウには、エラーの種類と発生回数と共に、失敗した要求の一覧が表示されます。

    ![The request failures pane](./media/app-service-web-app-performance-test/multiple-01b-results.png)

0. 右側のウィンドウの上部にある **[再実行]** アイコンを選択して、テストを再実行します。

    ![Rerunning the test](./media/app-service-web-app-performance-test/multiple-rerun-test.png)

##  Q & A

#### Q: テストの実行時間に関して制限はありますか? 

**A**: はい。Azure ポータルでは、最長 1 時間までテストを実行できます。

#### Q: パフォーマンス テストの実行に使用できる時間はどれだけユーザーに与えられますか。 

**A**: パブリック プレビュー以降、Visual Studio Team Services アカウントに対して毎月 20,000 仮想ユーザー時間 (VUM) が無料で提供されます。VUM は、仮想ユーザー数にテスト時間 (分) を掛けた値です。この無料分を超える時間を使用する必要がある場合は、時間を購入し、使用した時間分のみ支払うことができます。

#### Q: これまで使用した VUM はどこで確認できますか。

**A**: Azure ポータルでこの時間を確認できます。

![Team Services アカウントに移動します。](./media/app-service-web-app-performance-test/azure-np-vso-accounts.png)

![使用した VUM の確認](./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png)

#### Q: 既定のオプションは教えてください。既存のテストに影響がありますか。

**A**: パフォーマンス ロード テストの既定のオプションは手動テストで、複数 URL テスト オプションがポータルに追加される前と同じです。既存のテストでは構成済みの URL が引き続き使用され、以前と同様に動作します。

#### Q: Visual Studio Web テスト ファイルでサポートされていない機能を教えてください。

**A**: 現時点では、Web テストのプラグイン、データ ソース、および抽出規則がサポートされていません。Web テスト ファイルを編集してこれらを削除する必要があります。Microsoft では、将来の更新プログラムでこれらの機能のサポートを追加したいと考えています。

#### Q: 他の Web テスト ファイル形式はサポートされていますか。
  
**A**: 現時点では、Visual Studio Web テスト形式ファイルのみがサポートされています。他のファイル形式のサポートが必要な場合は、ご意見をお寄せください。[vsoloadtest@microsoft.com](mailto:vsoloadtest@microsoft.com) までメールをお送りください。

#### Q: Visual Studio Team Services アカウントでほかにできることは何ですか。

**A**: 新しいアカウントを見つけるには、```https://{accountname}.visualstudio.com``` にアクセスしてください。任意のツールまたは言語を使用して、コードの共有、ビルド、テスト、作業の追跡、ソフトウェアの出荷などの操作をすべてクラウドで実行できます。[Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) の機能とサービスがどのようにチームの共同作業と継続的なデプロイを支援するかについて学習してください。

## 関連項目

* [単純なクラウドのパフォーマンス テストの実行](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-simple-cloud-load-test)
* [Apache Jmeter パフォーマンス テストの実行](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-jmeter-test)
* [クラウド ベースのロード テストの記録と再生](https://www.visualstudio.com/docs/test/performance-testing/getting-started/record-and-replay-cloud-load-tests)
* [クラウドでのアプリのパフォーマンス テスト](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)

<!---HONumber=AcomDC_0525_2016-->