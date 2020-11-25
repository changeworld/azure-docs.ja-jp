---
title: エンタープライズ セキュリティとガバナンス
titleSuffix: Azure Machine Learning
description: 'Azure Machine Learning を安全に使用します: 認証、認可、ネットワーク セキュリティ、データ暗号化、監視。'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/09/2020
ms.openlocfilehash: fb1f1d098970927ba04c840e77ec0a0b8d76ca02
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561320"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Azure Machine Learning のエンタープライズ セキュリティとガバナンス

この記事では、Azure Machine Learning で利用できるセキュリティ機能について学習します。

クラウド サービスを使用する場合は、アクセスを必要とするユーザーのみに制限することをお勧めします。 まず、サービスによって使用される認証および認可モデルについて理解します。 ネットワーク アクセスを制限したり、オンプレミス ネットワークのリソースをクラウドと安全に結合したりすることも必要になる場合があります。 保存時とサービス間の移動時の両方でデータを暗号化することも不可欠です。 また、ポリシーを作成して、非対応の構成を作成するときに特定の構成またはログを適用することもできます。 最後に、サービスを監視し、すべてのアクティビティの監査ログを生成できることも必要です。

> [!NOTE]
> この記事の情報は、Azure Machine Learning Python SDK バージョン 1.0.83.1 以降に対応します。

## <a name="authentication--authorization"></a>認証と認可

Azure Machine Learning リソースに対するほとんどの認証では、認証に Azure Active Directory (Azure AD) が使用され、認可にロールベースのアクセス制御 (Azure RBAC) が使用されます。 これに対する例外は次のとおりです。

* __SSH__:一部のコンピューティング リソース (Azure Machine Learning コンピューティング インスタンスなど) への SSH アクセスを有効にすることができます。 SSH アクセスでは、キーベースの認証が使用されます。 SSH キーの作成の詳細については、[SSH キーの作成と管理](../virtual-machines/linux/create-ssh-keys-detailed.md)に関するページを参照してください。 SSH アクセスを有効にする方法の詳細については、「[Azure Machine Learning コンピューティング インスタンスを作成して管理する](how-to-create-manage-compute-instance.md)」を参照してください。
* __Web サービスとしてデプロイされたモデル__:Web サービスのデプロイでは、__キー__ または __トークン__ ベースのアクセス制御を使用できます。 キーは静的な文字列です。 トークンは、Azure AD アカウントを使用して取得されます。 詳細については、「[Web サービスとしてデプロイされたモデルの認証を構成する](how-to-authenticate-web-service.md)」を参照してください。

Azure Machine Learning が依存する特定のサービス (Azure データ ストレージ サービスなど) には、独自の認証方法と認可方法があります。 ストレージ サービスの認証の詳細については、[ストレージ サービスへの接続](how-to-access-data.md)に関する記事を参照してください。

### <a name="azure-ad-authentication"></a>Azure AD 認証

多要素認証がサポートされるのは、それを使用するように Azure Active Directory (Azure AD) が構成されている場合です。 以下に認証プロセスを示します。

1. クライアントでは、Azure AD にサインインして、Azure Resource Manager トークンを取得します。  ユーザーとサービス プリンシパルは完全にサポートされています。
1. クライアントによって、Azure Resource Manager とすべての Azure Machine Learning にトークンが提示されます。
1. Machine Learning service では、ユーザーのコンピューティング先 (たとえば、Machine Learning コンピューティング) に Machine Learning service トークンが提供されます。 このトークンは、実行の完了後に Machine Learning service にコールバックするために、ユーザーのコンピューティング ターゲットによって使用されます。 スコープはワークスペースに制限されます。

