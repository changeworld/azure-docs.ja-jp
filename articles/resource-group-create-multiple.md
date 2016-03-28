<properties
   pageTitle="リソースの複数のインスタンスのデプロイ | Microsoft Azure"
   description="Azure リソース マネージャー テンプレートで copy 操作と配列を使用して、リソースをデプロイする際に複数回反復処理する方法について説明します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/09/2016"
   ms.author="tomfitz"/>

# Azure リソース マネージャーでリソースの複数のインスタンスを作成する

このトピックでは、Azure リソース マネージャー テンプレートで反復処理して、リソースの複数のインスタンスを作成する方法について説明します。

## copy、copyIndex、length

複数回作成するリソース内では、反復処理する回数を指定する **copy** オブジェクトを定義できます。copy は次の形式で指定します。

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

次の concat 関数内に示すように、現在の反復値には、**copyIndex()** 関数でアクセスできます。

    [concat('examplecopy-', copyIndex())]

値の配列から複数のリソースを作成する場合は、**length** 関数を使用して数を指定できます。length 関数のパラメーターとして、配列を指定します。

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## name でのインデックス値の使用

copy 操作では、増分するインデックス値に基づいた一意の名前を付けたリソースの複数のインスタンスを作成できます。たとえば、デプロイ済みの各リソース名の末尾に一意の数値を追加することもできます。次の 3 つの Web サイトをデプロイするには:

- examplecopy-0
- examplecopy-1
- examplecopy-2.

次のテンプレートを使用します。

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
      } 
    ]

## インデックス値のオフセット

前述の例では、インデックス値が 0 から 2 に増えることがわかります。インデックス値をオフセットするには、**copyIndex(1)** のように、**copyIndex()** 関数に値を渡します。実行する反復処理の数は copy 要素で指定されたままですが、copyIndex の値が指定された値でオフセットされます。そのため前の例と同じテンプレートを使用しても、**copyIndex(1)** を指定すると、次の名前の 3 つの Web サイトがデプロイされます。

- examplecopy-1
- examplecopy-2
- examplecopy-3

## 配列の使用
   
コピー操作は、配列内の各要素に対して反復処理するため、配列で作業するときに特に便利です。次の 3 つの Web サイトをデプロイするには:

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

次のテンプレートを使用します。

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

もちろん、コピー数を配列の長さ以外の値に設定します。たとえば、さまざまな値を含む配列を作成し、デプロイする配列要素の数を指定するパラメーターの値を渡すことができます。この場合は、最初の例に示すようにコピー数を設定します。

## ループ内のリソースに基づく

**dependsOn** 要素を使用し、あるリソースを別のリソースの後にデプロイするように指定できます。ループ内のリソースの集合に依存するリソースをデプロイする必要があるとき、**dependsOn** 要素にコピー ループの名前を指定できます。次の例では、仮想マシンをデプロイする前に 3 つのストレージ アカウントをデプロイする方法について示します。完全な仮想マシン定義は示されていません。コピー要素の **name** が **storagecopy** に設定され、Virtual Machines の **dependsOn** 要素も **storagecopy** に設定されていることに注意してください。

    {
	    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "contentVersion": "1.0.0.0",
	    "parameters": {},
	    "resources": [
	        {
		        "apiVersion": "2015-06-15",
		        "type": "Microsoft.Storage/storageAccounts",
		        "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
		        "location": "[resourceGroup().location]",
		        "properties": {
                    "accountType": "Standard_LRS"
            	 },
		        "copy": { 
         	        "name": "storagecopy", 
         	        "count": 3 
      		    }
	        },
           {
               "apiVersion": "2015-06-15", 
               "type": "Microsoft.Compute/virtualMachines", 
               "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
               "dependsOn": ["storagecopy"],
               ...
           }
	    ],
	    "outputs": {}
    }

## 入れ子になったリソースでのループ

入れ子になったリソースにコピー ループを使用することはできません。通常、他のリソース内の入れ子として定義するリソースの複数のインスタンスを作成する場合、代わりに最上位のリソースとしてリソースを作成してから**タイプ**と**名前**プロパティを使用して、親リソースとのリレーションシップを定義します。

たとえば、通常は Data Factory 内の入れ子になったリソースとしてデータセットを定義する場合、

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]
    
