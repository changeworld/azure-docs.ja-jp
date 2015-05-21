<properties
   pageTitle="HDInsight での Hive による JSON ドキュメントの分析および処理 | Microsoft Azure"
   description="HDInsight での Hive を使用した JSON ドキュメントの使用と分析方法について"
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/14/2015"
   ms.author="rashimg"/>


# Hive での JSON ドキュメントの処理と分析の方法について

JSON は、Web で最もよく使用される形式の 1 つです。このチュートリアルを使用すると、Hive でユーザーがよく直面する質問の 1 つ、つまり Hive で JSON ドキュメントを使用して、それを分析する方法を理解するのに役立ちます。

## JSON の例

例を考慮してみましょう。次に示す JSON ドキュメントがあるとします。目標とするのは、この JSON ドキュメントを解析し、このドキュメントでクエリ \(キーによる値の検索や集約など\) を実行できるようにすることです。

	  {
        "StudentId": "trgfg-5454-fdfdg-4346",
	    "Grade": 7,
        "StudentDetails": [
          {
            "FirstName": "Peggy",
            "LastName": "Williams",
            "YearJoined": 2012
          }
        ],
        "StudentClassCollection": [
          {
            "ClassId": "89084343",
            "ClassParticipation": "Satisfied",
            "ClassParticipationRank": "High",
            "Score": 93,
            "PerformedActivity": false
          },
          {
            "ClassId": "78547522",
            "ClassParticipation": "NotSatisfied",
            "ClassParticipationRank": "None",
            "Score": 74,
            "PerformedActivity": false
          },
          {
            "ClassId": "78675563",
            "ClassParticipation": "Satisfied",
            "ClassParticipationRank": "Low",
            "Score": 83,
            "PerformedActivity": true
          }
        ] 
      }

## JSON ドキュメントのフラット化 \(整形された JSON がある場合にのみ必要な手順\)

Hive 演算子を使用して分析を実行するには、その前に、JSON ドキュメントに対して事前処理を行い、Hive で使用する準備を整える必要があります。

次のセクションで一覧表示されているすべてのオプションでは、JSON ドキュメントが 1 行に表示される形式であると想定されています。つまり、Hive 演算子が使用できるように JSON ドキュメントを 1 つの文字列としてフラット化する必要があります。

**JSON ドキュメントが既にフラット化され、ドキュメント全体が 1 つの行になっている場合には、この手順をスキップし、JSON データの分析に関する次のセクションにすぐに移ることができます。**

