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
	ms.date="11/10/2015"
	ms.author="gauravbh;tomfitz"/>

# ポリシーを使用したリソース管理とアクセス制御

Azure リソース マネージャーで、カスタム ポリシーを使用してアクセスを制御できるようになりました。ポリシーを使用すれば、組織のリソースを管理するために必要な規則に組織内のユーザーが違反するのを防ぐことができます。

ポリシー定義を作成し、行為やリソースを具体的に否認します。サブスクリプション、リソース グループ、個別リソースなど、任意の範囲でポリシー定義を割り当てます。

この記事では、ポリシーの作成で使用する、ポリシー定義言語の基本構造について説明します。その後、これらのポリシーをさまざまなスコープに適用する方法と、最後には REST API を使用してこれを実現する方法の例をいくつか紹介します。

現在、ポリシーはプレビューとして利用できます。

## RBAC との違いは何か。

ポリシーとロールベースのアクセス制御には大きな違いがいくつかありますが、最初に理解するべきことは、ポリシーと RBAC は連動するということです。ポリシーを使用するには、RBAC でユーザーを認証する必要があります。RBAC とは異なり、ポリシーは既定で許可し、明示的に否認するシステムです。

RBAC は、**ユーザー**がさまざまな範囲で実行できるアクションにフォーカスしています。たとえば、任意の範囲で、あるリソース グループの共同作成者ロールに特定のユーザーを追加すると、そのユーザーはそのリソース グループを変更できます。

ポリシーは、さまざまな範囲の**リソース** アクションにフォーカスしています。たとえば、ポリシーを介して、プロビジョニング可能なリソースの種類を制御したり、リソースをプロビジョニングできる場所を制限したりできます。

## 一般的なシナリオ

よくあるシナリオの 1 つは、チャージバック目的で部門別のタグが必要性な場合です。適切なコスト センターが関連付けられている場合にのみ、組織で操作を許可し、それ以外の場合は要求を拒否したい場合があります。これにより、実行された操作を適切なコスト センターから請求できるようになります。

別のよくあるシナリオに、組織が、リソースの作成場所を制御したい場合があります。または、特定の種類のリソースのみのプロビジョニングを許可することによって、リソースへのアクセスを制御したい場合があります。

同様に、組織がサービス カタログを制御したい場合や、リソースに希望の名前付け規則を適用したい場合があります。

これらのシナリオは、ポリシーを使用して次に示すように簡単に実現できます。

## ポリシー定義の構造

ポリシー定義は JSON を使用して作成します。これは、条件が満たされた場合に何が発生するかを伝えるアクションおよび効果を定義する 1 つ以上の条件および論理演算子で構成されます。

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


## 論理演算子

サポートされている論理演算子とその構文を次に示します。

| 演算子名 | 構文 |
| :------------- | :------------- |
| Not | "not" : {&lt;condition or operator &gt;} |
| 論理積 | "allOf" : [ {&lt;condition1&gt;},{&lt;condition2&gt;}] |
| または | "anyOf" : [ {&lt;condition1&gt;},{&lt;condition2&gt;}] |

入れ子の条件はサポートされていません。

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

条件は、フィールドおよびソースを使用して構成します。フィールドは、リソース要求ペイロードのプロパティを表します。ソースは、要求自体の特性を表します。

次のフィールドおよびソースがサポートされています。

フィールド: **name**、**kind**、**type**、**location**、**tags**、**tags.***。

ソース: **action**

アクションの詳細については、「[RBAC - 組み込みのロール](active-directory/role-based-access-built-in-roles.md)」を参照してください。

## ポリシー定義の例

ここでは、上で挙げたシナリオを実現するポリシーを定義する方法を見てをみましょう。

### チャージバック:、部門別のタグが必要

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
              "source" : "action",
              "like" : "Microsoft.Resources/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Compute/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Storage/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
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
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
      "type":"Microsoft.Authorization/policyDefinitions",
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
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyAssignments/VMPolicyAssignment",
      "type":"Microsoft.Authorization/policyAssignments",
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

<!---HONumber=Nov15_HO3-->