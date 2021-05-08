---
title: 概念 - Azure Kubernetes Services (AKS) におけるアクセスと ID
description: Azure Kubernetes Service (AKS) におけるアクセスと ID について説明します。Azure Active Directory の統合、Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC)、およびロールとバインドの説明が含まれています。
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: b10d31cf069bc4f28a1597ec12160fa6ed98b8ce
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789557"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのアクセスと ID オプション

Kubernetes クラスターへのアクセスをさまざまな方法で認証、承認、セキュリティ保護、制御できます。 
* Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) を使用して、ユーザー、グループ、サービス アカウントに対し、必要とするリソースのみへのアクセス権を付与できます。 
* Azure Kubernetes Service (AKS) では、Azure Active Directory と Azure RBAC を使用して、セキュリティとアクセス許可構造をさらに強化できます。 

Kubernetes の RBAC と AKS を使用すると、クラスターへのアクセスをセキュリティで保護し、開発者やオペレーターに必要最低限のアクセス許可のみを与えることができます。

この記事では、AKS で認証とアクセス許可の割り当てを行うために役立つ中心概念を紹介します。

## <a name="aks-service-permissions"></a>AKS サービスの権限

クラスターの作成時に、クラスターを作成して実行するために必要なリソース (VM や NIC など) が、ユーザーに代わり、AKS によって作成または変更されます。 この ID は、クラスターの作成時に作成される、クラスターの ID 権限とは異なります。

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>クラスターの権限を作成および操作する ID

クラスターを作成および操作する ID には、次の権限が必要です。

| 権限 | 理由 |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | ディスク暗号化セット ID を読み取るために必要です。 |
| `Microsoft.Compute/proximityPlacementGroups/write` | 近接配置グループを更新する場合に必要です。 |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | アプリケーション ゲートウェイを構成し、サブネットに参加するために必要です。 |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | カスタム VNET を使用する場合、サブネットのネットワーク セキュリティ グループを構成するために必要です。|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | Standard Load Balancer において送信パブリック IP を構成するために必要です。 |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Log Analytics ワークスペース、およびコンテナーの Azure 監視を作成および更新するために必要です。 |

### <a name="aks-cluster-identity-permissions"></a>AKS クラスター ID 権限

作成後に AKS クラスターに関連付けられる AKS クラスター ID によって、以下の権限が使用されます。 各アクセス許可は、次の理由で使用されます。

| 権限 | 理由 |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | ユーザーを作成し、クラスターを操作するために必要です
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | LoadBalancer サービスのロード バランサーを構成するために必要です。 |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | LoadBalancer サービスのパブリック IP を検索して構成するために必要です。 |
| `Microsoft.Network/publicIPAddresses/join/action` | LoadBalancer サービスのパブリック IP を構成する場合に必要です。 |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | LoadBalancer サービスのセキュリティ規則を作成または削除するために必要です。 |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | AzureDisks を構成するために必要です。 |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | AzureFile または AzureDisk のストレージ アカウントを構成するために必要です。 |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | ノードのルート テーブルとルートを構成するために必要です。 |
| `Microsoft.Compute/virtualMachines/read` | ゾーン、障害ドメイン、サイズ、データ ディスクなど、VMAS 内の仮想マシンに関する情報を検索するために必要です。 |
| `Microsoft.Compute/virtualMachines/write` | VMAS 内の仮想マシンに AzureDisks をアタッチするために必要です。 |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | ゾーン、障害ドメイン、サイズ、データ ディスクなど、仮想マシン スケール セット内の仮想マシンに関する情報を検索するために必要です。 |
| `Microsoft.Network/networkInterfaces/write` | VMAS 内の仮想マシンをロード バランサーのバックエンド アドレス プールに追加するために必要です。 |
| `Microsoft.Compute/virtualMachineScaleSets/write` | 仮想マシン スケール セットをロード バランサーのバックエンド アドレスプールに追加し、仮想マシン スケール セット内のノードをスケール アウトするために必要です。 |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | AzureDisks をアタッチし、仮想マシン スケール セットからロード バランサーに仮想マシンを追加するために必要です。 |
| `Microsoft.Network/networkInterfaces/read` | VMAS 内の仮想マシン用の内部 IP およびロード バランサーのバックエンド アドレス プールを検索するために必要です。 |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | 仮想マシン スケール セット内の仮想マシン用の内部 IP およびロード バランサーのバックエンド アドレス プールを検索するために必要です。 |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | 仮想マシン スケール セット内の仮想マシンのパブリック IP を検索するために必要です。 |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | 別のリソース グループ内の内部ロード バランサーに対してサブネットが存在するかどうかを確認するために必要です。 |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | AzureDisk のスナップショットを構成するために必要です。 |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | AzureDisk ボリュームの制限を把握する目的で仮想マシンのサイズを検索するために必要です。 |

