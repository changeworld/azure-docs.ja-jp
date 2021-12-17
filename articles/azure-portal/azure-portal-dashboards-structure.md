---
title: Azure ダッシュボードの構造
description: ダッシュボードの例を使用して、Azure ダッシュボードの JSON 構造について説明します。 リソース プロパティへの参照が含まれています。
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: 4f005d6b232a7bba15d55055d49ac1c7adf49866
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239706"
---
# <a name="the-structure-of-azure-dashboards"></a>Azure ダッシュボードの構造

このドキュメントでは、次のダッシュボードを例として、Azure のダッシュボードの構造を説明します。

:::image type="content" source="media/azure-portal-dashboards-structure/sample-dashboard.png" alt-text="Azure portal のサンプル ダッシュボードのスクリーンショット。":::

共有 [Azure ダッシュボードは、リソース](../azure-resource-manager/management/overview.md)であるため、このダッシュボードを JSON として表すことができます。  次の JSON は、上記のダッシュボードを視覚化したものです。

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

## <a name="common-resource-properties"></a>一般的なリソースのプロパティ

JSON の関連するセクションを分割してみましょう。  最上位のプロパティでは、__ID__、__名前__、__型__、__場所__、および __タグ__ プロパティが、すべての種類の Azure リソースで共有されます。 つまり、ダッシュボードのコンテンツとはあまり関係がありません。

### <a name="id"></a>id

Azure リソース ID、[Azure リソースの名前付け規則](/azure/architecture/best-practices/resource-naming)に従います。 ポータルでダッシュボードを作成するときは、通常、GUID 形式の ID が選択されますが、プログラムでダッシュボードを作成するときは任意の有効な名前を使用できます。

### <a name="name"></a>名前

この名前は、サブスクリプション、リソースの種類、またはリソース グループの情報が含まれていないリソース ID のセグメントです。 基本的に、これはリソース ID の最後のセグメントです。

### <a name="type"></a>Type

すべてのダッシュボードは、__Microsoft.Portal/dashboards__ の型です。

### <a name="location"></a>場所

他のリソースとは異なり、ダッシュボードにはランタイム コンポーネントがありません。  ダッシュボードの場合、場所は、ダッシュボードの JSON 表現を格納するプライマリ地理的場所を示します。 値は、[サブスクリプション リソースの場所 API](/rest/api/resources/subscriptions) を使用してフェッチできる場所コードの 1 つです。

### <a name="tags"></a>タグ

タグは、任意の名前値のペア別にリソースを整理するための Azure リソース共通の機能です。 ダッシュボードには、__非表示タイトル__ と呼ばれる 1 つの特別なタグが含まれます。 ダッシュボードにこのプロパティが設定されている場合は、その値がポータルのダッシュボードの表示名として使われます。 Azure リソース ID の名前は変更できませんが、タグ名は変更できます。 このタグを使用することにより、名前変更可能な表示名をダッシュボードで使用できます。

`"tags": { "hidden-title": "Created via API" }`

### <a name="properties"></a>Properties

プロパティ オブジェクトには、__レンズ__ と __メタデータ__ の 2 つのプロパティが含まれます。 __レンズ__ プロパティには、ダッシュボード上のタイルに関する情報が含まれています。  __メタデータ__ プロパティには、今後機能が設定される可能性があります。

### <a name="lenses"></a>レンズ

__レンズ__ プロパティには、ダッシュボードが含まれています。 この例のレンズ オブジェクトには、"0" というプロパティが 1 つ含まれています。 レンズとは、現在は実装されていないグループ化の概念です。 現時点では、すべてのダッシュボードのレンズ オブジェクトに、やはり "0" という名前のこの 1 つのプロパティがあります。

### <a name="parts"></a>指定項目

"0" のオブジェクトには、__順序__ と __パーツ__ の 2 つのプロパティが含まれています。  現在のバージョンのダッシュボードでは __順序__ は常に 0 です。 __パーツ__ プロパティには、ダッシュボード上の個々のパーツ (タイルとも呼ばれます) を定義するオブジェクトが含まれています。

