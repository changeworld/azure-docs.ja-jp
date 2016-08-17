<properties 
	pageTitle="ロジック アプリの定義の作成 | Microsoft Azure" 
	description="ロジック アプリの JSON 定義を記述する方法について説明します。" 
	authors="jeffhollan" 
	manager="erikre" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jehollan"/>
	
# ロジック アプリの定義の作成
このトピックでは、単純な宣言型の JSON 言語である、[Azure Logic Apps](app-service-logic-what-are-logic-apps.md) 定義の使用方法を示します。まだ使用したことがない場合は、まず[新しいロジック アプリの作成方法](app-service-logic-create-a-logic-app.md)に関するページを参照してください。また、[MSDN の定義言語の完全リファレンス マテリアル](http://aka.ms/logicappsdocs)を参照することもできます。

## リストに対して繰り返す複数のステップ

最大 10,000 件の配列の要素を反復処理しながら、それぞれにアクションを実行する場合は、[foreach タイプ](app-service-logic-loops-and-scopes.md)を利用できます。

## 問題が発生した場合のエラー処理ステップ

一般的に、*修復ステップ*を作成できるようにする必要があります。これは、1 つ以上の呼び出しが失敗した**場合に限り**実行されるいくつかのロジックです。この例では、さまざまな場所からデータを取得しますが、呼び出しに失敗した場合は、後でそのエラーを追跡できるように、どこかにメッセージを POST する必要があります。

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"readData": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://myurl"
			}
		},
		"postToErrorMessageQueue": {
			"type": "ApiConnection",
			"inputs": "...",
			"runAfter": {
				"readData": ["Failed"]
			}
		}
	},
	"outputs": {}
}
```

`readData` が **Failed** になった後でのみ `postToErrorMessageQueue` を実行するよう指定するには、`runAfter` プロパティを利用できます。ここには、想定される複数の値を指定することもできます。つまり、`runAfter` を `["Succeeded", "Failed"]` のように指定することもできます。

最後に、エラーの処理が完了したので、実行は **"失敗"** としてマークされなくなります。ご覧のとおり、この実行は、1 つのステップは失敗しましたが、このエラーを処理するステップを記述したため、**"成功"** となっています。

## 並列実行される 2 つ (以上) のステップ

複数のアクションを並列実行するには、その `runAfter` プロパティが実行時に統一されている必要があります。

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"readData": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://myurl"
			}
		},
		"branch1": {
			"type": "Http",
			"inputs": "...",
			"runAfter": {
				"readData": ["Succeeded"]
			}
		},
		"branch2": {
			"type": "Http",
			"inputs": "...",
			"runAfter": {
				"readData": ["Succeeded"]
			}
		}
	},
	"outputs": {}
}
```

上の例を見るとわかるように、`branch1` と `branch2` はどちらも、`readData` の後で実行するように設定されています。その結果、これらの分岐 (branch) の両方が並列実行されます。

![並列](./media/app-service-logic-author-definitions/parallel.png)

両方の分岐のタイムスタンプが一致していることを確認できます。

## 2 つの並列分岐の結合

並列実行するように設定された 2 つのアクションは、先ほどの例と同様、`runAfter` プロパティに項目を追加することで結合することができます。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "branch1": [
                    "Succeeded"
                ],
                "branch2": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![並列](./media/app-service-logic-author-definitions/join.png)

## リスト内のアイテムをいくつかの異なる構成にマップする

