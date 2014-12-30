<properties urlDisplayName="Understand NoSQL technologies" pageTitle="Azure での NoSQL テクノロジについて | Azure" metaKeywords="" description="Learn how NoSQL technologies on HDInsight can help you manage data not suited to relational databases, such as big data sets and JSON documents or graphs." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="storage,hdinsight" documentationCenter="" title="Data management: Understanding NoSQL technologies on Azure" authors="dchappell" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/01/2014" ms.author="dchappell" />

# データ管理:Azure での NoSQL テクノロジについて

**概要:**Azure には NoSQL ワークロード用に多様なオプションが用意されていますが、利用できるオプションすべての相違点を判別することは簡単ではありません。このドキュメントでは、Azure での NoSQL テクノロジについて説明し、リレーショナルモデルでデータを管理する場合との違いを説明します。

リレーショナル テクノロジは、長年、データ処理の支配的な手法でした。ただし、さまざまな種類のデータを大量に処理する場合 (今日ではそのようなケースがますます増えてきていますが)、その大量のデータを管理するという新たな問題に直面します。リレーショナル テクノロジは、このようなデータの管理に最適な場合もありますが、あまり適さない場合もあります。このガイドでは、オプションについて順に説明し、各オプションが提供する機能とその使用理由を示します。


**目次:**

* Azure に関するデータ:全体像	
* Azure のリレーショナル テクノロジ:簡単な解説	
* Azure の NoSQL テクノロジ
	* ドキュメント ストア:DocumentDB
	* キー/値ストア:テーブル
	* 列ファミリ ストア:HBase
	* Big Data の分析:HDInsight 


**執筆者:**David Chappell (Principal of Chappell & Associates)

**発行:**2014 年 11 月

**リビジョン:**2.0

**ダウンロード:** <a href="http://go.microsoft.com/fwlink/p/?LinkId=330292" target="_blank">「Understanding NoSQL on Microsoft Azure (Microsoft Azure での NoSQL テクノロジについて)」の PDF ファイル</a>

<!--HONumber=35_1-->
