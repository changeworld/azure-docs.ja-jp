---
title: Azure DDoS Protection Standard 用組み込みポリシー定義
description: Azure DDoS Protection Standard 用の Azure Policy 組み込みポリシー定義を一覧表示します。 これらの組み込みポリシー定義は、Azure リソースを管理するための一般的な方法を示します。
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: 5519d72d4d6d33381ce4381c7fd4e65c4d94d01f
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807946"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Azure DDoS Protection Standard 用 Azure Policy 組み込みポリシー定義

このページは、Azure DDoS Protection Standard 用の [Azure Policy](../governance/policy/overview.md) 組み込みポリシー定義のインデックスです。 他のサービス用の Azure Policy 組み込みについては、[Azure Policy 組み込み定義](../governance/policy/samples/built-in-policies.md)に関するページをご覧ください。

各組み込みポリシー定義の名前は、Azure portal のポリシー定義にリンクしています。 **[バージョン]** 列のリンクを使用すると、[Azure Policy GitHub リポジトリ](https://github.com/Azure/azure-policy)のソースを表示できます。

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想ネットワークを Azure DDoS Protection Standard によって保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Azure DDoS Protection Standard を使用して、仮想ネットワークを帯域幅消費およびプロトコル攻撃から保護します。 詳細については、[https://aka.ms/ddosprotectiondocs](./ddos-protection-overview.md) を参照してください。|Modify、Audit、Disabled|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[パブリック IP アドレスでは Azure DDoS Protection Standard のリソース ログが有効になっている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|診断設定で、パブリック IP アドレスのリソース ログを Log Analytics ワークスペースにストリーム配信できるようになります。 通知、レポート、フロー ログを使用して、攻撃のトラフィックと DDoS 攻撃を軽減するために取られた処置に関する詳細を表示します。|AuditIfNotExists、DeployIfNotExists、Disabled|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>次のステップ

- [Azure Policy GitHub リポジトリ](https://github.com/Azure/azure-policy)のビルトインを参照します。
- 「[Azure Policy の定義の構造](../governance/policy/concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../governance/policy/concepts/effects.md)」を確認します。
