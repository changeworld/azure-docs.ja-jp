---
title: エンタープライズ セキュリティとガバナンス
titleSuffix: Azure Machine Learning
description: 'Azure Machine Learning を安全に使用します: 認証、認可、ネットワーク セキュリティ、データ暗号化、監視。'
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.openlocfilehash: d5034f59cc71352518c0570e525f952f0f657c71
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343137"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Azure Machine Learning のエンタープライズ セキュリティとガバナンス

この記事では、Azure Machine Learning で利用できるセキュリティおよび管理機能について学習します。 これらの機能は、企業のポリシーに準拠したセキュリティで保護された構成を作成する管理者、DevOps、MLOps にとって有用です。 Azure Machine Learning と Azure プラットフォームを使用すると、次のことができます。

* ユーザー アカウントまたはグループでリソースと操作へのアクセスを制限する
* 受信および送信のネットワーク通信を制限する
* 転送中および保存中のデータを暗号化する
* 脆弱性をスキャンする
* 構成ポリシーを適用および監査する

## <a name="restrict-access-to-resources-and-operations"></a>リソースおよび操作へのアクセスを制限する

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) は Azure Machine Learning の ID サービス プロバイダーです。 これにより、Azure リソースへの _認証_ を行うために使用されるセキュリティ オブジェクト (ユーザー、グループ、サービス プリンシパル、マネージド ID) を作成および管理できます。 多要素認証がサポートされるのは、これを使用するように Azure AD が構成されている場合です。

Azure AD で多要素認証を使用する Azure Machine Learning のための認証プロセスを次に示します。

1. クライアントでは、Azure AD にサインインして、Azure Resource Manager トークンを取得します。
1. クライアントによって、Azure Resource Manager とすべての Azure Machine Learning にトークンが提示されます。
1. Azure Machine Learning では、ユーザーのコンピューティング先 (たとえば、Azure Machine Learning コンピューティング クラスター) に Machine Learning サービス トークンが提供されます。 このトークンは、実行の完了後に Machine Learning service にコールバックするために、ユーザーのコンピューティング ターゲットによって使用されます。 スコープはワークスペースに制限されます。

