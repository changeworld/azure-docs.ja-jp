<properties 
	pageTitle="DocumentDB SQL を使用したクエリ | Azure" 
	description="NoSQL ドキュメント データベース サービスである DocumentDB は、階層型の JSON ドキュメントに対し、SQL に似た文法を使用することによって行うクエリをサポートしています。明確なスキーマが不要であり、セカンダリ インデックスを作成する必要もありません。" 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mimig"/>

#DocumentDB のクエリ
Microsoft Azure DocumentDB は、階層型の JSON ドキュメントに対する、SQL (Structured Query Language) を使ったドキュメント クエリをサポートしています。DocumentDB は完全にスキーマフリーです。データベース エンジン内で JSON データ モデルを直接処理することで、明示的なスキーマやセカンダリ インデックスの作成を必要とせずに、JSON ドキュメントの自動インデックス作成を実現しています。 

マイクロソフトは、以下の 2 点を目標に DocumentDB 向けのクエリ言語を設計しました。

-	<strong>SQL を活用する</strong> - 新しいクエリ言語を開発するのではなく、SQL 言語を活用しました。SQL は最も幅広く普及したクエリ言語の 1 つです。DocumentDB SQL 言語は、JSON ドキュメントに対するリッチ クエリを、正式なプログラミング モデルを通して実現します。
-	<strong>SQL を拡張する</strong> - マイクロソフトでは、データベース エンジン内で JavaScript を直接実行できる JSON ドキュメント データベースを設計するため、JavaScript のプログラミング モデルに基づいて SQL クエリ言語を開発することにしました。DocumentDB SQL クエリ言語は、JavaScript の型システム、式評価、関数呼び出しを基盤としています。これによって、リレーショナル プロジェクション、JSON ドキュメント間の階層型ナビゲーション、自己結合、完全に JavaScript で記述されたユーザー定義関数 (UDF) の呼び出しなどに対して、自然なプログラミング モデルが提供されます。 

マイクロソフトでは、アプリケーションとデータベース間の不整合を削減し、開発者の生産性を高めるには、こうした方針が鍵になると考えています。

最初に、Aravind Ramachandran 氏が DocumentDB のクエリ機能を紹介している次のビデオを見てから[クエリ プレイグラウンド](http://www.documentdb.com/sql/demo)のページで DocumentDB を使ってデータセットに対して SQL クエリを実行する機能を試すことから始めることをお勧めします。

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

次に、この記事に戻り、いくつかの簡単な JSON ドキュメントおよびクエリについて理解していきます。

## 作業の開始
DocumentDB SQL の動作を確認するため、最初にシンプルな JSON ドキュメントを見てみましょう。その後このドキュメントに対して実施するシンプルなクエリについて説明します。これら 2 つの JSON ドキュメントは、2 つの家族に関するドキュメントです。DocumentDB では、スキーマやセカンダリ インデックスを明示的に作成する必要がありません。必要なことは、JSON ドキュメントを DocumentDB コレクションに挿入した後、クエリを実行するだけです。 
以下は、Andersen 一家に関するシンプルな JSON ドキュメントです。両親、子供 (および子供のペット)、住所、登録に関する情報が記載されています。ドキュメントには、文字列、数値、ブール値、配列、入れ子になったプロパティがあります。 

**ドキュメント**  

	{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "isRegistered": true
	}


2 つ目のドキュメントは、 `givenName` と  `familyName` が  `firstName` と  `lastName` の代わりに使用されている点だけが違います。

**ドキュメント**  

	{
	    "id": "WakefieldFamily",
	    "parents": [
	        { "familyName": "Wakefield", "givenName": "Robin" },
	        { "familyName": "Miller", "givenName": "Ben" }
	    ],
	    "children": [
	        {
	            "familyName": "Merriam", 
	             "givenName": "Jesse", 
	            "gender": "female", "grade": 1,
	            "pets": [
	                { "givenName": "Goofy" },
	                { "givenName": "Shadow" }
	            ]
	        },
	        { 
	            "familyName": "Miller", 
	             "givenName": "Lisa", 
	             "gender": "female", 
	             "grade": 8 }
	    ],
	    "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
	    "isRegistered": false
	}



DocumentDB SQL の重要な項目について理解するため、このデータに対していくつかのクエリを実行してみます。たとえば、以下のクエリを実行すると、ID フィールドが  `AndersenFamily` に一致するドキュメントが返されます。`SELECT *` であるため、クエリの出力は JSON ドキュメント全体になります。

**クエリ**

	SELECT * 
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "isRegistered": true
	}]


ここで、この JSON 出力を異なる形式に変更する必要がある場合を考えてみます。このクエリは、住所の都市名と州名が同じ場合に、2 つの特定のフィールド (Name と City) を持つ JSON オブジェクトをプロジェクションします。この場合、"NY, NY" の一致となります。

**クエリ**	

	SELECT {"Name":f.id, "City":f.address.city} AS Family 
	FROM Families f 
	WHERE f.address.city = f.address.state

**結果**

	[{
	    "Family": {
	        "Name": "WakefieldFamily", 
	        "City": "NY"
	    }
	}]


次のクエリでは、ID が  `WakefieldFamily` と一致する家族の子供の名前がすべて返されます。

**クエリ**

	SELECT c.givenName 
	FROM Families f 
	JOIN c IN f.children 
	WHERE f.id = 'WakefieldFamily'

**結果**

	[
	  { "givenName": "Jesse" }, 
	  { "givenName": "Lisa"}
	]


これまでの例からわかる、DocumentDB クエリ言語の注目すべき特性を以下に示します。  
 
