<properties 
	pageTitle="Stream Analytics を使って IOT ソリューションを構築する | Microsoft Azure" 
	description="料金所ブースを例に Stream Analytics を使った基本的な IOT ソリューションを紹介します。"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/16/2016" 
	ms.author="jeffstok"
/>

# Stream Analytics を使って IOT ソリューションを構築する #

## はじめに ##

このチュートリアルでは、データに隠された知見を Azure Stream Analytics を使ってリアルタイムで突き止める方法を説明します。蓄積されたデータではなく今まさに発生しているデータをいかに活用するかというテーマに今日、多くの開発者が取り組んでいます。Azure のストリーム処理サービスを利用すると、データのストリーム (クリック ストリーム、ログ、デバイスによって生成されたイベントなど) に履歴レコードや参照データを組み合わせて、すばやく簡単にビジネス インサイトを導き出すことができます。Azure Stream Analytics は、Microsoft Azure の徹底した管理によってホストされたリアルタイム ストリーム計算処理サービスであるため、高い障害回復力とスケーラビリティ、低遅延が実現され、短時間での立ち上げが可能となっています。

このチュートリアルを読むと、次のことができるようになります。

-   Azure Stream Analytics ポータルについて理解を深める。
-   ストリーミング ジョブを構成、デプロイする。
-   現実世界の問題を明確化し、Stream Analytics クエリ言語を使って解決する。
-   Azure Streaming Analytics を使って顧客向けのストリーミング ソリューションを自信を持って開発する。
-   監視機能とログ機能の使用経験を問題のトラブルシューティングに活かす。

## 前提条件 ##

このチュートリアルを正常に完了するための前提条件は次のとおりです。

