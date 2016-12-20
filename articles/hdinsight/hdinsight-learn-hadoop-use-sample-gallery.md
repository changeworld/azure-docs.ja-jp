---
title: "サンプル ギャラリーを使用した、HDInsight での Hadoop についての説明 | Microsoft Docs"
description: "HDInsight 概要ギャラリーのサンプル アプリケーションを実行して Hadoop をすばやく学習できます。 サンプル データを使用するか、独自のデータを用意します。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 3d391bf3-fcb2-4956-8a2c-7c142e952bbb
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0c5a9ee8d437a529c320f19a6f32c88bd38326e7


---
# <a name="learn-hadoop-by-using-the-azure-hdinsight-getting-started-gallery"></a>HDInsight 概要ギャラリーを使用して Hadoop について説明する
概要ギャラリーは、Windows ベースの HDInsight クラスターでのみ使用できます。 概要ギャラリーでは、HDInsight でサンプル アプリケーションを実行して Hadoop をすばやく簡単に学習できます。 一部のサンプルには、サンプル データが含まれています。 その他のサンプルでは、独自のデータを使用いただけます。 現時点では、次の 6 つのサンプルがあります (今後追加される予定)。

* Azure データを使用したソリューション
  * Microsoft Azure Web サイトのログ分析
  * Microsoft Azure のストレージ分析
* サンプル データを使用したソリューション
  * センサー データの分析
  * Twitter の傾向分析
  * Web サイト ログの分析
  * Mahout 映画のリコメンデーション

![HDInsight Hadoop、Storm、HBase 概要ギャラリー ソリューション (サンプル データ付き)。][hdinsight.sample.gallery]

次のビデオでは、Twitter の傾向分析サンプルを実行する方法を示しています。

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.youtube.com/embed/7ePbHot1SN4></a></center>

ダッシュボードには、http://<YourHDInsightClusterName> を参照してアクセスするか、Azure ポータルからアクセスできます。

**概要ギャラリーからサンプルを実行するには**

1. [Azure ポータル][azure.portal]にサインインします。
2. 左側のメニューから **[参照]** をクリックし、**[HDInsight クラスター]**、クラスター名の順にクリックします。
3. 上部のメニューから **[ダッシュボード]** をクリックします。
4. HTTP ユーザー (別名: クラスター ユーザー) のユーザー名とパスワードを入力します。
5. ページの上部にある **[概要ギャラリー]** をクリックします。
6. サンプルを 1 つクリックします。 各サンプルで、実行するための詳細な手順が表示されます。 次の図は、Twitter の傾向分析のサンプルを示しています。
   
    ![HDInsight Twitter の傾向分析サンプル][hdinsight.twitter.sample]

## <a name="next-steps"></a>次のステップ
HDInsight に関するその他の資料。

* [HDInsight の学習マップ][hdinsight.learn.map]
* [HDInsight のインフォグラフィック][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]:https://portal.azure.com



<!--HONumber=Nov16_HO3-->


