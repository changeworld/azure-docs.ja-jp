<properties
	pageTitle="Azure リソース マネージャーのポリシー | Microsoft Azure"
	description="Azure リソース マネージャーのポリシーを使用し、サブスクリプション、リソース グループまたは個々のリソースなどのさまざまなスコープの違反を防ぐ方法について説明します。"
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="02/26/2016"
	ms.author="gauravbh;tomfitz"/>

# ポリシーを使用したリソース管理とアクセス制御

Azure リソース マネージャーで、カスタム ポリシーを使用してアクセスを制御できるようになりました。ポリシーを使用すれば、組織のリソースを管理するために必要な規則に組織内のユーザーが違反するのを防ぐことができます。

ポリシー定義を作成し、行為やリソースを具体的に否認します。サブスクリプション、リソース グループ、個別リソースなど、任意の範囲でポリシー定義を割り当てます。

この記事では、ポリシーの作成で使用する、ポリシー定義言語の基本構造について説明します。その後、これらのポリシーをさまざまなスコープに適用する方法と、最後に REST API を使用してこれを実現する方法の例をいくつか紹介します。

現在、ポリシーはプレビューとして利用できます。

## RBAC との違いは何か。

ポリシーとロールベースのアクセス制御には大きな違いがいくつかありますが、最初に理解するべきことは、ポリシーと RBAC は連動するということです。ポリシーを使用するには、RBAC でユーザーを認証する必要があります。RBAC とは異なり、ポリシーは既定で許可し、明示的に否認するシステムです。

RBAC は、**ユーザー**がさまざまな範囲で実行できるアクションにフォーカスしています。たとえば、任意の範囲で、あるリソース グループの共同作成者ロールに特定のユーザーを追加すると、そのユーザーはそのリソース グループを変更できます。

ポリシーは、さまざまな範囲の**リソース** アクションにフォーカスしています。たとえば、ポリシーを介して、プロビジョニング可能なリソースの種類を制御したり、リソースをプロビジョニングできる場所を制限したりできます。

## 一般的なシナリオ

よくあるシナリオの 1 つは、配賦目的で部門別のタグが必要性な場合です。適切なコスト センターが関連付けられている場合にのみ、組織で操作を許可し、それ以外の場合は要求を拒否したい場合があります。これにより、実行された操作を適切なコスト センターから請求できるようになります。

別のよくあるシナリオに、組織が、リソースの作成場所を制御したい場合があります。または、特定の種類のリソースのみのプロビジョニングを許可することによって、リソースへのアクセスを制御したい場合があります。

同様に、組織がサービス カタログを制御したい場合や、リソースに希望の名前付け規則を適用したい場合があります。

これらのシナリオは、ポリシーを使用して次に示すように簡単に実現できます。

## ポリシー定義の構造

ポリシー定義は JSON を使用して作成します。これは、条件が満たされた場合に何が発生するかを伝えるアクションおよび効果を定義する 1 つ以上の条件および論理演算子で構成されます。[http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json) でスキーマが公開されています。

ポリシーには、基本的に次が含まれます。

**条件および論理演算子:** 一連の論理演算子を使用して操作できる条件のセットが含まれています。

**効果:** これには条件が満たされた場合の拒否または監査のいずれかの効果を示します。監査の効果は、警告イベント サービス ログを出力します。たとえば、管理者は、誰かがサイズの大きな VM を作成した場合に監査を実行するポリシーを作成し、後でログを確認することができます。

    {
      "if" : {
        <condition> | <logical operator>
      },
      "then" : {
        "effect" : "deny | audit"
      }
    }
    
## ポリシーの評価

HTTP PUT を使用してリソースの作成やテンプレートのデプロイメントが行われると、ポリシーが評価されます。テンプレートのデプロイメントの場合、ポリシーはテンプレート内の各リソースの作成時に評価されます。

注: Microsoft.Resources/deployments など、タグ、種別、場所をサポートしていないリソースの種類は、ポリシーによって評価されません。このサポートは将来、追加される予定です。下位互換性の問題を避けるために、ポリシーの作成時に種類を明示的に指定することを勧めします。たとえば、将来、リソースの種類が評価に追加されたときにタグをサポートしない入れ子になったリソースがある場合、種類を指定しないタグのポリシーがすべての種類に適用され、テンプレートのデプロイメントが失敗する可能性があります。

## 論理演算子

サポートされている論理演算子とその構文を次に示します。

