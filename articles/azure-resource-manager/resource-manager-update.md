---
title: "Azure Resource Manager テンプレート ‐ リソースの更新 | Microsoft Docs"
description: "Azure Resource Manager テンプレートの機能を拡張して、リソースを更新する方法について説明します"
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/02/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 7807b3f6135ede80cd7e7e6486364482cde72e29
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---updating-a-resource"></a>Azure Resource Manager テンプレートの機能の拡張パターン ‐ リソースの更新

デプロイ中にリソースの更新が必要になるシナリオはいくつかあります。 このシナリオは、他の依存リソースが作成されるまで、リソースの一部のプロパティを指定できないときに発生します。 たとえば、ロード バランサーのバックエンド プールを作成する場合、仮想マシン (VM) のネットワーク インターフェイス (NIC) をバックエンド プールに含めるために、その NIC を更新することがあります。 Resource Manager ではデプロイ中のリソース更新がサポートされますが、エラーが発生しないように、また、デプロイが更新として確実に処理されるようにするには、テンプレートを適切に設計する必要があります。

## <a name="understand-the-pattern"></a>パターンについて

最初に、リソースを作成するために、テンプレートでそのリソースを 1 回参照する必要がありますが、後でそのリソースを更新する場合、同じ名前でそのリソースを参照しなければなりません。 しかし、テンプレート内で同じ名前のリソースが 2 つあると、Resource Manager は例外をスローします。 このエラーが発生しないようにするには、更新されたリソースを、サブテンプレートとしてリンクまたは挿入されている 2 番目のテンプレートで、`Microsoft.Resources/deployments` リソース タイプを使用して指定します。

次に、2 番目のテンプレートで、変更する既存のプロパティの名前を指定するか、追加するプロパティの新しい名前を指定する必要があります。 その後、元のプロパティと、そのプロパティの元の値も指定しなければなりません。 元のプロパティと値を指定しないと、新しいリソースを作成する必要があるものと Resource Manager からみなされ、元のリソースは削除されます。 元のリソースは、指定した新しいプロパティのみを含む新しいリソースと置き換えられます。

最後に、そのリソースを、デプロイ対象の関連リソースすべてに依存させる必要があります。 この依存関係により、正しい順序でリソースが作成されます。 その順序を次に示します。

1. 作成したリソース
2. 作成した依存リソース
3. 依存リソース (手順 2) の値で更新されたリソース (手順 1)

## <a name="example-template"></a>テンプレートの例

次の例のテンプレートは、このパターンを示しています。 `firstSubnet` という名前のサブネットを含む `firstVNet` という名前の仮想ネットワーク (VNet) をデプロイしてから、 `nic1` という名前の仮想ネットワーク インターフェイス (NIC) をデプロイし、サブネットに関連付けます。 その後、`updateVNet` という名前のデプロイ リソースに、`firstVNet` リソースの名前を参照する入れ子になったテンプレートが追加されます。 

