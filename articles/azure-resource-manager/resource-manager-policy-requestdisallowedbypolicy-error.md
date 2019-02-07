---
title: Azure リソース ポリシーに関する RequestDisallowedByPolicy エラー | Microsoft Docs
description: RequestDisallowedByPolicy エラーの原因について説明します。
services: azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2d55d5958568deef5ce350c9678e55e2e37a2ff8
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55496399"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Azure リソース ポリシーに関する RequestDisallowedByPolicy エラー

この記事では、RequestDisallowedByPolicy エラーの原因について説明し、このエラーの解決策も示します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>症状

デプロイ中に、**RequestDisallowedByPolicy** エラーを受け取り、リソースを作成できないことがあります。 次の例は、そのエラーを示します。

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

### <a name="powershell"></a>PowerShell

PowerShell では、そのポリシー識別子を `Id` パラメーターとして指定して、デプロイをブロックしたポリシーの詳細を取得します。

```PowerShell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

Azure CLI では、ポリシー定義の名前を指定します。

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>解決策

セキュリティやコンプライアンスについて、サブスクリプション管理者はリソースのデプロイ方法を制限するポリシーを割り当てることができます。 たとえば、サブスクリプションには、パブリック IP アドレス、ネットワーク セキュリティ グループ、ユーザー定義ルート、またはルート テーブルの作成を禁止するポリシーがある場合があります。 **[現象]** セクションのエラー メッセージは、ポリシーの名前を示します。
この問題を解決するには、リソース ポリシーを確認し、それらのポリシーに準拠しているリソースのデプロイ方法を決定します。

詳細については、次の記事を参照してください。

- [Azure Policy とは](../azure-policy/azure-policy-introduction.md)
- [コンプライアンスを強制するポリシーの作成と管理](../azure-policy/create-manage-policy.md)