[![Azure Machine Learning での認証](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

各ワークスペースには、ワークスペースと同じ名前を持つ、関連付けられたシステム割り当て済み[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) があります。 このマネージド ID は、ワークスペースによって使用されるリソースに安全にアクセスするために使用されます。 これには、関連付けられているリソースに対する次の Azure RBAC アクセス許可があります。

| リソース | アクセス許可 |
| ----- | ----- |
| ワークスペース | Contributor |
| ストレージ アカウント | ストレージ BLOB データ共同作成者 |
| Key Vault | すべてのキー、シークレット、証明書へのアクセス |
| Azure Container Registry | Contributor |
| ワークスペースを含むリソース グループ | Contributor |

システム割り当てマネージド ID は、Azure Machine Learning と他の Azure リソースの間の内部サービス間認証に使用されます。 ユーザーは、ID トークンにアクセスできないので、これらのリソースにアクセスするために使用することはできません。 ユーザーは、十分な RBAC アクセス許可がある場合に、[Azure Machine Learning コントロールおよびデータ プレーン API](how-to-assign-roles.md) を介してのみリソースにアクセスできます。

関連付けられているリソースをプロビジョニングしたり、[Web サービス エンドポイントの Azure Container Instances をデプロイ](how-to-deploy-azure-container-instance.md)したりするために、マネージド ID には、ワークスペースを含むリソース グループに対する共同作成者アクセス許可が必要です。

管理者が上記の表のリソースに対するマネージド ID のアクセスを取り消すことはお勧めできません。 [キーの再同期操作](how-to-change-storage-access-key.md)を使用して、アクセスを復元できます。

> [!NOTE]
> Azure Machine Learning ワークスペースに、__2021 年 5 月 14 日より前に__ 作成されたコンピューティング先 (コンピューティング クラスター、コンピューティング インスタンス、Azure Kubernetes Service など) がある場合は、Azure Active Directory アカウントが追加されている可能性もあります。 このアカウントは、名前が `Microsoft-AzureML-Support-App-` で始まり、ワークスペースのすべてのリージョンのサブスクリプションに対する共同作成者レベルのアクセス権があります。
> 
> ワークスペースに Azure Kubernetes Service (AKS) がアタッチされていない場合は、この Azure AD アカウントを安全に削除できます。 
> 
> ワークスペースに AKS クラスターがアタッチされており、それらが "_2021 年 5 月 14 日より前に_" 作成されたものである場合は、__この Azure AD アカウントを削除しないでください__。 このシナリオでは、Azure AD アカウントを削除する前に、AKS クラスターを削除して再作成しておく必要があります。

たとえば、ベース Docker イメージ用の独自の Azure Container Registry にアクセスするために、ユーザー割り当てマネージド ID を使用するワークスペースをプロビジョニングし、マネージド ID に追加のロールを付与できます。 詳細については、[マネージド ID を使用したアクセスの制御](how-to-use-managed-identities.md)に関する記事を参照してください。

Azure Machine Learning コンピューティング クラスターで使用するためのマネージド ID を構成することもできます。 このマネージド ID は、ワークスペースのマネージド ID から独立しています。 コンピューティング クラスターでは、トレーニング ジョブを実行しているユーザーがアクセスできないセキュリティで保護されたデータストアなどのリソースに、マネージド ID を使用してアクセスします。 詳細については、[Azure のストレージ サービスへの ID ベースのデータ アクセス](how-to-identity-based-data-access.md)に関する記事を参照してください。

> [!TIP]
> Azure Machine Learning 内での Azure AD と Azure RBAC の使用には、いくつかの例外があります。
> * 必要に応じて、コンピューティング リソース (Azure Machine Learning コンピューティング インスタンスやコンピューティング インスタンスなど) への __SSH__ アクセスを有効にすることができます。 SSH アクセスは、Azure AD ではなく、公開/秘密キーのペアに基づきます。 SSH アクセスは、Azure RBAC によっては管理されません。
> * __キー__ または __トークン__ ベースの認証を使用して、Web サービス (推論エンドポイント) としてデプロイされたモデルに対して認証を行うことができます。 キーは静的な文字列ですが、Azure AD セキュリティ オブジェクトを使用してトークンが取得されます。 詳細については、「[Web サービスとしてデプロイされたモデルの認証を構成する](how-to-authenticate-web-service.md)」を参照してください。

詳細については、次の記事を参照してください。
* [Azure Machine Learning ワークスペースの認証](how-to-setup-authentication.md)
* [Azure Machine Learning へのアクセスの管理](how-to-assign-roles.md)
* [ストレージ サービスへの接続](how-to-access-data.md)
* [シークレット用の Azure Key Vault の使用](how-to-use-secrets-in-runs.md)
* [Azure Machine Learning での Azure AD マネージド ID の使用](how-to-use-managed-identities.md)
* [Web サービスでの Azure AD マネージド ID の使用](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>ネットワークのセキュリティと分離

Azure Machine Learning リソースへのネットワーク アクセスを制限するには、[Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md) を使用できます。 VNet を使用すると、パブリック インターネットから部分的または完全に分離されたネットワーク環境を作成できます。 これにより、ソリューションの攻撃面が減るだけでなく、データ窃盗の危険性も減少します。

仮想プライベート ネットワーク (VPN) ゲートウェイを使用して、個々のクライアントまたは独自のネットワークを VNet に接続することができます。

Azure Machine Learning ワークスペースでは、[Azure Private Link](../private-link/private-link-overview.md) を使用して、VNet の背後にプライベート エンドポイントを作成できます。 これにより、VNet 内からワークスペースにアクセスするために使用できるプライベート IP アドレスのセットが提供されます。 Azure Machine Learning が依存する一部のサービスでは、Azure Private Link を使用することもできますが、中にはネットワーク セキュリティ グループやユーザー定義のルーティングに依存するものもあります。

詳細については、以下のドキュメントをご覧ください。

* [仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)
* [ワークスペース リソースの保護](how-to-secure-workspace-vnet.md)
* [トレーニング環境の保護](how-to-secure-training-vnet.md)
* [推論環境の保護](how-to-secure-inferencing-vnet.md)
* [セキュリティで保護された仮想ネットワークでスタジオを使用する](how-to-enable-studio-virtual-network.md)
* [カスタム DNS を使用する](how-to-custom-dns.md)
* [ファイアウォールの構成](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>データの暗号化

Azure Machine Learning では、Azure プラットフォーム上でさまざまなコンピューティング リソースとデータ ストアが使用されます。 これらのそれぞれで保存時および転送中のデータの暗号化がサポートされる仕組みの詳細については、「[Azure Machine Learning でのデータの暗号化](concept-data-encryption.md)」を参照してください。

モデルを Web サービスとしてデプロイする場合は、トランスポート層セキュリティ (TLS) を有効にして転送中のデータを暗号化することができます。 詳しくは、[Web 同期の構成](how-to-secure-web-service.md)に関する記事をご覧ください。

## <a name="vulnerability-scanning"></a>脆弱性のスキャン

[Microsoft Defender for Cloud](../security-center/security-center-introduction.md) では、統合されたセキュリティ管理と高度な脅威に対する保護がハイブリッド クラウド ワークロード全体に提供されます。 Azure Machine Learning のために、[Azure Container Registry](../container-registry/container-registry-intro.md) リソースと Azure Kubernetes Service リソースのスキャンを有効にしてください。 詳細については、[Defender for Cloud による Azure Container Registry イメージのスキャン](../security-center/defender-for-container-registries-introduction.md)および [Azure Kubernetes Service と Defender for Cloud の統合](../security-center/defender-for-kubernetes-introduction.md)に関するページを参照してください。

## <a name="audit-and-manage-compliance"></a>コンプライアンスの監査と管理

[Azure Policy](../governance/policy/index.yml) は、Azure リソースがポリシーに準拠していることを確認できるガバナンス ツールです。 Azure Machine Learning ワークスペースでプライベート エンドポイントを使用するかどうかなど、特定の構成を許可または適用するポリシーを設定できます。 Azure Policy の詳細については、[ のドキュメント](../governance/policy/overview.md)を参照してください。 Azure Machine Learning に固有のポリシーの詳細については、[Azure Policy でのコンプライアンスの監査と管理](how-to-integrate-azure-policy.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning のエンタープライズ セキュリティに関するベスト プラクティス](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security)
* [TLS を使用して Azure Machine Learning Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた Machine Learning モデルを使用する](how-to-consume-web-service.md)
* [Azure Firewall と共に Azure Machine Learning を使用する](how-to-access-azureml-behind-firewall.md)
* [Azure Machine Learning と Azure Virtual Network を使用する](how-to-network-security-overview.md)
* [保存時および転送中のデータの暗号化](concept-data-encryption.md)
* [Azure 上でリアルタイム レコメンデーション API を構築する](/azure/architecture/reference-architectures/ai/real-time-recommendation)
