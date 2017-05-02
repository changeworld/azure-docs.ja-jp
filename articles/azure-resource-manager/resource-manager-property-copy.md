---
title: "Azure リソース プロパティの複数のコピー | Microsoft Docs"
description: "Azure Resource Manager テンプレートで、リソースの種類に対してプロパティの複数のインスタンスを作成します。 このシナリオでは、copy ループではなく take 関数を使用する必要があります。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: da79ff58e892bde5a35456992105de9bdbe36364
ms.lasthandoff: 04/19/2017


---
# <a name="create-multiple-instances-of-property-on-resource-type"></a>リソースの種類に対するプロパティの複数インスタンスの作成
[copy](resource-group-create-multiple.md) は、リソースの種類に対してのみ使用できます。リソースの種類内のプロパティに対して使用することはできません。 この要件により、リソースの一部の複数のインスタンスを作成する場合に問題が発生する可能性があります。 一般的なシナリオでは、仮想マシン用に複数のデータ ディスクを作成します。 dataDisks は仮想マシンのプロパティであり、それ自体がリソース タイプではないので、データ ディスクで copy を使用することはできません。 代わりに、必要な数のデータ ディスクで配列を作成し、作成するデータ ディスクの実際の数を渡します。 仮想マシンの定義で、`take` 関数を使用し、配列から実際に必要な要素の数のみを取得します。

このパターンの完全な例は、 [Create a VM with a dynamic selection of data disks (データディスクの動的な選択による VM の作成)](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) テンプレートに示されています。

デプロイ テンプレートの関連するセクションをこの記事で示します。 複数のデータ ディスクの動的な作成に関するセクションがよくわかるように、テンプレートの各部を削除してあります。 

## <a name="define-template-with-variable-for-the-property"></a>プロパティの変数を含むテンプレートの定義

複数のデータ ディスクを含む仮想マシンを作成するには、必要な数より多くのデータ ディスクを定義する配列変数を追加します。 パラメーター `numDataDisks` で、作成するデータ ディスクの実際の数を渡します。 `take` を使用して、仮想マシンに割り当てる配列内の要素の正確な数を指定します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ... /* not all elements shown */
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        "storageProfile": {
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

## <a name="create-multiple-virtual-machines-with-multiple-data-disks"></a>複数のデータ ディスクを含む複数の仮想マシンの作成

プロパティの変動的な数の項目とリソースの複数のインスタンスを作成する必要がある場合は、**take** 関数および **copy** 要素を一緒に使用することができます。 たとえば、仮想マシンごとにデータ ディスク数が異なる複数の仮想マシンを作成する必要があるとします。 

関連する仮想マシンを識別する名前を各データ ディスクに付けるには、データ ディスクの配列を別のテンプレートに追加します。 仮想マシンの名前と、返されるデータ ディスクの数を表すパラメーターを組み込みます。 outputs セクションで、指定した項目の数を返します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "numDataDisks": {
      "type": "int",
      "maxValue": 16,
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    }
  },
  "variables": {
    "diskArray": [
      {
        "name": "[concat(parameters('vmName'), '-datadisk1')]",
        "vhd": {
          "uri": "[concat('http://', parameters('storageAccountName'),'.blob.core.windows.net/vhds/', parameters('vmName'), '-datadisk1.vhd')]"
        },
        ...
      },
      ...
    ],
  },
  "resources": [
  ],
  "outputs": {
    "result": {
      "type": "array",
      "value": "[take(variables('diskArray'),parameters('numDataDisks'))]"
    }
  }
}
``` 

親テンプレートに、仮想マシンの数と、各仮想マシンのデータ ディスクの数の配列を表すパラメーターを含めます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numberOfInstances": {
      "type": "int",
      "defaultValue": 2,
      "metadata": {
        "description": "Number of VMs to deploy"
      }
    },
    "numberOfDataDisksPerVM": {
      "type": "array",
      "defaultValue": [1,2]
    }
  },
```

resources セクションで、データ ディスクを定義するテンプレートの複数のインスタンスをデプロイします。 

```json
{
  "apiVersion": "2016-09-01",
  "name": "[concat('nested-', copyIndex())]",
  "type": "Microsoft.Resources/deployments",
  "copy": {
    "name": "deploycopy",
    "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "{data-disk-template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "vmName": { "value": "[concat('myvm', copyIndex())]" },
      "storageAccountName": { "value": "[variables('storageAccountName')]" },
      "numDataDisks": { "value": "[parameters('numberOfDataDisksPerVM')[copyIndex()]]" }
    }
  }
},
```

resources セクションで、仮想マシンの複数のインスタンスをデプロイします。 データ ディスクについては、データ ディスクの正しい数とデータ ディスクの正しい名前を格納する入れ子になったデプロイを参照します。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat('myvm', copyIndex())]",
  "copy": {
    "name": "virtualMachineLoop",
      "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "storageProfile": {
      ...
      "dataDisks": "[reference(concat('nested-', copyIndex())).outputs.result.value]"
    },
    ...
  },
  ...
}
```

## <a name="next-steps"></a>次のステップ
* リソースの複数インスタンス作成の概要については、「[Azure Resource Manager テンプレート内における複数のリソースのインスタンスのデプロイ](resource-group-create-multiple.md)」を参照してください。
* 連続して繰り返されるリソースを作成する場合は、[Sequential looping for Azure Resource Manager templates](resource-manager-sequential-loop.md)を参照してください。
* テンプレートをデプロイする方法については、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。


