---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 5a9d60cd019e044fd19a8670c0843a8d155f5433
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166596"
---
|Name |説明 |効果 |Version |
|---|---|---|---|
|[リソース グループにタグを追加する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddTag_ResourceGroup_Modify.json) |このタグがない任意のリソース グループが作成または更新されたときに、指定されたタグと値を追加します。 修復タスクをトリガーすることで、既存のリソース グループを修復できます。 タグに異なる値が含まれている場合、タグは変更されません。 |変更 |1.0.0 |
|[リソースにタグを追加する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddTag_Modify.json) |このタグがない任意のリソースが作成または更新されたときに、指定されたタグと値を追加します。 修復タスクをトリガーすることで、既存のリソースを修復できます。 タグに異なる値が含まれている場合、タグは変更されません。 リソース グループのタグは変更されません。 |変更 |1.0.0 |
|[リソース グループのタグを追加または置換する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddOrReplaceTag_ResourceGroup_Modify.json) |任意のリソース グループが作成または更新されたときに、指定されたタグと値を追加または置換します。 修復タスクをトリガーすることで、既存のリソース グループを修復できます。 |変更 |1.0.0 |
|[リソースのタグを追加または置換する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddOrReplaceTag_Modify.json) |任意のリソースが作成または更新されたときに、指定されたタグと値を追加または置換します。 修復タスクをトリガーすることで、既存のリソースを修復できます。 リソース グループのタグは変更されません。 |変更 |1.0.0 |
|[タグとその既定値の追加](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ApplyTag_Append.json) |このタグがない任意のリソースが作成または更新されたときに、指定されたタグと値を追加します。 これらのリソースが変更されるまで、このポリシーが適用される前に作成されたリソースのタグは変更されません。 リソース グループには適用されません。 既存のリソースのタグの修復をサポートする新しい "変更" 効果ポリシーが利用可能です (https://aka.ms/modifydoc) を参照してください)。 |append |1.0.0 |
|[タグとその既定値のリソース グループへの追加](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupApplyTag_Append.json) |このタグがない任意のリソース グループが作成または更新されたときに、指定されたタグと値を追加します。 これらのリソース グループが変更されるまで、このポリシーが適用される前に作成されたリソース グループのタグは変更されません。 既存のリソースのタグの修復をサポートする新しい "変更" 効果ポリシーが利用可能です (https://aka.ms/modifydoc) を参照してください)。 |append |1.0.0 |
|[タグとその値をリソース グループから追加](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_Append.json) |このタグがない任意のリソースが作成または更新されたときに、リソース グループの指定されたタグと値を追加します。 これらのリソースが変更されるまで、このポリシーが適用される前に作成されたリソースのタグは変更されません。 既存のリソースのタグの修復をサポートする新しい "変更" 効果ポリシーが利用可能です (https://aka.ms/modifydoc) を参照してください)。 |append |1.0.0 |
|[リソース グループからタグを継承する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_AddOrReplace_Modify.json) |任意のリソースが作成または更新されたときに、親リソース グループの指定されたタグと値を追加または置換します。 修復タスクをトリガーすることで、既存のリソースを修復できます。 |変更 |1.0.0 |
|[存在しない場合は、リソース グループからタグを継承する](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_Add_Modify.json) |このタグがない任意のリソースが作成または更新されたときに、親リソース グループの指定されたタグと値を追加します。 修復タスクをトリガーすることで、既存のリソースを修復できます。 タグに異なる値が含まれている場合、タグは変更されません。 |変更 |1.0.0 |
|[指定されたタグを必須にする](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/RequireTag_Deny.json) |タグの存在を強制します。 リソース グループには適用されません。 |deny |1.0.0 |
|[リソース グループで指定されたタグを必須にする](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupRequireTag_Deny.json) |リソース グループでタグの存在を強制します。 |deny |1.0.0 |
|[タグとその値が必要](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/RequireTagAndValue_Deny.json) |必要なタグとその値を強制的に適用します。 リソース グループには適用されません。 |deny |1.0.0 |
|[タグとその値がリソース グループに必要](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupRequireTagAndValue_Deny.json) |リソース グループで必要なタグとその値を強制します。 |deny |1.0.0 |
