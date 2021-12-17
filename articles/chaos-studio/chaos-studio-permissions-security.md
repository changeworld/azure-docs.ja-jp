---
title: Azure Chaos Studio のアクセス許可とセキュリティ
description: Azure Chaos Studio でのアクセス許可のしくみと、偶発的なフォールト挿入からリソースをセキュリティで保護する方法について理解します。
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: fa547771b125b17d05e6a615f01cecc899cba7e0
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372210"
---
# <a name="permissions-and-security-in-azure-chaos-studio"></a>Azure Chaos Studio でのアクセス許可とセキュリティ

Azure Chaos Studio を使用して、Azure リソースにフォールトを体系的に挿入することで、サービスの回復性を向上させることができます。 フォールト挿入は、サービスの回復性を向上させる強力な方法ですが、危険を伴う可能性もあります。 アプリケーションで障害を発生させると、当初意図したより多くの影響を与え、悪意のあるアクターにアプリケーションに侵入する機会を与える可能性があります。 Chaos Studio には、誤って、または不正なアクターによってフォールトが実行されないようにする堅牢なアクセス許可モデルがあります。 この記事では、Chaos Studio を使用して、フォールト挿入のターゲットとなるリソースをセキュリティで保護する方法について学習します。

## <a name="how-can-i-restrict-the-ability-to-inject-faults-with-chaos-studio"></a>Chaos Studio でフォールトを挿入する機能を制限するにはどうすればよいですか。

Chaos Studio には 3 つのレベルのセキュリティがあり、リソースに対してフォールト挿入がいつどのように発生する可能性があるかを制御するのに役立ちます。

まず、カオス実験は、リージョン、リソース グループ、サブスクリプションにデプロイされる Azure リソースです。 ユーザーは、実験を作成、更新、開始、キャンセル、削除、または表示するための適切な Azure Resource Manager アクセス許可を持っている必要があります。 各アクセス許可は、ID に対して細かく割り当てたり、ワイルドカード アクセス許可を持つロールの一部として割り当てたりできる ARM 操作です。 たとえば、Azure の共同作成者ロールには、割り当てられたスコープでの */write アクセス許可があります。これには Microsoft.Chaos/experiments/write アクセス許可が含まれます。 リソースに対してフォールトを挿入する機能を制御しようとする場合、制限する最も重要な操作は、Microsoft.Chaos/experiments/start/action です。この操作では、フォールトを挿入するカオス実験が開始されるためです。

次に、カオス実験には、リソースに対してフォールトを実行する[システム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) があります。 実験を作成すると、システム割り当てマネージド ID は、アクセス許可のない Azure Active Directory テナントに作成されます。 カオス実験を実行する前に、すべてのターゲット リソースに対する[適切なアクセス許可](chaos-studio-fault-providers.md)をその ID に付与する必要があります。 リソースに対する適切なアクセス許可が実験 ID にない場合、そのリソースに対してフォールトを実行することはできません。

最後に、各リソースは、[対応する機能が有効になっているターゲット](chaos-studio-targets-capabilities.md)として Chaos Studio にオンボードにする必要があります。 ターゲットまたは実行されているフォールトの機能が存在しない場合、実験はリソースに影響を与えることなく失敗します。

## <a name="agent-authentication"></a>エージェントの認証

