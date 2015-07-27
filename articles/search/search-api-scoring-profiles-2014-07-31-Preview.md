<properties pageTitle="スコアリング プロファイルを検索インデックス REST API バージョン 2014-07-31-Preview に追加する" description="スコアリング プロファイルを検索インデックス: バージョン 2014-07-31-Preview に追加する" services="search" documentationCenter="" authors="HeidiSteen" manager="mblythe" editor=""/>

<tags ms.service="search" ms.devlang="rest-api" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="na" ms.date="05/21/2015" ms.author="heidist" />
      
#検索インデックスにスコアリング プロファイルを追加する (Azure Search サービス REST API バージョン 2014-07-31-Preview)

スコアリング プロファイルに関するこの文書は以前の Azure Search サービス REST API、バージョン 2014-07-31-Preview を対象としています。このバージョンは[スコアリング プロファイル (MSDN)](https://msdn.microsoft.com/library/dn798928.aspx) で一般公開バージョンに取って代わられています。

**スコアリング プロファイルについて**

スコアリングとは、検索結果に返されるすべての項目の検索スコアを計算することです。スコアは、現在の検索操作のコンテキストにおける項目の関連性を示すインジケーターです。スコアが高いほど、項目の関連性が高くなります。検索結果内の項目は、項目ごとに計算された検索スコアに基づいて、順位の高い順に並べられます。Azure Search では、既定のスコアリングを使用して初期スコアを計算しますが、スコアリング プロファイルを介して計算をカスタマイズすることができます。スコアリング プロファイルでは、検索結果の項目の順位付けをより細かく制御できます。たとえば、収益の可能性に基づいて項目をブーストしたり、より新しい項目を昇格させたり、場合によっては在庫期間が長すぎる項目をブーストしたりできます。

スコアリング プロファイルは、インデックス定義の一部であり、フィールド、関数、およびパラメーターで構成されます。

スコアリング プロファイルとはどのようなものかを説明するために、次の例に 'geo' という名前の簡単なプロファイルを示します。このプロファイルは、`hotelName` フィールド内の検索語句を含む項目をブーストします。また、`distance` 関数を使用して、現在の場所から 10 キロメートル以内にある項目を優先します。他のユーザーが語句 'inn' の検索を実行し、'inn' がたまたまホテル名の一部であった場合、'inn' を含むホテルが記載されているドキュメントが検索結果で上位に表示されます。

    "scoringProfiles": [
      {
        "name": geo,
        "text": {
          "weights": { "hotelName": 5 }
        },
        "functions": [ 
          {
            "type": "distance",
            "boost": 5,
            "fieldName": "location",
            "interpolation": "logarithmic",
            "distance": {
              "referencePointParameter": "currentLocation",
              "boostingDistance": 10
            }
          }
        ]
      }
    ]

このスコアリング プロファイルを使用するには、クエリを作成して、クエリ文字列にプロファイルを指定します。次のクエリで、要求内のクエリ パラメーター `scoringProfile=geo` に注目してください。

    GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation:-122.123,44.77233&api-version=2014-07-31-Preview

このクエリは、語句 'inn' を検索し、現在の場所を渡します。このクエリには、`scoringParameter` などの他のパラメーターも含まれていることがわかります。クエリ パラメーターについては、「[ドキュメントの検索 (Azure Search API)](https://msdn.microsoft.com/library/azure/dn798927.aspx)」を参照してください。

スコアリング プロファイルの詳細な例を確認するには、「[例](#bkmk_ex)」をクリックします。

## 既定のスコアリングとは

スコアリングでは、順位に従って並べられる結果セット内の各項目の検索スコアを計算します。検索結果セット内のすべての項目にそれぞれ検索スコアが割り当てられ、最上位から最下位まで順位が付けられます。アプリケーションには、より高いスコアを持つ項目が返されます。既定では、上位 50 個が返されますが、`$top` パラメーターを使用することで、返される項目数を減らしたり増やしたりできます (1 回の応答で 1000 個まで)。

既定では、検索スコアは、データとクエリの統計プロパティに基づいて計算されます。Azure Search はクエリ文字列内の検索語句 (`searchMode` に応じて一部またはすべて) を含むドキュメントを検索し、検索語句のインスタンスを多く含むドキュメントを優先します。データ コーパス全体での語句の出現頻度は低いがドキュメント内でよく使用されている場合、検索スコアはより高くなります。関連性を計算するこのアプローチの基礎となる手法は、TF-IDF (単語の出現頻度 - 逆文書頻度) と呼ばれています。カスタムの並べ替えが行われないと仮定した場合、結果は検索スコアによって順位付けされてから、呼び出し元のアプリケーションに返されます。`$top` が指定されていない場合、最も高い検索スコアを持つ 50 個の項目が返されます。

検索スコアの値は、結果セット全体で繰り返すことができます。たとえば、スコアが 1.2 である項目が 10 個、スコアが 1.0 である項目が 20 個、スコアが 0.5 である項目が 20 個あるとします。同じ検索スコアを持つ項目が複数ヒットした場合、同じスコアを持つ項目の順序付けは定義されていないので安定しません。もう一度クエリを実行すると、項目の位置が変わる可能性があります。同一スコアの項目が 2 つ存在する場合、最初に表示される項目を特定することはできません。

## カスタム スコアリングを使用する場合

既定の順位付けの動作ではビジネス目標を達成するに至らない場合、1 つ以上のスコアリング プロファイルを作成する必要があります。たとえば、検索の関連性で、新しく追加された項目を優先するとします。同様に、利益率を含んでいるフィールド、または他に収益の可能性を示す何らかのフィールドがあるとします。ビジネスに利点をもたらすブースティング ヒットは、スコアリング プロファイルの使用を決定する上で重要な要因になる場合があります。

関連性に基づく順序付けも、スコアリング プロファイルを通して実装されます。価格、日付、評価、または関連性での並べ替えが可能な、以前に使用した検索結果ページを検討してしてください。Azure Search では、スコアリング プロファイルによって「関連性」オプションが開始されます。関連性の定義は、ビジネス目標および提供する検索機能の種類に基づいて、ユーザーによって制御されます。

## 例

前述のように、カスタマイズされたスコアリングは、インデックス スキーマに定義されたスコアリング プロファイルを介して実装されます。

次の例では、2 つのスコアリング プロファイル (`boostGenre`、`newAndHighlyRated`) を使用するインデックス スキーマを示します。いずれかのプロファイルをクエリ パラメーターとして指定したクエリを、このインデックスに対して実行すると、該当するプロファイルを使用して結果セットのスコアリングが行われます。

    {
      "name": "musicstoreindex",
	  "fields": [
	    { "name": "key", "type": "Edm.String", "key": true },
	    { "name": "albumTitle", "type": "Edm.String", "suggestions": true },
	    { "name": "albumUrl", "type": "Edm.String", "filterable": false },
	    { "name": "genre", "type": "Edm.String" },
	    { "name": "genreDescription", "type": "Edm.String", "filterable": false },
	    { "name": "artistName", "type": "Edm.String", "suggestions": true },
	    { "name": "orderableOnline", "type": "Edm.Boolean" },
	    { "name": "rating", "type": "Edm.Int32" },
	    { "name": "tags", "type": "Collection(Edm.String)" },
	    { "name": "price", "type": "Edm.Double", "filterable": false },
	    { "name": "margin", "type": "Edm.Int32", "retrievable": false },
	    { "name": "inventory", "type": "Edm.Int32" },
	    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
	  ],
      "scoringProfiles": [
        {
	      "name": "boostGenre",
          "text": {
            "weights": {
              "albumTitle": 1,
              "genre": 5 ,
              "artistName": 2 
            }
          }
	    },
        {
	      "name": "newAndHighlyRated",
          "functions": [
            {
              "type": "freshness",
              "fieldName": "lastUpdated",
              "boost": 10,
              "interpolation": "quadratic",
              "freshness": {
                "boostingDuration": "P365D"
              }
            },
            {
              "type": "magnitude",
              "fieldName": "rating",
              "boost": 10,
              "interpolation": "linear",
              "magnitude": {
                "boostingRangeStart": 1,
                "boostingRangeEnd": 5,
                "constantBoostBeyondRange": false
              }
            }
          ]
        }
      ]
    }


##ワークフロー

カスタムのスコアリング動作を実装するには、インデックスを定義するスキーマにスコアリング プロファイルを追加します。インデックスには複数のスコアリング プロファイルを含めることができますが、クエリには一度に 1 つのプロファイルしか指定できません。

このトピックで提供される[テンプレート](#bkmk_template)を使用して開始します。

名前を指定します。スコアリング プロファイルは省略できますが、追加する場合は、名前が必要ですフィールドの名前付け規則に従ってください (文字で始め、特殊文字や予約語の使用は避ける)。詳細については、「[名前付け規則](https://msdn.microsoft.com/library/dn857353.aspx)」を参照してください。

スコアリング プロファイルの本文は、重み付けされたフィールドと関数によって構成されます。

<font> <table style="font-size:12"> <thead> <tr><td>要素</td><td>説明</td></tr></thead> < tbody <tr> <td><b>重み</b></td> <td>相対的な重みをフィールドに割り当てる名前と値のペアを指定します。[例](#bkmk_ex)では、albumTitle、genre、および artistName のフィールドはそれぞれ 1、5、および null でブーストされます。なぜ genre は他のものよりも大幅に高くブーストされるのでしょうか? ある程度同じようなデータに対して検索を実行する場合 (`musicstoreindex` での 'genre' の場合のように)、相対的な重みをより分散することが必要になる可能性があります。たとえば、`musicstoreindex` では、「ロック」はジャンルとして表示されると共に、言葉で表現されるジャンルの説明の中にも表示されます。ジャンルがジャンルの説明を上回るようにする場合は、genre フィールドの相対的な重みをより高くする必要があります。</td> </tr> <tr> <td><b>関数</b></td><td>特定のコンテキストに対して追加の計算が必要な場合に使用されます。有効な値として、`freshness`、`magnitude`、または `distance` があります。各関数には固有のパラメーターがあります。<br> - 項目の新しさまたは古さに基づいてブーストする場合は、`freshness` を使用してください。この関数は、日時フィールドでのみ使用できます (Edm.DataTimeOffset)。`boostingDuration` 属性は freshness 関数でのみ使用されることに注意してください。<br> - 数値の高さまたは低さに基づいてブーストする場合は、`magnitude` を使用してください。この関数を呼び出すシナリオとしては、利益率、最高価格、最低価格、またはダウンロード回数に基づくブーストがあります。この関数は、倍精度浮動小数点フィールドと整数フィールドでのみ使用できます。<br> - 近接性または地理的場所に基づいてブーストする場合は、`distance` を使用してください。この関数は、`geo.distance` フィールドでのみ使用できます。<br> <b>関数を使用するための規則</b> <br> 関数型 (freshness、magnitude、distance) は小文字にする必要があります。<br>関数に null または空の値を含めることはできません。具体的には、フィールド名を含めた場合、それを何かに設定する必要があります。<br>関数はフィルターの適用が可能なフィールドにのみ適用できます。フィルターの適用が可能なフィールドの詳細については、「[インデックスの作成 (Azure Search API)]()」を参照してください。<br>関数は、インデックスのフィールド コレクションで定義されているフィールドにのみ適用できます。<td> </tr> </tbody> </table> </font>

インデックスが定義されたら、インデックス スキーマをアップロードし、ドキュメントをアップロードして、インデックスを構築します。これらの操作の手順については、「[インデックスの作成 (Azure Search API)](https://msdn.microsoft.com/library/azure/dn798941.aspx)」と「[ドキュメントの追加または更新 (Azure Search API)](https://msdn.microsoft.com/library/azure/dn798930.aspx)」を参照してください。インデックスが構築されると、検索データと連携する機能的なスコアリング プロファイルが使用できるようになります。

##テンプレート
このセクションでは、スコアリング プロファイルの構文とテンプレートを示します。属性の説明については、次のセクションの「[インデックスの属性のリファレンス](#bkmk_indexref)」を参照してください。

    ...
    "scoringProfiles": [
      { 
        "name": "name of scoring profile", 
        "text": (optional, only applies to searchable fields) { 
          "weights": { 
            "searchable_field_name": relative_weight_value (positive #'s), 
            ... 
          } 
        }, 
        "functions": (optional) [
          { 
            "type": "magnitude | freshness | distance", 
            "boost": # (positive number used as multiplier for raw score != 1), 
            "fieldName": "...", 
            "interpolation": "constant | linear (default) | quadratic | logarithmic", 
            
            "magnitude": { 
              "boostingRangeStart": #, 
              "boostingRangeEnd": #, 
              "constantBoostBeyondRange": true | false (default) 
            }

            // (- or -) 
    
            "freshness": { 
              "boostingDuration": "..." (value representing timespan over which boosting occurs) 
            } 

            // (- or -) 

            "distance": { 
              "referencePointParameter": "...", (parameter to be passed in queries to use as reference location) 
              "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends) 
            } 
          } 
        ], 
        "functionAggregation": (optional, applies only when functions are specified) 
            "sum (default) | average | minimum | maximum | firstMatching" 
      } 
    ], 
    "defaultScoringProfile": (optional) "...",
    ...

##インデックスの属性のリファレンス

**注**： スコアリング関数はフィルターの適用が可能なフィールドにのみ適用できます。

<table style="font-size:12">
<thead>
<tr>
<td>属性</td>
<td>説明</td>
</tr>
<tr>
<td>名前</td>	<td>必須。これは、スコアリング プロファイルの名前です。これは、フィールドの場合と同じ名前付け規則に従います。先頭は英文字にする必要があります。ドット、コロン、または @ 記号を含めることはできません。さらに、'azureSearch' (大文字小文字の区別あり) という句で開始することはできません。</td>
</tr><tr>
<td>テキスト</td>	<td>重み プロパティが含まれます。</td>
</tr><tr>
<td>Weights</td>	<td>省略可能。フィールドの名前と相対的な重みを指定する名前と値のペア。相対的な重みは正の整数にする必要があります。最大値は int32.MaxValue です。対応する重みなしでフィールド名を指定することができます。フィールド間の相対的な重要度を示すには、重みを使用します。</td>
<tr>
<td>関数</td>	<td>省略可能。スコアリング関数はフィルターの適用が可能なフィールドにのみ適用できることに注意してください。</td>
</tr><tr>
<td>型</td>	<td>スコアリング関数の場合は必須です。使用する関数の型を示します。有効な値には、magnitude、freshness、および distance があります。各スコアリング プロファイルには複数の関数を含めることができます。関数名は小文字にする必要があります。</td>
</tr><tr>
<td>Boost</td>	<td>スコアリング関数の場合は必須です。生のスコアの乗数として使用される正の数値。1 にすることはできません。</td>
</tr><tr>
<td>Fieldname</td>	<td>スコアリング関数の場合は必須です。スコアリング関数は、インデックスのフィールド コレクションに属し、フィルターの適用が可能なフィールドにのみ適用できます。さらに、各関数型には追加の制限 (freshness は日時フィールドで使用され、magnitude は整数または倍精度浮動小数点フィールドで使用され、distance は場所フィールドで使用される) が導入されています。関数の定義ごとに指定できるフィールドは 1 つだけです。たとえば、同じプロファイルで magnitude を 2 回使用するには、magnitude の定義を 2 つ含める必要があります (フィールドごとに 1 つ)。</td>
</tr><tr>
<td>Interpolation</td>	<td>スコアリング関数の場合は必須です。スコア ブースティングが範囲の始点から範囲の終点に向かって高くなる場合の傾斜を定義します。有効な値には、線形 (既定値)、定数、二次方程式、および対数があります。詳細については、「補間の設定」(#bkmk_interpolation) を参照してください。</td>
</tr><tr>
<td>magnitude</td>	<td>数値フィールドの値の範囲に基づいて順位付けを変更するには、magnitude スコアリング関数を使用します。最も一般的な使用例には、次のようなものがあります。 
<br>
- 星評価: [星評価] フィールド内の値に基づいて、スコアリングを変更します。2 つの項目が該当する場合は、評価の高い方の項目が最初に表示されます。
<br>
- 利益: 2 つのドキュメントが該当する場合、小売業者は利益が高い方のドキュメントを最初にブーストできます。
<br>
- クリック回数: 製品やページに対するクリック操作を追跡するアプリケーションの場合は、magnitude を使用することで、最大トラフィックを示す傾向にある項目をブーストすることが可能です。
<br>
- ダウンロード回数: ダウンロードを追跡するアプリケーションの場合は、magnitude 関数を使用することで、ダウンロード回数が最も多い項目をブーストすることができます。
<tr>
<td>magnitude | boostingRangeStart</td>	<td>magnitude をスコアリングする範囲の開始値を設定します。値は整数または倍精度浮動小数点にする必要があります。1 ～ 4 の星評価を対象とする場合、これは 1 になります。50% を超える利益を対象とする場合、これは 50 になります。</td>
</tr><tr>
<td>magnitude | boostingRangeEnd</td>	<td>magnitude をスコアリングする範囲の終了値を設定します。値は整数または倍精度浮動小数点にする必要があります。1 ～ 4 の星評価を対象とする場合、これは 4 になります。</td>
</tr><tr> 
<td>magnitude | constantBoostBeyondRange</td>	<td>有効な値は、true または false (既定値) です。true に設定すると、対象フィールドの値が範囲の上限を超えているドキュメントに、フル ブーストが継続的に適用されます。false に設定すると、対象フィールドの値が範囲外にあるドキュメントに対して、この関数のブーストは適用されません。</td>
</tr><tr>
<td>freshness</td>	<td>DateTimeOffset フィールドの値に基づいて項目の順位付けのスコアを変更するには、freshness スコアリング関数を使用します。たとえば、最近の日付を持つ項目に、より古い日付を持つ項目よりも高い順位を付けることができます。現在のサービス リリースでは、範囲の一端が現在の時刻に固定されます。最大および最小の範囲からのブースティングの変化率は、スコアリング プロファイルに適用した補間によって決定されます (下図参照)。適用されるブースティング係数を反転するには、ブースト係数として &lt; 1 を選択します。</td>
</tr><tr>
<td>freshness | boostingDuration</td>	<td>特定のドキュメントに対して有効期限を設定します。この期限を過ぎるとブースティングは停止します。構文と例については、次のセクションの「boostingDuration の設定」(#bkmk_boostdur) を参照してください。</td>
</tr><tr>
<td>distance</td>	<td>地理的な参照場所との間の距離に基づいてドキュメントのスコアに影響を与えるには、distance スコアリング関数を使用します。参照場所は、クエリの一部としてパラメーター (’scoringParameterquery’ 文字列オプションを使用) 内に lon,lat 引数で指定します。</td>
</tr><tr>
<td>distance | referencePointParameter</td>	<td>参照場所として使用するためにクエリに渡されるパラメーター。scoringParameter はクエリ パラメーターです。クエリ パラメーターの説明については、[ドキュメントの検索 (Azure Search API)] (https://msdn.microsoft.com/library/azure/dn798927.aspx) を参照してください。</td>
</tr><tr>
<td>distance | boostingDistance</td>	<td>ブースティング範囲が終了する、参照場所からの距離をキロメートルで示す数値。</td>
</tr><tr>
<td>functionAggregation</td>	<td>省略可能。関数を指定した場合にのみ適用されます。有効な値には、sum (default)、average、minimum、maximum、および firstMatching があります。検索スコアは、複数の変数 (複数の関数など) から計算される単一の値です。この属性は、すべての関数のブーストを、基本ドキュメント スコアに適用される 1 つの集計ブーストに集約する方法を示します。基本スコアは、ドキュメントと検索クエリから算出される tf-idf 値に基づいています。</td>
</tr><tr>
<td>defaultScoringProfile</td>	<td>検索要求の実行時に、スコアリング プロファイルが指定されていない場合は、既定のスコアリングが使用されます (tf-idf のみ)。ここでは既定のスコアリング プロファイル名を設定できます。Azure Search は検索要求に特定のプロファイルが指定されていない場合、そのプロファイルを使用します。</td>
</tr>
</tbody>
</table>

##補間の設定

補間を使用することにより、スコア ブースティングが範囲の始点から範囲の終点に向かって高くなる場合の傾斜を定義できます。次の補間を使用できます。

- `Linear` 最大および最小の範囲内にある項目の場合、項目に適用されるブーストは一定の減少量で行われます。線形はスコアリング プロファイルの既定の補間です。

- `Constant` 開始と終了の範囲内にある項目については、一定のブーストが順位の結果に適用されます。

- `Quadratic` ブーストが一定の割合で低下する線形補間と比較して、二次方程式の場合は、最初は遅いペースで低下しますが、終了範囲に近づくにつれて低下するペースが速くなります。

- `Logarithmic` ブーストが一定の割合で低下する線形補間と比較して、対数の場合は、最初は速いペースで低下しますが、終了範囲に近づくにつれて低下するペースが遅くなります。
 
<a name="Figure1"></a> ![](https://findable.blob.core.windows.net/docs/scoring_interpolations.png)

<a name="bkmk_boostdu"></a>
##boostingDuration の設定

`boostingDuration` は freshness 関数の属性です。これを使用して特定のドキュメントに対して有効期限を設定します。この期限が過ぎるとブースティングは停止します。たとえば、10 日のプロモーション期間中に製品ラインまたはブランドをブーストするには、該当するドキュメントに対して 10 日の期間を "P10D" で指定します。

`boostingDuration` は、XSD "dayTimeDuration" 値 (ISO 8601 期間値の制限されたサブセット) としてフォーマットする必要があります。このパターンは "P(nD)(T(nH)(nM](nS))" です。

次の表に、いくつかの例を示します。

<table style="font-size:12">
<thead>
<tr>
<td><b>期間</b></td> <td><b>boostingDuration</b></td>
</tr>
</thead>
<tbody>
<tr>
<td>1 日</td>	<td>"P1D"</td>
</tr><tr>
<td>2 日と 12 時間</td>	<td>"P2DT12H"</td>
</tr><tr>
<td>約 15 分</td>	<td>"PT15M"</td>
</tr><tr>
<td>30 日 と 5 時間 10 分 6.334 秒</td>	<td>"P30DT5H10M6.334S"</td>
</tr>
</tbody>
</table>

**関連項目**

Azure Search サービス REST API インデックスの作成 (Azure Search API) ________________________________________

 

<!---HONumber=July15_HO3-->