| 演算子名 | 構文 |
| :------------- | :------------- |
| Not | "not" : {&lt;condition or operator &gt;} |
| 論理積 | "allOf" : [ {&lt;condition or operator &gt;},{&lt;condition or operator &gt;}] |
| または | "anyOf" : [ {&lt;condition or operator &gt;},{&lt;condition or operator &gt;}] |

リソース マネージャーでは、入れ子になった演算子を使用して複雑なロジックをポリシーに指定できます。たとえば、指定したリソースの種類の特定の場所でのリソースの作成を拒否できます。入れ子になった演算子の例を次に示します。

## 条件

条件は、**フィールド**または**ソース**が特定の基準を満たすかどうかを評価します。サポートされている条件名と構文を次に示します。

| 条件名 | 構文 |
| :------------- | :------------- |
| 等しい | "equals" : "&lt;value&gt;" |
| LIKE | "like" : "&lt;value&gt;" |
| 指定値を含む | "contains" : "&lt;value&gt;"|
| [ | "in" : [ "&lt;value1&gt;","&lt;value2&gt;" ]|
| ContainsKey | "containsKey" : "&lt;keyName&gt;" |

## フィールドおよびソース

条件は、フィールドおよびソースを使用して構成します。フィールドは、リソースの状態の記述に使用されるリソース要求ペイロード内のプロパティを表します。ソースは、要求自体の特性を表します。

次のフィールドおよびソースがサポートされています。

フィールド: **name**、**kind**、**type**、**location**、**tags**、**tags.***、**property alias**

ソース: **action**

プロパティのエイリアスは、設定や SKU など、リソースの種類固有のプロパティにアクセスするためにポリシー定義で使用できる名前です。プロパティが存在するすべての API バージョンで機能します。エイリアスは、次の REST API を使用して取得できます (今後、Powershell のサポートが追加される予定です)。

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
	
エイリアスの定義は次のようになります。ご覧のように、プロパティ名が変更された場合も、エイリアスではさまざまな API バージョンでのパスを定義します。

    "aliases": [
      {
        "name": "Microsoft.Storage/storageAccounts/sku.name",
        "paths": [
          {
            "path": "Properties.AccountType",
            "apiVersions": [ "2015-06-15", "2015-05-01-preview" ]
          }
        ]
      }
    ]

現在サポートされているエイリアスは次のとおりです。

| エイリアス名 | 説明 |
| ---------- | ----------- |
| {resourceType}/sku.name | サポートされているリソースの種類: Microsoft.Storage/storageAccounts、<br />Microsoft.Scheduler/jobcollections、<br />Microsoft.DocumentDB/databaseAccounts、<br />Microsoft.Cache/Redis、<br />Microsoft..CDN/profiles |
| {resourceType}/sku.family | サポートされているリソースの種類: Microsoft.Cache/Redis |
| {resourceType}/sku.capacity | サポートされているリソースの種類: Microsoft.Cache/Redis |
| Microsoft.Cache/Redis/enableNonSslPort | |
| Microsoft.Cache/Redis/shardCount | |


アクションの詳細については、「[RBAC - 組み込みのロール](active-directory/role-based-access-built-in-roles.md)」を参照してください。現時点では、ポリシーは、PUT 要求でのみ機能します。


## ポリシー定義の例

ここでは、上で挙げたシナリオを実現するポリシーを定義する方法を見てをみましょう。

### 配賦: 部門別のタグが必要

次のポリシーは、"costCenter" キーを含むタグがないすべての要求を拒否します。

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


### 地理的準拠: リソースの場所を確認

次では、場所が北ヨーロッパや西ヨーロッパでない、すべての要求を拒否するポリシーの例を示しています。

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

### サービスのキュレーション: サービス カタログを選択

次では、ソースの使用例を示しています。種類が Microsoft.Resources/*、Microsoft.Compute/*、Microsoft.Storage/*、Microsoft.Network/* のサービスのアクションのみが許可されることを示しています。その他は拒否されます。

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

### 承認された SKU の使用

次の例は、プロパティのエイリアスを使用して SKU を制限する方法を示しています。この例では、Standard\_LRS と Standard\_GRS のみ、ストレージ アカウントで使用することが認められます。

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
    

### 命名規則

次では、"like" の条件でサポートされているワイルドカードの使用例を示しています。この条件は、名前が示しているパターンと一致する場合 (namePrefix*nameSuffix)、要求を拒否するよう示しています。

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
    
### ストレージ リソースのみに対するタグ要件

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
                "source": "action",
                "like": "Microsoft.Storage/*"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## ポリシーの割り当て

ポリシーは、サブスクリプション、リソース グループおよび個々のリソースなどのさまざまなスコープを適用できます。ポリシーは、すべての子リソースが継承します。したがって、リソース グループに適用されたポリシーは、そのリソース グループのすべてのリソースに適用されます。

## ポリシーの作成

ここでは、REST API を使用して、ポリシーを作成する方法を詳細に説明します。

### REST API を使用したポリシー定義の作成

[ポリシー定義用の REST API](https://msdn.microsoft.com/library/azure/mt588471.aspx) を使用して、ポリシーを作成できます。REST API を使用すると、ポリシー定義の作成と削除、既存の定義に関する情報の取得を実行できます。

新しいポリシーを作成するには、次を実行します。

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

要求の本文は次のようになります。

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "name":"testdefinition"
    }


ポリシー定義は、前述の例のいずれかとして定義できます。api-version には、*2015-10-01-preview* を使用します。例と詳細については、[ポリシー定義用 REST API](https://msdn.microsoft.com/library/azure/mt588471.aspx) のページを参照してください。

### PowerShell を使用したポリシー定義の作成

以下に示されているように、New-AzureRmPolicyDefinition コマンドレットを使用して新しいポリシー定義を作成できます。以下の例では、リソースを北ヨーロッパと西ヨーロッパに限定できるポリシーを作成します。

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation onlyin certain regions" -Policy '{	"if" : {
    	    			    "not" : {
    	      			    	"field" : "location",
    	      			    		"in" : ["northeurope" , "westeurope"]
    	    			    	}
    	    		          },
    	      		    		"then" : {
    	    			    		"effect" : "deny"
    	      			    		}
    	    		    	}'    		

実行の出力は後でポリシーを割り当てるときに使用できるように、$policy オブジェクトに保存されます。ポリシー パラメーターの場合、以下に示されているように、ポリシー インラインを指定するのではなく、ポリシーが含まれている .json ファイルへのパスを提供することもできます。

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain 	regions" -Policy "path-to-policy-json-on-disk"


## ポリシーの適用

### REST API を使用したポリシーの割り当て

[ポリシーの割り当て用 REST API](https://msdn.microsoft.com/library/azure/mt588466.aspx) で、目的のスコープに対してポリシー定義を適用します。REST API を使用すると、ポリシー割り当ての作成と削除、既存の割り当てに関する情報の取得を実行できます。

新しいポリシー割り当てを作成するには、次を実行します。

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

ポリシー割り当ての名前は、{policy-assignment} です。api-version には、*2015-10-01-preview* を使用します。

要求の本文は次のようになります。

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "name":"VMPolicyAssignment"
    }

例と詳細については、[ポリシー割り当て用 REST API](https://msdn.microsoft.com/library/azure/mt588466.aspx) のページをご覧ください。

### PowerShell を使用したポリシーの割り当て

以下に示されているように、New-AzureRmPolicyAssignment コマンドレットを使用して、上記で作成したポリシーを PowerShell を介して目的のスコープに適用できます。

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
ここで、$policy は上記のように New-AzureRmPolicyDefinition コマンドレットを実行した結果として返されたポリシー オブジェクトです。ここでのスコープは、指定するリソース グループの名前です。

上記のポリシー割り当てを削除するには、次のようにします。

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Get-AzureRmPolicyDefinition、Set-AzureRmPolicyDefinition、および Remove-AzureRmPolicyDefinition コマンドレットをそれぞれ使用して、ポリシー定義を取得、変更、または削除できます。

同様に、Get-AzureRmPolicyAssignment、Set-AzureRmPolicyAssignment、および Remove-AzureRmPolicyAssignment コマンドレットをそれぞれ使用して、ポリシーの割り当てを取得、変更、または削除することもできます。

##ポリシー監査イベント

ポリシーを適用した後、ポリシー関連イベントの表示を開始します。このデータを取得するには、ポータルに移動するか、PowerShell を使用します。

拒否効果に関連するすべてのイベントを表示するには、次のコマンドを使用できます。

    Get-AzureRmLog | where {$_.subStatus -eq "Forbidden"}     

監査効果に関連するすべてのイベントを表示するには、次のコマンドを使用できます。

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 
    

<!---HONumber=AcomDC_0330_2016------>