-	JSON 値を利用する DocumentDB SQL は、行と列ではなくツリー形式のエンティティを処理します。つまり、この言語では  `Node1.Node2.Node3.....Nodem` のように任意の深さのツリーのノードを参照できます。これは、リレーショナル SQL が `<table>.<column>` という 2 項目の参照で参照するのに似ています。   
-	この言語ではスキーマのないデータを扱います。このため、型システムを動的にバインドする必要があります。同じ式でも、ドキュメントが異なれば異なる型が導出される場合があります。このようなクエリ結果は有効な JSON 値ですが、固定スキーマの場合でも有効とは限りません。  
-	DocumentDB は厳密な JSON ドキュメントだけをサポートします。つまり、型システムおよび式は、JSON 型のみを扱うように制限されます。詳細については、[JSON の仕様](http://www.json.org/)に関するページを参照してください。  
-	DocumentDB コレクションは、スキーマフリーの JSON ドキュメントのコンテナーです。コレクションにあるドキュメント内およびドキュメント間のデータ エンティティの関係はコンテインメントによって暗黙的にキャプチャされます。プライマリ キーと外部キーの関係ではキャプチャされません。これは、この記事で後述するドキュメント間結合の点で注意すべき要素です。

##DocumentDB のインデックス作成

DocumentDB SQL 言語について詳しく説明する前に、DocumentDB のインデックス作成に関する設計について説明します。 

データベース インデックスの目的は、さまざまな形式のクエリを処理しながら、リソース (CPU、入力/出力など) の消費量を最小化し、スループットを高め、遅延時間を少なくすることです。多くの場合、データベース クエリにおける適切なインデックスの選択には、入念な計画と長期間の試行錯誤が必要とされます。データが厳密なスキーマに準拠せず、データ量も急速に増大するスキーマのないデータベースの場合、このアプローチは現実的ではありません。 

このため、マイクロソフトでは、以下の目標に従って DocumentDB のインデックス サブシステムを設計しました。

-	スキーマを必要としないインデックス ドキュメント:インデックス作成サブシステムは、スキーマ情報を一切必要とせず、ドキュメントのスキーマを想定しません。 

-	高度な階層化に対応した、効率的なリレーショナル クエリのサポート:インデックスは DocumentDB クエリ言語を効率的にサポートします。これには、階層型かつリレーショナルなプロジェクションのサポートも含まれます。

-	一定量かつ持続的に実行される書き込みに対する、一貫性のあるクエリのサポート:一貫性のあるクエリで書き込みスループットの負荷が高くなる場合は、一定量の書き込みが持続的に実行されたとしても、インデックスは効率的かつ段階的にオンラインで更新されます。ユーザーがドキュメント サービスを構成した際の一貫性レベルでクエリを処理するためには、インデックスの一貫した更新が欠かせません。

-	マルチテナントのサポート:テナント間のリソースは、予約ベースのモデルで制御されます。インデックスの更新は、これに従って、レプリカごとに割り当てられたシステム リソース (CPU、メモリ、および 1 秒あたりの入力/出力操作) 量の範囲内で実行されます。 

-	ストレージの効率性:コスト効率性を実現するため、インデックスのディスク上のストレージ オーバーヘッドは、有限かつ予測可能なものにします。これは、開発者がインデックスのオーバーヘッドとクエリのパフォーマンスを比較して、コストに基づくトレードオフを DocumentDB で実施できるようになるためには必須です。  

コレクションのインデックス作成ポリシーの構成方法については、MSDN の [DocumentDB のサンプル](http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af#content)を参照してください。以降は、DocumentDB SQL 言語の詳細を説明していきます。


##DocumentDB クエリの基礎
すべてのクエリは ANSI-SQL 標準に従って SELECT 句とオプションの FROM および WHERE 句で構成されます。通常は、各クエリで FROM 句のソースが列挙されます。次に WHERE 句のフィルターがソースに適用され、JSON ドキュメントのサブセットが取得されます。最後に SELECT 句を使用して、要求された JSON 値が特定のリストにプロジェクションされます。
    
    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]    


##FROM 句
 `FROM <from_specification>` 句はオプションです (ソースがクエリの後半でフィルター処理またはプロジェクションされる場合を除く)。この句の目的は、クエリが動作する対象のデータ ソースを指定することです。通常はコレクション全体がソースになりますが、コレクションのサブセットを指定することもできます。 

 `SELECT * FROM Families` というクエリは、Families コレクション全体がソースとなり、このソースを対象に列挙が行われることを示します。特別な識別子 ROOT を使うと、コレクション名の代わりにコレクションを表現することができます。 
クエリごとに以下のバインド規則が強制されます。

- コレクションは  `SELECT f.id FROM Families AS f` またはシンプルに  `SELECT f.id FROM Families f` のようにエイリアス化することができます。ここで、 `f` is the equivalent of `Families`。 `AS` は識別子をエイリアス化するためのオプションのキーワードです。

-	エイリアス化されると、元のソースをバインドすることはできなくなります。たとえば、 `SELECT Familes.id FROM Families f` は無効な構文となります。これは、識別子 "Families" が解決できなくなるためです。

-	参照先になる必要があるすべてのプロパティは完全修飾する必要があります。準拠する厳密なスキーマが存在しない場合、これが強制されることによって曖昧なバインドが回避されます。このため、 `SELECT id FROM Families f` は無効な構文となります。これは、プロパティ  `id` がバインドされていないためです。
	
###サブドキュメント
ソースはさらに小さいサブセットに限定することができます。たとえば各ドキュメントのサブツリーだけを列挙する場合、以下の例のようにサブルートをソースにすることができます。

**クエリ**

	SELECT * 
	FROM Families.children

**結果**  

	[
	  [
	    {
	        "firstName": "Henriette Thaulow",
	        "gender": "female",
	        "grade": 5,
	        "pets": [
	          {
	              "givenName": "Fluffy"
	          }
	        ]
	    }
	  ],
	  [
	    {
	        "familyName": "Merriam",
	        "givenName": "Jesse",
	        "gender": "female",
	        "grade": 1
	    },
	    {
	        "familyName": "Miller",
	        "givenName": "Lisa",
	        "gender": "female",
	        "grade": 8
	    }
	  ]
	]

上記の例では配列をソースとして使用していますが、以下の例のようにオブジェクトをソースとして使用することもできます。ソースで検索できる有効なすべての JSON 値 (未定義でないもの) は、クエリ結果に含められることが想定されます。 `address.state` 値を持たない家族は、クエリ結果から除外されます。

**クエリ**

	SELECT * 
	FROM Families.address.state

**結果**

	[
	  "WA", 
	  "NY"
	]


##WHERE 句
WHERE 句 (**`WHERE <filter_condition>`**) はオプションです。WHERE 句は、ソースが提供する JSON ドキュメントを結果に含めるために満たす必要がある条件を指定します。結果の対象となるには、指定された条件についてすべての JSON ドキュメントが "true" と評価される必要があります。WHERE 句がインデックス レイヤーで使用されることで、結果に含めることが可能なソース ドキュメントの最小のサブセットが判断されます。 

以下のクエリは、名前プロパティを含み、プロパティ値が  `AndersenFamily` であるようなドキュメントを要求しています。名前プロパティを持たない、またはプロパティ値が  `AndersenFamily` に一致しないドキュメントはすべて除外されます。 

**クエリ**

	SELECT f.address
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	  "address": {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }
	}]


上記の例では単純な等値クエリを紹介しました。DocumentDB SQL はさまざまなスカラー式もサポートしています。最も多く使用されるのはバイナリ式と単項式です。ソース JSON オブジェクトからのプロパティ参照も有効な式です。 

現在サポートされている 2 項演算子を以下に示します。これらは、以下の例のようにクエリ内で使用することができます。  
<table>
<tr>
<td>算術</td>	
<td>+、-、*、/、%</td>
</tr>
<tr>
<td>ビット</td>	
<td>|、&、^、<<、>>、>>> (0 埋め右シフト) </td>
</tr>
<tr>
<td>論理</td>
<td>AND、OR</td>
</tr>
<tr>
<td>比較</td>	
<td>=、!=、>、>=、<、<=、<></td>
</tr>
<tr>
<td>String</td>	
<td>|| (連結)</td>
</tr>
</table>  

2 項演算子を使用したクエリを見てみます。

	SELECT * 
	FROM Families.children[0] c
	WHERE c.grade % 2 = 1     -- matching grades == 5, 1
	
	SELECT * 
	FROM Families.children[0] c
	WHERE c.grade ^ 4 = 1    -- matching grades == 5
	
	SELECT *
	FROM Families.children[0] c
	WHERE c.grade >= 5     -- matching grades == 5


単項演算子 (+、-、~、および NOT) もサポートされています。これらはクエリの内側で次の例のように使用することができます。

	SELECT *
	FROM Families.children[0] c
	WHERE NOT(c.grade = 5)  -- matching grades == 1
	
	SELECT *
	FROM Families.children[0] c
	WHERE (-c.grade = -5)  -- matching grades == 5



2 項演算子と単項演算子に加えてプロパティ参照も許可されます。たとえば、 `SELECT * FROM Families f WHERE f.isRegistered` は  `isRegistered` プロパティを含む JSON ドキュメントを返し、このプロパティの値は JSON の  `true` 値と等しくなります。その他すべての値 (false、null、Undefined、`<number>`、`<string>`、`<object>`、`<array>` など) の場合、ソース ドキュメントが結果から除外されます。 

###等値演算子と比較演算子
以下の表は、DocumentDB SQL の 2 つの JSON 型で等値比較を実行した結果を示しています。
<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>演算子</strong>
         </td>
         <td valign="top">
            <strong>Undefined</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>Boolean</strong>
         </td>
         <td valign="top">
            <strong>Number</strong>
         </td>
         <td valign="top">
            <strong>String</strong>
         </td>
         <td valign="top">
            <strong>オブジェクト</strong>
         </td>
         <td valign="top">
            <strong>Array</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Undefined<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Boolean<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Number<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>String<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>オブジェクト<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Array<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
      </tr>
   </tbody>
