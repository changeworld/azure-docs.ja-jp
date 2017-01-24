---
title: "Azure Resource Manager ポリシー | Microsoft Docs"
description: "Azure リソース マネージャーのポリシーを使用し、サブスクリプション、リソース グループまたは個々のリソースなどのさまざまなスコープの違反を防ぐ方法について説明します。"
services: azure-resource-manager
documentationcenter: na
author: ravbhatnagar
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2016
ms.author: gauravbh;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e9143e1d9bb9f009b5cfe2e0d7d6e16056219542
ms.openlocfilehash: f3fb6d266e3d2258c40d1088c79cce2780cac2e0


---
# <a name="use-policy-to-manage-resources-and-control-access"></a>ポリシーを使用したリソース管理とアクセス制御
Azure Resource Manager で、カスタム ポリシーを使用してアクセスを制御できます。 ポリシーを使用すれば、組織のリソースを管理するために必要な規則に組織内のユーザーが違反するのを防ぐことができます。 

ポリシー定義を作成し、行為やリソースを具体的に否認します。 サブスクリプション、リソース グループ、個別リソースなど、任意の範囲でポリシー定義を割り当てます。 ポリシーは、すべての子リソースが継承します。 したがって、リソース グループに適用されたポリシーは、そのリソース グループのすべてのリソースに適用されます。

この記事では、ポリシーの作成で使用する、ポリシー定義言語の基本構造について説明します。 その後、これらのポリシーをさまざまなスコープに適用する方法について説明します。

## <a name="how-is-it-different-from-rbac"></a>RBAC との違いは何か。
ポリシーとロールベースのアクセス制御には大きな違いがいくつかありますが、最初に理解するべきことは、ポリシーと RBAC は連動するということです。 ポリシーを使用するには、RBAC で認証される必要があります。 RBAC とは異なり、ポリシーは既定で許可し、明示的に否認するシステムです。 

RBAC は、 **ユーザー** がさまざまな範囲で実行できるアクションにフォーカスしています。 たとえば、任意の範囲で、あるリソース グループの共同作成者ロールに特定のユーザーを追加すると、そのユーザーはそのリソース グループを変更できます。 

ポリシーは、さまざまな範囲の **リソース** アクションにフォーカスしています。 たとえば、ポリシーを介して、プロビジョニング可能なリソースの種類を制御したり、リソースをプロビジョニングできる場所を制限したりできます。

## <a name="common-scenarios"></a>一般的なシナリオ
よくあるシナリオの 1 つは、配賦目的で部門別のタグが必要性な場合です。 適切なコスト センターが関連付けられている場合にのみ、組織で操作を許可し、それ以外の場合は要求を拒否したい場合があります。 ポリシーにより、実行された操作を適切なコスト センターから請求できるようになります。

別のよくあるシナリオに、組織が、リソースの作成場所を制御したい場合があります。 または、特定の種類のリソースのみのプロビジョニングを許可することによって、リソースへのアクセスを制御したい場合があります。

同様に、組織がサービス カタログを制御したい場合や、リソースに希望の名前付け規則を適用したい場合があります。

これらのシナリオは、ポリシーを使用して簡単に実現できます。

## <a name="policy-definition-structure"></a>ポリシー定義の構造
ポリシー定義は JSON を使用して作成します。 これは、アクションを定義する 1 つ以上の条件および論理演算子と、条件が満たされた場合に何が発生するかを伝える効果で構成されます。 [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json)でスキーマが公開されています。 

次の例は、リソースがデプロイされる場所を制限するために使用できるポリシーを示しています。