このリソースの `addressSpace` プロパティと `subnets` プロパティを確認してください。 `addressSpace` 値は、`firstVNet` リソース デプロイ オブジェクトで同じプロパティ値に設定されています。 `subnets` 配列では、`firstSubnet` の値が同様に設定されます。 元の `firstVNet` プロパティすべてが指定されているため、Resource Manager によって、Azure のリソースが更新されます。 この場合、更新は、`secondSubnet` という名前の新しいサブネットを追加することです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
      {
      "apiVersion": "2016-03-30",
      "name": "firstVNet",
      "location":"[resourceGroup().location]",
      "type": "Microsoft.Network/virtualNetworks",
      "properties": {
          "addressSpace":{"addressPrefixes": [
              "10.0.0.0/22"
          ]},
          "subnets":[              
              {
                  "name":"firstSubnet",
                  "properties":{
                    "addressPrefix":"10.0.0.0/24"
                  }
              }
            ]
      }
    },
    {
        "apiVersion": "2015-06-15",
        "type":"Microsoft.Network/networkInterfaces",
        "name":"nic1",
        "location":"[resourceGroup().location]",
        "dependsOn": [
            "firstVNet"
        ],
        "properties": {
            "ipConfigurations":[
                {
                    "name":"ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod":"Dynamic",
                        "subnet": {
                            "id": "[concat(resourceId('Microsoft.Network/virtualNetworks','firstVNet'),'/subnets/firstSubnet')]"
                        }
                    }
                }
            ]
        }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "updateVNet",
      "dependsOn": [
          "nic1"
      ],
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
              {
                  "apiVersion": "2016-03-30",
                  "name": "firstVNet",
                  "location":"[resourceGroup().location]",
                  "type": "Microsoft.Network/virtualNetworks",
                  "properties": {
                      "addressSpace": "[reference('firstVNet').addressSpace]",
                      "subnets":[
                          {
                              "name":"[reference('firstVNet').subnets[0].name]",
                              "properties":{
                                  "addressPrefix":"[reference('firstVNet').subnets[0].properties.addressPrefix]"
                                  }
                          },
                          {
                              "name":"secondSubnet",
                              "properties":{
                                  "addressPrefix":"10.0.1.0/24"
                                  }
                          }
                     ]
                  }
              }
          ],
          "outputs": {}
          }
        }
    }
  ],
  "outputs": {}
}
```

## <a name="try-the-template"></a>テンプレートを試行する

このテンプレートを実験する場合は、次の手順に従います。

1.    Azure Portal に移動し、[+] アイコンを選択して、"テンプレートのデプロイ" のリソースの種類を検索します。 検索結果に見つかった場合は、選択します。
2.    [テンプレートのデプロイ] ページが表示されたら、**[作成]** を選択します。これにより、[カスタム デプロイ] ブレードが開きます。
3.    **[編集]** アイコンを選択します。
4.    空のテンプレートを削除します。
5.    前述のサンプル テンプレートをコピーして、右側のペインに貼り付けます。
6.    **[保存]** を選択します。
7.    [カスタム デプロイ] ウィンドウに戻りますが、今回はドロップダウン ボックスがいくつか表示されています。 サブスクリプションを選択し、新しいリソース グループを作成するか、既存のリソース グループを使用して、場所を選択します。 使用条件を確認し、**[同意する]** を選択します。
8.    **[購入]** を選択します。

デプロイが完了したら、ポータルで指定したリソース グループを開きます。 `firstVNet` という名前の VNet と、`nic1` という名前の NIC が表示されます。 `firstVNet`、`subnets` の順にクリックします。 最初に作成された `firstSubnet` のほか、`updateVNet` リソースで追加された `secondSubnet` が表示されます。 

![元のサブネットと、更新されたサブネット](./media/resource-manager-update/firstVNet-subnets.png)

次に、リソース グループに戻り、`nic1`、`IP configurations` の順にクリックします。 `IP configurations` セクションで、`Subnet` が `firstSubnet (10.0.0.0/24)` に設定されています。 

![nic1 IP 構成設定](./media/resource-manager-update/nic1-ipconfigurations.png)

元の `firstVNet` が、再作成ではなく更新されています。 `firstVNet` が再作成されているとしたら、`nic1` は `firstVNet` に関連付けられていません。

## <a name="next-steps"></a>次のステップ

テンプレートでこのパターンを使用すると、更新するリソースの元のプロパティを再指定できます。 リソースの種類 `Microsoft.Resources/deployments` を使用して、リンクされたテンプレートまたは入れ子になったテンプレートのいずれかで、更新リソースを指定します。

* `reference()` 関数の概要については、「[Azure Resource Manager テンプレートの関数](resource-group-template-functions.md)」を参照してください。
* このパターンは、[テンプレート構成ブロックのプロジェクト](https://github.com/mspnp/template-building-blocks)と [Azure 参照アーキテクチャ](/azure/architecture/reference-architectures/)で実装することもできます。