</table>

その他の比較演算子 (>、>=、! =、<、および <=) については、以下のようになります。   

-	型の間の比較は Undefined になる。
-	2 つのオブジェクト間または 2 つの配列間の比較は Undefined になる。   

フィルター内のスカラー式が Undefined という結果になった場合、Undefined は論理上 "true" と等しくならないため、対応するドキュメントは結果に含まれません。

###BETWEEN キーワード
また、ANSI SQL などの場合と同様に、値の範囲に対してクエリを表現するときに、BETWEEN キーワードを使用することができます。BETWEEN はすべての JSON プリミティブ型 (数値、文字列、ブール値、および null) に対して使用できます。 

たとえば、次のクエリは、最初の子のレベルが 1 ～ 5 (両方の値を含む) の間であるすべての家族のドキュメントを返します。 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

ANSI-SQL の場合と異なり、次の例のように、FROM 句内に BETWEEN 句を使用することもできます。

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

クエリの実行速度を速めるには、BETWEEN 句でフィルター処理される数値プロパティやパスに対して範囲のインデックス型を使用するインデックス作成ポリシーを作成してください。 

DocumentDB と ANSI SQL の BETWEEN の使用に関する主な違いは、混合型のプロパティに対して範囲クエリを表すことができる点です。たとえば、"grade" に数値 (5) が入力されているドキュメントや、文字列 ("grade4") が入力されているドキュメントを混在させることができます。このような場合、JavaScript の場合と同様に、2 種類を比較した結果は "undefined" になり、ドキュメントはスキップされます。


###論理 (AND、OR、および NOT) 演算子
論理演算子は Boolean 値に対して使用されます。これらの演算子に関する真理値表は以下のようになります。

<table style = "width:300px">
    <tbody>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>OR</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    True
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    Undefined
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
    </tbody>
</table>

<table style = "width:300px">
    <tbody>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>AND</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    False
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    Undefined
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
    </tbody>
</table>

<table style = "width:300px">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>NOT</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong></strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    False
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    True
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
    </tbody>
</table>

###3 項 (?) 演算子と合体 (??) 演算子:
3 項演算子と合体演算子は、一般的なプログラミング言語である C# や JavaScript と同様に、条件式の構築に使用することができます。 

3 項 (?) 演算子は、実行中に新しい JSON プロパティを構築するときに役立つ場合があります。たとえば、次のように、初級、中級、上級など、人間が判読できる形式でクラス レベルを分類するクエリを作成できます。
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

次のクエリのように、演算子への呼び出しを入れ子にすることもできます。
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

他のクエリ演算子と同様に、すべてのドキュメントで条件付きの式の参照先のプロパティが見つからない場合、または比較対象となる型が異なる場合、これらのドキュメントはクエリの結果から除外されます。

合体 (??) 演算子を使用すると、ドキュメントにプロパティが存在するかどうか (つまり定義されているかどうか) を効率的に確認できます。この演算子は、半構造化されたデータや混合型のデータに対してクエリを実行するときに役立ちます。たとえば、次のクエリは、存在する場合に "lastName" を返し、存在しない場合に "surname" を返します。

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

同様にして、次の例のようにプロパティが存在しない ("undefined") かどうかを確認するクエリを実行することもできます。

    SELECT *
    FROM classes c
    WHERE c.lastName ?? true

##SELECT 句
ANSI-SQL と同様に、クエリから取得する値を指定する SELECT 句 (**`SELECT <select_list>`**) は必須です。ソース ドキュメントの先頭でフィルターされたサブセットがプロジェクション フェーズに渡され、渡された入力のそれぞれについて、指定された JSON 値が取得され、新しい JSON オブジェクトが構築されます。 

一般的な SELECT クエリの例を次に示します。 

**クエリ**

	SELECT f.address
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	  "address": {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }
	}]


###入れ子になったプロパティ
以下の例では、 `f.address.state` と `f.address.city` という 2 つの入れ子になったプロパティをプロジェクションしています。

**クエリ**

	SELECT f.address.state, f.address.city
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	  "state": "WA", 
	  "city": "seattle"
	}]


プロジェクションは、以下の例のように JSON 式もサポートします。

**クエリ**

	SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	  "$1": {
	    "state": "WA", 
	    "city": "seattle", 
	    "name": "AndersenFamily"
	  }
	}]


この `$1` の役割について説明します。 `SELECT` 句は、JSON オブジェクトを作成する必要がありますが、キーが提供されていないため、`$1` で始まる暗黙的な引数の変数名を使用しています。たとえば、このクエリは `$1` と `$2` でラベル付けされた暗黙的な引数の変数を返します。

**クエリ**

	SELECT { "state": f.address.state, "city": f.address.city }, 
	       { "name": f.id }
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	  "$1": {
	    "state": "WA", 
	    "city": "seattle"
	  }, 
	  "$2": {
	    "name": "AndersenFamily"
	  }
	}]


###エイリアス化
ここで、値を明示的にエイリアス化することによって、上記の例を拡張します。AS はエイリアス化に使用されるキーワードです。2 つ目の値を `NameInfo` としてプロジェクションしていますが、例からわかるようにこれはオプションです。 

同じ名前を持つ 2 つのプロパティがクエリにある場合、エイリアス化を使ってプロパティのいずれかまたは両方の名前を変更する必要があります。こうすることで、プロジェクションされた結果でこれらを区別することができます。

**クエリ**

	SELECT 
	       { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
	       { "name": f.id } NameInfo
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	  "AddressInfo": {
	    "state": "WA", 
	    "city": "seattle"
	  }, 
	  "NameInfo": {
	    "name": "AndersenFamily"
	  }
	}]


###スカラー式
SELECT 句は、プロパティ参照に加えて、定数、算術式、論理式などのスカラー式をサポートします。例として、単純な "Hello World" クエリを以下に示します。

**クエリ**

	SELECT "Hello World"

**結果**

	[{
	  "$1": "Hello World"
	}]


スカラー式を使用したより複雑な例は以下のようになります。

**クエリ**

	SELECT ((2 + 11 % 7)-2)/3	

**結果**

	[{
	  "$1": 1.33333
	}]


以下の例では、スカラー式の結果は Boolean になります。

**クエリ**

	SELECT f.address.city = f.address.state AS AreFromSameCityState
	FROM Families f	

**結果**

	[
	  {
	    "AreFromSameCityState": false
	  }, 
	  {
	    "AreFromSameCityState": true
	  }
	]


###オブジェクトと配列の作成
配列/オブジェクトの作成も、DocumentDB SQL の重要な機能です。新しい JSON オブジェクトはこれまでの例で作成しました。同様に、以下の例のように配列を構築することもできます。

**クエリ**

	SELECT [f.address.city, f.address.state] AS CityState 
	FROM Families f	

**結果**  

	[
	  {
	    "CityState": [
	      "seattle", 
	      "WA"
	    ]
	  }, 
	  {
	    "CityState": [
	      "NY", 
	      "NY"
	    ]
	  }
	]

###VALUE キーワード
**VALUE** キーワードは、JSON 値を返す方法を提供します。たとえば以下のクエリでは、スカラー `"Hello World"` が返され、 `{$1: "Hello World"}` とはなりません。

**クエリ**

	SELECT VALUE "Hello World"

**結果**

	[
	  "Hello World"
	]


以下のクエリでは、結果に `"address"` ラベルなしで JSON 値が返されます。

**クエリ**

	SELECT VALUE f.address
	FROM Families f	

**結果**  

	[
	  {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }, 
	  {
	    "state": "NY", 
	    "county": "Manhattan", 
	    "city": "NY"
	  }
	]

