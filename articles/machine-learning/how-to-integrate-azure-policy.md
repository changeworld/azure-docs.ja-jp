---
title: Azure Policy を使用したコンプライアンスの監査と管理
titleSuffix: Azure Machine Learning
description: Azure Policy を使用して、Azure Machine Learning の組み込みポリシーを使用する方法について説明します。
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: e7eebb22efe32b290e078348337049c6c3e762db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90993293"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Azure Policy を使用した Azure Machine Learning の監査と管理

[Azure Policy](/azure/governance/policy) は、Azure リソースがポリシーに準拠していることを確認できるガバナンス ツールです。 Azure Machine Learning を使用すると、次のポリシーを割り当てることができます。

* **カスタマー マネージド キー**: ワークスペースでカスタマー マネージド キーを使用する必要があることを監査または適用します。
* **Private Link**: ワークスペースで仮想ネットワークとの通信にプライベート エンドポイントが使用されているかどうかを監査します。

ポリシーは、サブスクリプション レベルやリソース グループ レベルなど、さまざまなスコープで設定できます。 詳細については、[Azure Policy のドキュメント](/azure/governance/policy/overview)を参照してください。

## <a name="built-in-policies"></a>組み込みのポリシー

Azure Machine Learning には、Azure Machine Learning を使用した一般的なシナリオに使用できる一連のポリシーが用意されています。 これらのポリシー定義は、既存のサブスクリプションに割り当てることも、独自のカスタム定義を作成するための基礎として使用することもできます。 Azure Machine Learning の組み込みポリシーの詳細な一覧については、[Azure Machine Learning の組み込みポリシー](/azure/governance/policy/samples/built-in-policies#machine-learning)に関するページを参照してください。

Azure Machine Learning に関連する組み込みのポリシー定義を表示するには、次の手順を使用します。

1. [Azure portal](https://portal.azure.com) で __Azure Policy__ に移動します。
1. __[定義]__ を選択します。
1. __[種類]__ で _[組み込み]_ を選択し、 __[カテゴリ]__ で __[Machine Learning]__ を選択します。

ここから、ポリシー定義を選択して表示することができます。 定義を表示しているときに、 __[割り当て]__ リンクを使用して、ポリシーを特定のスコープに割り当て、ポリシーのパラメーターを構成することができます。 詳細については、「[ポリシーの割り当て - ポータル](/azure/governance/policy/assign-policy-portal)」を参照してください。

[Azure PowerShell](/azure/governance/policy/assign-policy-powershell)、[Azure CLI](https://docs.microsoft.com/azure/governance/policy/assign-policy-azurecli)、および[テンプレート](/azure/governance/policy/assign-policy-template)を使用してポリシーを割り当てることもできます。

## <a name="workspaces-encryption-with-customer-managed-key"></a>カスタマーマネージド キーによるワークスペースの暗号化

ワークスペースをカスタマーマネージド キー (CMK) で暗号化するか、Microsoft マネージド キーを使用してメトリックとメタデータを暗号化するかを制御します。 CMK の使用の詳細については、エンタープライズ セキュリティの記事の [Azure Cosmos DB](concept-enterprise-security.md#azure-cosmos-db) に関するセクションを参照してください。

このポリシーを構成するには、effect パラメーターを __audit__ または __deny__ に設定します。 __audit__ に設定すると、CMK を使用せずにワークスペースを作成できます。また、アクティビティ ログに警告イベントが作成されます。

ポリシーが __deny__ に設定されている場合、CMK を指定しないのであれば、ワークスペースを作成できません。 CMK を使用せずにワークスペースを作成しようとすると、`Resource 'clustername' was disallowed by policy` のようなエラーが発生し、アクティビティ ログにエラーが作成されます。 このエラーの一部としてポリシー識別子も返されます。

## <a name="workspaces-should-use-private-link"></a>プライベート リンクを使用する必要があるワークスペース

ワークスペースで Azure Private Link を使用して Azure Virtual Network と通信するかどうかを制御します。 プライベート リンクの使用の詳細については、[ワークスペースのプライベート リンクの構成](how-to-configure-private-link.md)に関するページを参照してください。

このポリシーを構成するには、effect パラメーターを __audit__ に設定します。 プライベート リンクを使用せずにワークスペースを作成すると、アクティビティ ログに警告イベントが作成されます。

## <a name="next-steps"></a>次のステップ

* [Azure Policy のドキュメント](/azure/governance/policy/overview)
* [Azure Machine Learning の組み込みポリシー](policy-reference.md)
* [Azure Security Center でのセキュリティ ポリシーの操作](/azure/security-center/tutorial-security-policy)