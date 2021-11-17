---
title: Azure Machine Learning の監査と管理
titleSuffix: Azure Machine Learning
description: ワークスペースが要件に準拠していることを確認するために、Azure Machine Learning の組み込みポリシーを使用するように、Azure Policy を使用する方法について説明します。
author: aashishb
ms.author: aashishb
ms.date: 10/21/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: ea374a6503b2d015ca92d3aee8179b3511c9755f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289682"
---
# <a name="audit-and-manage-azure-machine-learning"></a>Azure Machine Learning の監査と管理

チームが Azure Machine Learning の共同作業を行うときには、リソースの構成や編成について、さまざまな要件に直面することがあります。 機械学習チームは、コラボレーションのためにワークスペースを編成したり、コンピューティング クラスターのサイズを実際のユース ケースの要件に合わせたりする方法に柔軟性があることを望む場合があります。 これらのシナリオでは、アプリケーション チームが専用のインフラストラクチャを管理できる場合に最大の生産性に達する可能性があります。

プラットフォーム管理者は、ポリシーを使用して、チームが独自のリソースを管理するためのガードレールを配置できます。 [Azure Policy](../governance/policy/index.yml) は、リソースの状態を監査および管理するのに役立ちます。 この記事では、Azure Machine Learning に対して使用できる監査制御とガバナンス プラクティスについて説明します。

## <a name="policies-for-azure-machine-learning"></a>Azure Machine Learning のためのポリシー

[Azure Policy](../governance/policy/index.yml) は、Azure リソースがポリシーに準拠していることを確認できるガバナンス ツールです。

Azure Machine Learning には、Azure Machine Learning を使用した一般的なシナリオに使用できる一連のポリシーが用意されています。 これらのポリシー定義は、既存のサブスクリプションに割り当てることも、独自のカスタム定義を作成するための基礎として使用することもできます。

