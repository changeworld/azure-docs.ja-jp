<properties
   pageTitle="Azure の Elasticsearch でのデータ集計および qQuery のパフォーマンスのチューニング | Microsoft Azure"
   description="Elasticsearch のクエリおよび検索のパフォーマンスを最適化するときの考慮事項の概要。"
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
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Azure の Elasticsearch でのデータ集計および qQuery のパフォーマンスのチューニング

これは[一連の記事の一部](guidance-elasticsearch.md)です。

Elasticsearch を使用する主な理由は、データの検索をサポートするためです。ユーザーは、探している情報をすばやく見つけることができる必要があります。さらに、ユーザーがデータについて質問し、相関関係を発見し、ビジネス上の判断を下せる結論を得ることができるようなシステムでなければなりません。情報をデータと異なるものにしているのはこのような処理です。

このドキュメントでは、クエリと検索に関して最高のパフォーマンスが得られるシステムにする最善の方法を決定するときに考慮できるオプションについて説明します。

パフォーマンスに関するすべての推奨事項は、状況に適用されるシナリオ、インデックスを作成するデータの量、アプリケーションやユーザーがデータをクエリする頻度に大きく依存します。独自のデータとワークロードを使用して構成またはインデックス構造の変更の結果を慎重にテストし、特定のシナリオに対するメリットを評価する必要があります。そのため、このドキュメントでは、さまざまな構成を使用して実装されている 1 つの特定のシナリオに対して実行されたさまざまなベンチマークについても説明します。使用されている方法を調整して、実際のシステムのパフォーマンスを評価できます。

## インデックスとクエリのパフォーマンスに関する考慮事項

このセクションでは、高速なクエリと検索をサポートする必要があるインデックスを設計するときに考慮する必要のあるいくつかの一般的な要因について説明します。

### インデックスに複数のタイプを格納する

Elasticsearch のインデックスは、複数のタイプを含むことができます。このようなことはせず、タイプごとに個別のインデックスを作成する方がよい場合があります。次の点を考慮してください。

