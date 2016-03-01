<properties
   pageTitle="Elasticsearch のパフォーマンスをテストするための JMeter JUnit サンプラーを作成し、デプロイする | Microsoft Azure"
   description="JUnit サンプラーを使用してデータを生成し、Elasticsearch クラスターにアップロードする方法"
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="mabsimms"/>
   
# Elasticsearch のパフォーマンスをテストするための JMeter JUnit サンプラーを作成し、デプロイする

これは[一連の記事の一部](guidance-elasticsearch-introduction.md)です。

このドキュメントでは、JMeter テスト プランの一環として、データを生成し Elasticsearch クラスターにアップロードするための JUnit サンプラーを作成し使用する方法について説明します。この方法は、ロード テストに柔軟に対処し、外部のデータ ファイルに頼ることなく大量のテスト データを生成することができます。

> [AZURE.NOTE] ドキュメント「Maximizing Data Ingestion Performance with Elasticsearch on Azure (Elasticsearch on Azure でのデータ取り込みのパフォーマンスを最大限に高める)」に説明されているデータ取り込みのパフォーマンスを評価するためのロード テストは、この方法を使用して構築されています。JUnit コードの詳細については、そのドキュメントを参照してください。

データの取り込みのパフォーマンスをテストするために、Eclipse (Mars) を使用して JUnit コードが開発され、Maven を使用して依存関係が解決されています。次の手順では、Eclipse をインストールし、Maven を構成し、JUnit テストを作成し、さらに、このテストを JMeter テストの JUnit Request サンプラーとしてデプロイするための詳細なプロセスを説明します。

> [AZURE.NOTE] テスト環境の構造と構成の詳細については、ドキュメント「How-To: Create a Performance Testing Environment for Elasticsearch (方法: Elasticsearch 用のパフォーマンス テスト環境の作成)」を参照してください。

## インストールの前提条件

開発用のコンピューター上で [Java ランタイム環境](http://www.java.com/en/download/ie_manual.jsp)がサポートされている必要があります。
また、[Eclipse IDE for Java Developers](https://www.eclipse.org/downloads/index.php?show_instructions=TRUE) をインストールする必要があります。

> [AZURE.NOTE] 「[パフォーマンス テスト環境の構成](#overview)」セクションに説明されている JMeter Master VM を開発環境として使用する場合は、Windows 32 ビット バージョンの Eclipse インストーラーをダウンロードしてください。

## Elasticsearch のロード テストのための JUnit テスト プロジェクトを作成する

1.  Eclipse IDE がまだ起動していない場合は起動し、 *ようこそ* ページを閉じます。

2.  *[File (ファイル)]* メニューの *[New (新規)]* をクリックし、*[Java Project (Java プロジェクト)]* をクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy7.png)

3.  *[New Java Project (新しい Java プロジェクト)]* ウィンドウで、プロジェクト名を入力し、*[Use default JRE (既定の JRE を使用)]* をクリックし、*[Finish (完了)]* をクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy8.png)

4.  *[Package Explorer]* ウィンドウで、プロジェクトに基づいて名前を付けたノードを展開します。その中に、*src* という名前のフォルダーと、指定した JRE への参照が含まれていることを確認します。

![](./media/guidance-elasticsearch-jmeter-deploy9.png)

5.  *[src]* を右クリックし、*[New (新規)]* をクリックし、*[JUnit Test Case (JUnit テスト ケース)]* をクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy10.png)

6.  *[New JUnit Test Case (新規 JUnit テスト ケース)]* ウィンドウで、*[New Junit 4 tesｔ (新規 Junit 4 テスト)]* を選択し、パッケージの名前とテスト クラスの名前を入力し (パッケージ名はプロジェクトの名前と同じにすることができますが、規則により、小文字で始める必要があります)、テストに必要なメソッド スタブを生成するオプションを選択します。*[Class under test (テスト対象のクラス)]* ボックスは空のままとし、*[Finish (完了)]* をクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy11.png)

7.  次に示す *[New JUnit Test Case (新規 JUnit テスト ケース)]* ダイアログ ボックスが表示されたら、「ビルド パスに JUnit 4 ライブラリを追加する」オプションを指定して、*[OK]* をクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy12.png)

8.  JUnit テスト用のスケルトン コードが生成され、Java エディター ウィンドウに表示されることを確認します。

![](./media/guidance-elasticsearch-jmeter-deploy13.png)

9.  *Package Explorer* で、プロジェクト ノードを右クリックし、*[Configure (構成)]* をクリックし、*[Convert to Maven Project (Maven プロジェクトに変換)]* をクリックします。

> [AZURE.NOTE] Maven を使用することで、プロジェクトが依存する外部の依存関係 (Elasticsearch Java クライアント ライブラリなど) をより簡単に管理できます。

![](./media/guidance-elasticsearch-jmeter-deploy14.png)

10.  *[Create new POM (新規 POM の作成)]* ダイアログ ボックスの *[Packaging (パッケージ)]* ドロップダウン リスト ボックスで、*[jar]* をクリックし、*[Finish (完了)]* をクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy15.png)