これをさらに拡張した以下の例では、JSON のプリミティブ値、つまり、JSON ツリーのリーフ レベルの値が返されます。 

**クエリ**

	SELECT VALUE f.address.state
	FROM Families f	

**結果**

	[
	  "WA",
	  "NY"
	]


###* 演算子
サポートされている特別な演算子 (*) によって、ドキュメントが現状のままプロジェクションされます。使用する場合は、この演算子が唯一のプロジェクションされるフィールドである必要があります。 `SELECT * FROM Families f` のようなクエリは有効ですが、`SELECT VALUE * FROM Families f ` および  `SELECT *, f.id FROM Families f` は無効です。

**クエリ**

	SELECT * 
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "isRegistered": true
	}]

##高度な概念
###反復
DocumentDB SQL の **IN** キーワードによる新しいコンストラクトを追加することで、JSON 配列に対する反復がサポートされています。反復のサポートは FROM ソースが提供します。まず、以下の例から始めます。

**クエリ**

	SELECT * 
	FROM Families.children

**結果**  

	[
	  [
	    {
	      "firstName": "Henriette Thaulow", 
	      "gender": "female", 
	      "grade": 5, 
	      "pets": [{ "givenName": "Fluffy"}]
	    }
	  ], 
	  [
	    {
	        "familyName": "Merriam", 
	        "givenName": "Jesse", 
	        "gender": "female", 
	        "grade": 1
	    }, 
	    {
	        "familyName": "Miller", 
	        "givenName": "Lisa", 
	        "gender": "female", 
	        "grade": 8
	    }
	  ]
	]

次に、コレクションの子に対する反復を実行する別のクエリを見てみます。出力配列の違いに注目してください。この例では、 `children` を分割し、結果を 1 つの配列にフラット化しています。  

**クエリ**

	SELECT * 
	FROM c IN Families.children

**結果**  

	[
	  {
	      "firstName": "Henriette Thaulow",
	      "gender": "female",
	      "grade": 5,
	      "pets": [{ "givenName": "Fluffy" }]
	  },
	  {
	      "familyName": "Merriam",
	      "givenName": "Jesse",
	      "gender": "female",
	      "grade": 1
	  },
	  {
	      "familyName": "Miller",
	      "givenName": "Lisa",
	      "gender": "female",
	      "grade": 8
	  }
	]

これをさらに発展させることで、以下の例のように、配列の個々のエントリをフィルターすることができます。

**クエリ**

	SELECT c.givenName
	FROM c IN Families.children
	WHERE c.grade = 8

**結果**  

	[{
	  "givenName": "Lisa"
	}]

###結合
リレーショナル データベースでは、テーブル間を結合できることは非常に重要です。このことは、正規化されたスキーマの設計においては論理的必然です。一方 DocumentDB は、スキーマフリーのドキュメントの正規化されていないデータ モデルを扱います。これは、論理的には "自己結合" と同義です。

この言語がサポートする構文は、<from_source1> JOIN <from_source2> JOIN ...JOIN <from_sourceN> です。これによって、**N**-タプル (**N** 個の値を持つタプル) のセットが返されます。それぞれのタプルは、対応するセットに対する、すべてのコレクションのエイリアスの反復によって生成された値を持ちます。これは、結合に含まれるセットの完全なクロス積と言うことができます。

JOIN 句の動作を示す例をいくつか紹介します。以下の例の場合、ソースの各ドキュメントと空集合のクロス積は空になるため、結果は空となります。

**クエリ**

	SELECT f.id
	FROM Families f
	JOIN f.NonExistent

**結果**  

	[{
	}]


以下の例は、ドキュメント ルートと `children` サブルートの間の結合です。これは 2 つの JSON オブジェクトのクロス積となります。この JOIN には、children が配列であるという事実は影響していません。これは、扱っている単一のルートがその children 配列であるためです。そのため、結果には 2 つの結果しか含まれません。これは、その配列を持つ各ドキュメントのクロス積によってドキュメントが 1 つだけ導出されるためです。

**クエリ**

	SELECT f.id
	FROM Families f
	JOIN f.children
 
**結果**

	[
	  {
	    "id": "AndersenFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }
	]


より一般的な結合の例を以下に示します。

**クエリ**

	SELECT f.id
	FROM Families f
	JOIN c IN f.children 

**結果**

	[
	  {
	    "id": "AndersenFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }
	]



まず注意が必要な点は、この **JOIN** 句の `from_source` が反復子であるという点です。このため、この場合のフローは以下のようになります。  

-	各子要素 **c** を配列に展開します。
-	ドキュメント **f** のルートと、最初の手順でフラット化された各子要素 **c** とのクロス積を適用します。
-	最後に、ルート オブジェクト **f** の name プロパティだけをプロジェクションします。 

最初のドキュメント (`AndersenFamily`) には子要素が 1 つだけ含まれているため、結果セットには、このドキュメントに対応するオブジェクトが 1 つだけ含まれます。2 つ目のドキュメント (`WakefieldFamily`) には子が 2 つ含まれています。このため、クロス積によってそれぞれの子で個別のオブジェクトが生成されることで、オブジェクトが 2 つ (このドキュメントに対応するそれぞれの子に 1 つずつ) になります。クロス積の段階で想定されるとおり、これらのドキュメントのルート フィールドが同じになる点に注意してください。

JOIN の便利な点は、クロス積からタプルを生成できる点です。これ以外の形式によるプロジェクションは簡単ではありません。さらに、以下の例でわかるとおり、すべてのタプル全体によって満たされる条件をユーザーが選択できるようなタプルの組み合わせをフィルターすることができます。

**クエリ**

	SELECT 
		f.id AS familyName,
		c.givenName AS childGivenName,
		c.firstName AS childFirstName,
		p.givenName AS petName 
	FROM Families f 
	JOIN c IN f.children 
	JOIN p IN c.pets
 
**結果**

	[
	  {
	    "familyName": "AndersenFamily", 
	    "childFirstName": "Henriette Thaulow", 
	    "petName": "Fluffy"
	  }, 
	  {
	    "familyName": "WakefieldFamily", 
	    "childGivenName": "Jesse", 
	    "petName": "Goofy"
	  }, 
	  {
	   "familyName": "WakefieldFamily", 
	   "childGivenName": "Jesse", 
	   "petName": "Shadow"
	  }
	]



この例は前述の例を拡張したもので、二重結合を実行しています。このため、クロス積は以下の擬似コードのように捉えることができます。

	for-each(Family f in Families)
	{	
		for-each(Child c in f.children)
		{
			for-each(Pet p in c.pets)
			{
				return (Tuple(f.id AS familyName, 
	              c.givenName AS childGivenName, 
	              c.firstName AS childFirstName,
	              p.givenName AS petName));
			}
		}
	}

`AndersenFamily` には 1 人の子供がいて、子供はペットを 1 匹飼っています。このため、クロス積によってこの家族から 1 行 (1*1*1) が導出されます。WakefieldFamily には子供が 2 人いますが、ペットを飼っているのは "Jesse" 1 人だけです。ただしペットは 2 匹います。したがって、この家族からのクロス積は 1*1*2 = 2 行となります。

次の例では、 `pet` に対するフィルターが追加されています。これによって、ペットの名前が "Shadow" ではないタプルがすべて除外されます。配列からタプルを構築し、タプルのすべての要素に対してフィルターを実行し、要素の任意の組み合わせをプロジェクションできる点に注目してください。 

**クエリ**

	SELECT 
		f.id AS familyName,
		c.givenName AS childGivenName,
		c.firstName AS childFirstName,
		p.givenName AS petName 
	FROM Families f 
	JOIN c IN f.children 
	JOIN p IN c.pets
	WHERE p.givenName = "Shadow"

**結果**

	[
	  {
	   "familyName": "WakefieldFamily", 
	   "childGivenName": "Jesse", 
	   "petName": "Shadow"
	  }
	]


##JavaScript 統合
DocumentDB が提供するプログラミング モデルでは、ストアド プロシージャとトリガーという点では、JavaScript ベースのアプリケーション ロジックをコレクションで直接実行することができます。これによって以下の両方が実現されます。

-	データベース エンジン内部での JavaScript ランタイムとの直接かつ緊密な統合により、高パフォーマンスなトランザクション CRUD の操作とクエリを実行する能力。 
-	制御フロー、変数のスコープ設定、割り当て、例外処理プリミティブとデータベース トランザクションの割り当てと統合の自然なモデリング。DocumentDB による JavaScript 統合のサポートの詳細については、JavaScript のサーバー側プログラミングに関する文書を参照してください。

###ユーザー定義関数 (UDF)
この記事で定義した型に加えて、DocumentDB SQL ではユーザー定義関数 (UDF) のサポートを提供しています。具体的にはスカラー UDF がサポートされています。開発者は、0 個またはいくつかの引数を渡し、1 つの引数を結果として返すことができます。また、これらの引数のそれぞれが、有効な JSON 値であることがチェックされます。  

これらのユーザー定義関数を使用することで、DocumentDB SQL の文法を拡張し、カスタムのアプリケーション ロジックをサポートすることができます。UDF は DocumentDB に登録し、SQL クエリの一部として参照することができます。実際、その高度な設計に基づいて UDF はクエリから呼び出すことができます。この選択に基づく当然の帰結として、UDF には、その他の JavaScript の型 (ストアド プロシージャおよびトリガー) とは異なり、コンテキスト オブジェクトへのアクセスはありません。クエリは読み取り専用で実行されるため、プライマリ レプリカでもセカンダリ レプリカでも実行することができます。このため、UDF は、その他の JavaScript の型とは異なり、セカンダリ レプリカで実行されるように設計されています。

以下は、DocumentDB データベースに、具体的にはドキュメント コレクションに、UDF を登録する方法の例です。

   
	   UserDefinedFunction sqrtUdf = new UserDefinedFunction
	   {
	       Id = "SQRT",
	       Body = @"function(number) { 
	                   return Math.sqrt(number);
	               };",
	   };
	   UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
	       collectionSelfLink/* link of the parent collection*/, 
	       sqrtUdf).Result;  
                                                                             