-   最新の [Azure PowerShell](../powershell-install-configure.md)
-   Visual Studio 2015 または無料の [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
-   [Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/)
-   コンピューターの管理特権
-   [GitHub](https://github.com/streamanalytics/samples/releases) からダウンロードした最新版の TollApp.zip

## シナリオの説明 - "料金所" ##


高速道路には必ず料金所があります。海外に行くと、橋やトンネルに料金所が設けられていることもあります。それぞれの料金所には複数の料金所ブースがあり、それぞれ手動式と自動式とに分けられます。停車して通行料金を精算するのが手動式です。一方、車両が料金所ブースを通過する際、ブース上方に設置されているセンサーが、車両のフロントガラスに貼り付けられた RFID カードをスキャンするのが自動式です。車両が料金所を通過した事実は、イベント ストリームとして簡単に可視化でき、そのストリームを介して必要な処理を実行することができます。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## 受信データ ##

操作の対象となるのは、料金所の入口と出口に設置されたセンサーによって生成される 2 つのデータ ストリームと、車両の登録データが格納された静的なルックアップ データセットです。

### 入口データ ストリーム ###

入口データ ストリームは、料金所に入る車両の情報を含んでいます。
  
  
| TollID | EntryTime | LicensePlate | State | 保存する | モデル | Vehicle Type | Vehicle Weight | Toll | タグ |
|---------|-------------------------|--------------|-------|--------|---------|--------------|----------------|------|-----------|
| 1 | 2014-09-10 12:01:00.000 | JNB 7001 | NY | Honda | CRV | 1 | 0 | 7 | |
| 1 | 2014-09-10 12:02:00.000 | YXZ 1001 | NY | Toyota | Camry | 1 | 0 | 4 | 123456789 |
| 3 | 2014-09-10 12:02:00.000 | ABC 1004 | CT | Ford | Taurus | 1 | 0 | 5 | 456789123 |
| 2 | 2014-09-10 12:03:00.000 | XYZ 1003 | CT | Toyota | Corolla | 1 | 0 | 4 | |
| 1 | 2014-09-10 12:03:00.000 | BNJ 1007 | NY | Honda | CRV | 1 | 0 | 5 | 789123456 |
| 2 | 2014-09-10 12:05:00.000 | CDE 1007 | NJ | Toyota | 4x4 | 1 | 0 | 6 | 321987654 |
  

次に、それぞれの列について簡単に説明します。
  
  
| TollID | 料金所ブースを一意に識別する ID |
|--------------|----------------------------------------------------------------|
| EntryTime | 車両が料金所ブースに入った日時 (UTC) |
| LicensePlate | 車両のナンバー プレートの番号 |
| State | 米国の州 |
| 保存する | 自動車の製造元 |
| モデル | 自動車の型式 |
| VehicleType | 乗用車の場合は 1、商用車の場合は 2 |
| WeightType | 車両重量 (トン)。乗用車の場合は 0 |
| Toll | 通行料金の値 (米ドル) |
| タグ | 決済を自動化するために自動車に設置された電子タグ (支払いが手動で行われた場合は空) |


### 出口データ ストリーム

出口データ ストリームは、料金所から出る車両の情報を含んでいます。
  
  
| **TollId** | **ExitTime** | **LicensePlate** |
|------------|------------------------------|------------------|
| 1 | 2014-09-10T12:03:00.0000000Z | JNB 7001 |
| 1 | 2014-09-10T12:03:00.0000000Z | YXZ 1001 |
| 3 | 2014-09-10T12:04:00.0000000Z | ABC 1004 |
| 2 | 2014-09-10T12:07:00.0000000Z | XYZ 1003 |
| 1 | 2014-09-10T12:08:00.0000000Z | BNJ 1007 |
| 2 | 2014-09-10T12:07:00.0000000Z | CDE 1007 |

次に、それぞれの列について簡単に説明します。
  
  
| 分割 | 説明 |
|--------------|-----------------------------------------------------------------|
| TollID | 料金所ブースを一意に識別する ID |
| ExitTime | 車両が料金所ブースから出た日時 (UTC) |
| LicensePlate | 車両のナンバー プレートの番号 |

###商用車の登録データ

商用車登録データベースを使用します。特定の時点の静的なデータであり変化しません。
  
  
| LicensePlate | RegistrationId | 有効期限切れ |
|--------------|----------------|---------|
| SVT 6023 | 285429838 | 1 |
| XLZ 3463 | 362715656 | 0 |
| BAC 1005 | 876133137 | 1 |
| RIV 8632 | 992711956 | 0 |
| SNY 7188 | 592133890 | 0 |
| ELH 9896 | 678427724 | 1 |                      

次に、それぞれの列について簡単に説明します。
  
  
| 分割 | 説明 |
|--------------|-----------------------------------------------------------------|
| LicensePlate | 車両のナンバー プレートの番号 |
| RegistrationId | RegistrationId |
| 有効期限切れ | 車両登録が有効期間内である場合は 0、期限切れである場合は 1 |


## Azure Stream Analytics の環境の設定

このチュートリアルには Microsoft Azure サブスクリプションが必要です。Microsoft Azure サービスの無料試用版がマイクロソフトから提供されています (下記参照)。

Azure アカウントをお持ちでない場合は、<http://azure.microsoft.com/pricing/free-trial/> にアクセスして無料試用版にサインアップしてください。

注: 無料試用版にサインアップするためには、テキスト メッセージを受信できるモバイル デバイスと有効なクレジット カードが必要となります。

\\$200 相当の Azure クレジットを利用できるよう、この演習の最後にある「Azure アカウントのクリーンアップ」セクションの手順を忘れずに実行してください。

## チュートリアルに必要な Azure リソースのプロビジョニング

このチュートリアルでは、"入口" と "出口" のデータ ストリームを受信するために、2 つの Azure イベント ハブが必要となります。Stream Analytics ジョブの結果は Azure SQL Database を使用して出力します。また、車両登録に関する参照データは Azure Storage を使用して格納します。

必要なリソースはすべて、GitHub の TollApp フォルダーにある Setup.ps1 スクリプトを使って作成できます。時間を節約するために、このスクリプトを実行することをお勧めします。Azure ポータルでこれらのリソースを構成する方法については、Azure ポータルでチュートリアル リソースを構成する方法について書かれた付録を参照してください。

関連する [TollApp](https://github.com/streamanalytics/samples/releases) フォルダーとファイルをダウンロードして保存します。必ず最新版をダウンロードしてください。

[Microsoft Azure PowerShell] ウィンドウを**管理者として**開きます。まだ Azure PowerShell をお持ちでない場合は、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」の手順に従ってインストールしてください。

インターネットからダウンロードされた ps1 ファイル、dll ファイル、exe ファイルは、Windows によって自動的にブロックされます。スクリプトを実行する前に、実行ポリシーを設定しておく必要があります。Azure PowerShell ウィンドウが管理者権限で実行されていることを確認してください。「Set-ExecutionPolicy unrestricted」を実行し、確認を求められたら「Y」と入力します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Get-ExecutionPolicy を実行して、コマンドが正しく実行されたことを確認します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

スクリプトとジェネレーター アプリケーションが置かれているディレクトリに移動します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

「.\\Setup.ps1」と入力して Azure アカウントを設定します。必要なリソースをすべて作成および構成して、イベントの生成を開始します。対象リソースの作成先となるリージョンは、スクリプトによってランダムに選択されます。リージョンを明示的に指定する場合は、次の例のように -location パラメーターを指定してください。

**.\\Setup.ps1 -location “Central US”**

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

このスクリプトを実行すると、Microsoft Azure の "サインイン" ページが表示されます。ご利用のアカウントの資格情報を入力してください。

ご利用のアカウントで複数のサブスクリプションを利用できる場合は、チュートリアルに使用するサブスクリプション名を入力するように求められます。

このスクリプトは完了までに数分かかる場合があります。完了すると、以下のスクリーンショットのような画面が出力されます。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

他にも、以下のスクリーンショットに示したようなウィンドウが表示されます。これは、EventHub にイベントを送信するアプリケーションで、チュートリアルの演習を実行するために必要となります。そのため、チュートリアルが完了するまで、アプリケーションを停止したり、このウィンドウを閉じたりしないでください。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

この時点で Azure 管理ポータルには、作成されたすべてのリソースが表示されます。<https://manage.windowsazure.com> にアクセスし、ご利用のアカウントの資格情報でログインしてください。

### Event Hubs

Azure 管理ポータルの左側にある [Service Bus] メニュー項目をクリックすると、前セクションのスクリプトで作成されたイベント ハブが表示されます。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

ご利用のサブスクリプションで使用できる名前空間がすべて表示されます。"TollData" で始まる項目をクリックしてください(この例では TollData4637388511)。[イベント ハブ] タブをクリックします。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

この名前空間に作成された *entry* と *exit* という 2 つのイベント ハブが表示されます。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### Azure Storage コンテナー

Azure 管理ポータルの左側にある [Storage] メニュー項目をクリックすると、ラボで使用するストレージ コンテナーが表示されます。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

"tolldata" で始まる項目をクリックしてください (この例では tolldata4637388511)。[コンテナー] タブを開くと、作成されたコンテナーが表示されます。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

[tolldata] コンテナーをクリックし、車両の登録データが含まれる、アップロード済みの JSON ファイルを表示します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### Azure SQL Database

Azure 管理ポータルの左側にある [SQL データベース] メニュー項目をクリックすると、ラボで使用する Azure SQL Database が表示されます。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

[TollDataDB] をクリックします。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

ポート番号を除いたサーバー名をコピーします (例: *servername*.database.windows.net)。

##Visual Studio からデータベースに接続する

出力データベースに格納されているクエリの結果には、Visual Studio を使用してアクセスします。

次の手順に従って、Azure データベース (接続先) に Visual Studio から接続します。

1) Visual Studio を起動し、[ツール] をクリックして [データベースへの接続] メニュー項目をクリックします。

2) データ ソースの確認を求められたら、[Microsoft SQL Server] を選択します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3) [サーバー名] フィールドで、前のセクションで Azure ポータルからコピーした SQL Server の名前 (*servername*.database.windows.net) を貼り付けます。