データセットの複数のインスタンスを作成するには、次のように、テンプレートを変更する必要があります。完全修飾のタイプと名前にデータ ファクトリ名が含まれていることに注目してください。

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## 仮想マシン用の複数のデータ ディスクの作成

一般的なシナリオでは、仮想マシン用に複数のデータ ディスクを作成します。**dataDisks** は仮想マシンのプロパティであり、それ自体がリソース タイプではないので、データ ディスクで **copy** を使用することはできません。**copy** はリソースでのみ動作します。代わりに、必要な数のディスクが定義されているリンクされたテンプレートを作成し、指定した数のデータ ディスクの配列を出力します。デプロイ テンプレートからこのテンプレートにリンクして、返されたデータ ディスクの数を渡します。デプロイ テンプレートでは、リンクされたテンプレートからの出力の値を **dataDisks** プロパティに設定します。

このパターンの完全な例は、[Create a VM with a dynamic selection of data disks (データディスクの動的な選択による VM の作成)](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) テンプレートに示されています。

デプロイ テンプレートの関連するセクションを以下に示します。複数のデータ ディスクの動的な作成に関するセクションがよくわかるように、多くのテンプレートを削除してあります。作成するディスクの数を渡すことができる **numDataDisks** パラメーターに注意してください。**diskSelection** という名前のリンクされたテンプレートは、resources セクションで指定されています。そのリンクされたテンプレートからの出力は、仮想マシンで **dataDisks** プロパティに割り当てられます。

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        ...
        "numDataDisks": {
          "type": "string",
          "allowedValues": [
            "1",
            "2",
            "3",
            "4",
            "5",
            "6",
            "7",
            "8",
            "9",
            "10",
            "11",
            "12",
            "13",
            "14",
            "15",
            "16",
            "32"
          ],
          "metadata": {
            "description": "This parameter allows the user to select the number of disks they want"
          }
        }
      },
      "variables": {
        "artifactsLocation": "https://raw.githubusercontent.com/singhkay/azure-quickstart-templates/master/201-vm-dynamic-data-disks-selection/", 
        "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
        "sizeOfDataDisksInGB": 100,
        "diskCaching": "ReadWrite",
        ...
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "diskSelection",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "uri": "[concat(variables('artifactsLocation'), 'disksSelector', '.json')]",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "numDataDisks": {
                "value": "[parameters('numDataDisks')]"
              },
              "diskStorageAccountName": {
                "value": "[variables('storageAccountName')]"
              },
              "diskCaching": {
                "value": "[variables('diskCaching')]"
              },
              "diskSizeGB": {
                "value": "[variables('sizeOfDataDisksInGB')]"
              }
            }
          }
        },
        ...
        {
          "type": "Microsoft.Compute/virtualMachines",
          "properties": {
            "storageProfile": {
              ...
              "dataDisks": "[reference('diskSelection').outputs.dataDiskArray.value]"
            },
            ...
          }
        }
      ]
    }

