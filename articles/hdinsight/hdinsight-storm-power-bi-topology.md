<properties
 pageTitle="Apache Storm から Power BI へのデータの書き込み | Microsoft Azure"
 description="HDInsight の Apache Storm クラスター上で実行されている C# トポロジから Power BI にデータを書き込みます。また、Power BI を使用してレポートとリアルタイムのダッシュボードも作成します。"
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="01/08/2016"
 ms.author="larryfr"/>

# Power BI を使用した Apache Storm トポロジのデータの視覚化

Power BI を使用すると、データをレポートとして表示したり、ダッシュボードに表示したりできます。Power BI REST API によって、HDInsight クラスター上の Apache Storm で実行しているトポロジのデータを Power BI で簡単に使用できます。

このドキュメントでは、Power BI を使用して、Storm トポロジによって作成されたデータから、レポートとダッシュボードを作成する方法について説明します。

## 前提条件

- Azure サブスクリプション。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

* [Power BI](https://powerbi.com) アクセス権を持つ Azure Active Directory ユーザー

* 下記のいずれかのバージョンの Visual Studio

    * Visual Studio 2012 ([update 4](http://www.microsoft.com/download/details.aspx?id=39305))

    * Visual Studio 2013 ([update 4](http://www.microsoft.com/download/details.aspx?id=44921)) または [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)

    * Visual Studio 2015 [CTP6](http://visualstudio.com/downloads/visual-studio-2015-ctp-vs)

* HDInsight Tools for Visual Studio: インストールの情報については、「[HDInsight Tools for Visual Studio の使用開始](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)」を参照してください。

## 動作のしくみ

この例に含まれる C# Storm トポロジは、文をランダムに生成し、その文を単語に分割し、単語をカウントし、単語とカウントを Power BI REST API に送信します。[PowerBi.Api.Client](https://github.com/Vtek/PowerBI.Api.Client) Nuget パッケージは Power BI との通信に使用されます。

このプロジェクトの次のファイルによって、Power BI 固有の機能が実装されます。

* **PowerBiBolt.cs**: Power BI にデータを送信するStorm ボルトを実装します。

* **Data.cs**: Power BI に送信されるデータのオブジェクト/行について記述します。

> [AZURE.WARNING] Power BI が、同じ名前を持つ複数のデータセットの作成を許可しているように見える場合があります。これは、データセットが存在しない場合に、トポロジで Power BI ボルトの複数のインスタンスを作成すると生じる可能性があります。これを回避するには、(この例の場合のように) ボルトの並列処理ヒントを 1 に設定するか、トポロジをデプロイする前にデータセットを作成します。
>
> このソリューションに含まれている **CreateDataset** コンソール アプリケーションは、トポロジ外部でデータセットを作成する方法の例となっています。

## Power BI アプリケーションの登録

1. 「[Power BI のクイック スタート](https://msdn.microsoft.com/library/dn931989.aspx)」に示されている手順に従って、Power BI にサインアップします。

2. 「[アプリの登録](https://msdn.microsoft.com/library/dn877542.aspx)」に示されている手順に従って、アプリケーションの登録を行います。登録情報は、Power BI REST API にアクセスするときに使用します。

    > [AZURE.IMPORTANT] アプリケーション登録に**クライアント ID** を保存します。

## 例のダウンロード

[HDInsight C# Storm Power BI の例](https://github.com/Blackmist/hdinsight-csharp-storm-powerbi)をダウンロードします。ダウンロードするには、[git](http://git-scm.com/) を使用してフォーク/複製するか、**ダウンロード** リンクを使用してアーカイブの ZIP ファイルをダウンロードします。

## サンプルの構成

1. Visual Studio でサンプルを開きます。**ソリューション エクスプローラー**で **App.config** ファイルを開き、**<OAuth .../>** 要素を見つけます。この要素の次のプロパティの値を入力します。

    * **Client**: 作成済みのアプリケーション登録のクライアント ID。

    * **User**: Power BI に対するアクセス権を持つ Azure Active Directory アカウント。

    * **Password**: Azure Active Directory アカウントのパスワード。

2. (省略可能)。このプロジェクトで使用される既定のデータセット名は **Words** です。これを変更する場合、**ソリューション エクスプローラー**で **WordCount** プロジェクトを右クリックし、[**プロパティ**]、[**設定**] の順に選択します。**DatasetName** エントリを必要な値に変更します。

2. ファイルを保存して閉じます。

## サンプルのデプロイ

1. **ソリューション エクスプローラー**で **WordCount** プロジェクトを右クリックし、[**HDInsight の Storm に送信 (Submit to Storm on HDInsight)**] を選択します。[**Storm クラスター**] ドロップダウン ダイアログから HDInsight クラスターを選択します。

    > [AZURE.NOTE] [**Storm クラスター**] ドロップダウンにサーバー名が設定されるには数秒かかることがあります。
    >
    > メッセージが表示されたら、Azure サブスクリプションのログイン資格情報を入力します。2 つ以上のサブスクリプションをお持ちの場合は、HDInsight クラスターの Storm があるサブスクリプションにログインします。

2. トポロジが正常に送信されたら、クラスターの Storm トポロジが表示されます。一覧から [WordCount] トポロジを選択して、実行中のトポロジに関する情報を表示します。

    ![WordCount トポロジが選択されているトポロジ](./media/hdinsight-storm-power-bi-topology/topologysummary.png)

    > [AZURE.NOTE] また、サーバー エクスプローラーから Storm トポロジを表示することもできます。その場合、[Azure]、[HDInsight] の順に展開して、HDInsight クラスターの Storm を右クリックして [Storm トポロジの表示 (View Storm Topologies)] を選択します。

3. [**トポロジの概要**] を表示した状態で、[**ボルト (Bolts)**] セクションが表示されるまでスクロールします。このセクションで、**PowerBI** ボルトの [**実行済み (Executed)**] 列に注目します。ページ上部にある [更新] ボタンを使用して、値が 0 以外の値を変更するまで更新を実行します。この数値が増えると、対象項目が Power BI に書き込まれていることを示します。

## レポートの作成

1. ブラウザーで、[https://PowerBI.com](https://powerbi.com) を表示します。ご使用のアカウントでサインインします。

2. ページの左側にある [**データセット**] を展開します。[**Words**] エントリを選択します。これは、トポロジの例で作成したデータセットです。

    ![Words データセット エントリ](./media/hdinsight-storm-power-bi-topology/words.png)

3. [**フィールド**] 領域で、[**WordCount**] を展開します。[**カウント (Count)**] エントリと [**単語 (Word)**] エントリをページ中ほどにドラッグします。これにより、対象の単語が何回出現したかを示す棒が単語ごとに表示された新しいグラフが作成されます。

    ![WordCount グラフ](./media/hdinsight-storm-power-bi-topology/wordcountchart.png)

4. ページの左上で、[**保存**] を選択して新しいレポートを作成します。レポートの名前として **Word Count** と入力します。

5. Power BI ロゴを選択し、ダッシュボードに戻ります。これで、**Word Count** レポートが [**レポート**] に表示されるようになりました。

## ライブ ダッシュボードの作成

1. [**ダッシュボード**] の横にある **+** アイコンを選択し、新しいダッシュボードを作成します。新しいダッシュボードの名前を **Live Word Count** とします。

2. 作成済みの **Word Count** レポートを選択します。表示されたら、グラフを選択し、グラフの右上にあるプッシュピン アイコンを選択します。ピン留めしたことを示す通知をダッシュボードで受信します。

    ![プッシュピンが表示されているグラフ](./media/hdinsight-storm-power-bi-topology/pushpin.png)

2. Power BI ロゴを選択し、ダッシュボードに戻ります。**Live Word Count** ダッシュボードを選択します。ダッシュボードに Word Count グラフが含まれるようになり、HDInsight で実行されている WordCount トポロジから Power BI に新しいエントリが送信されるとグラフが更新されます。

    ![ライブ ダッシュボード](./media/hdinsight-storm-power-bi-topology/dashboard.png)

## WordCount トポロジの停止

トポロジの実行は、意図的に停止するか、HDInsight クラスター上で Storm を削除するまで続きます。トポロジを停止するには、以下の手順を実行します。

1. Visual Studio で、WordCount トポロジの [**トポロジの概要**] ウィンドウを開きます。[トポロジの概要] が開いていない場合、**サーバー エクスプローラー**に移動し、[**Azure**] エントリ、[**HDInsight**] エントリの順に展開し、HDInsight クラスター上の Storm を右クリックし、[**Storm トポロジの表示 (View Storm Topologies)**] を選択します。最後に、[**WordCount**] トポロジを選択します。

2. [**強制終了 (Kill)**] ボタンを選択して [**WordCount**] トポロジを停止します。

    ![[トポロジの概要] の [強制終了 (Kill)] ボタン](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## 次のステップ

このドキュメントでは、REST を使用して Strom トポロジのデータを Power BI に送信する方法を説明します。他の Azure テクノロジを使用する方法については、以下をご覧ください。

* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0218_2016-->