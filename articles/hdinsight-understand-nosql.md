<properties linkid="manage-hdinsight-understand-nosql" urlDisplayName="NoSQL のテクノロジを理解する" pageTitle="Azure での NoSQL テクノロジについて | Azure" metaKeywords="" description="ビッグ データ セットと JSON ドキュメント、またはグラフなど、リレーショナル データベースに適していないデータの管理に、HDInsight の NoSQL テクノロジがどのように役立つかを説明します。" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="storage,hdinsight" documentationCenter="" title="データ管理: Azure での NoSQL テクノロジについて" authors="dchappell" />

#データ管理: Azure での NoSQL テクノロジについて

**概要:** Azure には、NoSQL ワークロード用に多様なオプションが用意されていますが、どれが適切かを判断することは容易ではありません。このドキュメントでは、Azure 上で利用できる NoSQL テクノロジについて説明した後、非構造化データ セットや、特にしばしば "ビッグ データ" と呼ばれる大規模なデータ セットなどの非リレーショナル データを管理する適切なサービスを選択する方法を説明します。

リレーショナル テクノロジは、長年、データ処理の支配的な手法でした。ところが、種類の異なる非常に大量のデータを処理しようとすると、多数のサーバーにまたがって規模を拡大することは容易ではありません。また、リレーショナル テクノロジは、JSON ドキュメントやグラフのようなデータを管理する場合は最適でもありません。このガイドでは、オプションについて順に説明し、各オプションが提供する機能とその使用理由を示します。

**目次:**

*Azure 上のデータ: 全体像	
*Azure リレーショナル テクノロジ: 概要	
*Azure NoSQL テクノロジ: 運用データ	
	*キー/値ストア	
	*列ファミリ ストア	
	*ドキュメント ストア	
	*グラフ データベース	
*Azure NoSQL テクノロジ: 分析データ	
	*Hadoop MapReduce	
	*HDInsight	

**作成者**: David Chappell (Principal of Chappell &amp; Associates)

**発行:** 2013年 11 月

**リビジョン:** 1.0

**ダウンロード:**<a href="http://go.microsoft.com/fwlink/p/?LinkId=330292" target="_blank">「Azure での NoSQL テクノロジについて」の PDF ファイル</a>