エージェントベース フォールトを実行する場合は、仮想マシンまたは仮想マシン スケール セットに Chaos Studio エージェントをインストールする必要があります。 エージェンでは、[ユーザー割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用して Chaos Studio を認証し、''*エージェントプ ロファイル*'' を使用して特定の VM リソースとの関係を確立します。 エージェントベース フォールトに対して仮想マシンまたは仮想マシン スケール セットをオンボードする場合は、まずエージェント ターゲットを作成します。 エージェント ターゲットには、認証に使用されるユーザー割り当てのマネージド ID への参照が含まれている必要があります。 エージェント ターゲットには、エージェントのインストール時に構成として指定される ''*エージェント プロファイル ID*'' が含まれます。 エージェント プロファイルはターゲットごとに一意であり、ターゲットはリソースごとに一意となります。

## <a name="arm-operations-and-roles"></a>ARM の操作とロール

Chaos Studio には次の操作があります。

| Operation | 説明 |
| -- | -- |
| Microsoft.Chaos/targets/[Read,Write,Delete] | ターゲットを取得、作成、更新、または削除します。 |
| Microsoft.Chaos/targets/capabilities/[Read,Write,Delete] | 機能を取得、作成、更新、または削除します。 |
| Microsoft.Chaos/locations/targetTypes/Read | すべてのターゲットの種類を取得します。 |
| Microsoft.Chaos/locations/targetTypes/capabilityTypes/Read | すべての機能の種類を取得します。 |
| Microsoft.Chaos/experiments/[Read,Write,Delete] | カオス実験を取得、作成、更新、または削除します。 |
| Microsoft.Chaos/experiments/start/action | カオス実験を開始します。 |
| Microsoft.Chaos/experiments/cancel/action | カオス実験を停止します。 |
| Microsoft.Chaos/experiments/statuses/Read | カオス実験の実行状態を取得します。 |
| Microsoft.Chaos/experiments/executionDetails/Read | カオス実験の実行について、実行の詳細 (各アクションの状態とエラー) を取得します。 |

これらのアクセス許可を細かく割り当てるために、[カスタム ロールを作成する](../role-based-access-control/custom-roles.md)ことができます。

## <a name="network-security"></a>ネットワークのセキュリティ

Chaos Studio とのユーザーのすべてのやり取りは、Azure Resource Manager を通して行われます。 ユーザーが実験を開始した場合、実験では、フォールトに応じて Resource Manager 以外のエンドポイントとやり取りすることがあります。
* サービス直接フォールト - ほとんどのサービス直接フォールトは Azure Resource Manager を通じて実行されます。 ターゲット リソースには、許可リストに含まれるネットワーク エンドポイントは必要ありません。
* サービス直接 AKS Chaos Mesh フォールト - Chaos Mesh を使用する Azure Kubernetes Service のサービス直接フォールトには、AKS クラスターに公開されている Kubernetes API サーバーを含めるためのアクセスが必要です。 [IP 範囲のセットへの AKS ネットワーク アクセスを制限する方法については、こちらを参照してください。](../aks/api-server-authorized-ip-ranges.md)
* エージェントベース フォールト - エージェントベース フォールトには、Chaos Studio エージェント サービスへのエージェント アクセスが必要です。 エージェントを正常に接続するには、仮想マシンまたは仮想マシン スケール セットに http://agentcommunicationservice-frontdoor-canary.trafficmanager.net への発信アクセスが必要です。

Azure Chaos Studio では、サービス タグやプライベート リンクはサポートされていません。

## <a name="data-encryption"></a>データの暗号化

Chaos Studio によって、既定ですべてのデータが暗号化されます。 Chaos Studio では、マネージド ID オブジェクト ID、実験、ステップおよびブランチの名前、フォールト パラメーター (ネットワーク切断フォールトでブロックするネットワーク ポート範囲など) のようなシステム プロパティの入力のみが受け入れられます。 これらのプロパティを使用して、支払い情報やパスワードなどの機微なデータを格納することはできません。 Chaos Studio でのデータの保護方法について詳しくは、[Azure での顧客データの保護に関する記事](../security/fundamentals/protection-customer-data.md)を参照してください。

## <a name="next-steps"></a>次のステップ
これでカオス実験をセキュリティで保護する方法を理解できたので、次のことを行う準備が整いました。
- [最初の実験を作成して実行する](chaos-studio-tutorial-service-direct-portal.md)
- [最初の Azure Kubernetes Service の実験を作成して実行する](chaos-studio-tutorial-aks-portal.md)
