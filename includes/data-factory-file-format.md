## <a name="specifying-formats"></a>形式の指定
### <a name="specifying-textformat"></a>TextFormat の指定
書式が **TextFormat** に設定されている場合、次の**任意の**プロパティを **Format** セクションで指定できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| columnDelimiter |ファイル内の列を区切るために使用する文字。 |使用できるのは 1 文字だけです。 既定値はコンマ (,) です。 |いいえ |
| rowDelimiter |ファイル内の行を区切るために使用する文字。 |使用できるのは 1 文字だけです。 読み取り時の既定値は [“\r\n”、“\r”、” \n”] のいずれかになり、書き込み時の既定値は “\r\n” になります。 |なし |
| escapeChar |入力ファイルのコンテンツの列区切り記号をエスケープするために使用する特殊文字。 <br/><br/>1 つのテーブルに escapeChar と quoteChar の両方を指定することはできません。 |使用できるのは 1 文字だけです。 既定値はありません。 <br/><br/>たとえば、列の区切り文字としてコンマ (,) を使用しているときにテキストにもコンマ文字が必要な場合 (例: "Hello, world")、エスケープ文字として "$" を定義し、ソースで文字列 "Hello$, world" を使用できます。 |いいえ |
| quoteChar |文字列値の引用に使用する文字。 引用符文字内の列区切り文字と行区切り文字は文字列値の一部として処理されます。 このプロパティは、入力と出力の両方のデータセットに適用されます。<br/><br/>1 つのテーブルに escapeChar と quoteChar の両方を指定することはできません。 |使用できるのは 1 文字だけです。 既定値はありません。 <br/><br/>たとえば、列の区切り文字としてコンマ (,) を使用しているときにテキストにもコンマ文字が必要な場合 (例: Hello, world)、引用符文字として " (二重引用符) を定義し、ソースで文字列 "Hello, world" を使用できます。 |いいえ |
| nullValue |Null 値を表すために使用する 1 つ以上の文字。 |1 つ以上の文字。 既定値は、読み取り時は “\N” および “NULL”、書き込み時は “\N” です。 |いいえ |
| encodingName |エンコード名の指定。 |有効なエンコード名。 詳細については、[Encoding.EncodingName プロパティ](https://msdn.microsoft.com/library/system.text.encoding.aspx)に関するページを参照してください。 例: windows-1250 または shift_jis。 既定値は UTF-8 です。 |いいえ |
| firstRowAsHeader |先頭行をヘッダーと見なすかどうかを指定します。 入力データセットでは、Data Factory は先頭行をヘッダーとして読み取ります。 出力データセットでは、Data Factory は先頭行をヘッダーとして書き込みます。 <br/><br/>サンプル シナリオについては、「[**firstRowAsHeader** と **skipLineCount** を使用するシナリオ](#scenarios-for-using-firstrowasheader-and-skiplinecount)」を参照してください。 |True<br/>False (既定値) |いいえ |
| skipLineCount |入力ファイルからのデータ読み取り時にスキップする行数を示します。 skipLineCount と firstRowAsHeader の両方を指定した場合は、まず行がスキップされ、次に入力ファイルからヘッダー情報が読み取られます。 <br/><br/>サンプル シナリオについては、「 [firstRowAsHeader と skipLineCount を使用するシナリオ](#scenarios-for-using-firstrowasheader-and-skiplinecount) 」を参照してください。 |Integer |いいえ |
| treatEmptyAsNull |入力ファイルのデータの読み取り時に null または空の文字列 を Null 値として扱うかどうかを指定します。 |True (既定値)<br/>False |いいえ |

#### <a name="textformat-example"></a>TextFormat の例
次の例は、TextFormat の format プロパティの一部を示します。

    "typeProperties":
    {
        "folderPath": "mycontainer/myfolder",
        "fileName": "myblobname"
        "format":
        {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";",
            "quoteChar": "\"",
            "NullValue": "NaN"
            "firstRowAsHeader": true,
            "skipLineCount": 0,
            "treatEmptyAsNull": true
        }
    },

quoteChar ではなく escapeChar を使用するには、quoteChar の行を次の escapeChar で置き換えます。

    "escapeChar": "$",



### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>firstRowAsHeader と skipLineCount を使用するシナリオ
* ファイル以外のソースからテキスト ファイルにコピーし、スキーマ メタデータ (例: SQL スキーマ) を含むヘッダー行を追加する:  このシナリオでは、出力データセットの **firstRowAsHeader** を True として指定します。 
* ヘッダー行を含むテキスト ファイルから、ファイル以外のシンクにコピーし、その行を削除する:  入力データセットの **firstRowAsHeader** を True として指定します。
* テキスト ファイルからコピーして、データやヘッダー情報を含まない先頭の数行をスキップする:  **skipLineCount** を指定してスキップする行数を示します。 ファイルの残りの部分にヘッダー行が含まれている場合は、**firstRowAsHeader** も指定できます。 **skipLineCount** と **firstRowAsHeader** の両方を指定した場合は、まず行がスキップされ、次に入力ファイルからヘッダー情報が読み取られます。

### <a name="specifying-avroformat"></a>AvroFormat の指定
形式が AvroFormat に設定されている場合、typeProperties セクション内の Format セクションにプロパティを指定する必要はありません。 例:

    "format":
    {
        "type": "AvroFormat",
    }

Hive テーブルで Avro 形式を使用するには、 [Apache Hive のチュートリアルに関するページ](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)を参照してください。

### <a name="specifying-jsonformat"></a>JsonFormat の指定
形式を **JsonFormat** に設定した場合、**Format** セクションで次の**オプションの**プロパティを指定できます。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| filePattern |各 JSON ファイルに格納されたデータのパターンを示します。 使用できる値は、**setOfObjects** と **arrayOfObjects** です。 **既定**値は **setOfObjects** です。 これらのパターンの詳細については、以下のセクションを参照してください。 |いいえ |
| encodingName |エンコード名の指定。 有効なエンコード名の一覧については、 [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) プロパティを参照してください。 例: windows-1250 または shift_jis。 **既定**値は **UTF-8** です。 |いいえ |
| nestingSeparator |入れ子レベルの分割に使用される文字。 既定値は "." (ドット) です。 |いいえ |

#### <a name="setofobjects-file-pattern"></a>setOfObjects ファイル パターン
各ファイルには、単一のオブジェクト、または改行区切り/連結された複数のオブジェクトが含まれます。 出力データセットでこのオプションを選択すると、コピー アクティビティによって、各オブジェクトが行ごとに配置された (改行区切り) 1 つの JSON ファイルが生成されます。

**単一オブジェクト** 

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }

**改行区切りの JSON** 

    {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
    {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
    {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**連結 JSON**

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }
    {
        "time": "2015-04-29T07:13:21.0220000Z",
        "callingimsi": "466922202613463",
        "callingnum1": "123436380",
        "callingnum2": "789037573",
        "switch1": "US",
        "switch2": "UK"
    }
    {
        "time": "2015-04-29T07:13:21.4370000Z",
        "callingimsi": "466923101048691",
        "callingnum1": "678901578",
        "callingnum2": "345626404",
        "switch1": "Germany",
        "switch2": "UK"
    }


#### <a name="arrayofobjects-file-pattern."></a>arrayOfObjects ファイル パターン
各ファイルにはオブジェクトの配列が含まれます。 

    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "789037573",
            "callingnum2": "789044691",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789044691",
            "switch1": "US",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:24.2120000Z",
            "callingimsi": "466922201102759",
            "callingnum1": "345698602",
            "callingnum2": "789097900",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:25.6320000Z",
            "callingimsi": "466923300236137",
            "callingnum1": "567850552",
            "callingnum2": "789086133",
            "switch1": "China",
            "switch2": "Germany"
        }
    ]

### <a name="jsonformat-example"></a>JsonFormat の例
次の内容が含まれる JSON ファイルをお持ちで、  

    {
        "Id": 1,
        "Name": {
            "First": "John",
            "Last": "Doe"
        },
        "Tags": ["Data Factory”, "Azure"]
    }

上記の内容を Azure SQL テーブルに次の形式でコピーする場合: 

| ID | Name.First | Name.Middle | Name.Last | タグ |
| --- | --- | --- | --- | --- |
| 1 |John |null |Doe |["Data Factory”, "Azure"] |

JsonFormat 型の入力データセットは次のように定義されます (関連する部分のみでの部分的な定義)。

    "properties": {
        "structure": [
            {"name": "Id", "type": "Int"},
            {"name": "Name.First", "type": "String"},
            {"name": "Name.Middle", "type": "String"},
            {"name": "Name.Last", "type": "String"},
            {"name": "Tags", "type": "string"}
        ],
        "typeProperties":
        {
            "folderPath": "mycontainer/myfolder",
            "format":
            {
                "type": "JsonFormat",
                "filePattern": "setOfObjects",
                "encodingName": "UTF-8",
                "nestingSeparator": "."
            }
        }
    }

構造が定義されていない場合、コピー アクティビティでは既定で構造をフラット化し、すべてのものをコピーします。 

#### <a name="supported-json-structure"></a>サポートされている JSON 構造
以下の点に注意してください。 

* 名前/値ペアのコレクションを持つ各オブジェクトは、表形式のデータの 1 つの行にマップされます。 オブジェクトは入れ子にすることができ、既定では入れ子の区切り記号 (.) を使用してデータセット内の構造をフラット化する方法を定義できます。 例については、前の「[JsonFormat の例](#jsonformat-example)」を参照してください。  
* Data Factory データセット内で構造が定義されていない場合、コピー アクティビティでは最初のオブジェクトからスキーマを検出し、オブジェクト全体をフラット化します。 
* JSON 入力に配列がある場合、コピー アクティビティでは配列値全体を文字列に変換します。 この変換は、 [列マッピングまたはフィルター処理](#column-mapping-with-translator-rules)を使用することでスキップできます。
* 同じレベルに重複する名前がある場合、コピー アクティビティでは最後の 1 つが選択されます。
* プロパティ名では大文字と小文字が区別されます。 名前は同じでも大文字小文字が異なる 2 つのプロパティは、2 つの個別のプロパティとして扱われます。 

### <a name="specifying-orcformat"></a>OrcFormat の指定
形式が OrcFormat に設定されている場合、typeProperties セクション内の Format セクションにプロパティを指定する必要はありません。 例:

    "format":
    {
        "type": "OrcFormat"
    }

> [!IMPORTANT]
> ORC ファイルを、オンプレミス データ ストアとクラウド データ ストア間で **そのまま** コピーしない場合は、JRE 8 (Java Runtime Environment) をゲートウェイ マシンにインストールする必要があります。 64 ビットのゲートウェイでは 64 ビットの JRE が必要になり、32 ビットのゲートウェイでは 32 ビットの JRE が必要になります。 どちらのバージョンも [こちらのページ](http://go.microsoft.com/fwlink/?LinkId=808605)で入手できます。 適切なバージョンを選択してください。
> 
> 

以下の点に注意してください。

* 複雑なデータ型はサポートされていません (STRUCT、MAP、LIST、UNION)。
* ORC ファイルには 3 つの [圧縮関連のオプション](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/)(NONE、ZLIB、SNAPPY) があります。 Data Factory では、これらすべての圧縮形式の ORC ファイルからデータを読み取ることができます。 データの読み取りには、メタデータ内の圧縮コーデックが使用されます。 ただし、Data Factory で ORC ファイルに書き込むときは、ORC の既定の動作である ZLIB が選択されます。 現時点でこの動作をオーバーライドするオプションはありません。 

### <a name="specifying-parquetformat"></a>ParquetFormat の指定
形式が ParquetFormat に設定されている場合は、typeProperties セクション内の Format セクションにプロパティを指定する必要はありません。 例:

    "format":
    {
        "type": "ParquetFormat"
    }

> [!IMPORTANT]
> Parquet ファイルを、オンプレミス データ ストアとクラウド データ ストア間で **そのまま** コピーしない場合は、JRE 8 (Java Runtime Environment) をゲートウェイ マシンにインストールする必要があります。 64 ビットのゲートウェイでは 64 ビットの JRE が必要になり、32 ビットのゲートウェイでは 32 ビットの JRE が必要になります。 どちらのバージョンも [こちらのページ](http://go.microsoft.com/fwlink/?LinkId=808605)で入手できます。 適切なバージョンを選択してください。
> 
> 

以下の点に注意してください。

* 複雑なデータ型はサポートされていません (MAP、LIST)。
* Parquet ファイルには圧縮関連のオプション (NONE、SNAPPY、GZIP、LZO) があります。 Data Factory では、これらすべての圧縮形式の ORC ファイルからデータを読み取ることができます。 データの読み取りには、メタデータ内の圧縮コーデックが使用されます。 ただし、Data Factory で Parquet ファイルに書き込むときは、Parquet 形式の既定の動作である SNAPPY が選択されます。 現時点でこの動作をオーバーライドするオプションはありません。 

<!--HONumber=Oct16_HO2-->