11.  POM エディターの下部のウィンドウに次の警告が表示される場合があります。 *ビルド パスによって実行環境 J2SE-1.5 が指定されています。この環境と厳密に互換性がある JRE がワークスペースにインストールされていません* 。Java のバージョンが 1.5 より新しい場合、この警告は無視してかまいません。

![](./media/guidance-elasticsearch-jmeter-deploy16.png)

12.  POM エディターで、*[Properties (プロパティ)]* を展開し、*[Create (作成)]* をクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy17.png)

13.  *[Add Property (プロパティの追加)]* ダイアログ ボックスで、*[Name (名前)]* ボックスに 「*es.version*」 と入力し、*[Value (値)]* ボックスに 「*1.7.2*」 と入力し、*[OK]* をクリックします。この値は、使用する Elasticsearch Java クライアント ライブラリのバージョンです (このバージョンは、将来、置き換えられる可能性があります。バージョンを POM プロパティとして定義し、プロジェクト内の別の場所内からこのプロパティを参照することで、バージョンをすばやく変更することができます)。

![](./media/guidance-elasticsearch-jmeter-deploy18.png)

14.  POM エディターのベースにある *[Dependencies (依存関係)]* タブをクリックし、*[Dependencies (依存関係)]* リスト ボックスの横にある *[Add (追加)]* をクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy19.png)

15.  *[Select Dependency (依存関係の選択)]* ダイアログ ボックスで、*[Group Id (グループ ID)]* ボックスに 「*org.elasticsearch*」 と入力し、*[Artifact Id (アーティファクト ID)]* ボックスに 「*elasticsearch*」 と入力し、*[Version (バージョン)]* ボックスに 「*\\${es.version}*」 と入力して、*[OK]* をクリックします。Java Elasticsearch クライアント ライブラリに関する情報はオンラインの Maven Central リポジトリに保持されます。この構成では、プロジェクトのビルド時に、ライブラリとその依存関係が自動的にダウンロードされます。

![](./media/guidance-elasticsearch-jmeter-deploy20.png)

16.  *[File (ファイル)]* メニューの *[Save All (すべて保存)]* をクリックします。この操作でプロジェクトを保存およびビルドすると、Maven で指定された依存関係がダウンロードされます。Package Explorer に *Maven Dependencies* フォルダーが表示されていることを確認します。このフォルダーを展開して、Elasticsearch Java クライアント ライブラリをサポートするためにダウンロードした jar ファイルを表示します。

![](./media/guidance-elasticsearch-jmeter-deploy21.png)


## 既存の JUnit テスト プロジェクトを Eclipse にインポートする

> [AZURE.NOTE] この手順では、Eclipse によって以前に作成された Maven プロジェクトがダウンロード済みであることを前提とします。

1.  Eclipse IDE を起動します。

2.  *[File (ファイル)]* メニューの *[Import(インポート)]* をクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy22.png)

3.  *[Select (選択)]* ウィンドウで、*[Maven]* フォルダーを展開し、*[Existing Maven Projects (既存の Maven プロジェクト)]* をクリックし、*[Next (次へ)]* をクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy23.png)

4.  *Maven Projects* ウィンドウで、プロジェクトを保持しているフォルダー (pom.xml ファイルを含むフォルダー) を指定し、*[Select All (すべて選択)]* をクリックし、*[Finish (完了)]* をクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy24.png)

5.  *[Package Explorer]* ウィンドウで、プロジェクトに対応するノードを展開します。プロジェクトに *src* という名前のフォルダーが含まれていることを確認します。このフォルダーには、JUnit テストのソース コードが含まれています。プロジェクトをコンパイルしてデプロイするには、「[JMeter に JUnit テストをデプロイする](#deploying-a-junit-test-to-jmeter)」に示す手順に従ってください。

![](./media/guidance-elasticsearch-jmeter-deploy25.png)

## JMeter に JUnit テストをデプロイする

> [AZURE.NOTE] このプロジェクトでは、コンストラクターに単一の文字列として渡される構成パラメーターを受け取る BulkLoadTest.java という名前の JUnit テスト クラスを含む LoadTest という名前のプロジェクトが作成済みであることを前提とします (これは JMeter が必要とするメカニズムです)。

1.  Eclipse IDE の Package Explorer で、プロジェクト ノードを右クリックし、*[Export (エクスポート)]* をクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy26.png)

2.  *[Select (選択)]* ページの *[Export Wizard (エクスポート ウィザード)]* で、*Java* ノードを展開し、*[JAR file (JAR ファイル)]*、*[Next (次へ)]* の順にクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy27.png)

3.  *[JAR File Specification (JAR ファイルの仕様)]* ページの *[Select the resources to export (エクスポートするリソースを選択)]* ボックスで、エクスポートするプロジェクトを展開し、*src* フォルダーを除くすべての項目の選択を解除し、さらに *.classpath* 、 *.project* 、および *pom.xml* の選択を解除します。*[JAR ファイル]* ボックスで、JAR のファイル名と場所を指定し (.jar ファイル拡張子が付いたファイル)、*[Finish (完了)]* をクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy28.png)

