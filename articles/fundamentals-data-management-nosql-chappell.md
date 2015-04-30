<properties urlDisplayName="Understand NoSQL technologies" pageTitle="Azure での NoSQL テクノロジについて | Azure" metaKeywords="" description="ビッグ データや JSON ドキュメント、グラフのようなリレーショナル データベースに適していないデータを管理するのに、HDInsight 上の NoSQL テクノロジがどのように役立つかについて説明します。" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="storage,hdinsight,documentdb" documentationCenter="" title="Data management: Understanding NoSQL technologies on Azure" authors="cgronlun" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/01/2014" ms.author="cgronlun" />

# データ管理:Microsoft Azure での NoSQL について

**概要:** Azure には NoSQL ワークロード向けのさまざまなオプションが用意されていますが、利用可能なすべてのオプションの相違点を逐一確認するのは困難な場合があります。このドキュメントでは、Azure での NoSQL テクノロジについて説明し、データを管理する際のリレーショナル モデルとの相違点について確認して行きます。

リレーショナル テクノロジは、長年、データ処理の支配的な手法でした。ところが、最近よくあるケースですが、さまざまな種類の大量のデータを扱う際に管理が大変になる場合があります。リレーショナル テクノロジはこうしたデータの管理に最適な場合もあれば、最適でない場合もあります。このガイドでは、オプションについて順に説明し、各オプションが提供する機能とその使用理由を示します。


**目次:**

* Azure に関するデータ:全体像	
* Azure のリレーショナル テクノロジ:簡単な概要	
* Azure の NoSQL テクノロジ
	* ドキュメント ストア:DocumentDB
	* キー/値ストア:テーブル
	* 列ファミリ ストア:HBase
	* ビッグ データ分析:HDInsight 


**作成者**: David Chappell (Principal of Chappell &amp; Associates)

**発行:** 2014年 11 月

**リビジョン:** 2.0

**ダウンロード:** <a href="http://go.microsoft.com/fwlink/p/?LinkId=330292" target="_blank">「Azure での NoSQL について」の PDF ファイル</a>


<!--HONumber=35.2-->