[![Azure Machine Learning での認証](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

詳細については、[Azure Machine Learning ワークスペースの認証](how-to-setup-authentication.md)に関する記事を参照してください。

### <a name="azure-rbac"></a>Azure RBAC

複数のワークスペースを作成でき、各ワークスペースを複数のユーザーで共有できます。 ユーザーがアクセスできるワークスペースの機能や操作を制御するには、Azure RBAC ロールに Azure AD アカウントを割り当てます。 組み込みのロールを次に示します。

* 所有者
* Contributor
* Reader

所有者と共同作成者は、ワークスペースにアタッチされているすべてのコンピューティング ターゲットとデータ ストアを使用できます。  

次の表では、いくつかの主要な Azure Machine Learning の操作とそれらを実行できるロールを示します。

| Azure Machine Learning の操作 | 所有者 | Contributor | Reader |
| ---- |:----:|:----:|:----:|
| ワークスペースの作成 | ✓ | ✓ | |
| ワークスペースを共有する | ✓ | |  |
| コンピューティング先を作成する | ✓ | ✓ | |
| コンピューティング先をアタッチする | ✓ | ✓ | |
| データ ストアをアタッチする | ✓ | ✓ | |
| 実験を実行する | ✓ | ✓ | |
| 実行/メトリックを表示する | ✓ | ✓ | ✓ |
| モデルの登録 | ✓ | ✓ | |
| イメージを作成する | ✓ | ✓ | |
| Web サービスのデプロイ | ✓ | ✓ | |
| モデル/イメージを表示する | ✓ | ✓ | ✓ |
| Web サービスを呼び出す | ✓ | ✓ | ✓ |

組み込みのロールがニーズを満たしていない場合は、カスタムのロールを作成できます。 カスタム ロールによって、コンピューティングの作成、実行の送信、データストアの登録、モデルのデプロイなど、ワークスペース内のすべての操作が制御されます。 カスタム ロールには、クラスター、データストア、モデル、エンドポイントなど、ワークスペースのさまざまなリソースに対する読み取り、書き込み、または削除のアクセス許可を付与することができます。 ロールは、特定のワークスペース レベル、特定のリソース グループ レベル、または特定のサブスクリプション レベルで使用できるようにすることができます。 詳細については、[Azure Machine Learning ワークスペースでのユーザーとロールの管理](how-to-assign-roles.md)に関するページを参照してください。

> [!IMPORTANT]
> Azure Machine Learning は、Azure Blob Storage や Azure Kubernetes Services などの他の Azure サービスに依存しています。 各 Azure サービスには、独自の Azure RBAC 構成があります。 必要なレベルのアクセス制御を実現するには、Azure Machine Learning 用の Azure RBAC 構成と Azure Machine Learning で使用されるサービスの Azure RBAC 構成の両方を適用する必要があります。

> [!WARNING]
> Azure Machine Learning は Azure Active Directory の B2B の共同作業ではサポートされていますが、現時点では Azure Active Directory の B2C の共同作業ではサポートされていません。

### <a name="managed-identities"></a>マネージド ID

各ワークスペースには、ワークスペースと同じ名前を持つ、関連付けられたシステム割り当て済み[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) もあります。 マネージド ID は、ワークスペースによって使用されるリソースに安全にアクセスするために使用されます。 これには、アタッチされたリソースに対する次のアクセス許可があります。

| リソース | アクセス許可 |
| ----- | ----- |
| ワークスペース | Contributor |
| ストレージ アカウント | ストレージ BLOB データ共同作成者 |
| Key Vault | すべてのキー、シークレット、証明書へのアクセス |
| Azure Container Registry | Contributor |
| ワークスペースを含むリソース グループ | Contributor |
| キー コンテナーを含むリソース グループ (ワークスペースを含むものと異なる場合) | Contributor |

管理者が上記の表のリソースに対するマネージド ID のアクセスを取り消すことはお勧めできません。 キーの再同期操作を使用して、アクセスを復元できます。

Azure Machine Learning では、すべてのワークスペース リージョンのサブスクリプションに、共同作成者レベルのアクセス権を持つ追加のアプリケーション (名前が `aml-` または `Microsoft-AzureML-Support-App-` で始まるもの) が作成されます。 たとえば、同じサブスクリプション内で、米国東部に 1 つのワークスペースがあり、北ヨーロッパに 1 つのワークスペースがある場合、これらのアプリケーションが 2 つ表示されます。 これらのアプリケーションでは、コンピューティング リソースの管理に役立つ Azure Machine Learning を有効にします。

必要に応じて、Azure Virtual Machines および Azure Machine Learning コンピューティング クラスターで使用するための独自のマネージド ID を構成できます。 VM では、個々のユーザーの Azure AD アカウントではなく、マネージド ID を使用して SDK からワークスペースにアクセスできます。 コンピューティング クラスターでは、トレーニング ジョブを実行しているユーザーがアクセスできないセキュリティで保護されたデータストアなどのリソースに、マネージド ID を使用してアクセスします。 詳細については、[Azure Machine Learning ワークスペースの認証](how-to-setup-authentication.md)に関する記事を参照してください。

## <a name="network-security-and-isolation"></a>ネットワークのセキュリティと分離

Azure Machine Learning リソースへの物理的なアクセスを制限するには、Azure Virtual Network (VNet) を使用します。 VNet を使用すると、パブリック インターネットから部分的または完全に分離されたネットワーク環境を作成できます。 これにより、ソリューションの攻撃面が減るだけでなく、データ窃盗の危険性も減少します。

詳細については、以下のドキュメントをご覧ください。

* [仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)
* [ワークスペース リソースの保護](how-to-secure-workspace-vnet.md)
* [トレーニング環境の保護](how-to-secure-training-vnet.md)
* [推論環境の保護](how-to-secure-inferencing-vnet.md)
* [セキュリティで保護された仮想ネットワークでスタジオを使用する](how-to-enable-studio-virtual-network.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>データの暗号化

Azure Machine Learning では、さまざまなコンピューティング リソースとデータ ストアが使用されます。 これらのそれぞれで保存時および転送中のデータの暗号化がサポートされる仕組みの詳細については、「[Azure Machine Learning でのデータの暗号化](concept-data-encryption.md)」を参照してください。

### <a name="microsoft-generated-data"></a>Microsoft が生成したデータ

自動化された機械学習などのサービスを使用する場合、Microsoft では、複数のモデルをトレーニングするために、一時的な処理済みのデータを生成することがあります。 このデータはワークスペース内のデータストアに格納されます。これにより、アクセス制御と暗号化を適切に適用できます。

また、[デプロイされているエンドポイントからログ記録された診断情報](how-to-enable-app-insights.md)を Azure Application Insights インスタンスに暗号化することもできます。

## <a name="monitoring"></a>監視

Azure Machine Learning には、ロールと監視対象に応じて、いくつかの監視シナリオがあります。

| Role | 使用する監視方法 | Description |
| ---- | ----- | ----- |
| Admin、DevOps、MLOps | [Azure Monitor メトリック](#azure-monitor)、[アクティビティ ログ](#activity-log)、[脆弱性スキャン](#vulnerability-scanning) | サービス レベル情報 |
| Data Scientist、MLOps | [実行を監視する](#monitor-runs) | トレーニングの実行中にログに記録される情報 |
| MLOps | [モデル データの収集](how-to-enable-data-collection.md)、[Application Insights による監視](how-to-enable-app-insights.md) | Web サービスまたは IoT Edge モジュールとしてデプロイされたモデルによってログされた情報|

### <a name="monitor-runs"></a>実行を監視する

Azure Machine Learning での実験の実行を監視できます (トレーニング スクリプト内からのログ情報を含む)。 この情報は、SDK、Azure CLI、およびスタジオを使用して表示できます。 詳細については、次の記事を参照してください。

* [トレーニングの実行の開始、監視、およびキャンセル](how-to-manage-runs.md)
* [ログの有効化](how-to-track-experiments.md)
* [ログを表示する](how-to-monitor-view-training-logs.md)
* [TensorBoard を使用して実行を視覚化する](how-to-monitor-tensorboard.md)

### <a name="azure-monitor"></a>Azure Monitor

Azure Monitor メトリックを使用し、Azure Machine Learning ワークスペースのメトリックを表示したり、監視したりできます。 [Azure portal](https://portal.azure.com) で、ワークスペースを選び、 **[メトリック]** を選択します。

[![ワークスペースのメトリックの例を示すスクリーンショット](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

メトリックには、実行、デプロイ、および登録に関する情報が含まれます。

詳しくは、「[Metrics in Azure Monitor (Azure Monitor のメトリック)](../azure-monitor/platform/data-platform-metrics.md)」をご覧ください。

### <a name="activity-log"></a>アクティビティ ログ

ワークスペースのアクティビティ ログを表示して、ワークスペース上で実行されるさまざまな操作を確認できます。 ログには、操作名、イベント イニシエーター、タイムスタンプなどの基本情報が含まれています。

このスクリーンショットは、ワークスペースのアクティビティ ログを示しています。

[![ワークスペースのアクティビティ ログを示すスクリーンショット](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

スコアリング要求の詳細は Application Insights に格納されます。 Application Insights は、ワークスペースの作成時にサブスクリプションに作成されます。 ログに記録される情報には、次のようなフィールドがあります。

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Azure Machine Learning ワークスペース内の一部のアクションでは、情報はアクティビティ ログに記録されません。 たとえば、トレーニングの実行の開始やモデルの登録はログに記録されません。
>
> これらのアクションの一部はワークスペースの **[アクティビティ]** 領域に表示されますが、これらの通知ではアクティビティを開始したユーザーは示されません。

### <a name="vulnerability-scanning"></a>脆弱性のスキャン

Azure Security Center は、ハイブリッド クラウド ワークロード全体で統合されたセキュリティ管理と高度な脅威保護を実現します。 Azure Machine Learning のために、Azure Container Registry リソースと Azure Kubernetes Service リソースのスキャンを有効にしてください。 「[Security Center による Azure Container Registry のイメージ スキャン](../security-center/defender-for-container-registries-introduction.md)」および「[Azure Kubernetes Service と Security Center の統合](../security-center/defender-for-kubernetes-introduction.md)」をご覧ください。

## <a name="audit-and-manage-compliance"></a>コンプライアンスの監査と管理

[Azure Policy](../governance/policy/index.yml) は、Azure リソースがポリシーに準拠していることを確認できるガバナンス ツールです。 Azure Machine Learning を使用すると、次のポリシーを割り当てることができます。

* **カスタマー マネージド キー**: ワークスペースでカスタマー マネージド キーを使用する必要があることを監査または適用します。
* **Private Link**: ワークスペースで仮想ネットワークとの通信にプライベート エンドポイントが使用されているかどうかを監査します。

Azure Policy の詳細については、[ のドキュメント](../governance/policy/overview.md)を参照してください。

Azure Machine Learning に固有のポリシーの詳細については、[Azure Policy でのコンプライアンスの監査と管理](how-to-integrate-azure-policy.md)に関するページを参照してください。

## <a name="resource-locks"></a>リソース ロック

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>次のステップ

* [TLS を使用して Azure Machine Learning Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた Machine Learning モデルを使用する](how-to-consume-web-service.md)
* [Azure Firewall と共に Azure Machine Learning を使用する](how-to-access-azureml-behind-firewall.md)
* [Azure Machine Learning と Azure Virtual Network を使用する](how-to-network-security-overview.md)
* [保存時および転送中のデータの暗号化](concept-data-encryption.md)
* [Azure 上でリアルタイム レコメンデーション API を構築する](/azure/architecture/reference-architectures/ai/real-time-recommendation)
