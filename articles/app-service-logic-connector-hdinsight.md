<properties 
   pageTitle="HDInsight コネクタ" 
   description="HDInsight コネクタの使用方法" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Microsoft HDInsight コネクタ #

コネクタをロジック アプリで使用すると、フローの一部として、データをフェッチ、処理、またはプッシュ転送できます。HDInsight コネクタでは、Azure 上で Hadoop クラスターを作成し、Hive、Pig、MapReduce、ストリーミング MapReduce など、さまざまな Hadoop ジョブを作成して送信できます。Azure HDInsight サービスは、Apache Hadoop クラスターをクラウドにデプロイしてプロビジョニングするためのサービスです。ビッグ データの管理、分析、レポート生成を支援するソフトウェア フレームワークとなります。Hadoop コアには、データ ストレージの信頼性を高める Hadoop 分散ファイル システム (HDFS) と、この分散システムに格納されているデータの処理と分析を同時に行うシンプルな MapReduce プログラミング モデルが用意されています。また、HDInsight コネクタを使用して、クラスターのスピン、ジョブの送信、ジョブの完了待機を実行できます。

### 基本的なアクション
		
- クラスターを作成
- クラスターの作成を待機
- Pig ジョブを送信
- Hive ジョブを送信
- MapReduce ジョブを送信
- ジョブの完了を待機
- クラスターを削除


## HDInsight コネクタ API アプリのインスタンスを作成する ##

HDInsight コネクタを使用するには、まず HDInsight コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の操作を実行します。

1. Azure ポータルの左下にある '[+ 新規]' を使用して Azure Marketplace を開きます。
2. [Web + モバイル]、[API アプリ] の順に移動して、"HDInsight コネクタ" を検索します。
3. 最初のブレードで、名前、アプリ サービス プランなどの一般的な詳細を入力します。
4. パッケージ設定の一部として、HDInsight クラスターのユーザー名とパスワードを入力します。


 ![][1]  

## 証明書の構成 (省略可能) ##

注:この手順は、ロジック アプリで管理操作 (クラスターの作成と削除) を実行する場合のみ必要です。

[参照]、[API アプリ]、[<作成した API アプリの名前>] の順に選択して、作成した API アプリを参照します。次の動作が表示されます。 '[セキュリティ]' コンポーネントに 0 が表示されます。これはアップロードされている管理証明書がないことを意味します。

![][2] 

API アプリに管理証明書をアップロードするには、次の手順に従います。
1.  '[セキュリティ]' コンポーネントをクリックします。
2. 開いている  '[セキュリティ]' ブレードで、 [証明書のアップロード]' をクリックします。
3. 次のブレードで証明書ファイルを参照して選択します。
4. 証明書を選択したら、[OK] をクリックします。

証明書が正常にアップロードされると、証明書の詳細が表示されます。

![][3] 

注:証明書を変更する場合は、既存の証明書を置き換える別の証明書をアップロードできます。

## ロジック アプリでの使い方 ##

HDInsight コネクタは、ロジック アプリでアクションとしてのみ使用できます。クラスタを作成し、 'Hive' ジョブを実行し、ジョブの完了時にクラスタを削除する、簡単なロジック アプリを見てみましょう。


- ロジック アプリを作成または編集するときに、アクションとして作成された HDInsight コネクタの API アプリを選択すると、使用可能なすべてのアクションが表示されます。.

![][5] 


-  '[クラスターの作成]' を選択し、必要なクラスター パラメーターをすべて入力したら、[✓] をクリックします。

![][6] 



- ロジック アプリで構成されると、アクションが表示されます。アクションの出力が表示されます。これは、後続のアクションの入力として使用できます。 

![][7] 



- ギャラリーからアクションと同じ HDInsight コネクタを選択します。 '[Wait For Cluster Creation (クラスターの作成を待機)]' アクションを選択し、必要なパラメーターをすべて入力したら、[✓] をクリックします。

![][8] 



- ギャラリーからアクションと同じ HDInsight コネクタを選択します。 '[Hive ジョブの送信]' アクションを選択し、必要なパラメーターをすべて入力したら、[✓] をクリックします。

![][9] 



- ギャラリーからアクションと同じ HDInsight コネクタを選択します。 '[ジョブの完了を待機]' アクションを選択し、必要なパラメーターをすべて入力したら、[✓] をクリックします。

![][10] 



- ギャラリーからアクションと同じ HDInsight コネクタを選択します。 '[Hive ジョブの送信]' アクションを選択し、必要なパラメーターをすべて入力したら、[✓] をクリックします。

![][11] 


 '[実行]' をクリックしてロジック アプリを手動で起動し、シナリオをテストできます。

<!--Image references-->
[1]: ./media/app-service-logic-connector-hdinsight/Create.jpg
[2]: ./media/app-service-logic-connector-hdinsight/CertNotConfigured.jpg
[3]: ./media/app-service-logic-connector-hdinsight/CertConfigured.jpg
[5]: ./media/app-service-logic-connector-hdinsight/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-hdinsight/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-hdinsight/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-hdinsight/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-hdinsight/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-hdinsight/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-hdinsight/LogicApp7.jpg


<!--HONumber=52-->