- タイプが異なると指定されるアナライザーが異なる場合があり、クエリがタイプ レベルではなくインデックス レベルで実行される場合は、Elasticsearch で使用する必要があるアナライザーがはっきりしないことがあります。詳細については、「[Avoiding Type Gotchas (タイプの注意事項の回避)](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html#_avoiding_type_gotchas)」を参照してください。

- 複数のタイプを保持するインデックスのシャードは、単一のタイプを含むインデックスのシャードより大きくなりがちです。シャードが大きいほど、クエリ実行時に Elasticsearch がデータのフィルター処理に必要な作業が多くなります。

- タイプによってデータ量に大きな差がある場合、少ないタイプの情報は多くのシャードの間にまばらに分散し、そのデータを取得する検索の効率が低下します。

![](./media/guidance-elasticsearch/query-performance1.png)

***図 1.異なるタイプでインデックスを共有した場合の影響***

図 1 は、このような状況を示したものです。図の上部では、同じインデックスをタイプ A とタイプ B のドキュメントで共有しています。タイプ A のドキュメントの方がタイプ B より多く存在します。タイプ B の検索では、4 つのシャードすべてがクエリされます。図の下部では、タイプごとにインデックスを分けた場合の効果が示されています。この場合、タイプ A の検索でアクセスする必要があるシャードは 2 つだけです。

小さいシャードは大きいシャードより均等に分散でき、Elasticsearch によるノード間への負荷の分散が容易になります。

タイプが異なると、保持期間が異なる場合があります。アクティブなデータとシャードを共有する古いデータをアーカイブするのは難しいことがあります。

ただし、次のような状況では、異なるタイプの間でインデックスを共有すると効率的な場合があります。

- 通常、検索が同じインデックスで保持されている異なるタイプを対象とする場合。

- 各タイプのドキュメントの数が少ない場合。タイプごとに個別にシャードのセットを管理すると、オーバーヘッドが大きくなります。

### インデックス タイプの最適化

Elasticsearch インデックスには、インデックスの作成に使用された元の JSON ドキュメントのコピーが含まれています。この情報は、インデックスを作成された各項目の [*\_source*](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-source-field.html#mapping-source-field) フィールドに保持されています。このデータは検索できませんが、*取得*および*検索*要求では既定で返されます。ただし、このフィールドはオーバーヘッドを発生させ、記憶域を占めるので、シャードが大きくなり、実行される I/O の量が増加します。タイプごとに *\_source* フィールドを無効にできます。

```http
PUT my_index
{
    "mappings": {
		"my_type": {
			"_source": {
				"enabled": false
			}
		}
	}
}
```

このフィールドを無効にすると、次の操作を実行する機能も削除されます。

- *更新* API を使用してインデックス内のデータを更新する。
- 強調表示されたデータを返す検索を実行する。
- Elasticsearch のあるインデックスから別のインデックスに直接インデックスを再作成する。
- マッピングまたは分析の設定を変更する。
- 元のドキュメントを表示してクエリをデバッグする。
 
### データのインデックスの再作成

インデックスに使用可能なシャードの数は、最終的にインデックスの容量を決定します。必要になるシャードの数を最初に (情報に基づいて) 予測できますが、ドキュメントのインデックス再作成戦略を常に前もって考えておく必要があります。多くの場合、インデックスの再作成はデータの増加に合わせた意図的なタスクです。検索を最適化するため最初はインデックスに割り当てるシャードの数を少なくしておき、データの量が増えたら新しいシャードを割り当てます。それ以外では、データ量増加の推定が正しくないことがわかった場合は、さらに臨機応変にインデックス再作成の実行が必要になることがあります。

> [AZURE.NOTE] すぐに古くなるデータの場合は、インデックスの再作成が必要ないことがあります。その場合は、アプリケーションで各期間の新しいインデックスを作成する場合があります。たとえば、毎日新しいインデックスに格納されるパフォーマンス ログや監査データなどです。

インデックスの再作成では、実質的に、古いインデックスのデータからの新しいインデックスの作成と、古いインデックスの削除が行われます。インデックスが大きい場合、この処理には時間がかかり、この処理の間もデータを検索可能にしておくことが必要な場合があります。そのためには、[各インデックスのエイリアス](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html)を作成し、クエリではエイリアスからデータを取得する必要があります。インデックスの再作成中はエイリアスが古いインデックスを参照するようにしておき、インデックスの再作成が完了したら新しいインデックスを参照するように切り替えます。この方法は、毎日新しいインデックスが作成される時間ベースのデータにアクセスする場合にも便利です。現在のデータにアクセスするには、新しいインデックスが作成されたらそれにロール オーバーするエイリアスを使用します。

### マッピングの管理

Elasticsearch では、ドキュメント内の各フィールドのデータを解釈する方法を決定するためにマッピングが使用されます。タイプごとに固有のマッピングがあり、実質的にそのタイプのスキーマを定義します。Elasticsearch はこの情報を使用して、あるタイプのドキュメントの各フィールドの逆インデックスを生成します。どのようなドキュメントでも、フィールドごとにデータ型 (*string*、*date*、*long* など) と値があります。インデックスを最初に作成するときにインデックスのマッピングを指定することも、または新しいドキュメントをタイプに追加するときに Elasticsearch で推測することもできます。ただし、次の点を考慮してください。

- 動的に生成されるマッピングは、ドキュメントがインデックスに追加されるときのフィールドの解釈方法によっては、エラーの原因になることがあります。たとえば、数値を保持するフィールド A を含むドキュメント 1 により、このフィールドが *long* であると指定するマッピングを Elasticsearch が追加するものとします。その後で追加されるドキュメントのフィールド A が非数値データの場合、エラーになります。この場合はおそらく、最初のドキュメントが追加されるときに、フィールド A を文字列と解釈する必要があります。インデックスの作成時にこのマッピングを指定すると、このような問題を回避するのに役立ちます。

- 過剰に大きなマッピングが生成されないように、ドキュメントをデザインします。マッピングが大きいと、検索実行時に大きなオーバーヘッドが発生し、大量のメモリを消費し、クエリでデータが見つからない原因になることもあります。ドキュメント内の同じタイプのフィールドには、一貫性のある名前付け規則を使用します。たとえば、ドキュメントごとに "first\_name"、"FirstName"、"forename" などと異なるフィールド名を使うのではなく、すべてのドキュメントで同じフィールド名を使用します。さらに、値をキーとして使わないようにします (これは列ファミリ データベースでの一般的な方法ですが、Elasticsearch では非効率性とエラーの原因になることがあります)。 詳細については、「[Mapping Explosion (マッピングの爆発的増加)](https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion)」を参照してください。

- 必要に応じて、*not\_analyzed* を使用してトークン化を回避します。たとえば、あるドキュメントの *data* という名前の文字列フィールドに値 "ABC-DEF" が保持されていて、次のようにこの値と一致するすべてのドキュメントを検索するものとします。

```http
GET /myindex/mydata/_search
{
	"query" : {
		"filtered" : {
			"filter" : {
				"term" : {
					"data" : "ABC-DEF"
				}
			}
		}
	}
}
```

しかし、インデックス作成時の文字列 ABC-DEF のトークン化方法のため、この検索では期待したような結果が返りません。この値は、ハイフンによって ABC と DEF という 2 つのトークンに分割されます。この機能はフルテキスト検索をサポートするために設計されていますが、文字列が 1 つのアトミックな項目として解釈されてほしい場合は、ドキュメントをインデックスに追加するときにトークン化を無効にする必要があります。次のようにマッピングを使用できます。

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					"type" : "string",
					"index" : "not_analyzed"
				}
			}
		}
	}
}
```

詳細については、「[Finding Exact Values (正確な値の検索)](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_term_filter_with_text)」を参照してください。

### doc 値の使用

多くのクエリと集計では、検索操作の一部としてデータの並べ替えが必要になります。並べ替えでは、1 つまたは複数の用語をドキュメントのリストにマップできる必要があります。このプロセスを補助するため、Elasticsearch では並べ替えキーとして使用されるフィールドのすべての値をメモリに読み込むことができます。この情報は*フィールドデータ*と呼ばれます。この機能の目的は、フィールドデータをメモリにキャッシングすることで I/O を減らすことであり、同じデータをディスクから繰り返し読むより速くなる可能性があります。ただし、フィールドの基数が高い場合は、フィールドデータをメモリに格納すると、大量のヒープ領域を消費し、他の同時操作のパフォーマンスに影響を与えたり、記憶域を使い果たして Elasticsearch で障害が発生したりする可能性さえあります。

代わりの方法として、Elasticsearch では *doc 値*もサポートされています。doc 値はメモリ内のフィールドデータの項目と似ていますが、ディスクに格納され、データがインデックスに格納されるときに作成される点が異なります (フィールドデータはクエリ実行時に動的に構築されます)。 doc 値はヒープ領域を消費しないので、非常に多数の一意値を含む可能性があるフィールド間でデータの並べ替えまたは集計を行うクエリに適しています。さらに、ヒープの負荷が減ることで、データをディスクから取得する場合とメモリから読み取る場合のパフォーマンスの差を埋め合わせできます。ガベージ コレクションの発生頻度や、メモリを利用する他の同時操作が受ける影響も、低下する可能性があります。

次の例で示すように、doc 値は、*doc\_values* 属性を使用してインデックスのプロパティ単位で有効または無効にします。

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					...
				"doc_values": true
				}
			}
		}
	}
}
```

