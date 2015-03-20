<properties
   pageTitle="HDInsight | Azure での Apache Storm を使用した Twitter のトレンディング トピック"
   description="Trident を使用して、ハッシュタグに基づいてトレンディング トピックを見つける Storm トポロジを作成する方法について説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#HDInsight での Apache Storm を使用した Twitter のトレンディング トピック

Trident を使用して、Twitter のトレンディング トピック (ハッシュタグ) を見つける Storm トポロジを作成する方法について説明します。 

Trident は、結合、集計、グループ化、フィルタリングなどの実行を容易にするための、高レベルの抽象概念です。さらに、Trident はステートフルな増分処理を行うためのプリミティブを追加します。この例では、カスタムのスパウト、関数、Trident で提供される複数のビルトイン関数を用いてトポロジを構築する方法を示しています。

> [AZURE.NOTE] これは、Juan Alonso による [trident-storm](https://github.com/jalonsoramos/trident-storm) の例に基づいています。

##要件

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java と JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Twitter の開発者アカウント

##プロジェクトのダウンロード

次を使用してプロジェクトの複製をローカルに作成します。

	git clone https://github.com/Blackmist/TwitterTrending

##トポロジ

この例のトポロジは次のとおりです。

![topology](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE] これはトポロジの簡略化されたビューです。コンポーネントの複数のインスタンスが、クラスター内のノード全体に配布されます。

トポロジを実装する Trident コードは次のとおりです。

	topology.newStream("spout", spout)
	    .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
	    .groupBy(new Fields("hashtag"))
	    .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
	    .newValuesStream()
	    .applyAssembly(new FirstN(10, "count"))
		.each(new Fields("hashtag", "count"), new Debug());

コードの内容は次のとおりです。

1. スパウトから新しいストリームを作成します。スパウトが Twitter からツイートを取得します。この例では、このツイートは、愛、音楽、コーヒーフィルターのキーワードでフィルターされています。

2. カスタム関数の HashtagExtractor を使用して各ツイートからハッシュタグを抽出します。これらは、ストリームに出力されます。

3. その後、ストリームはハッシュタグごとにグループ化され、アグリゲーターに渡されます。このアグリゲーターがハッシュタグが発生した回数のカウント (メモリ内に保持されている) を作成します。最後に、ハッシュタグとカウントを含む新しいストリームが出力されます。

4. 必要なのは、特定のツイート群の最も人気のあるハッシュタグのみであるため、カウント フィールドに基づいて上位 10 個の値のみを返す FirstN アセンブリが適用されます。

> [AZURE.NOTE] スパウトと HashtagExtractor 以外については、ビルトインの Trident 関数を使用します。
> 
> ビルトイン操作については、<a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">storm.trident.operation.builtin</a> を参照してください。
> 
> MemoryMapState 以外の Trident ステートの実装については、以下をご覧ください。
> 
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">https://github.com/fhussonnois/storm-trident-elasticsearch</a>
> 
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">https://github.com/kstyrc/trident-redis</a>

###スパウト

スパウトである **TwitterSpout** は、<a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a> を使用して Twitter からのツイートを取得します。フィルター (love、music、および coffee) が作成され、フィルターに一致する受信ツイート (ステータス) が <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">LinkedBlockingQueue</a> に格納されます。最後に、アイテムがキューから取り出されてトポロジに出力されます。

###HashtagExtractor

ハッシュタグを抽出するには、<a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a> を使用してツイートに含まれるすべてのハッシュタグを取得します。その後、これらはストリームに出力されます。

##Twitter を有効にする

次の手順を使用して、新しい Twitter アプリケーションを登録し、Twitter からの読み取りに必要なコンシューマーとアクセス トークンを取得します。

1. <a href="" target="_blank">https://apps.twitter.com/</a> にアクセスし、**[Create new app]** ボタンを使用します。フォームの入力時、**[Callback URL (コールバック URL)]** は空白のままにします。

2. アプリを作成したら、**[Keys and Access Tokens (キーとアクセス トークン)]** タブを選択します。

3. **[Consumer Key (コンシューマー キー)]** と **[Consumer Secret (コンシューマーシークレット)]** の情報をコピーします。

4. トークンがない場合、ページ下部で **[Create my access token (アクセス トークンを作成)]** を選択します。トークンを作成したら、**[Access Token (アクセス トークン)]** と **[Access Token Secret (アクセス トークン シークレット)]** の情報をコピーします。

5. 前に複製した **TwitterSpoutTopology** プロジェクトで、**resources/twitter4j.properties** ファイルを開いて、前の手順で収集した情報を追加し、ファイルを保存します。

##トポロジの作成

次を使用してプロジェクトを構築します。

		cd [directoryname]
		mvn compile

##トポロジのテスト

次のコマンドを使用して、ローカルでトポロジをテストします。

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

トポロジの起動後、トポロジから出力されたハッシュタグとカウントを含むデバッグ情報を確認します。出力は次のようになります。

	DEBUG:[Quicktellervalentine, 7]
	DEBUG:[GRAMMYs, 7]
	DEBUG:[AskSam, 7]
	DEBUG:[poppunk, 1]
	DEBUG:[rock, 1]
	DEBUG:[punkrock, 1]
	DEBUG:[band, 1]
	DEBUG:[punk, 1]
	DEBUG:[indonesiapunkrock, 1]

##次のステップ

これで、トポロジをローカルでテストできました。次は[HDInsight でこのトポロジを Storm にデプロイする]方法をご確認ください(../hdinsight-storm-deploy-monitor-topology/)。

必要に応じて次の Storm 関連のトピックもご覧ください。

* [Develop Java topologies for Storm on HDInsight using Maven (Maven を使用して HDInsight で Storm の Java トポロジを開発する)](../hdinsight-storm-develop-java-topology/)

* [Visual Studio を使用して HDInsight で Storm の C# トポロジを開発する](../hdinsight-storm-develop-csharp-visual-studio-topology/)

HDInsight での Storm のその他の例:

* [HDInsight での Storm の例](https://github.com/hdinsight/hdinsight-storm-examples)

* [HDInsight での Storm を使用した EventHub センサー データの分析](../hdinsight-storm-sensor-data-analysis/)
<!--HONumber=47-->