### <a name="additional-cluster-identity-permissions"></a>追加のクラスター ID 権限

特定の属性を持つクラスターを作成する場合、クラスター ID には次の追加の権限が必要です。 これらの権限は自動的に割り当てられないため、クラスター ID を作成したら、これらの権限を追加する必要があります。

| 権限 | 理由 |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | 別のリソース グループ内のネットワーク セキュリティ グループを使用する場合に必要です。 LoadBalancer サービスのセキュリティ規則を構成するために必要です。 |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | カスタム VNET など、別のリソース グループ内のサブネットを使用する場合に必要です。 |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | カスタム ルート テーブルを持つカスタム VNET など、別のリソース グループ内のルート テーブルに関連付けられているサブネットを使用する場合に必要です。 他のリソース グループ内のサブネットに対してサブネットが既に存在しているかどうかを確認するために必要です。 |
| `Microsoft.Network/virtualNetworks/subnets/read` | 別のリソース グループの内部ロード バランサーを使用する場合に必要です。 リソース グループの内部ロード バランサーに対してサブネットが既に存在しているかどうかを確認するために必要です。 |
| `Microsoft.Network/privatednszones/*` | カスタム privateDNSZone など、別のリソース グループ内のプライベート DNS ゾーンを使用する場合に必要です。 |

## <a name="kubernetes-rbac"></a>Kubernetes RBAC

Kubernetes RBAC は、ユーザー操作の詳細なフィルター処理を提供します。 この制御メカニズムより、以下を行います。
* ユーザーまたはユーザー グループに対して、リソースの作成および変更、実行中のアプリケーション ワークロードのログの表示などの操作を行う権限を割り当てます。 
* 権限のスコープを、単一の名前空間または AKS クラスター全体に設定できます。 
* 権限を定義する "*ロール*" を作成し、それらのロールを "*ロールのバインド*" を使用してユーザーに割り当てます。

詳細については、[Kubernetes RBAC 認可の使用][kubernetes-rbac]に関するページをご覧ください。

### <a name="roles-and-clusterroles"></a>ロールと ClusterRole

#### <a name="roles"></a>ロール
Kubernetes の RBAC を使用して権限をユーザーに割り当てる前に、ユーザー権限を "*ロール*" として定義します。 ロールを使用して、名前空間内に権限を付与します。 

> [!NOTE]
> Kubernetes ロールは権限を "*付与*" します。権限は "*拒否*" されません。

クラスター全体または特定の名前空間の外部のクラスター リソースへの権限を付与するには、代わりに *ClusterRole* を使用できます。

#### <a name="clusterroles"></a>ClusterRole

ClusterRole は、特定の名前空間ではなく、クラスター全体のリソースへの権限を付与して適用します。

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBinding と ClusterRoleBinding