次に、プロパティの値によってまったく異なるコンテンツを取得する必要があるとします。取得先に対する値のマップをパラメーターとして作成できます。

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"specialCategories": {
			"defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
			"type": "Array"
		},
		"destinationMap": {
			"defaultValue": {
				"science": "http://www.nasa.gov",
				"microsoft": "https://www.microsoft.com/ja-JP/default.aspx",
				"google": "https://www.google.com",
				"robots": "https://en.wikipedia.org/wiki/Robot",
				"NSA": "https://www.nsa.gov/"
			},
			"type": "Object"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"getArticles": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
			},
			"conditions": []
		},
		"getSpecialPage": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
			},
			"conditions": [{
				"expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
			}],
			"forEach": "@body('getArticles').responseData.feed.entries"
		}
	}
}
```

この場合、最初に記事のリストを取得した後、2 番目のステップでは、マップ内で、パラメーターとして定義されたカテゴリに基づいて、コンテンツの取得元である URL を検索します。

ここで注意する点が 2 つあります。1 つは、カテゴリが定義済みの既知のカテゴリの 1 つと一致するかどうかを確認するために [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) 関数が使用されている点です。もう 1 つは、カテゴリを取得した後は、`parameters[...]` のように、角かっこを使用してマップのアイテムを取り出すことができる点です。

## 文字列の操作

文字列の操作に使用できる関数には、さまざまな種類があります。ある文字列をシステムに渡す必要はあっても、文字エンコードが正しく処理されるかどうか確信を持てない例を考えてみます。1 つのオプションとして、この文字列に Base64 エンコードを使用します。ただし、URL 内のエスケープを回避するために、いくつかの文字を置き換えます。

また、最初の 5 文字は使用されないため、注文の名前の部分文字列も必要になります。

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"order": {
			"defaultValue": {
				"quantity": 10,
				"id": "myorder1",
				"orderer": "NAME=Stèphén__Šīçiłianö"
			},
			"type": "Object"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"order": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
			}
		}
	},
	"outputs": {}
}
```

次に、操作を詳しく説明します。

1. 注文の名前の [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) を取得します。これにより、文字数の合計が返されます。

2. 5 を引きます (文字列を短くする必要があるため)

3. 実際に [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring) を使用します。ここでは、インデックス `5` から開始し、文字列の残りの部分を取得します。

4. この部分文字列を、[`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) 文字列に変換します。

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) で、すべての `+` 文字を `-` に置き換えます。

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) で、すべての `/` 文字を `_` に置き換えます。

## 日付と時刻の操作

日付と時刻は、特に、**トリガー**を本来サポートしていないデータ ソースからデータを取り出すときに、役立つ場合があります。また、日付と時刻を使用すると、さまざまなステップにかかる時間を計算することもできます。

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"order": {
			"defaultValue": {
				"quantity": 10,
				"id": "myorder1"
			},
			"type": "Object"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"order": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?id=@{parameters('order').id}"
			}
		},
		"timingWarning": {
			"actions" {
				"type": "Http",
				"inputs": {
					"method": "GET",
					"uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
				},
				"runAfter": {}
			}
			"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
		}
	},
	"outputs": {}
}
```

この例では、前のステップの `startTime` を抽出しています。次に、現在の時刻を取得して、[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) で 1 秒引いています (`minutes` や `hours` のように他の時間単位も使用できます)。最後に、この 2 つの値を比較できます。最初の値が 2 番目の値より小さい場合は、最初に注文が実行されてから 2 秒以上経過していることを意味します。

また、文字列フォーマッタを使用して日付の書式を設定できることにも注意してください。ここでは、RFC1123 を取得するためにクエリ文字列で [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) を使用しています。すべての日付の書式設定については、[MSDN のドキュメント](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow)に記載されています。

## さまざまな環境でデプロイ時のパラメーターを使用する

一般に、デプロイのライフサイクルには、開発環境、ステージング環境、および運用環境があります。たとえば、これらすべての環境で同じ定義を使用しながら、異なるデータベースを使用することが必要な場合があります。同様に、高可用性を確保するため、さまざまなリージョン間で同じ定義を使用する一方で、各ロジック アプリのインスタンスではそのリージョンのデータベースと通信することが必要な場合もあります。

これは、上の例で説明したように `trigger()` 関数を使用する必要があるため、*実行時*に異なるパラメーターを受け取る方法とは違います。

次のような、きわめて単純な定義から始めることができます。

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"uri": {
			"type": "string"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"readData": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@parameters('uri')"
			}
		}
	},
	"outputs": {}
}
```

その後、ロジック アプリの実際の `PUT` 要求で、`uri` パラメーターを指定できます。既定値はもう存在しないため、ロジック アプリのペイロードではこのパラメーターが必要です。

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

それぞれの環境で、`connection` パラメーターに異なる値を指定できます。

ロジック アプリの作成と管理用に用意したすべてのオプションについては、[REST API のドキュメント](https://msdn.microsoft.com/library/azure/mt643787.aspx)を参照してください。

<!---HONumber=AcomDC_0803_2016-->