4) [認証] フィールドで [SQL Server 認証] を選択します。

5) [ログイン名] に「tolladmin」、[ログイン パスワード] に「123toll!」と入力します。

6) データベースとして TollDataDB を選択します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
    
7) [OK] をクリックします。

8) サーバー エクスプローラーを開きます。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
  
9) TollDataDB データベースに作成された 4 つのテーブルが表示されます。
  
![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)
  
##イベント ジェネレーター - TollApp サンプル プロジェクト

PowerShell スクリプトは、TollApp というサンプル アプリケーション プログラムを使用して、イベントの送信を自動的に開始します。別途処理を実行する必要はありません。

ただし、詳しい実装内容に関心がある方は、GitHub の [samples/TollApp](https://github.com/streamanalytics/samples/tree/master/TollApp) で、TollApp アプリケーションのソース コードをご覧いただけます。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

##Stream Analytics ジョブの作成

Azure ポータルで Stream Analytics を開き、ページの左下隅にある [新規] ボタンをクリックして、新しい分析ジョブを作成します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

[簡易作成] をクリックします。スクリプトで他のリソースが作成されるのと同じリージョンを選択してください。

[地域の監視ストレージ アカウント] の設定で、[新しいストレージ アカウントの作成] を選択し、一意の名前を付けます。このアカウントは、今後実行するすべてのジョブの監視情報を保存するために、Azure Stream Analytics によって使用されます。

ページ下部の [Stream Analytics ジョブの作成] をクリックします。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

##入力ソースの定義

ポータルで作成した分析ジョブをクリックします。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

[入力] タブを開いてソース データを定義します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

[入力の追加] をクリックします。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

最初のページで [データ ストリーム] を選択します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

ウィザードの 2 ページ目で [イベント ハブ] を選択します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

[入力のエイリアス] として「EntryStream」と入力します。

[イベント ハブ] ボックスの一覧をクリックし、"TollData" で始まる項目を選択します (例: TollData9518658221)。

[イベント ハブ名] として [entry] を、[イベント ハブ ポリシー名] として [all] を選択します。

設定は次のようになります。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

次のページに移動します。JSON と UTF8 エンコーディングを値として選択します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

ダイアログの下部にある [OK] をクリックしてウィザードを終了します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

同じ一連の手順に従って、Exit イベントのストリーム用に 2 つ目のイベント ハブの入力を作成する必要があります。3 ページ目で、以下のスクリーンショットと同じように値を入力してください。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

これで 2 つの入力ストリームが定義されました。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

次に、車両登録データの BLOB ファイルに対する "参照" データ入力を追加します。

[入力の追加] をクリックします。[参照データ] を選択します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

次のページで、"tolldata" から始まるストレージ アカウントを選択します。コンテナー名は "tolldata"、[パス パターン] の下の BLOB 名は "registration.json" とする必要があります。このファイル名は大文字と小文字が区別されるため、すべて小文字で入力してください。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

次のページで以下のように値を選択し、[OK] をクリックしてウィザードを終了します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

これで、すべての入力が定義されました。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

##出力の定義

[出力] タブに移動し、[出力の追加] をクリックします。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

[SQL Database] を選択します。

「Visual Studio からデータベースに接続する」で使用したサーバー名を選択します。データベース名は TollDataDB にしてください。

ユーザー名に「tolladmin」、パスワードに「123toll!」を入力します。テーブル名は「TollDataRefJoin」に設定します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

##Azure Stream Analytics クエリ

[クエリ] タブには、入力データに対して変換を実行する SQL クエリが表示されます。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

このチュートリアルでは、通行料金データに伴う実務上の課題を解決していきたいと思います。必要な答えを導くために、Azure Stream Analytics で使用できる Stream Analytics クエリを構築します。

初めての Azure Stream Analytics ジョブを作成する前に、いくつかのシナリオとクエリ構文について詳しく見ていきましょう。

##Azure Stream Analytics クエリ言語について
-----------------------------------------------------

料金所ブースに入る車両の台数をカウントしなければならなくなったとします。絶えず発生するイベントであるため、"期間" の定義が不可欠となります。そこで目的を少し変更し、"3 分ごとに料金所ブースに入る車両の台数" を調べることにします。これを一般に "タンブリング カウント" といいます。

以下に示したのは、それを実現する Azure Stream Analytics クエリです。

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

ご覧のとおり、Azure Stream Analytics では SQL に似たクエリ言語が使用されており、さらに、時間に関連したクエリ要素を指定できるように、いくつかの拡張機能が追加されています。

詳細については、クエリにおける[時間管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)と[時間枠](https://msdn.microsoft.com/library/azure/dn835019.aspx)の概念についての記事を MSDN でご覧ください。

##Azure Stream Analytics クエリのテスト

初めての Azure Stream Analytics クエリを作成したら、TollApp フォルダーの以下のパスに格納されているサンプル データ ファイルを使ってクエリをテストします。

**..\\TollApp\\TollApp\\Data**

このフォルダーには次のファイルが格納されています。

1.  Entry.json

2.  Exit.json

3.  Registration.json

## テスト 1 - 料金所ブースに入る車両の台数

Azure 管理ポータルを開き、先ほど作成した Azure Stream Analytics ジョブに移動します。[クエリ] タブを開き、前セクションからクエリをコピーして貼り付けます。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

サンプル データに対してこのクエリを検証するには、[テスト] ボタンをクリックします。表示されたダイアログで、Entry.json (EntryTime イベント ストリームからのサンプル データを含んだファイル) に移動します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

クエリの出力結果が想定どおりであることを確認します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

##テスト 2 - 各車両が料金所ブースを通過するのにかかる総時間を調べる

効率と顧客満足を評価するために、車両が料金所を通過するのにかかる平均時間を調べるとします。

そのためには、EntryTime を含んだストリームと ExitTime を含んだストリームを結合する必要があります。ストリームの結合条件には、TollId 列と LicencePlate 列を指定することにします。結合したイベントどうしの間隔として許容される時間差を JOIN 演算子で指定する必要があります。DATEDIFF 関数を使用し、発生間隔が 15 分以内のイベントに限定する条件を指定します。また、通行料金の徴収に費やされた実際の時間を計算するために、ExitTime と EntryTime にも DATEDIFF 関数を適用します。DATEDIFF を SELECT ステートメントで使用する場合と JOIN 条件で使用する場合の使い方の違いに注目してください。

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

このクエリをテストするには、ジョブの [クエリ] タブでクエリを更新します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

[テスト] をクリックして、EntryTime と ExitTime のサンプル入力ファイルを指定します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

チェック ボックスをクリックしてクエリをテストし、出力結果を確認します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

##テスト 3 – 登録が期限切れとなっているすべての商用車を調べる

Azure Stream Analytics では、特定の時点の静的データを使用して経時的に発生するデータ ストリームを結合することができます。この機能のデモとして、次のテストを行います。

料金徴収会社に登録されている商用車は、検査のために停車しなくても料金所ブースを通過することができます。商用車登録参照テーブルを使用して、登録期限切れとなっているすべての商用車を特定することにしましょう。

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

参照データを使ってクエリをテストするためには、その参照データの入力ソースが定義されている必要があります (手順 5. で行った作業です)。

このクエリをテストするには、[クエリ] タブにクエリを貼り付けて [テスト] をクリックし、次の 2 つの入力ソースを指定します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

クエリの出力結果を確認します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

##Stream Analytics ジョブの開始


初めての Azure Stream Analytics クエリを作成したところで、必要な構成を行ってジョブを開始します。テスト 3 で作成したクエリを保存してください。出力テーブル **TollDataRefJoin** のスキーマに適合した出力結果が、このクエリによって生成されます。

ジョブのダッシュボードに移動して [開始] をクリックします。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

表示されたダイアログで、[出力の開始] の時刻を [ユーザー設定時刻] に変更します。時間 (時) を編集し、1 時間前の時刻に設定します。これで、チュートリアルの最初にイベントの生成を開始したとき以降、イベント ハブから受け取ったすべてのイベントを確実に処理することができます。チェック マークをクリックしてジョブを開始してください。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

ジョブが開始されるまでに数分かかる場合があります。そのステータスは、Stream Analytics の最上位のページで確認できます。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

##Visual Studio での結果の確認

Visual Studio のサーバー エクスプローラーを開いて TollDataRefJoin テーブルを右クリックします。[テーブル データの表示] を選択すると、ジョブの出力結果が表示されます。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

##Azure Stream Analytics ジョブのスケールアウト

Azure Stream Analytics は、エラスティックな拡張と高い負荷のデータ処理に対応するように設計されています。Azure Stream Analytics クエリで **PARTITION BY** 句を使用すると、そのステップをスケールアウトして処理するようシステムに命令することができます。PartitionId は、システムによって追加される特殊な列で、入力 (イベント ハブ) のパーティション ID と一致します。

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId    

現在のジョブを停止し、[クエリ] タブでクエリを更新して、[スケール] タブを開きます。

ジョブに割り当てることができる計算処理能力の大きさは、ストリーミング ユニットによって定義されます。

スライダーを 6 に移動します。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

出力タブに移動して、SQL テーブルの名前を "TollDataTumblingCountPartitioned" に変更してください。

これでジョブを開始すると、より多くのコンピューティング リソースに処理が分散され、スループットが向上します。TollApp アプリケーションから送信されるイベントも TollId でパーティション分割されることに注目してください。

##Monitoring

[監視] タブには、実行中のジョブに関する統計情報が表示されます。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

操作ログには、[ダッシュボード] タブからアクセスできます。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

特定のイベントに関する詳しい情報を表示するには、そのイベントを選択し、[詳細] ボタンをクリックします。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

##まとめ

このチュートリアルでは、Azure Stream Analytics サービスの概要を紹介しました。デモンストレーションでは、Stream Analytics ジョブの入力と出力の構成方法を説明しました。イベント データをリアルタイムで扱ううえでの一般的な課題と、Azure Stream Analytics の SQL に似た単純なクエリを使ってそれを解決する方法について、通行料金データを例に説明しました。その中で触れた SQL 拡張機能の概念は、経時的に発生するデータを扱う際に必要となります。データ ストリームを結合する方法や、データ ストリームの利用価値を静的な参照データで高める方法についても紹介しました。また、クエリをスケールアウトしてスループットを高める方法について説明しました。

このチュートリアルで取り上げた内容はあくまで入門的な位置付けであり、ここでは紹介しきれない情報も数多くあります。SAQL 言語を使ったクエリ パターンの詳細については、[こちら](stream-analytics-stream-analytics-query-patterns.md)を参照してください。Azure Stream Analytics の詳細については、[オンライン ドキュメント](https://azure.microsoft.com/documentation/services/stream-analytics/)を参照してください。

##Azure アカウントのクリーンアップ

Azure ポータルから Stream Analytics ジョブを停止してください。

Setup.ps1 スクリプトでは、2 つの Azure イベント ハブと Azure SQL データベース サーバーが作成されます。チュートリアルの最後に、次の手順に従ってリソースをクリーンアップしてください。

PowerShell ウィンドウで「.\\Cleanup.ps1」と入力します。これにより、チュートリアルで使用したリソースを削除するスクリプトが開始されます。

リソースを表す名前が表示されます。それぞれ慎重に確認してから削除してください。

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)

<!---HONumber=AcomDC_0218_2016-->