リソースへの権限を付与するロールを定義したら、*RoleBinding* を使用して、それらの Kubernetes RBAC 権限を割り当てます。 AKS クラスターが [Azure Active Directory と統合](#azure-ad-integration)されている場合、RoleBinding により、クラスター内でアクションを実行するための権限が Azure AD ユーザーに付与されます。 その方法については、[Kubernetes のロールベースのアクセス制御と Azure Active Directory ID を使用したクラスター リソースへのアクセスの制御](azure-ad-rbac.md)に関する記事を参照してください。

#### <a name="rolebindings"></a>RoleBindings

RoleBindings を使用して、指定した名前空間のユーザーにロールを割り当てます。 RoleBindings を使用すると、1 つの AKS クラスターを論理的に分離でき、ユーザーは各自に割り当てられた名前空間内のアプリケーション リソースにのみアクセスが可能になります。 

クラスター全体または特定の名前空間の外部のクラスター リソースにロールをバインドするには、代わりに *ClusterRoleBinding* を使用します。

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

ClusterRoleBinding では、ロールをユーザーにバインドし、特定の名前空間ではなく、クラスター全体のリソースに適用します。 このアプローチでは、管理者またはサポートエンジニアに対して、AKS クラスター内のすべてのリソースへのアクセス許可を付与できます。


> [!NOTE]
> Microsoft および AKS は、すべてのクラスター アクションを、ユーザーの同意を得たうえで、組み込みの Kubernetes ロール `aks-service` と組み込みロール バインド `aks-service-rolebinding` に基づいて実行します。 
> 
> このロールにより、AKS はクラスターの問題のトラブルシューティングや診断を行うことができますが、アクセス許可の変更や、ロールやロール バインドの作成など、高い特権を必要とする操作を行うことはできません。 ロールによるアクセスは、アクティブなサポート チケットで Just-In-Time (JIT) アクセスを使用する場合にのみ有効になります。 詳細については、[AKS サポート ポリシー](support-policies.md)に関する記事を参照してください。


### <a name="kubernetes-service-accounts"></a>Kubernetes サービス アカウント

"*サービス アカウント*" は、Kubernetes のプライマリ ユーザー タイプの 1 つです。 Kubernetes API により、サービス アカウントが保持および管理されます。 サービス アカウントの資格情報は Kubernetes シークレットとして保管され、承認されたポッドが API サーバーと通信するために使用できます。 ほとんどの API 要求では、サービス アカウントまたは通常のユーザー アカウント用の認証トークンが提供されします。

通常のユーザー アカウントでは、人間の管理者または開発者は、サービスとプロセスだけではなく、従来のアクセスも許可されます。 Kubernetes は、通常のユーザー アカウントとパスワードを格納するための ID 管理ソリューションを提供していませんが、外部の ID ソリューションを Kubernetes に統合することができます。 AKS クラスターの場合、この統合される ID ソリューションは Azure AD です。

Kubernetes における ID オプションの詳細については、[Kubernetes の認証][kubernetes-authentication]に関する記事を参照してください。

## <a name="azure-ad-integration"></a>Azure AD の統合

Azure AD 統合を使用して、AKS クラスターのセキュリティを強化します。 数十年におよぶエンタープライズ ID 管理の上に構築された Azure AD は、マルチテナントに対応したクラウドベースのディレクトリであり、中核となるディレクトリ サービス、アプリケーションのアクセス管理機能、および ID 保護機能が統合された ID 管理サービスです。 Azure AD を使用してオンプレミスの ID を AKS クラスターに統合することで、アカウント管理とセキュリティのための単一のソースを提供できます。

![Azure Active Directory と AKS クラスターの統合](media/concepts-identity/aad-integration.png)

Azure AD が統合された AKS クラスターでは、名前空間内またはクラスター間での Kubernetes リソースへのアクセス権をユーザーまたはグループに付与できます。 

1. `kubectl` 構成コンテキストを取得するには、ユーザーは、[az aks get-credentials][az-aks-get-credentials] コマンドを実行します。 
1. ユーザーが `kubectl` を使用して AKS クラスターにアクセスすると、Azure AD 資格情報でサインインするように求められます。 

このアプローチでは、ユーザー アカウントの管理とパスワードの資格情報のために 1 つのソースが提供されます。 ユーザーは、クラスター管理者によって定義されているリソースにのみアクセスできます。

Azure AD 認証は、OpenID Connect によって AKS クラスターに提供されます。 OpenID Connect は、OAuth 2.0 プロトコル上に構築された ID レイヤーです。 OpenID Connect の詳細については、[OpenID Connect のドキュメント][openid-connect]を参照してください。 Kubernetes クラスターの内部からは、[Webhook トークン認証][webhook-token-docs]を使用して認証トークンが確認されます。 webhook トークン認証は、AKS クラスターの一部として構成および管理されます。

### <a name="webhook-and-api-server"></a>Webhook と API サーバー

![Webhook と API サーバーの認証フロー](media/concepts-identity/auth-flow.png)

上の図に示すように、API サーバーでは、AKS Webhook サーバーが呼び出されて、次の手順が実行されます。

1. `kubectl` では、[OAuth 2.0 デバイス承認許可フロー](../active-directory/develop/v2-oauth2-device-code.md)でユーザーをサインインさせるために、Azure AD クライアント アプリケーションが使用されます。
2. Azure AD では、access_token、id_token、refresh_token が提供されます。
3. ユーザーは、`kubeconfig` からの access_token を使用して `kubectl` に対する要求を行います。
4. `kubectl` から API サーバーに access_token が送信されます。
5. API サーバーは、検証を実行するように Auth Webhook サーバーで構成されます。
6. 認証 Webhook サーバーでは、Azure AD の公開署名キーをチェックすることによって、JSON Web トークン署名が有効であることが確認されます。
7. サーバー アプリケーションでは、ユーザーが指定した資格情報を使用して、MS Graph API からログインしたユーザーのグループ メンバーシップが照会されます。
8. 応答は、アクセス トークンのユーザー プリンシパル名 (UPN) 要求や、オブジェクト ID に基づくユーザーのグループ メンバーシップなどのユーザー情報と共に API Server に送信されます。
9. API では、Kubernetes のロールと RoleBinding に基づいて認可の決定が実行されます。
10. 認可されると、API サーバーから `kubectl` に応答が返されます。
11. `kubectl` からユーザーにフィードバックが提供されます。
 
AKS と Azure AD を統合する方法については、[AKS マネージド Azure AD 統合のハウツー ガイド](managed-aad.md)に関する記事をご覧ください。

## <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

Azure ロールベースのアクセス制御 (RBAC) は [Azure Resource Manager](../azure-resource-manager/management/overview.md) 上に構築された承認システムであり、Azure リソースに対するアクセスをきめ細かく管理できます。

| RBAC システム | 説明 |
|---|---|
| Kubernetes RBAC | AKS クラスター内の Kubernetes リソースに対して機能するように設計されています。 |
| Azure RBAC | Azure サブスクリプション内のリソースに対して機能するように設計されています。 |

Azure の RBAC では、適用されるアクセス許可の概要を説明する *ロール定義* を作成します。 次に、特定の "*スコープ*" の "*ロールの割り当て*" を使用して、ユーザーまたはグループにこのロール定義を割り当てます。 スコープは、個々のリソース、リソース グループ、またはサブスクリプション全体にすることができます。

詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは][azure-rbac]」