前の例では `SQRT` という名前の UDF を作成しています。この UDF は、JSON 値の単一の `number` を受け入れ、Math ライブラリを使用して数値の平方根を算出します。


これで、この UDF をプロジェクション内のクエリで使用できるようになりました。UDF をクエリ内から呼び出すときは、大文字と小文字が区別されるプレフィックス "udf." で修飾する必要があります。 

>[AZURE.NOTE] 2015 年 3 月 17 日以前では、SELECT SQRT(5) のような、"udf." プレフィックスのない UDF 呼び出しがサポートされていました。この呼び出しパターンは廃止されました。  

**クエリ**

	SELECT udf.SQRT(c.grade)
	FROM c IN Families.children

**結果**

	[
	  {
	    "$1": 2.23606797749979
	  }, 
	  {
	    "$1": 1
	  }, 
	  {
	    "$1": 2.8284271247461903
	  }
	]

以下の例のように、UDF はフィルター内で使用することもできます。この場合も、"udf." プレフィックスで修飾します。

**クエリ**

	SELECT c.grade
	FROM c IN Familes.children
	WHERE udf.SQRT(c.grade) = 1

**結果**

	[{
	    "grade": 1
	}]


基本的には UDF は有効なスカラー式であり、プロジェクションとフィルターの両方で使用することができます。 

