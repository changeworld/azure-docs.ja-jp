---
title: "Azure リソース ポリシーに関する RequestDisallowedByPolicy エラー | Microsoft Docs"
description: "RequestDisallowedByPolicy エラーの原因について説明します。"
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 2e821c0369c6f01a7f09361c1093259429a79fa6
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2017
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Azure リソース ポリシーに関する RequestDisallowedByPolicy エラー

この記事では、RequestDisallowedByPolicy エラーの原因について説明し、このエラーの解決策も示します。

## <a name="symptom"></a>症状

デプロイ中にアクションを実行しようとすると、**RequestDisallowedByPolicy** エラーを受け取り、このアクションを完了できない場合があります。 次の例は、そのエラーを示します。

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>トラブルシューティング

次のいずれかの方法を使用して、デプロイをブロックしたポリシーに関する詳細を取得します。

### <a name="method-1"></a>方法 1

PowerShell では、そのポリシー識別子を `Id` パラメーターとして指定して、デプロイをブロックしたポリシーの詳細を取得します。

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>方法 2 

Azure CLI 2.0 では、ポリシー定義の名前を指定します。 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>解決策

IT 部門が、セキュリティとコンプライアンスに対応するために、Public IP Addresses、ネットワーク セキュリティ グループ、ユーザー定義ルート、ルート テーブルの作成を禁止するリソース ポリシーを適用している可能性があります。 「**症状**」セクション内のエラー メッセージが示しているのは、**regionPolicyDefinition** という名前のポリシーです。 ポリシーが別の名前である場合もあります。
この問題を解決するには、IT 部門と連携して、リソース ポリシーを確認し、それらのポリシーに従いながら要求されたアクションを実行する方法を判別します。

詳細については、次の記事を参照してください。

- [リソース ポリシーの概要](resource-manager-policy.md)
- [ポータルを通じてポリシーの割り当てを表示する](resource-manager-policy-portal.md)
