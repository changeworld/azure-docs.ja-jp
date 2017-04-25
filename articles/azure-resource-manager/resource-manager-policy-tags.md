---
title: "タグに関する Azure リソース ポリシー | Microsoft Docs"
description: "リソースのタグを管理するためのリソース ポリシーの例を示します"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 6e71fd9eda822478fa0555aa44908a4094fe8de2
ms.lasthandoff: 03/31/2017


---
# <a name="apply-resource-policies-for-tags"></a>タグに関するリソース ポリシーを適用する

このトピックでは、リソースのタグが一貫性を持って使用されることを保証するために適用できる一般的なポリシー ルールについて説明します。

既存のリソースがあるリソース グループまたはサブスクリプションにタグ ポリシーを適用しても、これらのリソースにポリシーがさかのぼって適用されることはありません。 これらのリソースにポリシーを適用するには、「[既存のリソースに対する更新をトリガーする](#trigger-updates-to-existing-resources)」の説明に従って、既存のリソースに対する更新をトリガーします。

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>リソース グループ内のすべてのリソースにタグ/値があることを確認する

一般的な要件は、リソース グループ内のすべてのリソースに特定のタグと値があることです。 多くの場合、この要件は、部門別にコストを追跡するために必要です。 次の条件を満たす必要があります。

* 既存のタグを持たない新規および更新されたリソースには、必要なタグと値が追加されます。
* 必要なタグと値ではないその他のタグと値を持つ新規および更新されたリソースには、必要なタグと値が追加されます。
* 既存のリソースから必要なタグと値を削除することはできません。

この要件は、リソース グループに次の 3 つのポリシーを適用することで対応できます。

* [タグを追加する](#append-tag) 
* [タグとその他のタグを追加する](#append-tag-with-other-tags)
* [必要なタグと値](#require-tag-and-value)

### <a name="append-tag"></a>タグを追加する

次のポリシー ルールは、タグが存在しない場合に定義済みの値を持つ costCenter タグを追加します。

```json
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
```

### <a name="append-tag-with-other-tags"></a>タグとその他のタグを追加する

次のポリシー ルールは、タグが存在する場合に、定義済みの値を持つ costCenter タグを追加しますが、costCenter タグは定義されていません。

```json
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
```

### <a name="require-tag-and-value"></a>必要なタグと値

次のポリシー ルールは、定義済みの値に割り当てられた costCenter タグがないリソースの更新または作成を拒否します。

```json
{
  "if": {
    "not": {
      "field": "tags.costCenter",
      "equals": "myDepartment"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="require-tags-for-a-resource-type"></a>リソースの種類に対してタグを要求する
次の例は、論理演算子を入れ子にして、特定の種類のリソース (ここではストレージ アカウント) にのみアプリケーション タグを要求する方法を示しています。

```json
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
```

## <a name="require-tag"></a>タグを要求する
次のポリシーは、"costCenter" キーを含むタグがない要求を拒否します (任意の値を適用できます)。

```json
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
```

## <a name="trigger-updates-to-existing-resources"></a>既存のリソースに対する更新をトリガーする

次の PowerShell スクリプトは、既存のリソースに対して、追加したタグ ポリシーを適用する更新をトリガーします。

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($_.Tags -eq $NULL) { @{}} else {$_.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="next-steps"></a>次のステップ
* (上記の例で示すように) ポリシー規則を定義した後、ポリシー定義を作成してスコープに割り当てる必要があります。 スコープには、サブスクリプション、リソース グループ、またはリソースを使用できます。 ポータルでポリシーを割り当てる方法については、「[Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md)」(Azure Portal によるリソース ポリシーの割り当てと管理) を参照してください。 REST API、PowerShell、Azure CLI でポリシーを割り当てる方法については、「[Assign and manage policies through script](resource-manager-policy-create-assign.md)」(スクリプトによるポリシーの割り当てと管理) を参照してください。
* リソース ポリシーの概要については、[リソース ポリシーの概要](resource-manager-policy.md)に関するページを参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。


