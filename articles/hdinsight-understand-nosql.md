<properties linkid="manage-hdinsight-understand-nosql" urlDisplayName="Understand NoSQL technologies" pageTitle="Understanding NoSQL Technologies on Azure | Azure" metaKeywords="" description="Learn how NoSQL technologies on HDInsight can help you manage data not suited to relational databases, such as big data sets and JSON documents or graphs." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="storage,hdinsight" documentationCenter="" title="Data management: Understanding NoSQL technologies on Azure" authors="dchappell" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="dchappell"></tags>

# データ管理: Azure での NoSQL テクノロジについて

**概要:** Azure には NoSQL ワークロード用に多様なオプションが用意されていますが、どれが適切か判断することは簡単ではありません。このドキュメントでは、Azure 上で利用できる NoSQL テクノロジについて説明した後、非構造化データ セットや、特にしばしば "ビッグ データ" と呼ばれる大規模なデータ セットなどの非リレーショナル データを管理する適切なサービスを選択する方法を説明します。

リレーショナル テクノロジは、長年、データ処理の支配的な手法でした。ところが、種類の異なる非常に大量のデータを処理しようとすると、多数のサーバーにまたがって規模を拡大することは容易ではありません。また、リレーショナル テクノロジは、JSON ドキュメントやグラフのようなデータを管理する場合は最適でもありません。このガイドでは、オプションについて順に説明し、各オプションが提供する機能とその使用理由を示します。

**目次:**

-   Azure に関するデータ: 全体像
-   Azure のリレーショナル テクノロジ: 概要
-   Azure の NoSQL テクノロジ: 運用データ

    -   キー/値ストア
    -   列ファミリ ストア
    -   ドキュメント ストア
    -   グラフ データベース
-   Azure の NoSQL テクノロジ: 分析データ

    -   Hadoop MapReduce
    -   HDInsight

**執筆者:** : David Chappell (Principal of Chappell & Associates)

**発行:** 2013 年 11 月

**リビジョン:** 1.0

**ダウンロード:** [「Understanding NoSQL technologies on Azure (Azure での NoSQL テクノロジについて)」の PDF ファイル][]

  [「Understanding NoSQL technologies on Azure (Azure での NoSQL テクノロジについて)」の PDF ファイル]: http://go.microsoft.com/fwlink/p/?LinkId=330292
