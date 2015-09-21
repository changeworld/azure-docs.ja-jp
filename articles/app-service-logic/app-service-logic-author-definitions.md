<properties 
	pageTitle="ロジック アプリの定義の作成" 
	description="ロジック アプリの JSON 定義を記述する方法について説明します。" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2015"
	ms.author="stepsic"/>
	
#ロジック アプリの定義の作成
このトピックでは、単純な宣言型の JSON 言語である、[App Service Logic Apps](app-service-logic-what-are-logic-apps.md) 定義の使用方法を示します。まだ使用したことがない場合は、まず[新しいロジック アプリの作成方法](../app-service-create-a-logic-app.md)に関するページを参照してください。また、[MSDN の定義言語の完全リファレンス マテリアル](https://msdn.microsoft.com/library/azure/dn948512.aspx)を参照することもできます。

## リストに対して繰り返す複数のステップ

一般的なパターンとしては、最初のステップでアイテムのリストを取得した後、そのリストに対して必要な 2 つ以上の一連のアクションを実行します。

![Repeat over lists](./media/app-service-logic-author-definitions/repeatoverlists.png)

この例には、次の 3 つのアクションがあります。

1. 記事のリストを取得する。これにより、配列を含むオブジェクトが返されます。

2. 各記事のリンク プロパティにアクセスするアクション。これにより、記事の実際の場所が返されます。

3. 2 番目のアクションの結果すべてを反復処理して実際の記事をダウンロードするアクション。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            }
        },
        "readLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem().link"
            },
            "repeat": "@body('getArticles').responseData.feed.entries"
        },
        "downloadLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatOutputs().headers.location"
            },
            "conditions": [
                {
                    "expression": "@not(equals(actions('readLinks').status, 'Skipped'))"
                }
            ],
            "repeat": "@actions('readLinks').outputs.repeatItems"
        }
    },
    "outputs": {}
}
```

[ロジック アプリの機能の使用](app-service-logic-use-logic-app-features.md)に関するページで説明されているように、2 番目のアクションの `repeat:` プロパティを使用して、最初に取得したリストを反復処理します。ただし、2 番目のアクションは各記事に対して実行されるため、3 番目のアクションでは、`@actions('readLinks').outputs.repeatItems` プロパティを選択する必要があります。

アクション内では、[`repeatItem()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatItem) 関数、[`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) 関数、または[`repeatBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatBody) 関数のいずれかを使用できます。この例では、`location` ヘッダーを取得する必要があったため、[`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) 関数を使用して 2 番目のアクションの実行結果の出力を取得しました。ここでは、それを反復処理しています。

## リスト内のアイテムをいくつかの異なる構成にマップする

次に、プロパティの値によってまったく異なるコンテンツを取得する必要があるとします。取得先に対する値のマップをパラメーターとして作成できます。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": [
                "science",
                "google",
                "microsoft",
                "robots",
                "NSA"
            ],
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
    "triggers": {},
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
            "repeat": "@body('getArticles').responseData.feed.entries",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(repeatItem().categories, parameters('specialCategories')))]"
            },
            "conditions": [
                {
                    "expression": "@greater(length(intersection(repeatItem().categories, parameters('specialCategories'))), 0)"
                }
            ]
        }
    }
}
```

この場合、最初に記事のリストを取得した後、2 番目のステップでは、マップ内で、パラメーターとして定義されたカテゴリに基づいて、コンテンツの取得元である URL を検索します。

ここで注意する点が 2 つあります。1 つは、カテゴリが定義済みの既知のカテゴリの 1 つと一致するかどうかを確認するために [`intersection()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#intersection) 関数が使用されている点です。もう 1 つは、カテゴリを取得した後は、`parameters[...]` のように、角かっこを使用してマップのアイテムを取り出すことができる点です。

## リストの繰り返し処理で Logic Apps を連結する/入れ子にする

Logic Apps は、多くの場合、離散されているほど管理が簡単になります。これを実現するには、ロジックを複数の定義に組み込み、それらの定義を同じ親定義から呼び出します。この例では、注文を受け取る親ロジック アプリと、注文ごとにいくつかのステップを実行する子ロジック アプリが存在します。

親ロジック アプリ内のコードは次のとおりです。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "iterateOverOrders": {
            "repeat": "@parameters('orders')",
            "type": "Workflow",
            "inputs": {
                "uri": "https://westus.logic.azure.com/subscriptions/xxxxxx-xxxxx-xxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Logic/workflows/xxxxxxx",
                "apiVersion": "2015-02-01-preview",
                "trigger": {
                    "name": "submitOrder",
                    "outputs": {
                        "body": "@repeatItem()"
                    }
                },
                "authentication": {
                    "type": "Basic",
                    "username": "default",
                    "password": "xxxxxxxxxxxxxx"
                }
            }
        },
        "sendInvoices": {
            "repeat": "@outputs('iterateOverOrders').repeatItems",
            "type": "Http",
            "inputs": {
                "uri": "http://www.example.com/?invoiceID=@{repeatOutputs().run.outputs.deliverTime.value}",
                "method": "GET"
            }
        }
    },
    "outputs": {}
}
```

次に、子ロジック アプリでは、子ワークフローに渡された値を取得するために [`triggerBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerBody) 関数を使用します。その後、親フローに返すデータを出力に設定します。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "calulatePrice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcPrice&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        },
        "calculateDeliveryTime": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcTime&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        }
    },
    "outputs": {
        "deliverTime": {
            "type": "String",
            "value": "@outputs('calculateDeliveryTime').headers.etag"
        }
    }
}
```

MSDN の[ロジック アプリの種類のアクション](https://msdn.microsoft.com/ja-JP/library/azure/dn948511.aspx)に関するページを参照してください。

>[AZURE.NOTE]ロジック アプリ デザイナーでは、ロジック アプリの種類のアクションがサポートされていないため、定義を手動で編集する必要があります。


## 問題が発生した場合のエラー処理ステップ

一般的に、*修復ステップ*を作成できるようになるいつ用があります。これは、1 つ以上の呼び出しが失敗した**場合に限り**実行されるいくつかのロジックです。この例では、さまざまな場所からデータを取得しますが、呼び出しに失敗した場合は、後でそのエラーを追跡できるように、どこかにメッセージを POST する必要があります。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/ja-JP/default.aspx",
                "https://gibberish.gibberish/"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "postToErrorMessageQueue": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?noteAnErrorFor=@{repeatItem().inputs.uri}"
            },
            "conditions": [
                {
                    "expression": "@equals(actions('readData').status, 'Failed')"
                },
                {
                    "expression": "@equals(repeatItem().status, 'Failed')"
                }
            ]
        }
    },
    "outputs": {}
}
```

2 つの条件を使用しているのは、最初のステップでリストの繰り返し処理を行っているためです。アクションが 1 つしかない場合、必要な条件は最初の 1 つだけになります。また、修復ステップでは、失敗したアクションに対して *inputs* を使用できる点にも注意してください。ここでは、エラーになった URL を 2 番目のステップに渡します。

![Remediation](./media/app-service-logic-author-definitions/remediation.png)

最後に、エラーの処理が完了したので、実行は **"失敗"** としてマークされなくなります。ご覧のとおり、この実行は、1 つのステップは失敗しましたが、このエラーを処理するステップを記述したため、**"成功"** となっています。

## 並列実行される 2 つ (以上) のステップ

複数のアクションを順番に実行するのではなく並列実行するには、2 つのアクションをリンクする `dependsOn` 条件を削除する必要があります。依存関係が削除されると、アクションは、互いのデータを必要とする場合を除いて、自動的に並列実行されます。

![Branches](./media/app-service-logic-author-definitions/branches.png)

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/ja-JP/default.aspx",
                "https://office.live.com/start/default.aspx"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "branch1": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch1Logic=@{repeatItem().inputs.uri}"
            }
        },
        "branch2": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch2Logic=@{repeatItem().inputs.uri}"
            }
        }
    },
    "outputs": {}
}
```