次の表には、Azure Machine Learning に割り当てることができるポリシーの選択肢が含まれています。 Azure Machine Learning の組み込みポリシーの詳細な一覧については、[Azure Machine Learning の組み込みポリシー](../governance/policy/samples/built-in-policies.md#machine-learning)に関するページを参照してください。

| ポリシー | [説明] |
| ----- | ----- |
| **カスタマー マネージド キー** | ワークスペースでカスタマー マネージド キーを使用する必要があることを監査または適用します。 |
| **プライベート リンク** | ワークスペースで仮想ネットワークとの通信にプライベート エンドポイントが使用されているかどうかを監査または強制します。 |
| **プライベート エンドポイント** | プライベート エンドポイントを作成する必要がある Azure Virtual Network サブネットを構成します。 |
| **プライベート DNS ゾーン** | プライベート リンクに使用するプライベート DNS ゾーンを構成します。 |
| **ユーザー割り当てマネージド ID** | ワークスペースでユーザー割り当てのマネージド ＩＤ が使用されているかどうかを監査または強制します。 |
| **ローカル認証の無効化** | Azure Machine Learning のコンピューティング リソースでローカル認証方法を無効にすべきかどうかを監査または強制します。 |
| **ローカル認証の変更または無効化** | ローカル認証方法を無効にするためにコンピューティング リソースを構成します。 |

ポリシーは、サブスクリプション レベルやリソース グループ レベルなど、さまざまなスコープで設定できます。 詳細については、[Azure Policy のドキュメント](../governance/policy/overview.md)を参照してください。

## <a name="assigning-built-in-policies"></a>組み込みポリシーの割り当て

Azure Machine Learning に関連する組み込みのポリシー定義を表示するには、次の手順を使用します。

1. [Azure portal](https://portal.azure.com) で __Azure Policy__ に移動します。
1. __[定義]__ を選択します。
1. __[種類]__ で _[組み込み]_ を選択し、 __[カテゴリ]__ で __[Machine Learning]__ を選択します。

ここから、ポリシー定義を選択して表示することができます。 定義を表示しているときに、 __[割り当て]__ リンクを使用して、ポリシーを特定のスコープに割り当て、ポリシーのパラメーターを構成することができます。 詳細については、「[ポリシーの割り当て - ポータル](../governance/policy/assign-policy-portal.md)」を参照してください。

[Azure PowerShell](../governance/policy/assign-policy-powershell.md)、[Azure CLI](../governance/policy/assign-policy-azurecli.md)、および[テンプレート](../governance/policy/assign-policy-template.md)を使用してポリシーを割り当てることもできます。

## <a name="conditional-access-policies"></a>条件付きアクセス ポリシー

Azure Machine Learning ワークスペースにアクセスできるユーザーを制御するには、Azure Active Directory の[条件付きアクセス](../active-directory/conditional-access/overview.md)を使用します。

## <a name="enable-self-service-using-landing-zones"></a>ランディング ゾーンを使用してセルフサービスを有効にする

ランディング ゾーンは、規模、ガバナンス、セキュリティ、生産性の基盤となる Azure 環境を設定するためのアーキテクチャ パターンです。 データ ランディング ゾーンは、データや分析ワークロードをホストするためにアプリケーション チームが使用する、管理者によって構成される環境です。

ランディング ゾーンの目的は、チームが Azure 環境で開始するときに、すべてのインフラストラクチャ構成作業が完了しているようにすることです。 たとえば、セキュリティ制御は組織の標準に準拠して設定され、ネットワーク接続が設定されます。

ランディング ゾーン パターンを使用すると、機械学習チームは独自のリソースをセルフサービスでデプロイし、管理できるようになります。 Azure のポリシーを使用すると、管理者は Azure リソースのコンプライアンスを監査および管理して、要件を満たすようにワークスペースが準拠していることを確認できます。 

Azure Machine Learning は、[クラウド導入フレームワークのデータ管理および分析のシナリオ](/azure/cloud-adoption-framework/scenarios/data-management/)において、[データ ランディング ゾーン](https://github.com/Azure/data-landing-zone)と統合されます。 このリファレンス実装では、機械学習ワークロードの移行先となる最適化された環境が提供され、その実装には、事前に構成された、Azure Machine Learning 用のポリシーが含まれています。

## <a name="configure-built-in-policies"></a>組み込みポリシーを構成する

### <a name="workspace-encryption-with-customer-managed-key"></a>カスタマー マネージド キーによるワークスペースの暗号化

ワークスペースをカスタマー マネージド キーで暗号化するか、Microsoft マネージド キーを使用してメトリックとメタデータを暗号化するかを制御します。 カスタマーマネージド キーの使用の詳細については、データ暗号化の記事の [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) に関するセクションをご覧ください。

このポリシーを構成するには、effect パラメーターを __audit__ または __deny__ に設定します。 __audit__ に設定すると、カスタマー マネージド キーを使用せずにワークスペースを作成できます。また、アクティビティ ログに警告イベントが作成されます。

ポリシーが __deny__ に設定されている場合は、カスタマー マネージド キーを指定しないとワークスペースを作成できません。 カスタマー マネージド キーを使用せずにワークスペースを作成しようとすると、`Resource 'clustername' was disallowed by policy` のようなエラーが発生し、アクティビティ ログにエラーが作成されます。 このエラーの一部としてポリシー識別子も返されます。

### <a name="workspace-should-use-private-link"></a>プライベート リンクを使用する必要があるワークスペース

ワークスペースで Azure Private Link を使用して Azure Virtual Network と通信するかどうかを制御します。 プライベート リンクの使用の詳細については、[ワークスペースのプライベート リンクの構成](how-to-configure-private-link.md)に関するページを参照してください。

このポリシーを構成するには、effect パラメーターを __audit__ または __deny__ に設定します。 __audit__ に設定すると、プライベート リンクを使用せずにワークスペースを作成できます。また、アクティビティ ログに警告イベントが作成されます。

ポリシーが __deny__ に設定されている場合、プライベート リンクを使用しないと、ワークスペースを作成できません。 プライベート リンクなしでワークスペースを作成しようとすると、エラーが発生します。 このエラーは、アクティビティ ログにも記録されます。 このエラーの一部としてポリシー識別子が返されます。

### <a name="workspace-should-use-private-endpoint"></a>プライベート エンドポイントを使用する必要があるワークスペース

Azure Virtual Network の指定したサブネット内にプライベート エンドポイントを作成するようにワークスペースを構成します。

このポリシーを構成するには、effect パラメーターを __DeployIfNotExists__ に設定します。 __privateEndpointSubnetID__ をサブネットの Azure Resource Manager ID に設定します。

### <a name="workspace-should-use-private-dns-zones"></a>プライベート DNS ゾーンを使用する必要があるワークスペース

プライベート DNS ゾーンを使用するようにワークスペースを構成し、プライベート エンドポイントの既定の DNS 解決を上書きします。

このポリシーを構成するには、effect パラメーターを __DeployIfNotExists__ に設定します。 __privateDnsZoneId__ を、使用するプライベート DNS ゾーンの Azure Resource Manager ID に設定します。 

### <a name="workspace-should-use-user-assigned-managed-identity"></a>ユーザー割り当てのマネージド ＩＤ を使用する必要があるワークスペース

システム割り当てのマネージド ID (既定値) とユーザー割り当てのマネージド ID のどちらを使用してワークスペースを作成するかを制御します。 ワークスペースのマネージド ID は、Azure Storage、Azure Container Registry、Azure Key Vault、Azure Application Insights などの関連リソースにアクセスするために使用されます。 詳細については、「[Azure Machine Learning でマネージド ID を使用する](how-to-use-managed-identities.md)」を参照してください。

このポリシーを構成するには、effect パラメーターを __audit__、__deny__、または __disabled__ に設定します。 __audit__ に設定すると、ユーザー割り当てのマネージド ID を指定せずにワークスペースを作成できます。 システムによって割り当てられた ID が使用され、アクティビティ ログに警告イベントが作成されます。

ポリシーが __deny__ に設定されている場合、作成プロセス中にユーザー割り当ての ID を指定しない限り、ワークスペースを作成することはできません。 ユーザー割り当て ID を指定せずにワークスペースを作成しようとすると、エラーが発生します。 このエラーは、アクティビティ ログにも記録されます。 このエラーの一部としてポリシー識別子が返されます。

### <a name="disable-local-authentication"></a>ローカル認証の無効化

Azure Machine Learning のコンピューティング クラスターまたはインスタンスで、ローカル認証 (SSH) を無効にするかどうかを制御します。

このポリシーを構成するには、effect パラメーターを __audit__、__deny__、または __disabled__ に設定します。 __audit__ に設定すると、SSH が有効なコンピューティングを作成できます。また、アクティビティ ログに警告イベントが作成されます。

ポリシーが __deny__ に設定されている場合、SSH が無効でない限り、コンピューティングを作成できません。 SSH が有効になっているコンピューティングを作成しようとすると、エラーが発生します。 このエラーは、アクティビティ ログにも記録されます。 このエラーの一部としてポリシー識別子が返されます。

### <a name="modifydisable-local-authentication"></a>ローカル認証の変更または無効化

Azure Machine Learning のコンピューティング クラスターまたはインスタンスの作成要求を変更して、ローカル認証 (SSH) を無効にします。

このポリシーを構成するには、effect パラメーターを __Modify__ または __Disabled__ に設定します。 __Modify__ に設定すると、このポリシーが適用されるスコープ内にコンピューティング クラスターやインスタンスを作成すると、自動的にローカル認証が無効になります。

## <a name="next-steps"></a>次のステップ

* [Azure Policy のドキュメント](../governance/policy/overview.md)
* [Azure Machine Learning の組み込みポリシー](policy-reference.md)
* [Microsoft Defender for Cloud でのセキュリティ ポリシーの使用](../security-center/tutorial-security-policy.md)
* [データ管理と分析のためのクラウド導入フレームワークのシナリオ](/azure/cloud-adoption-framework/scenarios/data-management/)に関するページには、クラウドでデータと分析のワークロードを実行する際の考慮事項の概要が示されています。
* [クラウド導入フレームワークのデータ ランディング ゾーン](https://github.com/Azure/data-landing-zone)に関するページでは、Azure でデータと分析のワークロードを管理するためのリファレンス実装が提供されています。
* [ポリシーを使用して Azure Private Link を Azure プライベート DNS ゾーンと統合する方法について学習](/azure/cloud-adoption-framework/ready/azure-best-practices/private-link-and-dns-integration-at-scale)し、ワークスペースと依存リソースのためのプライベート リンク構成を管理します。