4.  Windows エクスプ ローラーを使用して、先ほど作成した JAR ファイルを JMeter Master JVM にコピーし、それを、JMeter マスターをインストールしたフォルダーの下にある *apache-jmeter-2.13\\lib\\junit* フォルダーに保存します (詳細については、手順「[Installing and Configuring JMeter on the JMeter Master VM (JMeter Master VM に JMeter をインストールして構成する)](#_Installing_and_Configuring)」を参照してください)。

5.  Eclipse に戻り、[Package Explorer] ウィンドウを展開して、プロジェクトの *Maven Dependencies* フォルダーに一覧されている JAR ファイルとその場所をすべてメモします。次の図の中で表示されているファイルは、使用する Elasticsearch のバージョンによって異なる場合があるので注意してください。

![](./media/guidance-elasticsearch-jmeter-deploy29.png)

6.  Windows エクスプ ローラーを使用して、Maven Dependencies フォルダーで参照されている各 JAR ファイルを JMeter Master VM 上の *apache-jmeter-2.13\\lib\\junit* フォルダーにコピーします。

> [AZURE.NOTE] l*ib\\junit* フォルダーに、これらの JAR ファイルの以前のバージョンが含まれている場合は、それを削除します。そのまま放置しておくと、参照が正しい JAR に解決されないために、JUnit テストが機能しない可能性があります。

7.  JMeter Master VM で、JMeter が現在実行中である場合は、停止してください。

8.  JMeter を起動します。

9.  JMeter で、*[Test Plan (テスト プラン)]* を右クリックし、*[Add (追加)]* 、*[Threads (Users) (スレッド (ユーザー))]*、*[Thread Group (スレッド グループ)]* の順にクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy30.png)

10.  *[Test Plan (テスト プラン)]* ノードの下にある *[Thread-Group (スレッド グループ)]* を右クリックし、*[Add (追加)]*、*[Sampler (サンプラー)]*、*[JUnit Request]* の順にクリックします。

![](./media/guidance-elasticsearch-jmeter-deploy31.png)

11.  *[JUnit Request]* ページで、*[Search for JUnit4 annotations (instead of JUnit 3) (JUnit4 (JUnit 3 ではなく) の注釈を検索)]* を選択します。*[Classname (クラス名)]* ドロップダウン リスト ボックスで、まず、JUnit ロード テスト クラスを選択し (これは *&lt;package&gt;.&lt;class&gt;* の形式で一覧されます)、次に、*[Test Method (テスト メソッド)]* ドロップダウン リスト ボックスで、JUnit テスト メソッドを選択し (これは、テストに関連付けられた作業を実際に実行するメソッドであり、Eclipse プロジェクトでは注釈 *@test* でマーク付けされています)、最後に、*[Constructor String Label (コンストラクター文字列ラベル)]* ボックスに、コンストラクターに渡す値を入力します (下図に示す内容は例にすぎません。*[Classname (クラス名)]* 、*[Test Method (テスト メソッド)]* 、*[Constructor String Label (コンストラクター文字列ラベル)]* の実際の内容は、おそらく表示例とは異なります)。

![](./media/guidance-elasticsearch-jmeter-deploy32.png)

> [AZURE.NOTE]  *[Classname (クラス名)]* ドロップダウン リスト ボックスにクラスが表示されない場合は、JAR が正常にエクスポートされていないか、*lib\\junit* フォルダーに配置されていないか、または依存 JAR の一部が *lib\\junit* フォルダーに存在しない可能性があります。このような場合は、Eclipse からプロジェクトをもう一度エクスポートし、*src* リソースが選択されていることを確認します。JAR を *lib\\junit* フォルダーにコピーし、Maven で一覧されたすべての依存 JAR が *lib* フォルダーにコピーされていることを確認します。

12.  JMeter を閉じます。テスト プランを保存する必要はありません。

13.  JUnit テスト クラスを含む JAR ファイルを、各 JMeter 下位 VM 上の */home/&lt;username&gt;/apache-jmeter-2.13/lib/junit* フォルダーにコピーします (*&lt;username&gt;* は VM の作成時に指定した管理ユーザーの名前です。詳細については、「[Creating the JMeter Subordinate Virtual Machines (JMeter 下位仮想マシンを作成する)](#_Creating_the_JMeter_2)」を参照してください)。

14.  JUnit テスト クラスに必要な依存 JAR ファイルを、各 JMeter 下位 VM 上の */home/&lt;username&gt;/apache-jmeter-2.13/lib/junit* フォルダーにコピーします。事前に、JAR ファイルの古いバージョンを、このフォルダーから必ず削除してください。

> [AZURE.NOTE] pscp ユーティリティを使用して、Windows コンピューターから Ubuntu にファイルをコピーできます。

<!----HONumber=AcomDC_0211_2016-->
