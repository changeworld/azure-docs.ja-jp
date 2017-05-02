---
title: "HDInsight での Apache Storm を使用した Twitter のトレンディング トピック | Microsoft Docs"
description: "Trident を使用して、Twitter で (ハッシュタグに基づいて) トレンディング トピックを見つける Apache Storm トポロジを作成する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 63b280ea-5c07-4dc8-a35f-dccf5a96ba93
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: d588221586f151319436525c5098b0bb2694e5f9
ms.lasthandoff: 04/18/2017


---
# <a name="determine-twitter-trending-topics-with-apache-storm-on-hdinsight"></a>HDInsight での Apache Storm を使用した Twitter のトレンディング トピックの確認

Trident を使用して、Twitter のトレンディング トピック (ハッシュタグ) を見つける Storm トポロジを作成する方法について説明します。

Trident は、結合、集計、グループ化、関数、フィルタリングなどのツールを提供する大枠の抽象概念です。 さらに、Trident はステートフルな増分処理を行うためのプリミティブを追加します。 このドキュメントで使用されている例は、カスタムのスパウトと関数を持つ Trident トポロジです。 また、Trident によって提供されるいくつかの組み込み関数も使用されています。

## <a name="requirements"></a>必要条件

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java と JDK 1.8</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Twitter の開発者アカウント

## <a name="download-the-project"></a>プロジェクトのダウンロード

次のコードを使用してプロジェクトの複製をローカルに作成します。

    git clone https://github.com/Blackmist/TwitterTrending

## <a name="understanding-the-topology"></a>トポロジについて

次の図は、データがこのトポロジをどのように流れるかを示しています。

![トポロジ](./media/hdinsight-storm-twitter-trending/trident.png)

> [!NOTE]
> この図は、トポロジの簡略化されたビューです。 コンポーネントの複数のインスタンスが、クラスター内のノード全体に分散されています。


トポロジを実装する Trident コードは次のとおりです。

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

このコードは、次のアクションを実行します。

1. スパウトからストリームを作成します。 スパウトが Twitter からツイートを取得し、特定のキーワード (この例では、love、music、coffee) でフィルターします。

2. カスタム関数の HashtagExtractor を使用して各ツイートからハッシュタグを抽出します。 ハッシュタグはストリームに出力されます。

3. ストリームはハッシュタグごとにグループ化され、アグリゲーターに渡されます。 このアグリゲーターがハッシュタグが発生した回数のカウントを作成します。 このデータは、メモリに保存されます。 最後に、ハッシュタグとカウントを含む新しいストリームが出力されます。

4. **FirstN** アセンブリが適用され、カウント フィールドに基づいて 上位 10 個の値のみが返されます。

> [!NOTE]
> Trident の使用の詳細については、「[Trident API の概要](http://storm.apache.org/releases/current/Trident-API-Overview.html)」というドキュメントを参照してください。

### <a name="the-spout"></a>スパウト

スパウトである **TwitterSpout** は、[Twitter4j](http://twitter4j.org/en/) を使用して Twitter からのツイートを取得します。 __love__、**music**、および **coffee** という語のフィルターが作成されます。 フィルターに一致する受信ツイート (状態) が、リンクされたブロック キューに格納されます。 最後に、アイテムがキューから取り出されてトポロジに出力されます。

### <a name="the-hashtagextractor"></a>HashtagExtractor

ハッシュタグを抽出するには、[getHashtagEntities](http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--) を使用してツイートに含まれるすべてのハッシュタグを取得します。 その後、これらはストリームに出力されます。

## <a name="configure-twitter"></a>Twitter の構成

次の手順を使用して、新しい Twitter アプリケーションを登録し、Twitter からの読み取りに必要なコンシューマーとアクセス トークンを取得します。

1. [Twitter アプリ](https://apps.twitter.com)に移動して、**[Create new app (新しいアプリの作成)]** ボタンをクリックします。 フォームの入力時、 **[Callback URL]** フィールドは空白のままにします。

2. アプリが作成されたら、 **[Keys and Access Tokens]** タブをクリックします。

3. **[Consumer Key (コンシューマー キー)]** と **[Consumer Secret (コンシューマーシークレット)]** の情報をコピーします。

4. トークンがない場合、ページ下部で **[Create my access token]** を選択します。 トークンを作成したら、**[Access Token]** と **[Access Token Secret]** の情報をコピーします。

5. 前に複製した **TwitterSpoutTopology** プロジェクトで、**resources/twitter4j.properties** ファイルを開いて、前の手順で収集した情報を追加し、ファイルを保存します。

## <a name="build-the-topology"></a>トポロジの作成

次のコードを使用してプロジェクトを構築します。

        cd [directoryname]
        mvn compile

## <a name="test-the-topology"></a>トポロジのテスト

次のコマンドを使用して、ローカルでトポロジをテストします。

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

トポロジの起動後、トポロジから出力されたハッシュタグとカウントを含むデバッグ情報を確認します。 出力は次のテキストのようになります。

    DEBUG: [Quicktellervalentine, 7]
    DEBUG: [GRAMMYs, 7]
    DEBUG: [AskSam, 7]
    DEBUG: [poppunk, 1]
    DEBUG: [rock, 1]
    DEBUG: [punkrock, 1]
    DEBUG: [band, 1]
    DEBUG: [punk, 1]
    DEBUG: [indonesiapunkrock, 1]

## <a name="next-steps"></a>次のステップ

これで、トポロジをローカルでテストできました。次はこのトポロジをデプロイする方法、「[HDInsight での Apache Storm トポロジの展開と管理](hdinsight-storm-deploy-monitor-topology.md)」をご確認ください。

必要に応じて次の Storm 関連のトピックもご覧ください。

* [HDInsight での Apache Storm の Java ベース トポロジの開発](hdinsight-storm-develop-java-topology.md)
* [Visual Studio を使用して HDInsight で Storm の C# トポロジを開発する](hdinsight-storm-develop-csharp-visual-studio-topology.md)

HDInsight での Storm のその他の例:

* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)


