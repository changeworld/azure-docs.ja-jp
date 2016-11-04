### JsonFormat の指定
書式が **JsonFormat** に設定されている場合、次の**任意の**プロパティを **Format** セクションで指定できます。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| filePattern |各 JSON ファイルに格納されたデータのパターンを示します。使用できる値は、**setOfObjects** と **arrayOfObjects** です。**既定**値は **setOfObjects** です。これらのパターンの詳細については、以下のセクションを参照してください。 |いいえ |
| encodingName |エンコード名の指定。有効なエンコード名の一覧については、[Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) プロパティを参照してください。例: windows-1250 または shift\_jis。**既定**値は **UTF-8** です。 |いいえ |
| nestingSeparator |入れ子レベルの分割に使用される文字。**既定**値は **. (ドット)** です。 |いいえ |

#### setOfObjects ファイル パターン
各ファイルには、単一のオブジェクト、または改行区切り/連結された複数のオブジェクトが含まれます。出力データセットで、このオプションを選択すると、コピーによって、各オブジェクトが行ごとに配置された (改行区切り) 1 つの JSON ファイルが生成されます。

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


#### arrayOfObjects ファイル パターン
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

### JsonFormat の例
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

#### サポートされている JSON 構造
以下の点に注意してください。

* 名前/値ペアのコレクションを持つ各オブジェクトは、表形式のデータの 1 つの行にマップされます。オブジェクトは入れ子にすることができ、既定では入れ子の区切り記号 (.) を使用してデータセット内の構造をフラット化する方法を定義できます。例については、前の「[JsonFormat の例](#jsonformat-example)」セクションを参照してください。  
* Data Factory データセット内で構造が定義されていない場合、コピー アクティビティでは最初のオブジェクトからスキーマを検出し、オブジェクト全体をフラット化します。 
* JSON 入力に配列がある場合、コピー アクティビティでは配列値全体を文字列に変換します。この変換は、[列マッピングまたはフィルター処理](#column-mapping-with-translator-rules)を使用することでスキップすることができます。
* 同じレベルに重複する名前がある場合、コピー アクティビティでは最後の 1 つを選択します。
* プロパティ名は大文字と小文字が区別されます。名前は同じでも大文字小文字が異なる 2 つのプロパティは、2 つの個別のプロパティとして扱われます。 

<!---HONumber=AcomDC_0316_2016-->