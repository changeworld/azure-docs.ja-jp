---
title: ポリシーへの準拠の監査と管理
titleSuffix: Azure Machine Learning
description: ワークスペースが要件に準拠していることを確認するために、Azure Machine Learning の組み込みポリシーを使用するように、Azure Policy を使用する方法について説明します。
author: aashishb
ms.author: aashishb
ms.date: 03/25/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: f708e2181511da97ecffcd6f1636a2b232b4fbc6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105544368"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Azure Policy を使用した Azure Machine Learning の監査と管理

[Azure Policy](../governance/policy/index.yml) は、Azure リソースがポリシーに準拠していることを確認できるガバナンス ツールです。 Azure Machine Learning を使用すると、次のポリシーを割り当てることができます。

| ポリシー | [説明] |
| ----- | ----- |
| **カスタマー マネージド キー** | ワークスペースでカスタマー マネージド キーを使用する必要があることを監査または適用します。 |
| **プライベート リンク** | ワークスペースで仮想ネットワークとの通信にプライベート エンドポイントが使用されているかどうかを監査または強制します。 |
| **プライベート エンドポイント** | プライベート エンドポイントを作成する必要がある Azure Virtual Network サブネットを構成します。 |
| **プライベート DNS ゾーン** | プライベート リンクに使用するプライベート DNS ゾーンを構成します。 |
| **ユーザー割り当てマネージド ID** | ワークスペースでユーザー割り当てのマネージド ＩＤ が使用されているかどうかを監査または強制します。 |

ポリシーは、サブスクリプション レベルやリソース グループ レベルなど、さまざまなスコープで設定できます。 詳細については、[Azure Policy のドキュメント](../governance/policy/overview.md)を参照してください。

## <a name="built-in-policies"></a>組み込みのポリシー

Azure Machine Learning には、Azure Machine Learning を使用した一般的なシナリオに使用できる一連のポリシーが用意されています。 これらのポリシー定義は、既存のサブスクリプションに割り当てることも、独自のカスタム定義を作成するための基礎として使用することもできます。 Azure Machine Learning の組み込みポリシーの詳細な一覧については、[Azure Machine Learning の組み込みポリシー](../governance/policy/samples/built-in-policies.md#machine-learning)に関するページを参照してください。

Azure Machine Learning に関連する組み込みのポリシー定義を表示するには、次の手順を使用します。

1. [Azure portal](https://portal.azure.com) で __Azure Policy__ に移動します。
1. __[定義]__ を選択します。
1. __[種類]__ で _[組み込み]_ を選択し、 __[カテゴリ]__ で __[Machine Learning]__ を選択します。

ここから、ポリシー定義を選択して表示することができます。 定義を表示しているときに、 __[割り当て]__ リンクを使用して、ポリシーを特定のスコープに割り当て、ポリシーのパラメーターを構成することができます。 詳細については、「[ポリシーの割り当て - ポータル](../governance/policy/assign-policy-portal.md)」を参照してください。

[Azure PowerShell](../governance/policy/assign-policy-powershell.md)、[Azure CLI](../governance/policy/assign-policy-azurecli.md)、および[テンプレート](../governance/policy/assign-policy-template.md)を使用してポリシーを割り当てることもできます。

## <a name="workspace-encryption-with-customer-managed-key"></a>カスタマー マネージド キーによるワークスペースの暗号化

ワークスペースをカスタマー マネージド キーで暗号化するか、Microsoft マネージド キーを使用してメトリックとメタデータを暗号化するかを制御します。 カスタマーマネージド キーの使用の詳細については、データ暗号化の記事の [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) に関するセクションをご覧ください。

このポリシーを構成するには、effect パラメーターを __audit__ または __deny__ に設定します。 __audit__ に設定すると、カスタマー マネージド キーを使用せずにワークスペースを作成できます。また、アクティビティ ログに警告イベントが作成されます。

ポリシーが __deny__ に設定されている場合は、カスタマー マネージド キーを指定しないとワークスペースを作成できません。 カスタマー マネージド キーを使用せずにワークスペースを作成しようとすると、`Resource 'clustername' was disallowed by policy` のようなエラーが発生し、アクティビティ ログにエラーが作成されます。 このエラーの一部としてポリシー識別子も返されます。

## <a name="workspace-should-use-private-link"></a>プライベート リンクを使用する必要があるワークスペース

ワークスペースで Azure Private Link を使用して Azure Virtual Network と通信するかどうかを制御します。 プライベート リンクの使用の詳細については、[ワークスペースのプライベート リンクの構成](how-to-configure-private-link.md)に関するページを参照してください。

このポリシーを構成するには、effect パラメーターを __audit__ または __deny__ に設定します。 __audit__ に設定すると、プライベート リンクを使用せずにワークスペースを作成できます。また、アクティビティ ログに警告イベントが作成されます。

ポリシーが __deny__ に設定されている場合、プライベート リンクを使用しないと、ワークスペースを作成できません。 プライベート リンクなしでワークスペースを作成しようとすると、エラーが発生します。 このエラーは、アクティビティ ログにも記録されます。 このエラーの一部としてポリシー識別子が返されます。

## <a name="workspace-should-use-private-endpoint"></a>プライベート エンドポイントを使用する必要があるワークスペース

Azure Virtual Network の指定したサブネット内にプライベート エンドポイントを作成するようにワークスペースを構成します。

このポリシーを構成するには、effect パラメーターを __DeployIfNotExists__ に設定します。 __privateEndpointSubnetID__ をサブネットの Azure Resource Manager ID に設定します。
## <a name="workspace-should-use-private-dns-zones"></a>プライベート DNS ゾーンを使用する必要があるワークスペース

プライベート DNS ゾーンを使用するようにワークスペースを構成し、プライベート エンドポイントの既定の DNS 解決を上書きします。

このポリシーを構成するには、effect パラメーターを __DeployIfNotExists__ に設定します。 __privateDnsZoneId__ を、使用するプライベート DNS ゾーンの Azure Resource Manager ID に設定します。 

## <a name="workspace-should-use-user-assigned-managed-identity"></a>ユーザー割り当てのマネージド ＩＤ を使用する必要があるワークスペース

システム割り当てのマネージド ID (既定値) とユーザー割り当てのマネージド ID のどちらを使用してワークスペースを作成するかを制御します。 ワークスペースのマネージド ID は、Azure Storage、Azure Container Registry、Azure Key Vault、Azure Application Insights などの関連リソースにアクセスするために使用されます。 詳細については、「[Azure Machine Learning でマネージド ID を使用する](how-to-use-managed-identities.md)」を参照してください。

このポリシーを構成するには、effect パラメーターを __audit__、__deny__、または __disabled__ に設定します。 __audit__ に設定すると、ユーザー割り当てのマネージド ID を指定せずにワークスペースを作成できます。 システムによって割り当てられた ID が使用され、アクティビティ ログに警告イベントが作成されます。

ポリシーが __deny__ に設定されている場合、作成プロセス中にユーザー割り当ての ID を指定しない限り、ワークスペースを作成することはできません。 ユーザー割り当て ID を指定せずにワークスペースを作成しようとすると、エラーが発生します。 このエラーは、アクティビティ ログにも記録されます。 このエラーの一部としてポリシー識別子が返されます。

## <a name="next-steps"></a>次のステップ

* [Azure Policy のドキュメント](../governance/policy/overview.md)
* [Azure Machine Learning の組み込みポリシー](policy-reference.md)
* [Azure Security Center でのセキュリティ ポリシーの操作](../security-center/tutorial-security-policy.md)