上の例からわかるように、branch1 と branch2 は readData の内容のみに依存しています。その結果、これらの分岐 (branch) の両方が並列実行されます。

![Parallel](./media/app-service-logic-author-definitions/parallel.png)

両方の分岐のタイムスタンプが一致していることを確認できます。

## ロジックの 2 つの条件付き分岐の結合

ロジックの 2 つの条件付きフローを (実行されているかどうかは関係なく) 結合するには、両方の分岐からデータを受け取る単一のアクションを用意します。

このための方法は、処理しているのが 1 つのアイテムかアイテムのコレクションかによって異なります。1 つのアイテムの場合は、[`coalesce()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#coalesce) 関数を使用します。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(parameters('order').quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@greater(parameters('order').quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{coalesce(outputs('handleNormalOrders')?.headers?.etag,outputs('handleSpecialOrders')?.headers?.etag )}"
            },
            "conditions": [
                {
                    "expression": "@or(equals(actions('handleNormalOrders').status, 'Succeeded'), equals(actions('handleSpecialOrders').status, 'Succeeded'))"
                }
            ]
        }
    },
    "outputs": {}
}
```
 
また、たとえば、最初の 2 つの分岐両方で注文のリストが処理される場合は、[`union()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#union) 関数を使用して両方の分岐のデータを結合します。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(repeatItem().quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@greater(repeatItem().quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "repeat": "@union(actions('handleNormalOrders').outputs.repeatItems, actions('handleSpecialOrders').outputs.repeatItems)",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{repeatOutputs().headers.etag}"
            },
            "conditions": [
                {
                    "expression": "@equals(repeatItem().status, 'Succeeded')"
                }
            ]
        }
    },
    "outputs": {}
}
```
## 文字列の操作

文字列の操作に使用できる関数には、さまざまな種類があります。ある文字列をシステムに渡す必要はあっても、文字エンコードが正しく処理されるかどうか確信を持てない例を考えてみます。1 つのオプションとして、この文字列に Base64 エンコードを使用します。ただし、URL 内のエスケープを回避するために、いくつかの文字を置き換えます。

また、最初の 5 文字は使用されないため、注文の名前の部分文字列も必要になります。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
    "triggers": {},
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

1. 注文の名前の [`length()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#length) を取得します。これにより、文字数の合計が返されます。

2. 5 を引きます (文字列を短くする必要があるため)

3. 実際に [`substring()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#substring) を使用します。ここでは、インデックス `5` から開始し、文字列の残りの部分を取得します。

4. この部分文字列を、[`base64()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#base64) 文字列に変換します。

5. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) で、すべての `+` 文字を `-` に置き換えます。

6. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) で、すべての `/` 文字を `_` に置き換えます。

## 日付と時刻の操作

日付と時刻は、特に、**トリガー**を本来サポートしていないデータ ソースからデータを取り出すときに、役立つ場合があります。また、日付と時刻を使用すると、さまざまなステップにかかる時間を計算することもできます。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
    "triggers": {},
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{parameters('order').id}"
            }
        },
        "timingWarning": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
        	},
            "conditions": [
                {
                    "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
                }
            ]
        }
    },
    "outputs": {}
}
```

この例では、前のステップの `startTime` を抽出しています。次に、現在の時刻を取得して、[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/dn948512.aspx#addseconds) で 1 秒引いています (`minutes` や `hours` のように他の時間単位も使用できます)。最後に、この 2 つの値を比較できます。最初の値が 2 番目の値より小さい場合は、最初に注文が実行されてから 2 秒以上経過していることを意味します。

また、文字列フォーマッタを使用して日付の書式を設定できることにも注意してください。ここでは、RFC1123 を取得するためにクエリ文字列で [`utcnow('r')`](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow) を使用しています。すべての日付の書式設定については、[MSDN のドキュメント](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow)に記載されています。

## 実行時に値を渡して動作を変更する

たとえば、ロジック アプリの開始に使用するいくつかの値に基づいて、さまざまな動作を実行するとします。[`triggerOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerOutputs) 関数を使用すると、渡した内容からこれらの値を取得できます。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@triggerOutputs().uriToGet"
            }
        },
        "extraStep": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/extraStep"
            },
            "conditions": [
                {
                    "expression": "@triggerOutputs().doMoreLogic"
                }
            ]
        },  
    },
    "outputs": {}
}
```

実際にこれを動作させるには、実行を開始するときに、必要なプロパティを渡す必要があります (上の例では `uriToGet` と `doMoreLogic`)。次に示す呼び出しでは、[基本認証を使用](https://msdn.microsoft.com/library/azure/dn948513.aspx#basicAuth)できます。

```
POST https://<<Logic app endpoint from the Essentials>>/run?api-version=2015-02-01-preview
Authorization: Basic <<Based 64 encoded username (default) : password (from the Settings blade)>>
Content-type: application/json
```

次のペイロードを使用します。ここで使用する値をロジック アプリに指定しています。

```
{
    "outputs": {
        "uriToGet" : "http://my.uri.I.want/",
        "doMoreLogic" : true
    }
}
``` 

このロジック アプリを実行すると、渡した URI が呼び出され、さらに `true` を渡したことによって追加のステップが実行されます。*実行するたび*ではなく、デプロイ時にのみパラメーターを変更する場合は、次に説明するように、`parameters` を使用する必要があります。

## さまざまな環境でデプロイ時のパラメーターを使用する

一般に、デプロイのライフサイクルには、開発環境、ステージング環境、および運用環境があります。たとえば、これらすべての環境で同じ定義を使用しながら、異なるデータベースを使用することが必要な場合があります。同様に、高可用性を確保するため、さまざまなリージョン間で同じ定義を使用する一方で、各ロジック アプリのインスタンスではそのリージョンのデータベースと通信することが必要な場合もあります。

これは、上の例で説明したように `trigger()` 関数を使用する必要があるため、*実行時*に異なるパラメーターを受け取る方法とは違います。

次のような、きわめて単純な定義から始めることができます。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "connection": {
            "type": "string"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('connection')"
            }
        }        
    },
    "outputs": {}
}
```

その後、ロジック アプリの実際の `PUT` 要求で、`connection` パラメーターを指定できます。既定値はもう存在しないため、ロジック アプリのペイロードではこのパラメーターが必要です。

```
{
    "properties": {
        "sku": {
            "name": "Premium",
            "plan": {
                "id": "/subscriptions/xxxxx/resourceGroups/xxxxxx/providers/Microsoft.Web/serverFarms/xxxxxx"
            }
        },
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

## 条件が満たされるまでステップを実行する

API を呼び出しているときに、特定の応答を待ってから処理を行う必要のある場合があります。たとえば、他のユーザーがファイルをディレクトリにアップロードするのを待ってからそのファイルの処理する場合を考えます。これは、次のように *do-until* を使用して実行できます。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "http0": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://mydomain/listfiles"
            },
            "until": {
                "limit": {
                    "timeout": "PT10M"
                },
                "conditions": [
                    {
                        "expression": "@greater(length(action().outputs.body),0)"
                    }
                ]
            }
        }
    },
    "outputs": {}
}
```

ロジック アプリの作成と管理用に用意したすべてのオプションについては、[REST API のドキュメント](https://msdn.microsoft.com/library/azure/dn948513.aspx)を参照してください。

<!---HONumber=Sept15_HO2-->