この例では、Azure Blob ストアの既定のコンテナーにある */json/input/* フォルダーに、複数の行にまたがっていてフラット化されていない JSON ドキュメントが入っていると想定します。次のコードにより、元のフラット化されていない JSON ドキュメントを指す jsonexample テーブルが作成されます。

    drop table jsonexample;
    CREATE EXTERNAL TABLE jsonexample (textcol string) stored as textfile location '/json/input/';

次に、フラット化された JSON ドキュメントを指す one\_line\_json と呼ばれる新しいテーブルを作成し、それを Azure Blob ストアの既定のコンテナーにある */json/temp/* フォルダーに格納します。

    drop table if exists one_line_json;
    create external table one_line_json
    (
      json_body string
    )
    stored as textfile location '/json/temp';

最後に、フラット化された JSON データをこのテーブルに設定します。

    insert overwrite table one_line_json
    select concat_ws(' ',collect_list(textcol)) as singlelineJSON 
      from (select INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE,textcol from jsonexample distribute by INPUT__FILE__NAME sort by BLOCK__OFFSET__INSIDE__FILE ) 
      x group by INPUT__FILE__NAME;

この時点で、新しく作成したテーブル one\_line\_json を表示すると、JSON ドキュメントが 1 行で含まれているはずです。

    select * from one_line_json;

このクエリの出力を次に示します。

![JSON ドキュメントのフラット化。][image-hdi-hivejson-flatten]

## Hive による JSON ドキュメント分析のオプション

この時点で 1 列で構成される 1 つのテーブルに入っている JSON ドキュメントがあり、Hive を使用してこのデータにクエリを実行する準備が整っています。Hive は、JSON ドキュメントに対してクエリを実行するための次の 3 つの異なるメカニズムを提供します。

1.	ユーザー定義関数 \(UDF\) である get\_json\_object を使用する
2.	UDF である json\_tuple を使用する
3.	カスタム SerDe を使用する

それぞれの詳細について説明します。

## get\_json\_object UDF
Hive には [get\_json\_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) という組み込み UDF が備わっていて、ランタイム処理中に JSON クエリを実行できます。このメソッドは 2 つの引数を取ります。テーブル名とメソッド名で、解析することが必要なフラット化された JSON ドキュメントと JSON フィールドが含まれます。この UDF の動作を確認する例を見てみましょう。

各学生の姓と名を取得します。

    select get_json_object(one_line_json.json_body, '$.StudentDetails.FirstName'), get_json_object(one_line_json.json_body, '$.StudentDetails.LastName') from one_line_json;

このクエリをコンソール ウィンドウで実行した際の出力を次に示します。

![get\_json\_object UDF][image-hdi-hivejson-getjsonobject]

この UDF にはいくつかの制限があります。


- 主要な制限の 1 つは、クエリ内の各フィールドでクエリの再解析が必要となり、パフォーマンスに影響を与えるため、効率的でないことです。
- 次に、get\_json\_object\(\) は配列の文字列表現を返します。そのためこれを Hive 配列に変換するには、角括弧 ‘[‘ と ‘]’ を置換するための正規表現を使用してから、split を呼び出して配列を取得する必要があります。


このため、次に取り上げる json\_tuple の使用が Hive Wiki で推奨されています。


## json\_tuple UDF

Hive に備わっている別の UDF は [json\_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) で、[get\_json\_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) よりも効率的です。このメソッドは、一連のキーと、JSON 文字列を取り、1 つの関数を使用して値のタプルを返します。この UDF の動作を確認する例を見てみましょう。

JSON ドキュメントから学生 ID と評点を取得しましょう。

    select q1.StudentId, q1.Grade 
      from one_line_json jt
      LATERAL VIEW json_tuple(jt.json_body, 'StudentId', 'Grade') q1
        as StudentId, Grade;

この例では Hive で [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) 構文を使用します。この構文を使用すると、json\_tuple は UDT 関数を元のテーブルの各行に適用して仮想テーブルを作成できます。

Hive コンソールにおけるこのスクリプトの出力を表示します。

![json\_tuple UDF][image-hdi-hivejson-jsontuple]

この UDF の主な欠点の 1 つは、LATERAL VIEW が繰り返し使用されるため、複雑な JSON は処理が難しくなることです。実際、この UDF では入れ子になった JSON は処理できません。

## カスタム SerDe を使用する

入れ子になった JSON ドキュメントを解析する場合には、SerDe が**最善の選択肢**です。非常に簡単にクエリを実行する JSON スキーマを定義できるためです。

[rcongiu](https://github.com/rcongiu) が開発した、よく使用されている SerDe の 1 つの使用法について取り上げます。

手順 1: [Java SE Development Kit 7u55 JDK 1.7.0\_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR) がインストールされていることを確認します \(注: JDK 1.8 がインストールされている場合、この SerDe は機能しません\)。


- HDInsight の Windows 展開を使用する場合は、JDK の x64 Windows バージョンを選択します。
- インストールが完了したら、\[コントロール パネル\]、\[環境変数の追加\] と移動し、C:\\Program Files\\Java\\jdk1.7.0\_55 または JDKのインストール場所を指す新しい JAVA\_HOME 変数を追加します。次のスクリーン ショットは、環境変数を設定する方法を示しています。

![JDK の適切な構成値の設定][image-hdi-hivejson-jdk]

手順 2: [こちら](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)のリンクをたどり、Maven 3.3.1 をダウンロードします。バイナリを格納する場所にアーカイブを展開します。ここでは C:\\Program Files\\Maven に解凍します。\[コントロール パネル\] からご使用のアカウントの環境変数の \[システム変数の編集\] に移動して、パスの bin フォルダーを追加します。次のスクリーンショットに、この方法を示します。

![Maven のセットアップ][image-hdi-hivejson-maven]

手順 3: [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) github サイトからプロジェクトを複製します。そのためには、次のスクリーンショットに示すように、\[Zip のダウンロード\] ボタンをクリックします。

![プロジェクトの複製][image-hdi-hivejson-serde]

手順 4: このパッケージをダウンロードしたフォルダーに移動して、「mvn package」と入力します。これにより必要な jar ファイルが作成されるので、クラスターにコピーします。

手順 5: パッケージをダウンロードした、ルート フォルダーの下にある対象フォルダーに移動します。クラスターのヘッド ノードに json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar ファイルをアップロードします。通常は、Hive バイナリ フォルダーである C:\\apps\\dist\\hive-0.13.0.2.1.11.0-2316\\bin などに配置します。
 
手順 6: Hive プロンプトで、「add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar」と入力します。この例では jar は C:\\apps\\dist\\hive-0.13.x\\bin フォルダーにあるので、次に示す名前の jar を直接追加できます。

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

![プロジェクトへの JAR の追加][image-hdi-hivejson-addjar]

これで、SerDe を使用して、JSON ドキュメントに対してクエリを実行する準備ができました。

最初に、JSON ドキュメントのスキーマでテーブルを作成します。マップ、配列、構造体など、より多くの種類を処理できます。Hive コンソールに以下のコードを入力します。このコードによって、次のスキーマに基づいて JSON ドキュメントを読み取ることが可能な json\_table という名前のテーブルを Hive が作成できます。

    drop table json_table;
    create external table json_table (
      StudentId string, 
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) row format serde 'org.openx.data.jsonserde.JsonSerDe'
    location '/json/temp';

スキーマを定義できました。次に、Hive を使用して JSON ドキュメントに対してクエリを実行できる方法を示すいくつかの実行例を考慮してみましょう。

a\) 学生の姓と名を一覧表示します。

    select StudentDetails.FirstName, StudentDetails.LastName from json_table;

Hive コンソールの結果を次に示します。

![SerDe クエリ 1][image-hdi-hivejson-serde_query1]

b\) このクエリは、JSON ドキュメントのスコア合計を計算します。

    select sum(scores)
    from json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;
       
前述のクエリは [lateral view explode](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) UDF を使用してスコアの配列を展開し、合計を計算できます。

Hive コンソールの出力を次に示します。

![SerDe クエリ 2][image-hdi-hivejson-serde_query2]

c\) 特定の学生のスコアが 80 点を超えている科目を検索します。

    select  
      jt.StudentClassCollection.ClassId 
    from json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as score  where score > 80;
      
上記のクエリにより、Hive 配列が戻ります。文字列が返される get\_json\_object とは異なります。

![SerDe クエリ 3][image-hdi-hivejson-serde_query3]

形式が異常な JSON を除外する場合、この SerDe の [wiki ページ](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master)で説明されているように以下のコードを入力します。

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");


## その他のオプション
一覧表示されているオプションの他に、Python またはご使用のシナリオに特有の言語を使用して独自のカスタム コードを作成することもできます。python スクリプトの準備が整った後、Hive で独自の Python コードを実行するときには[こちらの記事][hdinsight-python]をご覧ください。

## 概要
結論として、Hive で選択する JSON 演算子の種類は、シナリオによって異なります。JSON ドキュメントが単純で、検索するのが 1 つのフィールドのみの場合には、Hive UDF get\_json\_object を選択できます。検索対象のキーが複数ある場合には、json\_tuple を使用できます。入れ子になったドキュメントの場合、JSON SerDe を使用する必要があります。

現在、HDInsight において、ユーザー側の作業量が多くならずに、Hive 固有の形式を簡単に使用できるようにさらに取り組んでいます。共有できる何らかの内容がある場合、このチュートリアルをその詳細情報を含めて更新する予定です。

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

<!--HONumber=52-->