```json
{
  "properties": {
    "parameters": {
      "listOfAllowedLocations": {
        "type": "array",
        "metadata": {
          "description": "An array of permitted locations for resources.",
          "strongType": "location",
          "displayName": "List of locations"
        }
      }
    },
    "displayName": "Geo-compliance policy template",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('listOfAllowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

ポリシーには、基本的に次のセクションが含まれます。

**パラメーター:** ポリシーの割り当て時に指定される値。

**条件/論理演算子:** 一連の論理演算子を使用して操作できる条件のセット。

**効果:** 条件が満たされた場合に何が発生するかを表し、拒否または監査のいずれかを示します。 監査の効果により、警告イベント サービス ログが出力されます。 たとえば、管理者は、だれかがサイズの大きな VM を作成した場合に監査イベントを実行するポリシーを作成できます。 管理者は後でログを確認できます。

    {
      "if" : {
          <condition> | <logical operator>
      },
      "then" : {
          "effect" : "deny | audit | append"
      }
    }

## <a name="policy-evaluation"></a>ポリシーの評価
リソースを作成すると、ポリシーが評価されます。 テンプレートのデプロイの場合、ポリシーはテンプレート内の各リソースの作成時に評価されます。 

> [!NOTE]
> 現時点では、タグ、種類、場所をサポートしていない、Microsoft.Resources/deployments のようなリソースの種類は、ポリシーによる評価の対象となりません。 このサポートは、今後追加される予定です。 下位互換性の問題を回避するためには、ポリシーの作成時に種類を明示的に指定する必要があります。 たとえば、種類が指定されていないタグ ポリシーは、すべての種類に適用されます。 そのような場合、タグをサポートしていない入れ子になったリソースがあり、デプロイ リソースの種類がポリシーの評価に追加されていると、テンプレートのデプロイに失敗する可能性があります。 
> 
> 

## <a name="parameters"></a>parameters
API バージョン 2016-12-01 以降、ポリシー定義でパラメーターを使用できます。 パラメーターを使用すると、ポリシー定義の数を減らすことで、ポリシーの管理を単純化できます。 パラメーターの値は、ポリシーを割り当てるときに指定します。

パラメーターは、ポリシーの定義を作成するときに宣言します。

    "parameters": {
      "listOfLocations": {
        "type": "array",
        "metadata": {
          "description": "An array of permitted locations for resources.",
          "displayName": "List Of Locations"
        }
      }
    }

パラメーターの型は、文字列または配列のどちらも可能です。 メタデータ プロパティは、Azure Portal などでわかりやすい情報を表示するために使用されます。 

ポリシー ルールでは、テンプレートと同じようにパラメーターを参照できます。 次に例を示します。 
        
    { 
        "field" : "location",
        "in" : "[parameters(listOfLocations)]"
    }

## <a name="logical-operators"></a>論理演算子
サポートされている論理演算子とその構文は次のとおりです。

| 演算子名 | 構文 |
|:--- |:--- |
| not |"not" : {&lt;condition  or operator &gt;} |
| と |"allOf" : [ {&lt;condition  or operator &gt;},{&lt;condition  or operator &gt;}] |
| または |"anyOf" : [ {&lt;condition  or operator &gt;},{&lt;condition  or operator &gt;}] |

リソース マネージャーでは、入れ子になった演算子を使用して複雑なロジックをポリシーに指定できます。 たとえば、指定したリソースの種類の特定の場所でのリソースの作成を拒否できます。 このトピックでは、入れ子になった演算子の例を示します。

## <a name="conditions"></a>条件
条件は、**フィールド**または**ソース**が特定の基準を満たすかどうかを評価します。 サポートされている条件名とその構文は次のとおりです。

| 条件名 | 構文 |
|:--- |:--- |
| 等しい |"equals" : "&lt;value&gt;" |
| LIKE |"like" : "&lt;value&gt;" |
| 指定値を含む |"contains" : "&lt;value&gt;" |
| [ |"in" : [ "&lt;value1&gt;","&lt;value2&gt;" ] |
| ContainsKey |"containsKey" : "&lt;keyName&gt;" |
| Exists |"exists" : "&lt;bool&gt;" |

### <a name="fields"></a>フィールド
条件は、フィールドとソースを使用して構成されます。 フィールドは、リソースの状態の記述に使用されるリソース要求ペイロード内のプロパティを表します。 ソースは、要求自体の特性を表します。 

次のフィールドおよびソースがサポートされています。

フィールド: **name**、**kind**、**type**、**location**、**tags**、**tags.***、**property alias**。 

### <a name="property-aliases"></a>プロパティのエイリアス
プロパティのエイリアスは、設定や SKU など、リソースの種類固有のプロパティにアクセスするためにポリシー定義で使用できる名前です。 プロパティが存在するすべての API バージョンで機能します。 エイリアスは、REST API を使用して取得できます (今後、Powershell のサポートが追加される予定です)。

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01

次の例はエイリアスの定義を示しています。 ご覧のように、プロパティ名が変更された場合も、エイリアスはさまざまな API バージョンでのパスを定義します。 

    "aliases": [
        {
          "name": "Microsoft.Storage/storageAccounts/sku.name",
          "paths": [
            {
              "path": "properties.accountType",
              "apiVersions": [
                "2015-06-15",
                "2015-05-01-preview"
              ]
            },
            {
              "path": "sku.name",
              "apiVersions": [
                "2016-01-01"
              ]
            }
          ]
        }
    ]

現在サポートされているエイリアスは次のとおりです。

| エイリアス名 | Description |
| --- | --- |
| {resourceType}/sku.name |サポートされているリソースの種類: Microsoft.Compute/virtualMachines、<br />Microsoft.Storage/storageAccounts、<br />Microsoft.Web/serverFarms、<br /> Microsoft.Scheduler/jobcollections、<br />Microsoft.DocumentDB/databaseAccounts、<br />Microsoft.Cache/Redis、<br />Microsoft.CDN/profiles |
| {resourceType}/sku.family |サポートされているリソースの種類: Microsoft.Cache/Redis |
| {resourceType}/sku.capacity |サポートされているリソースの種類: Microsoft.Cache/Redis |
| Microsoft.Compute/virtualMachines/imagePublisher | |
| Microsoft.Compute/virtualMachines/imageOffer | |
| Microsoft.Compute/virtualMachines/imageSku | |
| Microsoft.Compute/virtualMachines/imageVersion | |
| Microsoft.Storage/storageAccounts/accessTier | |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | |
| Microsoft.Cache/Redis/enableNonSslPort | |
| Microsoft.Cache/Redis/shardCount | |
| Microsoft.SQL/servers/version | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveId | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveName | |
| Microsoft.SQL/servers/databases/edition | |
| Microsoft.SQL/servers/databases/elasticPoolName | |
| Microsoft.SQL/servers/elasticPools/dtu | |
| Microsoft.SQL/servers/elasticPools/edition | |


## <a name="effect"></a>効果
ポリシーでは、**deny**、**audit**、**append** の 3 種類の効果がサポートされています。 

* deny は監査ログでイベントを生成し、要求は失敗します
* audit は監査ログでイベントを生成しますが、要求は失敗しません
* append は定義済みのフィールド セットを要求に追加します 

**append** の場合、次のように詳細を指定する必要があります。

    "effect": "append",
    "details": [
      {
        "field": "field name",
        "value": "value of the field"
      }
    ]

値には文字列または JSON 形式オブジェクトを指定できます。 


## <a name="policy-definition-examples"></a>ポリシー定義の例
ここで、上で挙げたシナリオを実現するポリシーを定義する方法を見てみましょう。

### <a name="chargeback-require-departmental-tags"></a>配賦: 部門別のタグが必要
次のポリシーは、"costCenter" キーを含むタグがない要求を拒否します。

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

次のポリシーは、タグが存在しない場合に定義済みの値を持つ costCenter タグを追加します。 

    {
      "if": {
        "field": "tags",
        "exists": "false"
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags",
            "value": {"costCenter":"myDepartment" }
          }
        ]
      }
    }

次のポリシーは、costCenter タグは存在しないものの、その他のタグが存在する場合に、定義済みの値を持つ costCenter タグを追加します。 

    {
      "if": {
        "allOf": [
          {
            "field": "tags",
            "exists": "true"
          },
          {
            "field": "tags.costCenter",
            "exists": "false"
          }
        ]

      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags.costCenter",
            "value": "myDepartment"
          }
        ]
      }
    }


### <a name="geo-compliance-ensure-resource-locations"></a>地理的準拠: リソースの場所を確認
次の例は、場所が北ヨーロッパや西ヨーロッパでない場合に要求を拒否するポリシーを示しています。

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="service-curation-select-the-service-catalog"></a>サービスのキュレーション: サービス カタログを選択
次の例は、種類が Microsoft.Resources/\*、Microsoft.Compute/\*、Microsoft.Storage/\*、Microsoft.Network/\* のサービスのアクションのみを許可するポリシーを示しています。 それ以外は拒否されます。

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "field" : "type",
              "like" : "Microsoft.Resources/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Compute/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Storage/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="use-approved-skus"></a>承認された SKU の使用
次の例は、プロパティのエイリアスを使用して SKU を制限する方法を示しています。 この例では、Standard_LRS と Standard_GRS のみ、ストレージ アカウントで使用することが認められます。

    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "allof": [
                {
                  "field": "Microsoft.Storage/storageAccounts/sku.name",
                  "in": ["Standard_LRS", "Standard_GRS"]
                }
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }


### <a name="naming-convention"></a>命名規則
次の例では、"like" の条件でサポートされているワイルドカードが使用されています。 この条件は、名前が示しているパターンと一致する場合 (namePrefix\*nameSuffix)、要求を拒否するよう示しています。

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="tag-requirement-just-for-storage-resources"></a>ストレージ リソースのみに対するタグ要件
次の例は、論理演算子を入れ子にして、ストレージ リソースのみにアプリケーション タグを要求する方法を示しています。

    {
        "if": {
            "allOf": [
              {
                "not": {
                  "field": "tags",
                  "containsKey": "application"
                }
              },
              {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## <a name="create-and-assign-a-policy"></a>ポリシーの作成と割り当て
ポリシーを適用するには、ポリシー定義を作成し、特定のスコープで適用する必要があります。 

### <a name="rest-api"></a>REST API
[ポリシー定義用の REST API](https://docs.microsoft.com/rest/api/resources/policydefinitions)を使用して、ポリシーを作成できます。 REST API を使用すると、ポリシー定義の作成と削除、既存の定義に関する情報の取得を実行できます。

ポリシーを作成するには、次を実行します。

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

api-version には、*2016-04-01* または *2016-12-01* を使用します。 要求の本文は次の例のようになります。

    {
      "properties": {
        "parameters": {
          "listOfAllowedLocations": {
            "type": "array",
            "metadata": {
              "description": "An array of permitted locations for resources.",
              "strongType": "location",
              "displayName": "List Of Locations"
            }
          }
        },
        "displayName": "Geo-compliance policy template",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
        "policyRule": {
          "if": {
            "not": {
              "field": "location",
              "in": "[parameters('listOfAllowedLocations')]"
            }
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    }

[ポリシーの割り当て用 REST API](https://docs.microsoft.com/rest/api/resources/policyassignments)で、目的のスコープに対してポリシー定義を適用します。 REST API を使用すると、ポリシー割り当ての作成と削除、既存の割り当てに関する情報の取得を実行できます。

ポリシー割り当てを作成するには、次を実行します。

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

ポリシー割り当ての名前は、{policy-assignment} です。 api-version には、*2016-04-01* または *2016-12-01* を使用します (パラメーター用)。 

要求の本文は次の例のようになります。

    {
      "properties":{
        "displayName":"West US only policy assignment on the subscription ",
        "description":"Resources can only be provisioned in West US regions",
        "parameters": {
             "listOfAllowedLocations": { "value": ["West US", "West US 2"] }
         },
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
    }

### <a name="powershell"></a>PowerShell
New-AzureRmPolicyDefinition コマンドレットを使用してポリシー定義を作成できます。 以下の例では、リソースを北ヨーロッパと西ヨーロッパに限定できるポリシーを作成します。

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'            

実行時の出力は $policy オブジェクトに保存され、後でポリシーを割り当てるときに使用できます。 ポリシー パラメーターの場合、ポリシー インラインを指定するのではなく、ポリシーが含まれている .json ファイルへのパスを提供することもできます。

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain     regions" -Policy "path-to-policy-json-on-disk"

New-AzureRmPolicyAssignment コマンドレットを使用して、ポリシーを目的のスコープに適用できます。

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

ここで、$policy は New-AzureRmPolicyDefinition コマンドレットを実行した結果として返されたポリシー オブジェクトです。 ここでのスコープは、指定するリソース グループの名前です。

ポリシー割り当てを削除するには、次のコマンドを使用します。

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Get-AzureRmPolicyDefinition、Set-AzureRmPolicyDefinition、Remove-AzureRmPolicyDefinition コマンドレットをそれぞれ使用して、ポリシー定義を取得、変更、削除できます。

同様に、Get-AzureRmPolicyAssignment、Set-AzureRmPolicyAssignment、および Remove-AzureRmPolicyAssignment コマンドレットをそれぞれ使用して、ポリシーの割り当てを取得、変更、または削除することもできます。

### <a name="azure-cli"></a>Azure CLI
Azure CLI でポリシー定義コマンドを使用して、ポリシー定義を作成できます。 以下の例では、リソースを北ヨーロッパと西ヨーロッパに限定できるポリシーを作成します。

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'    


ポリシー インラインを指定するのではなく、ポリシーが含まれている .json ファイルのパスを指定できます。

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"

ポリシーの割り当てコマンドを使用して、ポリシーを目的のスコープに適用できます。

    azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/<policy-name> --scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

ここでのスコープは、指定するリソース グループの名前です。 policy-definition-id パラメーターの値が不明な場合は、Azure CLI を使用して値を取得できます。 

    azure policy definition show <policy-name>

ポリシー割り当てを削除するには、次のコマンドを使用します。

    azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

ポリシー定義の表示、設定、削除コマンドをそれぞれ使用して、ポリシー定義を取得、変更、削除できます。

同様に、ポシリー割り当ての表示および削除コマンドをそれぞれ使用して、ポリシー割り当てを取得、変更、削除できます。

## <a name="policy-audit-events"></a>ポリシー監査イベント
ポリシーを適用した後、ポリシー関連イベントの表示を開始します。 このデータを取得するには、ポータルに移動するか、PowerShell または Azure CLI を使用します。 

### <a name="powershell"></a>PowerShell
拒否効果に関連するすべてのイベントを表示するには、次の PowerShell コマンドを使用できます。

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/deny/action"} 

監査効果に関連するすべてのイベントを表示するには、次のコマンドを使用できます。

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 

### <a name="azure-cli"></a>Azure CLI
拒否効果に関連するリソース グループのイベントをすべて表示するには、次の CLI コマンドを使用できます。

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/deny/action\")"

監査効果に関連するすべてのイベントを表示するには、次の CLI コマンドを使用できます。

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/audit/action\")"

## <a name="view-a-policy"></a>ポリシーの表示
ポリシーを表示するには、PowerShell、Azure CLI、または REST API を使用します。 デプロイの失敗後にポリシーを表示し、デプロイを拒否したルールを確認することが必要な場合があります。 エラー メッセージには、ポリシー定義の ID が含まれています。

### <a name="powershell"></a>PowerShell
ポリシーを取得するには、次のコマンドレットを使用します。

    (Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}").Properties.policyRule | ConvertTo-Json

ポリシー定義の JSON が返されます。

### <a name="azure-cli"></a>Azure CLI
ポリシーを取得するには、次のコマンドを使用します。

    azure policy definition show {definition-name} --json

### <a name="rest-api"></a>REST API
ポリシーを取得するには、[ポリシー定義の取得](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get)操作を使用します。

## <a name="next-steps"></a>次のステップ
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。




<!--HONumber=Dec16_HO3-->