AKS クラスターを完全に運用するには、次の 2 つのレベルのアクセスが必要です。 
* [Azure サブスクリプションの AKS リソースへのアクセス](#azure-rbac-to-authorize-access-to-the-aks-resource)。 
  * AKS API を使用してクラスターのスケーリングまたはアップグレードを制御します。
  * `kubeconfig` をプルします。
* Kubernetes API へのアクセス。 このアクセスは、次のいずれかによって制御されます。
  * [Kubernetes RBAC](#kubernetes-rbac) (従来)。
  * [Kubernetes の認可のための Azure RBAC と AKS の統合](#azure-rbac-for-kubernetes-authorization-preview)。

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>AKS リソースへのアクセスを認可するための Azure RBAC

Azure RBAC を使用すると、ユーザー (または ID) に、1 つ以上のサブスクリプションの AKS リソースに対するきめ細かいアクセスを提供できます。 たとえば、[Azure Kubernetes Service 共同作成者ロール](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)を使用して、クラスターをスケーリングおよびアップグレードできます。 一方で、[Azure Kubernetes Service クラスター管理者ロール](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role)を持つ別のユーザーは、管理者の `kubeconfig` をプルする権限のみを持ちます。

または、ユーザーに一般的な[共同作成者](../role-based-access-control/built-in-roles.md#contributor)ロールを付与することもできます。 一般的な共同作成者のロールを持つユーザーは、権限の管理を除き、上記の権限と AKS リソースに対して可能なすべての操作を実行できます。

[Azure RBAC を使用して AKS 内の Kubernetes 構成ファイルへのアクセス権を定義します](control-kubeconfig-access.md)。

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Kubernetes 認可に対する Azure RBAC (プレビュー)

Azure RBAC 統合では、AKS で Kubernetes 認可 Webhook サーバーが使用されることにより、Azure ロールの定義とロールの割り当てを使用して、Azure AD と統合された Kubernetes クラスター リソースの権限と割り当てを管理できます。

![Kubernetes 認可に対する Azure RBAC のフロー](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

上の図で示されているように、Azure RBAC 統合を使用すると、Kubernetes API に対するすべての要求は、「[Azure Active Directory の統合](#azure-ad-integration)」セクションで説明されているものと同じ認証フローに従います。 

要求を行っている ID が Azure AD に存在する場合、Azure は Kubernetes RBAC と連携して要求を承認します。 ID が Azure AD 外部に存在する (つまり、Kubernetes サービス アカウント) 場合、承認では通常の Kubernetes RBAC が抑止されます。

このシナリオでは、Kubernetes ロールの場合と同じように、Azure RBAC のメカニズムと API を使用してユーザーに組み込みロールを割り当てるか、カスタム ロールを作成します。 

この機能を使用すると、サブスクリプション間で AKS リソースへの権限をユーザーに付与するだけでなく、Kubernetes API アクセスを制御する各クラスター内でもロールと権限が構成されます。 たとえば、サブスクリプション スコープで `Azure Kubernetes Service RBAC Viewer` ロールを付与することができます。 このロールの受信者は、すべてのクラスターのすべての Kubernetes オブジェクトを変更せずに一覧表示して取得することができます。

> [!IMPORTANT]
> この機能を使用する前に、Kubernetes 認可のために Azure RBAC を有効にする必要があります。 詳細とステップバイステップ ガイダンスについては、「[Kubernetes 認可に Azure RBAC を使用する](manage-azure-rbac.md)」攻略ガイドをご覧ください。

#### <a name="built-in-roles"></a>組み込みのロール

AKS には、次の 4 つの組み込みロールがあります。 これらは、[Kubernetes の組み込みロール](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles)に似ていますが、CRD のサポートなど、いくつかの違いがあります。 各 [Azure 組み込みロール](../role-based-access-control/built-in-roles.md)で許可されるアクションの完全な一覧をご覧ください。

| Role                                | 説明  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC ビューアー  | 名前空間内のほとんどのオブジェクトを表示するための読み取り専用アクセスが許可されます。 <br> ロールまたはロールのバインドを表示することはできません。<br> `Secrets` を表示することはできません。 `Secrets` の内容を読み取ると、名前空間の `ServiceAccount` 資格情報にアクセスでき、それにより名前空間の任意の `ServiceAccount` として API にアクセスできるようになります (特権エスカレーションの一種)。  |
| Azure Kubernetes Service RBAC ライター | 名前空間内のほとんどのオブジェクトに対する読み取りと書き込みのアクセスが許可されます。 <br> ロールまたはロールのバインドを表示または変更することはできません。 <br> `Secrets` にアクセスし、名前空間内の任意の ServiceAccount としてポッドを実行することが許可されます。そのため、名前空間内の任意の ServiceAccount の API アクセス レベルを取得するために使用できます。 |
| Azure Kubernetes Service RBAC 管理者  | 名前空間内で付与されることが意図された、管理者アクセスが許可されます。 <br> 名前空間内でロールおよびロール バインドを作成する能力など、名前空間 (またはクラスター スコープ) 内のほとんどのリソースへの読み取りおよび書き込みアクセスが許可されます。 <br> リソース クォータまたは名前空間自体への書き込みアクセスは許可されません。 |
| Azure Kubernetes Service RBAC クラスター管理者  | 任意のリソースに対して任意のアクションを実行できるスーパー ユーザー アクセスが許可されます。 <br> クラスター内およびすべての名前空間内のすべてのリソースを完全に制御できます。 |


## <a name="summary"></a>まとめ

Azure AD 統合が有効になっている場合にユーザーが Kubernetes に対して認証を行う方法の概要について、表を参照してください。 すべての場合において、ユーザーのコマンド実行順序は次のようになります。
1. `az login` を実行して Azure に対して認証を行います。
1. `az aks get-credentials` を実行して、クラスターの資格情報を `.kube/config` にダウンロードします。
1. `kubectl` コマンドを実行します。 
   * 最初のコマンドにより、次の表で説明するように、クラスターに対して認証を行うためのブラウザーベースの認証がトリガーされることがあります。

Azure portal で、以下を検索できます。
* 2 番目の列に記載されている "*ロール付与*" (Azure RBAC ロール付与) は、 **[アクセス制御]** タブに表示されています。 
* クラスター管理 Azure AD グループは、 **[構成]** タブに表示されています。
  * Azure CLI でパラメーター名 `--aad-admin-group-object-ids` を使用しても見つかります。


| 説明        | ロール付与が必要| クラスター管理 Azure AD グループ | 使用する場合 |
| -------------------|------------|----------------------------|-------------|
| クライアント証明書を使用した、レガシの管理者ログイン| **Azure Kubernetes 管理者ロール**。 このロールにより、`--admin` フラグを指定して `az aks get-credentials` を使用できます。これを実行すると、[レガシ (非 Azure AD) クラスター管理用証明書](control-kubeconfig-access.md)がユーザーの `.kube/config` にダウンロードされます。 これが、"Azure Kubernetes 管理者ロール" の唯一の目的です。|該当なし|お使いのクラスターへのアクセス権を持つ有効な Azure AD グループへのアクセス権がないため、永続的にブロックされている場合。| 
| 手動の (Cluster)RoleBindings を使用した Azure AD| **Azure Kubernetes ユーザー ロール**。 "ユーザー" ロールにより、`--admin` フラグなしで `az aks get-credentials` を使用できます。 (これが、"Azure Kubernetes ユーザー ロール" の唯一の目的です。)この結果、Azure AD 対応のクラスターで、[空のエントリ](control-kubeconfig-access.md)が `.kube/config` にダウンロードされ、それが最初に `kubectl`によって使用されたときにブラウザーベースの認証がトリガーされます。| ユーザーはこれらのグループのいずれにも属しません。 ユーザーがどのクラスター管理グループにも属さないため、その権利は全面的に、クラスター管理者が設定した任意の RoleBindings または ClusterRoleBindings によって制御されます。 (Cluster)RoleBindings により、その `subjects` として[Azure AD ユーザーまたは Azure AD グループが指名されます](azure-ad-rbac.md)。 このようなバインドが設定されていない場合、ユーザーはどの `kubectl` コマンドも行できません。|きめ細かなアクセス制御が必要で、Kubernetes 認可に Azure RBAC を使用しない場合。 バインドを設定するユーザーは、この表に記載されている他の方法のいずれかでログインする必要があることに注意してください。|
| 管理グループのメンバーによる Azure AD| 同上|ユーザーは、こちらに一覧表示されているいずれかのグループのメンバーです。 一覧表示されているすべてのグループを `cluster-admin` Kubernetes ロールにバインドする ClusterRoleBinding が自動的に生成されます。 そのため、これらのグループのユーザーは、すべての `kubectl` コマンドを `cluster-admin`として実行できます。|ユーザーに完全な管理者権限を簡単に付与する必要があり、Kubernetes 認可に Azure RBAC を使用 "_しない_" 場合。|
| Kubernetes 認可に Azure RBAC を使用した Azure AD|2 つのロール: <br> 1 つ目は、**Azure Kubernetes ユーザー ロール** (上記と同じ)。 <br> 2 つ目は、上記の "Azure Kubernetes Service **RBAC**..."ロールのいずれか、または代わりとなる独自のカスタム ロール。|Kubernetes 認可に対する Azure RBAC が有効になっている場合、[構成] タブの [管理者ロール] フィールドは関係ありません。|Kubernetes 認可に Azure RBAC を使用しない場合。 この方法を使用すると、RoleBindings バインドや ClusterRoleBindings を設定することなく、きめ細かく制御できます。|

## <a name="next-steps"></a>次のステップ

- Azure AD と Kubernetes の RBAC の使用を開始するには、[Azure Active Directory と AKS の統合][aks-aad]に関する記事を参照してください。
- 関連するベスト プラクティスについては、[AKS の認証と認可のベスト プラクティス][operator-best-practices-identity]に関する記事を参照してください。
- Kubernetes 認可に Azure RBAC を使い始めるには、[Azure RBAC を使用して azure Kubernetes Service (AKS) クラスター内のアクセスを認可する方法](manage-azure-rbac.md)に関する記事を参照してください。
- `kubeconfig` ファイルのセキュリティ保護については、[クラスター構成ファイルへのアクセスの制限](control-kubeconfig-access.md)に関する記事を参照してください

Kubernetes と AKS の中心概念の詳細については、次の記事を参照してください。

- [Kubernetes/AKS クラスターとワークロード][aks-concepts-clusters-workloads]
- [Kubernetes/AKS のセキュリティ][aks-concepts-security]
- [Kubernetes/AKS の仮想ネットワーク][aks-concepts-network]
- [Kubernetes/AKS のストレージ][aks-concepts-storage]
- [Kubernetes/AKS のスケール][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