UDF の機能はさらに拡張できます。条件ロジックが使用された以下の例を見てみます。

	   UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
	   {
	       Id = "SEALEVEL",
	       Body = @"function(city) {
	       		switch (city) {
	       		    case 'seattle':
	       		        return 520;
	       		    case 'NY':
	       		        return 410;
	       		    case 'Chicago':
	       		        return 673;
	       		    default:
	       		        return -1;
	                }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(collection.SelfLink, seaLevelUdf);
	
	
この UDF を実行する例を以下に示します。

**クエリ**

	SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
	FROM Families f	

**結果**

	 [
	  {
	    "city": "seattle", 
	    "seaLevel": 520
	  }, 
	  {
	    "city": "NY", 
	    "seaLevel": 410
	  }
	]


前述の例でわかるように、UDF では、JavaScript 言語の性能と DocumentDB SQL を統合することによって、組み込みの JavaScript ランタイム機能のサポートを活用して手続き型で条件付きの複雑なロジックを実行するためのリッチ プログラミング インターフェイスを提供します。

DocumentDB SQL は、UDF の処理の現在のステージ (WHERE 句または SELECT 句) で、ソース内の各ドキュメントについて、UDF に対する引数を提供します。結果は、実行パイプライン全体にシームレスに組み込まれます。UDF のパラメーターに参照されるプロパティを JSON 値で利用できない場合、パラメーターは未定義と見なされ、UDF 呼び出し全体がスキップされます。同様に UDF の結果が未定義の場合は結果に含められません。 

複雑なビジネス ロジックをクエリで実行するには、UDF は非常に便利な手段です。

###演算子の評価
JSON データベースという特性を持つ DocumentDB は、JavaScript 演算子とその評価セマンティクスに似た関係を備えています。DocumentDB は JSON サポートという点では JavaScript のセマンティクスを可能な限り保持していますが、演算子の評価はいくつかの点で異なっています。

従来の SQL とは異なり、DocumentDB SQL クエリ言語では、実際に値がデータベースから取得されるまで値の型は未知です。クエリの実行を効率化するため、大部分の演算子には厳密な型の要件があります。 

DocumentDB SQL は JavaScript とは異なり暗黙的な変換を実行しません。たとえば、`SELECT * FROM Person p WHERE p.Age = 21` のようなクエリは、値が 21 の Age プロパティを含むドキュメントに一致します。Age プロパティが文字列の "21" に一致するドキュメントや、
"021"、"21.0"、"0021"、"00021" などの無限のバリエーションに一致するドキュメントは対象外となります。 
これは、(演算子 == などに基づいて) 文字列の値が暗黙的に数値にキャストされる JavaScript と対照的です。インデックスの効率的な一致のために、DocumentDB SQL ではこのような選択が避けられないものとなっています。 

##パラメーター化された SQL
DocumentDB では、使い慣れた @ 表記で表されたパラメーターを使用するクエリがサポートされます。パラメーター化された SQL により、ユーザーの入力を堅牢に処理し、流用して、SQL インジェクションによってデータが誤って開示されるリスクを回避することができます。 

たとえば、パラメーターとして姓と住所 (都道府県) を使用するクエリを記述し、ユーザーの入力に基づいて、姓と住所 (都道府県) にさまざまな値を指定して実行できます。

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

この要求は、次のように、パラメーター化された JSON クエリとして DocumentDB に送信できます。

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

パラメーターの値には、有効な任意の JSON (文字列、数値、ブール値、null、配列や入れ子になった JSON も含む) を指定できます。また DocumentDB はスキーマフリーであるため、パラメーターはどの型に対しても検証されません。

##組み込み関数
DocumentDB では、ユーザー定義関数 (UDF) のようにクエリ内で使用できる、一般的な操作向けのいくつかの組み込み関数をサポートしています。

<table>
<tr>
<td>数学関数</td>	
<td>ABS、CEILING、EXP、FLOOR、LOG、LOG10、POWER、ROUND、SIGN、SQRT、SQUARE、および TRUNC</td>
</tr>
<tr>
<td>型チェック関数</td>	
<td>IS_ARRAY、IS_BOOL、IS_NULL、IS_NUMBER、IS_OBJECT、および IS_STRING</td>
</tr>
</table>  

現在ユーザー定義関数 (UDF) を使用している処理に対して組み込み関数を利用できる場合は、より高速かつ効率的な対応する組み込み関数を使用する必要があります。 

###数学関数
一般に、各数学関数は、引数として提供された入力値に基づいて計算を実行し、数値を返します。次の表に、サポートされている組み込みの数学関数を示します。

<table>
<tr>
<td><strong>使用方法</strong></td>
<td><strong>説明</strong></td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_abs">ABS (num_expr)</a></td>	
<td>指定された数値式の絶対値 (正の値) を返します。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ceiling">CEILING (num_expr)</a></td>	
<td>指定された数値式以上の最小の整数値を返します。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_floor">FLOOR (num_expr)</a></td>	
<td>指定された数値式未満の最大の整数を返します。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_exp">EXP (num_expr)</a></td>	
<td>指定された数値式の指数を返します。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log">LOG (num_expr [,base])</a></td>	
<td>指定された数値式の自然対数、または指定された基数を使用して自然対数を返します。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log10">LOG10 (num_expr)</a></td>	
<td>指定された数値式の底 10 の対数値を返します。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_round">ROUND (num_expr)</a></td>	
<td>最も近い整数値に丸められた数値を返します。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_trunc">TRUNC (num_expr)</a></td>	
<td>最も近い整数値に切り捨てられた数値を返します。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sqrt">SQRT (num_expr)</a></td>	
<td>指定された数値式の平方根を返します。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_square">SQUARE (num_expr)</a></td>	
<td>指定された数値式の 2 乗を返します。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_power">POWER (num_expr, num_expr)</a></td>	
<td>指定された値の指定された数値式のべき乗を返します。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sign">SIGN (num_expr)</a></td>	
<td>指定された数値式の符号値 (-1、0、1) を返します。</td>
</tr>
</table> 

たとえば、次のようなクエリを実行できます。

**クエリ**

    SELECT VALUE ABS(-4)

**結果**

    [4]

DocumentDB の関数と ANSI SQL の間の主な違いとして、DocumentDB の関数はスキーマを持たないデータやスキーマが混在するデータをうまく扱えるようになっています。たとえば、Size プロパティがないドキュメントや "unknown" のような数値以外の値を持つドキュメントの場合、エラーを返す代わりに、ドキュメントがスキップされます。

###型チェック関数
型チェック関数を使用すると、SQL クエリ内の式の型をチェックできます。型チェック関数を使用すると、ドキュメント内のプロパティの型が変数または不明の場合に型をその場で判定できます。次の表に、サポートされている組み込みの型チェック関数を示します。

<table>
<tr>
  <td><strong>使用方法</strong></td>
  <td><strong>説明</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>値の型が配列であるかどうかを示すブール値を返します。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>値の型がブール値であるかどうかを示すブール値を返します。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>値の型が null であるかどうかを示すブール値を返します。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>値の型が数値であるかどうかを示すブール値を返します。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>値の型が JSON オブジェクトであるかどうかを示すブール値を返します。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>値の型が文字列であるかどうかを示すブール値を返します。</td>
</tr>
</table>

これらの関数を使用して、次のようなクエリを実行できます。

**クエリ**

    SELECT VALUE IS_NUMBER(-4)

**結果**

    [true]


##LINQ と DocumentDB SQL
LINQ は、計算処理をオブジェクトのストリームに対するクエリとして表現する .NET プログラミング モデルです。DocumentDB は LINQ と連携するためのクライアント側ライブラリを提供しています。ここでは、JSON オブジェクトと .NET オブジェクト間の変換と、LINQ クエリのサブセットから DocumentDB クエリへのマッピングを実施します。 

DocumentDB を使用した LINQ クエリ サポートのアーキテクチャは以下の図のようになります。開発者は DocumentDB クライアントを使用して **IQueryable** オブジェクトを作成できます。このオブジェクトが DocumentDB クエリ プロバイダーを直接照会することで、LINQ クエリが DocumentDB クエリに変換されます。次にクエリが DocumentDB サーバーに渡されることで、結果セットが JSON 形式で取得されます。返された結果は、クライアント側で .NET オブジェクトのストリームに逆シリアル化されます。

![][1]
 


###.NET と JSON のマッピング
.NET オブジェクトと JSON ドキュメント間のマッピングは自然に実行されます。各データ メンバー フィールドが JSON オブジェクトにマップされ、フィールド名がオブジェクトの "key" 部分にマップされ、"value" 部分がオブジェクトの値の部分に再帰的にマップされます。以下の例では、作成された Family オブジェクトが JSON ドキュメントにマップされています。同様に JSON ドキュメントから .NET オブジェクトへのマップも行われています。

**C# クラス**

	public class Family
	{
	    [JsonProperty(PropertyName="id")]
	    public string Id;
	    public Parent[] parents;
	    public Child[] children;
	    public bool isRegistered;
	};
	
	public struct Parent
	{
	    public string familyName;
	    public string givenName;
	};
	
	public class Child
	{
	    public string familyName;
	    public string givenName;
	    public string gender;
	    public int grade;
	    public List<Pet> pets;
	};
	
	public class Pet
	{
	    public string givenName;
	};
	
	public class Address
	{
	    public string state;
	    public string county;
	    public string city;
	};
	
	// Create a Family object.
	Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
	Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
	Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
	Pet pet = new Pet { givenName = "Fluffy" };
	Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
	Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

	{
	    "id": "WakefieldFamily",
	    "parents": [
	        { "familyName": "Wakefield", "givenName": "Robin" },
	        { "familyName": "Miller", "givenName": "Ben" }
	    ],
	    "children": [
	        {
	            "familyName": "Merriam", 
	            "givenName": "Jesse", 
	            "gender": "female", 
	            "grade": 1,
	            "pets": [
	                { "givenName": "Goofy" },
	                { "givenName": "Shadow" }
	            ]
	        },
	        { 
	          "familyName": "Miller", 
	          "givenName": "Lisa", 
	          "gender": "female", 
	          "grade": 8 
	        }
	    ],
	    "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
	    "isRegistered": false
	};



###LINQ から SQL への変換
DocumentDB クエリ プロバイダーは、LINQ クエリから DocumentDB SQL クエリへのマッピングをベスト エフォートで実行します。ここからの説明は、LINQ の基本的な知識を持つ読者向けとなります。

まず型システムについては、すべての JSON プリミティブ型 (数値型、ブール値、文字列、Null) がサポートされます。サポートされるのはこれらの JSON 型だけです。以下のスカラー式がサポートされます。

-	定数値 - これらには、クエリが評価された時点でのプリミティブ データ型の定数値が含まれます。

-	プロパティ/配列インデックス式 - これらの式は、オブジェクトまたは配列要素のプロパティを参照します。

		family.Id;
		family.children[0].familyName;
		family.children[0].grade;
		family.children[n].grade; //n is an int variable

-	算術式 - これらには、数値およびブール値に対する共通の算術式が含まれます。完全な一覧については、SQL 仕様を参照してください。

		2 * family.children[0].grade;
		x + y;

-	文字列比較式 - これらには、文字列値と定数文字列値との比較が含まれます。  
 
		mother.familyName == "Smith";
		child.givenName == s; //s is a string variable

-	オブジェクト/配列作成式 - これらの式は、複合値の型または匿名型のオブジェクト、またはこうしたオブジェクトの配列を返します。これらの値は入れ子にすることができます。

		new Parent { familyName = "Smith", givenName = "Joe" };
		new { first = 1, second = 2 }; //an anonymous type with 2 fields              
		new int[] { 3, child.grade, 5 };

###クエリ演算子
標準 LINQ クエリ演算子が DocumentDB クエリに変換される方法を以下のいくつかの例で示します。

####Select 演算子
構文は `input.Select(x => f(x))` です。`f` はスカラー式です。

**LINQ ラムダ式**

	input.Select(family => family.parents[0].familyName);

**SQL** 

	SELECT VALUE f.parents[0].familyName
	FROM Families f



**LINQ ラムダ式**

	input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

	SELECT VALUE f.children[0].grade + c
	FROM Families f 



**LINQ ラムダ式**

	input.Select(family => new
	{
	    name = family.children[0].familyName,
	    grade = family.children[0].grade + 3
	});


**SQL** 

	SELECT VALUE {"name":f.children[0].familyName, 
	              "grade": f.children[0].grade + 3 }
	FROM Families f



####SelectMany 演算子
構文は `input.SelectMany(x => f(x))` です。`f` はコレクション型を返すスカラー式です。

**LINQ ラムダ式**

	input.SelectMany(family => family.children);

**SQL** 

	SELECT VALUE child
	FROM child IN Families.children



####Where 演算子
構文は `input.Where(x => f(x))`です。`f` はブール値を返すスカラー式です。

**LINQ ラムダ式**

	input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith" 



**LINQ ラムダ式**

	input.Where(
	    family => family.parents[0].familyName == "Smith" && 
	    family.children[0].grade < 3);

**SQL** 

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith"
	AND f.children[0].grade < 3


###複合クエリ
上記の演算子を組み合わせることで、より強力なクエリを作成できます。DocumentDB は入れ子になったコレクションをサポートするため、連結による複合も入れ子による複合も可能です。

####連結 

構文は `input(.|.SelectMany())(.Select()|.Where())*`. A concatenated query can start with an optional `SelectMany` クエリで開始し、その後に複数の `Select` または `Where` 演算子を指定できます。


**LINQ ラムダ式**

	input.Select(family=>family.parents[0])
	    .Where(familyName == "Smith");

**SQL**

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith"



**LINQ ラムダ式**

	input.Where(family => family.children[0].grade > 3)
	    .Select(family => family.parents[0].familyName);

**SQL** 

	SELECT VALUE f.parents[0].familyName
	FROM Families f
	WHERE f.children[0].grade > 3



**LINQ ラムダ式**

	input.Select(family => new { grade=family.children[0].grade}).
	    Where(anon=> anon.grade < 3);
            
**SQL** 

	SELECT *
	FROM Families f
	WHERE ({grade: f.children[0].grade}.grade > 3)



**LINQ ラムダ式**

	input.SelectMany(family => family.parents)
	    .Where(parent => parents.familyName == "Smith");

**SQL** 

	SELECT *
	FROM p IN Families.parents
	WHERE p.familyName = "Smith"



####入れ子

構文は `input.SelectMany(x=>x.Q())` です。Q は `Select`、 `SelectMany`、または `Where` 演算子です。

入れ子になったクエリでは、内側のクエリが外側のコレクションの各要素に適用されます。重要な機能として、内側のクエリは外側のコレクションの要素のフィールドを自己結合のように参照できます。

**LINQ ラムダ式**

	input.SelectMany(family=> 
	    family.parents.Select(p => p.familyName));

**SQL** 

	SELECT VALUE p.familyName
	FROM Families f
	JOIN p IN f.parents


**LINQ ラムダ式**

	input.SelectMany(family => 
	    family.children.Where(child => child.familyName == "Jeff"));
            
**SQL** 

	SELECT *
	FROM Families f
	JOIN c IN f.children
	WHERE c.familyName = "Jeff"



**LINQ ラムダ式**
            
	input.SelectMany(family => family.children.Where(
	    child => child.familyName == family.parents[0].familyName));

**SQL** 

	SELECT *
	FROM Families f
	JOIN c IN f.children
	WHERE c.familyName = f.parents[0].familyName


##クエリの実行
DocumentDB が公開するリソースには、HTTP/HTTPS 要求機能を持つ任意の言語から REST API を呼び出すことでアクセスできます。さらに、.NET、Node.js、JavaScript、Python など、いくつかの主要な言語のプログラミング ライブラリも用意されています。REST API と各種のライブラリはすべて SQL 経由のクエリをサポートしています。.NET SDK は SQL に加えて LINQ クエリをサポートしています。

以下の例では、クエリを作成して DocumentDB データベース アカウントに送信する方法について説明します。
###REST API
DocumentDB は、HTTP を介したオープンな RESTful プログラミング モデルを提供します。データベース アカウントは Azure サブスクリプションを使用してプロビジョニングできます。DocumentDB リソース モデルは、データベース アカウントに従属する一連のリソースから成り、個々のリソースは、不変の論理 URI アドレスを使用して参照することができます。このドキュメントでは、そうした一連のリソースをフィードと呼ぶことにします。データベース アカウントはデータベースのセットで構成され、各データベースに複数のコレクションが含まれています。これらのコレクションのそれぞれに、ドキュメント、UDF、その他のリソースが含まれます。

これらのリソースとの基本的な対話モデルでは、HTTP の動詞である GET、PUT、POST、および DELETE が標準の解釈で使用されます。POST 動詞は、新しいリソースの作成、ストアド プロシージャの実行、または DocumentDB クエリの発行に使用されます。クエリは常に読み取り専用の操作で、副作用はありません。

以下の例では、これまでに説明した 2 つのサンプル ドキュメントを含むコレクションに対する、DocumentDB クエリの POST を示しています。このクエリには、JSON の名前プロパティに対するシンプルなフィルターがあります。 `x-ms-documentdb-isquery` および Content-Type: `application/query+json` ヘッダーは、クエリによる操作であることを示しています。


**要求**

	POST https://<REST URI>/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
	

**結果**

	HTTP/1.1 200 Ok
	x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
	x-ms-item-count: 1
	x-ms-request-charge: 0.32
	
	<indented for readability, results highlighted>
	
	{  
	   "_rid":"u1NXANcKogE=",
	   "Documents":[  
	      {  
	         "id":"AndersenFamily",
	         "lastName":"Andersen",
	         "parents":[  
	            {  
	               "firstName":"Thomas"
	            },
	            {  
	               "firstName":"Mary Kay"
	            }
	         ],
	         "children":[  
	            {  
	               "firstName":"Henriette Thaulow",
	               "gender":"female",
	               "grade":5,
	               "pets":[  
	                  {  
	                     "givenName":"Fluffy"
	                  }
	               ]
	            }
	         ],
	         "address":{  
	            "state":"WA",
	            "county":"King",
	            "city":"seattle"
	         },
	         "_rid":"u1NXANcKogEcAAAAAAAAAA==",
	         "_ts":1407691744,
	         "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
	         "_etag":"00002b00-0000-0000-0000-53e7abe00000",
	         "_attachments":"_attachments\/"
	      }
	   ],
	   "count":1
	}


2 つ目の例は、結合から複数の結果を返す、より複雑なクエリを示しています。

**要求**

	POST https://<REST URI>/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/query+json
	
    {      
        "query": "SELECT 
				     f.id AS familyName, 
				     c.givenName AS childGivenName, 
				     c.firstName AS childFirstName, 
				     p.givenName AS petName 
				  FROM Families f 
				  JOIN c IN f.children 
				  JOIN p in c.pets",     
        "parameters": [] 
    }


**結果**

	HTTP/1.1 200 Ok
	x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
	x-ms-item-count: 1
	x-ms-request-charge: 7.84
	
	<indented for readability, results highlighted>
	
	{  
	   "_rid":"u1NXANcKogE=",
	   "Documents":[  
	      {  
	         "familyName":"AndersenFamily",
	         "childFirstName":"Henriette Thaulow",
	         "petName":"Fluffy"
	      },
	      {  
	         "familyName":"WakefieldFamily",
	         "childGivenName":"Jesse",
	         "petName":"Goofy"
	      },
	      {  
	         "familyName":"WakefieldFamily",
	         "childGivenName":"Jesse",
	         "petName":"Shadow"
	      }
	   ],
	   "count":3
	}


クエリの結果を 1 つの結果ページに収めることができない場合、REST API は `x-ms-continuation-token` 応答ヘッダーを使って継続トークンを返します。クライアントは、このヘッダーを後続の結果に含めることで、結果を改ページすることができます。ページあたりの結果の数も、 `x-ms-max-item-count` 番号ヘッダーで制御できます。

クエリのデータ一貫性ポリシーを管理するには、すべての REST API 要求と同様に `x-ms-consistency-level` ヘッダーを使用します。セッションの一貫性を維持するには、すべてのクエリ要求で最新の `x-ms-session-token` Cookie ヘッダーもエコーする必要があります。クエリされたコレクションのインデックス作成ポリシーは、クエリ結果の一貫性にも影響を与える点に注意してください。既定のインデックス作成ポリシーの設定では、インデックスはドキュメントの内容に関して常に最新の状態になり、クエリ結果はデータ用に選択された一貫性と一致します。インデックス作成ポリシーが "遅延" に緩和されている場合、返されるクエリ結果も古いものになる可能性があります。詳細については、[DocumentDB の一貫性レベルに関する記事][consistency-levels]を参照してください。

指定されたクエリを、コレクションで構成されたインデックス作成ポリシーがサポートできない場合、DocumentDB サーバーは 400 "Bad Request" を返します。これは、ハッシュ (等値) 検索用に構成されたパスに対する範囲クエリと、インデックス作成から明示的に除外されたパスのために返されます。 `x-ms-documentdb-query-enable-scan` ヘッダーを指定することで、インデックスを利用できない場合のクエリによるスキャン実行を許可することができます。

###C# (.NET) SDK
.NET SDK は LINQ クエリと SQL クエリの両方をサポートしています。以下の例は、このドキュメントで前述したシンプルなフィルター クエリを実行する方法を示しています。


	foreach (var family in client.CreateDocumentQuery(collectionLink, 
	    "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
	{
	    Console.WriteLine("\tRead {0} from SQL", family);
	}
	
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

	foreach (var family in (
	    from f in client.CreateDocumentQuery(collectionLink)
	    where f.Id == "AndersenFamily"
	    select f))
	{
	    Console.WriteLine("\tRead {0} from LINQ query", family);
	}
	
	foreach (var family in client.CreateDocumentQuery(collectionLink)
	    .Where(f => f.Id == "AndersenFamily")
	    .Select(f => f))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", family);
	}


このサンプルでは、2 つのプロパティの等値比較を各ドキュメント内で実行し、匿名プロジェクションを使用しています。 


	foreach (var family in client.CreateDocumentQuery(collectionLink,
	    @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
	    FROM Families f 
	    WHERE f.address.city = f.address.state"))
	{
	    Console.WriteLine("\tRead {0} from SQL", family);
	}
	
	foreach (var family in (
	    from f in client.CreateDocumentQuery<Family>(collectionLink)
	    where f.address.city == f.address.state
	    select new { Name = f.Id, City = f.address.city }))
	{
	    Console.WriteLine("\tRead {0} from LINQ query", family);
	}
	
	foreach (var family in
	    client.CreateDocumentQuery<Family>(collectionLink)
	    .Where(f => f.address.city == f.address.state)
	    .Select(f => new { Name = f.Id, City = f.address.city }))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", family);
	}


次のサンプルは、LINQ SelectMany によって表された結合を示しています。


	foreach (var pet in client.CreateDocumentQuery(collectionLink,
	      @"SELECT p
	        FROM Families f 
	             JOIN c IN f.children 
	             JOIN p in c.pets 
	        WHERE p.givenName = ""Shadow"""))
	{
	    Console.WriteLine("\tRead {0} from SQL", pet);
	}
	
	// Equivalent in Lambda expressions
	foreach (var pet in
	    client.CreateDocumentQuery<Family>(collectionLink)
	    .SelectMany(f => f.children)
	    .SelectMany(c => c.pets)
	    .Where(p => p.givenName == "Shadow"))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", pet);
	}



上記に示したとおり、.NET クライアントは foreach ブロックのクエリ結果のすべてのページを自動で反復処理します。REST API のセクションで説明したクエリ オプションは .NET SDK でも利用可能です。これには、 `FeedOptions` クラスおよび `FeedResponse` クラスを CreateDocumentQuery メソッドで使用します。ページの数は `MaxItemCount` 設定を使用して制御できます。 

開発者は、 `IDocumentQueryable` を作成することでページ設定を明示的に制御できます。これには  `IQueryable` オブジェクトを使用し、次に `ResponseContinuationToken` の値を読み取り、これらを `RequestContinuationToken` として  `FeedOptions` 内で渡します。 `EnableScanInQuery` を設定することで、構成されたインデックス作成ポリシーでクエリをサポートできない場合に、スキャンを有効にすることができます。

クエリのその他のサンプルについては、[DocumentDB の .NET サンプル](http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af#content)を参照してください。 

###JavaScript のサーバー側 API 
DocumentDB が提供するプログラミング モデルでは、ストアド プロシージャとトリガーという点では、JavaScript ベースのアプリケーション ロジックをコレクションで直接実行することができます。コレクション レベルで登録された JavaScript ロジックは、特定のコレクション内のドキュメントの操作に対してデータベース操作を発行できるようになっています。これらの操作は周囲の ACID トランザクションにラップされます。

以下の例は、JavaScript サーバー API で queryDocuments を使用して、ストアド プロシージャとトリガーからクエリを実行する方法を示しています。


	function businessLogic(name, author) {
	    var context = getContext();
	    var collectionManager = context.getCollection();
	    var collectionLink = collectionManager.getSelfLink()
	
	    // create a new document.
	    collectionManager.createDocument(collectionLink,
	        { name: name, author: author },
	        function (err, documentCreated) {
	            if (err) throw new Error(err.message);
	
	            // filter documents by author
	            var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
	            collectionManager.queryDocuments(collectionLink,
	                filterQuery,
	                function (err, matchingDocuments) {
	                    if (err) throw new Error(err.message);
	context.getResponse().setBody(matchingDocuments.length);
	
	                    // Replace the author name for all documents that satisfied the query.
	                    for (var i = 0; i < matchingDocuments.length; i++) {
	                        matchingDocuments[i].author = "George R. R. Martin";
	                        // we don't need to execute a callback because they are in parallel
	                        collectionManager.replaceDocument(matchingDocuments[i]._self,
	                            matchingDocuments[i]);
	                    }
	                })
	        });
	}


##参照
1.	[Azure DocumentDB の概要][introduction]
2.	[DocumentDB SQL 言語の仕様](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.	[DocumentDB の .NET サンプル](http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af#content)
4.	[DocumentDB の一貫性レベル][consistency-levels]
5.	ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.	JSON [http://json.org/](http://json.org/)
7.	Javascript 仕様 [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8.	LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9.	大規模なデータベース向けのクエリ評価技術[http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10.	「Query Processing in Parallel Relational Database Systems」(IEEE Computer Society Press、1994 年)
11.	「Query Processing in Parallel Relational Database Systems」(Lu、Ooi、Tan、IEEE Computer Society Press、1994 年)
12.	「Pig Latin: A Not-So-Foreign Language for Data Processing」(Christopher Olston、Benjamin Reed、Utkarsh Srivastava、Ravi Kumar、Andrew Tomkins、SIGMOD、2008 年)
13.     G. Graefe.The Cascades framework for query optimization.IEEE Data Eng. Bull., 18(3):1995。


[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md

<!--HONumber=49-->