リンクされたテンプレートでは、返される配列が定義されています。次に示すテンプレートでは 3 ～ 32 のディスク定義の繰り返しが省略されていますが、実際のテンプレートではすべてを定義する必要があります。32 個より多くのデータ ディスクが必要な場合は、パターンを継続できます。このテンプレートではリソースはデプロイされないことに注意してください。このテンプレートは、要求された数のデータ ディスクを定義するオブジェクトの配列を返すだけです。

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numDataDisks": {
      "type": "string",
      "allowedValues": [
        "1",
        "2",
        "3",
        "4",
        "5",
        "6",
        "7",
        "8",
        "9",
        "10",
        "11",
        "12",
        "13",
        "14",
        "15",
        "16",
        "32"
      ],
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    },
    "diskStorageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the storage account where the data disks are stored"
      }
    },
    "diskCaching": {
      "type": "string",
      "allowedValues": [
        "None",
        "ReadOnly",
        "ReadWrite"
      ],
      "metadata": {
        "description": "Caching type for the data disks"
      }
    },
    "diskSizeGB": {
      "type": "int",
      "minValue": 1,
      "maxValue": 1023,
      "metadata": {
        "description": "Size of the data disks"
      }
    }
  },
  "variables": {
    "disksArray": {
      "1": "[variables('dataDisks')['1']]",
      "2": "[concat(variables('dataDisks')['1'], variables('dataDisks')['2'])]",
      "3": "[concat(variables('dataDisks')['1'], variables('dataDisks')['2'], variables('dataDisks')['3'])]",
      "4": "[variables('diskDeltas')['4delta']]",
      "5": "[concat(variables('diskDeltas')['4delta'], variables('dataDisks')['5'])]",
      "6": "[concat(variables('diskDeltas')['4delta'], variables('dataDisks')['5'], variables('dataDisks')['6'])]",
      "7": "[concat(variables('diskDeltas')['4delta'], variables('dataDisks')['5'], variables('dataDisks')['6'], variables('dataDisks')['7'])]",
      "8": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'])]",
      "9": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('dataDisks')['9'])]",
      "10": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('dataDisks')['9'], variables('dataDisks')['10'])]",
      "11": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('dataDisks')['9'], variables('dataDisks')['10'], variables('dataDisks')['11'])]",
      "12": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'])]",
      "13": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('dataDisks')['13'])]",
      "14": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('dataDisks')['13'], variables('dataDisks')['14'])]",
      "15": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('dataDisks')['13'], variables('dataDisks')['14'], variables('dataDisks')['15'])]",
      "16": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('diskDeltas')['16delta'])]",
      "32": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('diskDeltas')['16delta'], variables('diskDeltas')['32delta'])]"
    },
    "dataDisks": {
      "1": [
        {
          "name": "datadisk1",
          "lun": 0,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ],
      "2": [
        {
          "name": "datadisk2",
          "lun": 1,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ],
      "3": [
        {
          "name": "datadisk3",
          "lun": 2,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ],
      ...
      "32": [
        {
          "name": "datadisk32",
          "lun": 31,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk32.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ]
    },
    "_comment2": "The delta arrays below build the difference from 0 to 4, 4 to 8, 8 to 12 disks and so on",
    "diskDeltas": {
      "4delta": [
        "[variables('dataDisks')['1'][0]]",
        "[variables('dataDisks')['2'][0]]",
        "[variables('dataDisks')['3'][0]]",
        "[variables('dataDisks')['4'][0]]"
      ],
      "8delta": [
        "[variables('dataDisks')['5'][0]]",
        "[variables('dataDisks')['6'][0]]",
        "[variables('dataDisks')['7'][0]]",
        "[variables('dataDisks')['8'][0]]"
      ],
      "12delta": [
        "[variables('dataDisks')['9'][0]]",
        "[variables('dataDisks')['10'][0]]",
        "[variables('dataDisks')['11'][0]]",
        "[variables('dataDisks')['12'][0]]"
      ],
      "16delta": [
        "[variables('dataDisks')['13'][0]]",
        "[variables('dataDisks')['14'][0]]",
        "[variables('dataDisks')['15'][0]]",
        "[variables('dataDisks')['16'][0]]"
      ],
      "32delta": [
        "[variables('dataDisks')['17'][0]]",
        "[variables('dataDisks')['18'][0]]",
        "[variables('dataDisks')['19'][0]]",
        "[variables('dataDisks')['20'][0]]",
        "[variables('dataDisks')['21'][0]]",
        "[variables('dataDisks')['22'][0]]",
        "[variables('dataDisks')['23'][0]]",
        "[variables('dataDisks')['24'][0]]",
        "[variables('dataDisks')['25'][0]]",
        "[variables('dataDisks')['26'][0]]",
        "[variables('dataDisks')['27'][0]]",
        "[variables('dataDisks')['28'][0]]",
        "[variables('dataDisks')['29'][0]]",
        "[variables('dataDisks')['30'][0]]",
        "[variables('dataDisks')['31'][0]]",
        "[variables('dataDisks')['32'][0]]"
      ]
    }
  },
  "resources": [],
  "outputs": {
    "dataDiskArray": {
      "type": "array",
      "value": "[variables('disksArray')[parameters('numDataDisks')]]"
    }
  }
}

```

## 次のステップ
- テンプレートのセクションについては、「[Azure リソース マネージャーのテンプレートの作成](./resource-group-authoring-templates.md)」を参照してください。
- テンプレートで使用できるすべての関数については、「[Azure リソース マネージャーのテンプレートの関数](./resource-group-template-functions.md)」を参照してください。
- テンプレートをデプロイする方法については、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。

<!---HONumber=AcomDC_0316_2016-->