> [AZURE.NOTE] Elasticsearch バージョン 2.0.0 以降では、doc 値は既定で有効になっています。

doc 値を使用することによる厳密な影響は、実際のデータおよびクエリのシナリオと密接に関係するので、パフォーマンス テストを行って有用性を確認することを考える必要があります。また、doc 値は分析済みの文字列フィールドでは動作しないことにも注意してください。詳細については、「[Doc Values (doc 値)](https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html#doc-values)」を参照してください。

### クライアント ノードの使用

すべてのクエリは、要求を最初に受信したノードによって処理されます。このノードは、クエリ対象のインデックスのシャードが含まれている他のすべてのノードにさらに要求を送信し、応答を返すために結果を蓄積します。クエリにデータの集計または複雑な計算の実行が含まれる場合、最初のノードは適切な処理の実行を担当します。比較的少数の複雑なクエリをサポートするシステムの場合は、クライアント ノードのプールを作成してデータ ノードの負荷を軽減することを考えてください。逆に、多数の単純なクエリを処理する場合は、要求をデータ ノードに直接送信し、ロード バランサーを使用して要求を均等に分散させます。

### レプリカを使用したクエリの競合の削減

クエリのパフォーマンス向上のためによく使われる方法は、各インデックスの多数のレプリカを作成するというものです。データ取得操作には、レプリカからデータをフェッチして対応できます。ただし、この方法はデータ取り込み操作のパフォーマンスに大きな影響を与える可能性があるので、複数のワークロードが関連するシナリオでは注意して使用する必要があります。さらに、この方法にメリットがあるのは、レプリカが複数のノードに分散されていて、同じインデックスの一部であるプライマリ シャードとリソースを競合しない場合だけです。インデックスのレプリカの数を動的に増減できることを憶えておいてください。

### シャード要求キャッシュの使用

Elasticsearch は、クエリによって要求されたローカル データを各シャードのメモリにキャッシュできます。これにより、データをディスク ストレージではなくメモリから取得できるので、同じデータにアクセスするクエリの実行速度が速くなります。シャードが更新されてデータが変化すると、キャッシュ内のデータは無効になります。更新の頻度は、インデックスの *refresh\_interval* 設定の値で制御できます。既定ではインデックスのシャード要求キャッシュは無効になっていますが、次のようにして有効にできます。

```http
PUT /myindex/_settings
{
	"index.requests.cache.enable": true
}
```

シャード要求キャッシュが最も適しているのは、履歴データやログ データのような比較的変化しない情報の場合です。

## 集計と検索のパフォーマンスのテストと分析
このセクションでは、さまざまなクラスターおよびインデックス構成に対して実行された一連のテストの結果について説明します。各テストは、一括挿入操作 (各操作では 1000 個のドキュメントが追加されました) を実行してテスト用に設定された空のインデックスを使用して開始されました。同時に、特定のデータを検索して集計を生成するように設計された複数のクエリが、5 秒間隔で繰り返されました。テストの目的は、データの量によるクエリのパフォーマンスへの影響を確認することでした。

インデックス内の各ドキュメントのスキーマは同じでした。次の表はスキーマのフィールドをまとめたものです。

 名前 | 型 | メモ |
  ----------------------------- | ------------ | -------------------------------------------------------- |
 Organization | String | 200 個の一意の組織が生成されます。 |
 CustomField1 ～ CustomField5 |String |これら 5 つの文字列フィールドには空の文字列が設定されます。|
 DateTimeRecievedUtc |Timestamp |ドキュメントが追加された日付と時刻です。|
 Host |String |このフィールドには空の文字列が設定されます。|
 HttpMethod |String |このフィールドには、“POST”、”GET”、”PUT” のいずれかの値が設定されます。|
 HttpReferrer |String |このフィールドには空の文字列が設定されます。|
 HttpRequest |String |このフィールドには、長さが 10 ～ 200 文字のランダムなテキストが設定されます。|
 HttpUserAgent |String |このフィールドには空の文字列が設定されます。|
 HttpVersion |String |このフィールドには空の文字列が設定されます。|
 OrganizationName |String |このフィールドには、Organization フィールドと同じ値が設定されます。|
 SourceIp |IP |このフィールドには、データの「生成元」を示す IP アドレスが設定されます。 |
 SourceIpAreaCode |Long |このフィールドには 0 が設定されます。|
 SourceIpAsnNr |String |このフィールドには "AS#####" が設定されます。|
 SourceIpBase10 |Long |このフィールドには 500 が設定されます。|
 SourceIpCountryCode |String |このフィールドには、2 文字の国コードが設定されます。 |
 SourceIpCity |String |このフィールドには、国の都市を示す文字列が設定されます。 |
 SourceIpLatitude |Double |このフィールドには、ランダムな値が設定されます。|
 SourceIpLongitude |Double |このフィールドには、ランダムな値が設定されます。|
 SourceIpMetroCode |Long |このフィールドには 0 が設定されます。|
 SourceIpPostalCode |String |このフィールドには空の文字列が設定されます。|
 SourceLatLong |Geo point |このフィールドには、ランダムな地理的ポイントが設定されます。|
 SourcePort |String |このフィールドには、ランダムな数値の文字列表現が設定されます。|
 TargetIp |IP |このフィールドには、0.0.100.100 ～ 255.9.100.100 の範囲のランダムな IP アドレスが設定されます。|
 SourcedFrom |String |このフィールドには、文字列 "MonitoringCollector" が設定されます。|
 TargetPort |String |このフィールドには、ランダムな数値の文字列表現が設定されます。|
 Rating |String |このフィールドには、20 個の異なる文字列からランダムに選択された値が設定されます。|
 UseHumanReadableDateTimes |Boolean |このフィールドには false が設定されます。|

テストの各反復では、次のクエリがバッチとして実行されました (斜体の名前は、後でこれらのクエリを参照するために使用されます)。

- 過去 15 分間に追加されたドキュメントの *Rating* ごとの数 (*Rating 別数*)

- 過去 15 分間に追加されたドキュメントの 5 分ごとの数 (*時間別数*)

- 過去 15 分間に追加されたドキュメントの、国別かつ *Rating* 値別の数 (*国別ヒット数*)

- 過去 15 分間に追加されたドキュメントで、最も頻度が多かった上位 15 の組織 (*上位 15 組織*)

- 過去 15 分間に追加されたドキュメントでの異なる組織の数 (*固有組織数*)

- 過去 15 分間に追加されたドキュメントの数 (*合計ヒット数*)

- 過去 15 分間に追加されたドキュメントでの異なる *SourceIp* 値の数 (*固有 IP 数*)

次の変数の影響を理解するために、テストを実施しました。

- **ディスクの種類**。Standard Storage (HDD) を使用した D4 VM の 6 ノード クラスターと、Premium Storage (SSD) を使用した DS4 VM の 6 ノード クラスターでテストを実行しました。

- **マシンのサイズ - スケールアップ**。DS3 VM で構成される 6 ノード クラスター (*小規模*クラスター)、DS4 VM のクラスター (*中規模*クラスター)、DS14 マシンのクラスター (*大規模*クラスター) でテストを実行しました。次の表では、各 VM SKU の主な特性をまとめます。

クラスター |VM の SKU |コアの数 |データ ディスクの数 |RAM (GB)|
--------- |--------------- |----------------- |---------------------- |--------|
Small |Standard DS3 |4 |8 |14 |
Medium |Standard DS4 |8 |16 |28 |
Large |Standard DS14 |16 |32 |112 |

- **doc 値**。最初にインデックス設定 *doc\_values* を *true* に設定してテストを実行しました。次に、*doc\_values* を *false* に設定して選択したテストを繰り返しました。

## パフォーマンスの結果 – ディスクの種類

次の表は、D4 VM (HDD を使用) の 6 ノード クラスターと DS4 VM (SSD を使用) の 6 ノード クラスターでテストを実行したときの処理の応答時間をまとめたものです。Elasticsearch の構成はどちらのクラスターも同じです。データは各ノードの 16 個のディスクに分散し、各ノードでは Elasticsearch を実行する JVM に 14 GB の RAM が割り当てられていました。残りのメモリ (やはり 14 GB) はオペレーティング システム用に残されていました。各テストを 24 時間実行しました。この時間は、データの量が増えたことによる影響が明らかになり、システムが安定することを想定して選択されました。

 クラスター |操作/クエリ |平均応答時間 (ミリ秒)|
 -----------|---------------------------- |--------------------------|
 D4 |データの取り込み |978 |
 |Rating 別数 |103 |
 |時間別数 |134 |
 |国別ヒット数 |199 |
 |上位 15 組織 |137 |
 |固有組織数 |139 |
 |固有 IP 数 |510 |
 |合計ヒット数 |89 |
 DS4 |データの取り込み |511 |
 |Rating 別数 |187 |
 |時間別数 |411 |
 |国別ヒット数 |402 |
 |上位 15 組織 |307 |
 |固有組織数 |320 |
 |固有 IP 数 |841 |
 |合計ヒット数 |236 |

一見して、D4 クラスターより DS4 クラスターの方がクエリ実行パフォーマンスが低く、応答時間で約 2 倍以上悪いように見えます。これでは全体の様子はわかりません。次の表では、各クラスターで実行された取り込み操作の数を示します (各操作では 1000 個のドキュメントが読み込まれることに注意してください)。

 クラスター | 取り込み操作
 ----------|---------------------
 D4 | 264769              
 DS4 | 503157              

DS4 クラスターは、テストの間に D4 クラスターの約 2 倍のデータを読み込むことができました。したがって、各操作の応答時間を分析するときは、各クエリでスキャンする必要があるドキュメントの数、および返されたドキュメントの数も考慮する必要があります。

インデックスのドキュメントの量は継続的に増加しているので、これらは動的な数値です。単純に、503137 を 264769 で割り (各クラスターによって実行された取り込み操作の数)、その結果に D4 クラスターで実行された各クエリの平均応答時間を掛けただけでは、比較できる数値は得られません。この方法では、取り込み操作によって同時に実行された I/O の量が無視されています。

代わりに、テストの進行に伴って、物理的にディスクに書き込まれたデータ量とディスクから読み取られたデータ量を測定する必要があります。JMeter テスト計画では、各ノードについてこの情報が取得されます。集計された結果は次のとおりです。

 クラスター |各操作による平均書き込み/読み取りバイト数|
  --------- |--------------------------------------------|
 D4 |13471557 |
 DS4 |24643470 |

これらの値は、DS4 クラスターが D4 クラスターの約 1.8 倍の I/O 速度を維持できたことを示しています。ディスクの特性は考えずに、他のすべてのリソースが同じであるとすると、違いは HDD ではなく SSD を使用したことによるものです。

この結論を正当化するものとして、次のグラフでは各クラスターによる時間を追った I/O 実行の様子を示します。

![](./media/guidance-elasticsearch/query-performance2.png)

D4 クラスターのグラフでは大きな変動が示されています (特にテストの前半)。これはおそらく、I/O 速度を抑えるためのスロットルによるものです。テストの初期の段階では、分析するデータが少ないので、クエリは速く実行できます。したがって、D4 クラスターのディスクは IOPS 能力に近く動作していますが、各 I/O 操作では多くのデータを返されていない可能性があります。DS4 クラスターはさらに高い IOPS 速度をサポートでき、同じほどのスロットルは受けません。I/O 速度はより一定です。

この理論を説明するため、次の 2 つのグラフでは、ディスク I/O によって CPU がどのようにブロックされるかを示します (グラフに示されているディスク待機時間は、CPU が I/O の待機に使用した時間の割合です)。

![](./media/guidance-elasticsearch/query-performance3.png)

このテスト シナリオでは、I/O 操作が CPU をブロックするには 2 つの主要な理由があることを理解することが重要です。

- I/O サブシステムは、ディスクとの間でデータを読み書きしている可能性があります。

- I/O サブシステムは、ホスト環境によってスロットルされる可能性があります。Standard Storage を使用する Azure ディスクの最大スループットは 500 IOPS ですが、Premium Storage では 5000 IOPS です。

D4 クラスターの場合、テストの前半で I/O 待機に費やされた時間は、I/O 速度を示すグラフと密接に逆相関しています。低 I/O の期間は、CPU がブロックされている時間が長い期間に対応します。

これは、I/O がスロットルされていることを示します。クラスターに追加されたデータが多くなると状況は一変し、テストの後半では、I/O 待機時間のピークは I/O スループットのピークと対応しています。この時点では、CPU は実際の I/O の実行中にブロックされています。ここでも、DS4 クラスターでは、I/O 待機時間がはるかに一定であり、各ピークは I/O パフォーマンスの谷ではなく同等のピークと一致しています。これは、スロットルがほとんどまたはまったく発生していないことを意味します。

考慮すべき要因がもう 1 つあります。テスト中に、D4 クラスターでは 10584 件の取り込みエラーと 21 件のクエリ エラーが発生しました。DS4 クラスターのテストではエラーは発生しませんでした。

## パフォーマンスの結果 – スケールアップ

次の表は、中規模 (DS4) と大規模 (DS14) のクラスターでのテスト実行結果をまとめたものです。各 VM ではデータを保持するためにSSD を使用しました。各テストを 24 時間実行しました。

| クラスター |操作/クエリ |要求の数 |平均応答時間 (ミリ秒)|
|  -------------- |---------------------------- |-------------------- |--------------------------|
| 中規模 (DS4) |データの取り込み |503157 |511 |
| |Rating 別数 |6958 |187 |
| |時間別数 |6958 |411 |
| |国別ヒット数 |6958 |402 |
| |上位 15 組織 |6958 |307 |
| |固有組織数 |6956 |320 |
| |固有 IP 数 |6955 |841 |
| |合計ヒット数 |6958 |236 |
| 大規模 (DS14) |データの取り込み |502714 |511 |
| |Rating 別数 |7041 |201 |
| |時間別数 |7040 |298 |
| |国別ヒット数 |7039 |363 |
| |上位 15 組織 |7038 |244 |
| |固有組織数 |7037 |283 |
| |固有 IP 数 |7037 |681 |
| |合計ヒット数 |7038 |200 |

<!-- 
DISCUSSION POINTS:

Similar volume of data ingested – same disk configuration for each cluster, and ingestion rate is 
constrained by I/O performance?

Average response time for queries decreases with SKU.

Show CPU graphs

Show memory utilization – more data cached, fewer GCs, etc.

-->

<!--
To isolate the effects of the ingestion operations and show how query performance varies as nodes scale up, a second set of tests was performed using the same nodes. The ingestion part of the test was omitted, and the index on each node was pre-populated with 100 million documents. An amended set of queries was performed; the time element limiting documents to those added in the last 15 minutes was removed as the data was now static. The tests ran for 90 minutes; there is less need to allow the system to stabilize due to the fixed amount of data. The following table summarizes the results obtained on each cluster:

> [AZURE.NOTE] The amended version of the test that omits the data ingestion process and that uses a set of indexes containing 100 million documents is referred to as the *query-only* test in the remainder of this document. You should not compare the performance of the queries in this test with that of the tests that perform ingestion and query operations because the queries have been modified and the volume of documents involved is different.

 |Cluster        |Operation/Query              |Number of Requests   Average Response Time (ms)
 | --------------| ----------------------------| -------------------- ----------------------------
 | Small (DS3)   | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Medium (DS4)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Large (DS14)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      

### Performance Results – Scaling Out
-->


<!--
To show the system scales out with the number of nodes, tests were run using DS14 clusters comprising 1, 3, and 6 nodes. This time, only the query-only test was performed, using 100 million documents and running for 90 minutes:

> [AZURE.NOTE] For detailed information on how scaling out can affect the behavior of data ingestion operations, see the document [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md).

|  Cluster   |Operation/Query              |Number of Requests   |Average Response Time (ms)
|  --------- |---------------------------- |-------------------- |----------------------------
|  1 Node    |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  3 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  6 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
-->

<!--
### Performance Results – Number of Replicas

The ingestion and query tests were run against an index with a single replica. The tests were repeated on the 6-node DS4 and DS14 clusters using an index configured with two replicas. All tests ran for 24 hours. The table below shows the comparative results for one and two replicas:

|  Cluster|  Operation/Query            | response time 1 Replica (ms)| response time 2 Replicas (ms) | % Difference in Response Time
|  -------| ----------------------------| ----------------------------|----------- -------------------|--------------------- --------
|  DS4    |   Ingestion                 |   511                       |               655             |                          +28%
|         |   Count By Rating           |   187                       |               168             |                          -10%
|         |   Count Over Time           |   411                       |               309             |                          -25%
|         |   Hits By Country           |   402                       |               562             |                          +40%
|         |   Top 15 Organizations      |   307                       |               366             |                          +19%
|         |   Unique Count Organizations|   320                       |               378             |                          +18%
|         |   Unique IP Count           |   841                       |               987             |                          +17%
|         |   Total Hits Counts         |   236                       |               236             |                          +0%
|  DS14   |   Ingestion                 |   511                       |               618             |                          +21%
|         |   Count By Rating           |   201                       |               275             |                          +37%
|         |   Count Over Time           |   298                       |               466             |                          +56%
|         |   Hits By Country           |   363                       |               529             |                          +46%
|         |   Top 15 Organizations      |   244                       |               407             |                          +67%
|         |   Unique Count Organizations|   283                       |               403             |                          +42%
|         |   Unique IP Count           |   681                       |               823             |                          +21%
|         |   Total Hits Counts         |   200                       |               221             |                          +11%


NEED ## OF DOCUMENTS RETURNED TO JUSTIFY THIS DATA, OTHERWISE PERF FOR 2 REPLICAS LOOKS OFF!

PRESENT QUERY-ONLY TEST RESULTS TO SHOW BETTER RESULTS
-->

## パフォーマンスの結果 – doc 値

doc 値を有効にし、フィールドの並べ替えに使用されるデータを Elasticsearch がディスクに格納するようにして、取り込みとクエリのテストを実施しました。doc 値を無効にし、Elasticsearch がフィールドデータを動的に構築してメモリにキャッシュするようにして、テストを繰り返しました。すべてのテストを 24 時間実行しました。
 
 次の表は、D4、DS4、および DS14 VM を使用して構築された 6 ノード クラスターに対して実行したテストの応答時間を比較したものです。

| クラスター |操作/クエリ |doc 値有効 (ミリ秒) | doc 値無効 (ミリ秒) | 差異 % |
|  --------- |---------------------------| -----------------------|--------------------------  |--------------------|
| D4 |データの取り込み | 978 | 835 | -15% |
| |Rating 別数 | 103 | 132 | +28% |
| |時間別数 | 134 | 189 | +41% |
| |国別ヒット数 | 199 | 259 | +30% |
| |上位 15 組織 | 137 | 184 | +34% |
| |固有組織数 | 139 | 197 | +42% |
| |固有 IP 数 | 510 | 604 | +18% |
| |合計ヒット数 | 89 | 134 | +51% |
| DS4 |データの取り込み | 511 | 581 | +14% |
| |Rating 別数 | 187 | 190 | +2% |
| |時間別数 | 411 | 409 | -0.5% |
| |国別ヒット数 | 402 | 414 | +3% |
| |上位 15 組織 | 307 | 284 | -7% |
| |固有組織数 | 320 | 313 | -2% |
| |固有 IP 数 | 841 | 955 | +14% |
| |合計ヒット数 | 236 | 281 | +19% |
| DS14 |データの取り込み | 511 | 571 | +12% |
| |Rating 別数 | 201 | 232 | +15% |
| |時間別数 | 298 | 341 | +14% |
| |国別ヒット数 | 363 | 457 | +26% |
| |上位 15 組織 | 244 | 338 | +39% |
| |固有組織数 | 283 | 350 | +24% |
| |固有 IP 数 | 681 | 909 | +33% |
| |合計ヒット数 | 200 | 245 | +23% |


<!--
The next table compares the number of ingestion operations performed by the tests:

  Cluster   Ingestion Operations – Doc Values Enabled   ## Ingestion Operations – Doc Values Disabled   % Difference in ## Ingestion Operations
  --------- ---------------------------------------------- ----------------------------------------------- -----------------------------------------
  D4        264769                                         408690                                          +54%
  DS4       503137                                         578237                                          +15%
  DS14      502714                                         586472                                          +17%

The improved ingestion rates occur with doc values disabled as less data is being written to disk as documents are inserted. The improved performance is especially noticeable with the D4 VM using HDDs to store data. In this case, the response time for ingestion operations also decreased by 15% (see the first table in this section). This could be due to the reduced pressure on the HDDs which were likely running close to their IOPS limits in the test with doc values enabled; see the [Disk Type](#performance-results-disk-type) test for more information. The following graph compares the I/O performance of the D4 VMs with doc values enabled (values held on disk) and doc values disabled (values held in memory):

![](./media/guidance-elasticsearch/query-performance4.png)

In contrast, the ingestion figures for the VMs using SSDs show a small increase in the number of documents but also an increase in the response time of the ingestion operations. With one or two small exceptions, the query response times were also worse. The SSDs are less likely to be running close to their IOPS limits with doc values enabled, so changes in performance are more likely due to increased processing activity and the overhead of managing the JVM heap. This is evident by comparing the CPU utilization with doc values enabled and disabled. The next graph highlights this data for the DS4 cluster, where most of the CPU utilization moves from the 30%-40% band with doc values enabled, to the 40%-50% band with doc values disabled (the DS14 cluster showed a similar trend):

![](./media/guidance-elasticsearch/query-performance5.png)

To isolate the effects that doc values on query performance from data ingestion, a pair of query-only tests were performed for each cluster as follows:

In the first test, the Elasticsearch index was created with doc values enabled, the index was pre-populated with 100 million documents, and then modified set of queries used by the replica tests were performed repeatedly for 90 minutes while performance statistics were gathered.

In the second test, the Elasticsearch index was created with doc values disabled, populated, and then subjected to the same query load for the same period as the first test.
-->

<!--
## Appendix: The Query and Aggregation Performance Test

This appendix describes the performance test performed against the Elasticsearch cluster. The tests were run by using JMeter running on a separate set of VMs. Details the configuration of the test environment are described in [Creating a Performance Testing Environment for Elasticsearch on Azure][]. To perform your own testing, you can create your own JMeter test plan manually following the guidance in this appendix, or you can use the automated test scripts available separately. See [Running the Automated Elasticsearch Performance Tests][] for further information.

The data query workload performed the set of queries described below while performing a large-scale upload of documents at the same time (the data was uploaded by using a JUnit test, following the same approach for the data ingestion tests described in the document Maximizing Data Ingestion Performance with Elasticsearch on Azure.) The purpose of this workload was to simulate a production environment where new data is constantly being added while searches are performed. The queries were structured to retrieve only the most recent data from documents added in the last 15 minutes.

Each document was stored in a single index named *sample*, and had the type *ctip*. You can use the following HTTP request to create the index. The *number\_of\_replicas* and *number\_of\_shards* settings varied from the values shown below in many of the tests. Additionally, for the tests that used fielddata rather than doc values, each property was annotated with the attribute *"doc\_values" : false*.

**Important**. The index was dropped and recreated prior to each test run.

```http
PUT /sample
{  
    "settings" : {
        "number_of_replicas": 1,
        "refresh_interval": "30s",
        "number_of_shards": "5",
        "index.translog.durability": "async"    
    },
    "ctip": {
        "mappings": {
            "event": {
                "_all": {
                    "enabled": false
                },
                "_timestamp": {
                    "enabled": true,
                    "store": true,
                    "format": "date_time"
                },
                "properties": {
                    "Organization": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField1": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField2": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField3": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField4": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField5": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "DateTimeReceivedUtc": {
                        "type": "date",
                        "format": "dateOptionalTime"
                    },
                    "Host": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpMethod": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpReferrer": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpRequest": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpUserAgent": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpVersion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "OrganizationName": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIp": {
                        "type": "ip"
                    },
                    "SourceIpAreaCode": {
                        "type": "long"
                    },
                    "SourceIpAsnNr": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpBase10": {
                        "type": "long"
                    },
                    "SourceIpCity": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpCountryCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpLatitude": {
                        "type": "double"
                    },
                    "SourceIpLongitude": {
                        "type": "double"
                    },
                    "SourceIpMetroCode": {
                        "type": "long"
                    },
                    "SourceIpPostalCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpRegion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceLatLong": {
                        "type": "geo_point",
                        "doc_values": true,
                        "lat_lon": true,
                        "geohash": true
                    },
                    "SourcePort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourcedFrom": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "TargetIp": {
                        "type": "ip"
                    },
                    "TargetPort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "Rating": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "UseHumanReadableDateTimes": {
                        "type": "boolean"
                    }
                }
            }
        }
    }
}

```

The following queries were performed by the test:

- How many documents with each *Rating* value have been entered in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many documents have been added in each 5 minute interval during the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "sort": [],
  "aggs": {
    "2": {
      "date_histogram": {
        "field": "DateTimeReceivedUtc",
        "interval": "5m",
        "time_zone": "America/Los_Angeles",
        "min_doc_count": 1,
        "extended_bounds": {
          "min": "now-15m",
          "max": "now"
        }
      }
    }
  }
}

```

- How many documents of each *Rating* value have been added for each country in the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      },
      "aggs": {
        "3": {
          "terms": {
            "field": "SourceIpCountryCode",
            "size": 15,
            "order": {
              "_count": "desc"
            }
          }
        }
      }
    }
  }
}

```

- Which 15 organizations occur most frequently in documents added in the last 15 minutes?

``` http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Organization",
        "size": 15,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many different organizations occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "Organization"
      }
    }
  }
}

```

- How many documents have been added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "analyze_wildcard": true,
                  "query": "*"
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {}
}

```

- How many different *SourceIp* values occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "SourceIp"
      }
    }
  }
} 

```

[Running the Automated Elasticsearch Performance Tests]: guidance-elasticsearch-running-automated-performance-tests.md
[Creating a Performance Testing Environment for Elasticsearch on Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
-->

<!---HONumber=AcomDC_0224_2016-->