__パーツ__ オブジェクトには、プロパティ名が数字である、各パーツのプロパティが含まれています。 数値は重要ではありません。

各パーツ オブジェクトには、__位置__ および __メタデータ__ が含まれています。

### <a name="position"></a>位置

__位置__ プロパティには、__x__、__y__、__rowSpan__、および __colSpan__ で表されるパーツのサイズと場所情報が含まれています。 値は、グリッド単位です。 これらのグリッド単位は、ここで示すように、カスタマイズ モードのダッシュボードの場合に表示されます。 タイルの幅をグリッド単位 2、高さをグリッド単位 1、位置をダッシュボードの左上隅に設定した場合、位置オブジェクトはこのようになります。

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

:::image type="content" source="media/azure-portal-dashboards-structure/grid-units.png" alt-text="Azure portal でのダッシュボードのグリッド単位を示すスクリーンショット。":::

### <a name="metadata"></a>Metadata

各パーツにはメタデータ プロパティがあり、オブジェクトには __型__ と呼ばれる必須プロパティが 1 つのみあります。 この文字列は、ポータルに、どのタイルを表示するかを規定します。 このダッシュボードの例では、次の種類のタイルを使用します。

1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – 監視しているメトリックの表示に使用します
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – リストやリンクなどに、テキストや画像を基本的な書式設定で表示するために使用します
1. `Extension[azure]/HubsExtension/PartType/VideoPart` – YouTube、Channel9、および HTML の video タグを使用するその他の種類のビデオから動画を再生するために使用します。
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` – Azure の仮想マシンの状態と名前を表示するために使用します。

パーツの種類ごとに、独自の構成があります。 使用可能な構成プロパティは、__入力__、__設定__、および __資産__ です。 

### <a name="inputs"></a>入力

入力オブジェクトには、一般に、リソース インスタンスにタイルをバインドする情報が含まれています。  サンプル ダッシュボード内の仮想マシン パーツには、Azure リソース ID を使用してバインドを表す 1 つの入力が含まれています。  このリソース ID の形式は、すべての Azure リソースにわたって一貫しています。

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```

メトリック グラフ パーツには、バインド先のリソースを表す 1 つの入力が、表示されているメトリックスに関する情報と共に含まれます。 ネットワーク受信とネットワーク送信のメトリックを表示するタイルの入力を次に示します。

```json
"inputs":
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
                {
                    "name": "Network In",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                },
                {
                    "name": "Network Out",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                }
            ]
        }
    }
]

```

### <a name="settings"></a>Settings

設定オブジェクトには、パーツの構成可能な要素が含まれています。  このサンプル ダッシュボードのマークダウン パーツでは、設定を使用して、カスタム マークダウン コンテンツと、構成可能なタイトルおよびサブタイトルが格納されます。

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

同様に、ビデオ タイルには、再生する動画へのポインター、自動再生設定、および省略可能なタイトル情報を含む独自の設定が含まれます。

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="asset"></a>資産

ファースト クラスの管理ポータル オブジェクト ("資産") にバインドされているタイルには、資産オブジェクトによって表されるこの関係があります。  このサンプル ダッシュボードでは、仮想マシン タイルに、この資産の説明が含まれています。  __idInputName__ プロパティは、ID 入力に資産の一意識別子 (この場合は、リソース ID) が含まれていることをポータルに通知します。 ほとんどの種類の Azure リソースでは、資産がポータルで定義されています。

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`

## <a name="next-steps"></a>次のステップ

- [Azure portal](azure-portal-dashboards.md) または[プログラム](azure-portal-dashboards-create-programmatically.md)でダッシュボードを作成する方法を理解します。
- [Azure ダッシュボードのマークダウン タイルを使用してカスタム コンテンツを表示する](azure-portal-markdown-tile.md)方法を理解します。
