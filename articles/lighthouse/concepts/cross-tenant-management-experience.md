---
title: テナント間の管理エクスペリエンス
description: Azure の委任されたリソース管理によって、テナント間の管理エクスペリエンスが可能になります。
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: ad8fc7452a704a4a030e7a6eb45a5ba397912ef1
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402374"
---
# <a name="cross-tenant-management-experiences"></a>テナント間の管理エクスペリエンス

サービス プロバイダーは、[Azure portal](https://portal.azure.com) 上で自分のテナント内から複数の顧客の Azure リソースを管理するために、[Azure の委任されたリソース管理](../concepts/azure-delegated-resource-management.md)を使用できます。 ほとんどのタスクとサービスは、委任された Azure リソースに対して、マネージド テナントをまたいで実行できます。 この記事では、Azure の委任されたリソース管理が有効に機能する可能性がある強化されたシナリオの一部について説明します。

> [!NOTE]
> Azure の委任されたリソース管理はまた、[独自の Azure AD テナントが複数存在する企業内で](enterprise.md)使用して、テナントにまたがる管理を簡素化することもできます。

## <a name="understanding-customer-tenants"></a>顧客のテナントについて

Azure Active Directory (Azure AD) テナントは組織を表したものです。 これは、組織が、Azure、Microsoft 365、またはその他のサービスへのサインアップによって Microsoft との関係を築いたときに提供される Azure AD の専用インスタンスです。 各 Azure AD テナントは、他の Azure AD テナントと区別され分離されており、固有のテナント ID (GUID) があります。 詳細については、「[Azure Active Directory とは](../../active-directory/fundamentals/active-directory-whatis.md)」を参照してください。

通常、顧客の Azure リソースを管理するために、サービス プロバイダーは、その顧客のテナントに関連付けられたアカウントを使用して Azure portal にサインインする必要があります。その際、顧客のテナントの管理者は、そのサービス プロバイダー用にユーザー アカウントを作成して管理する必要があります。

Azure の委任されたリソース管理により、オンボード プロセスでは、顧客のテナント内のサブスクリプション、リソース グループ、およびリソースにアクセスして管理できるようにする、サービス プロバイダーのテナント内のユーザーを指定します。 これらのユーザーは、その後、自分の資格情報を使用して Azure portal にサインインできます。 Azure portal 内では、彼らは、アクセスできるすべての顧客に属するリソースを管理できます。 これを行うには、Azure portal の [[マイ カスタマー]](../how-to/view-manage-customers.md) ページにアクセスするか、Azure portal または API を使用してその顧客のサブスクリプションのコンテキスト内で直接作業します。

Azure の委任されたリソース管理を使用すると、テナントによって異なるアカウントにサインインしなくても、複数の顧客のリソースをより柔軟に管理できます。 たとえば、サービス プロバイダーには、責任とアクセス レベルが異なる 2 人の顧客が存在するとします。 Azure の委任されたリソース管理を使用すると、許可されているユーザーは、サービス プロバイダーのテナントにサインインしてこれらのリソースにアクセスすることができます。

![1 つのサービス プロバイダーのテナントを通じて管理される顧客のリソース](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>API と管理ツールのサポート

委任されたリソースに対して管理タスクをポータル上で直接実行するか、API および管理ツール (Azure CLI や Azure PowerShell など) を使用して実行することができます。 既存の API はすべて、委任されたリソースを操作するときに使用できます。ただし、テナント間の管理において機能がサポートされており、ユーザーが適切なアクセス許可を持っている必要があります。

Azure PowerShell の [Get-AzSubscription コマンドレット](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzSubscription?view=azps-3.5.0)では、返されたサブスクリプションがサービス プロバイダーのテナントまたは管理対象の顧客のテナントに属しているかどうかを識別できるように、各サブスクリプションの **tenantID** が表示されます。

同様に、[az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) などの Azure CLI コマンドでは、**homeTenantId** 属性と **managedByTenants** 属性が表示されます。

> [!TIP]
> Azure CLI の使用時にこれらの値が表示されない場合は、`az account clear` を実行してから `az login --identity` を実行して、キャッシュをクリアしてみてください。

また、Azure の委任されたリソース管理タスクの実行に固有の API も用意されています。 詳細については、**リファレンス**を参照してください。

## <a name="enhanced-services-and-scenarios"></a>強化されたサービスとシナリオ

ほとんどのタスクとサービスは、委任されたリソースに対して、マネージド テナントをまたいで実行できます。 テナント間の管理を有効にできる重要なシナリオの一部を以下に示します。

[サーバー向け Azure Arc (プレビュー)](../../azure-arc/servers/overview.md):

- Azure 内の委任されたサブスクリプションまたはリソース グループに [Azure外の Windows Server または Linux コンピューターを接続する](../../azure-arc/servers/quickstart-onboard-portal.md)
- Azure Policy やタグ付けなどの Azure コンストラクトを使用して接続されたコンピューターを管理する

[Azure Automation](../../automation/index.yml):

- Automation アカウントを使用して、委任された顧客のリソースにアクセスして操作する

[Azure Backup](../../backup/index.yml):

- 顧客のテナントにある顧客データをバックアップおよび復元する
- [バックアップ エクスプローラー](../../backup/monitor-azure-backup-with-backup-explorer.md)を使用すると、バックアップ項目 (まだバックアップ対象として構成されていない Azure リソースを含む) の運用情報と、委任されたサブスクリプションの監視情報 (ジョブとアラート) を表示できます。 バックアップ エクスプローラーは、現在、Azure VM データに対してのみ使用できます。
- 委任されたサブスクリプション全体で[バックアップ レポート](../../backup/configure-reports.md)を使用して、過去の傾向を追跡し、バックアップ ストレージの使用量を分析し、バックアップと復元を監査します。

[Azure Kubernetes Service (AKS)](../../aks/index.yml):

- ホストされている Kubernetes 環境を管理し、顧客のテナント内でコンテナー化されたアプリケーションをデプロイして管理する

[Azure Monitor](../../azure-monitor/index.yml):

- すべてのサブスクリプションにわたるアラートを表示する機能を使って、委任されたサブスクリプションに対するアラートを表示する
- 委任されたサブスクリプションのアクティビティ ログの詳細を表示する
- ログ分析: 複数のテナントにあるリモートの顧客ワークスペースからデータを照会する
- 顧客のテナント内に、サービス プロバイダー テナントで Azure Automation Runbook や Azure Functions などの自動化をトリガーするアラートを作成する

[Azure のネットワーク](../../networking/networking-overview.md):

- 顧客のテナント内で [Azure Virtual Network (VNet)](../../virtual-network/index.yml) と仮想ネットワーク インターフェイス カード (vNIC) をデプロイして管理する
- 顧客の Virtual Network リソースを保護するために [Azure Firewall](../../firewall/overview.md) をデプロイして構成する
- 顧客向けの [Azure Virtual WAN](../../virtual-wan/virtual-wan-about.md)、[ExpressRoute](../../expressroute/expressroute-introduction.md)、[VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) などの接続サービスを管理する
- Azure Lighthouse を使用して [Azure ネットワーク MSP プログラムの MSP プログラム](../../networking/networking-partners-msp.md)をサポートする


[Azure Policy](../../governance/policy/index.yml):

- コンプライアンスのスナップショットで、委任されたサブスクリプション内で割り当てられたポリシーの詳細を表示する
- 委任されたサブスクリプション内でポリシー定義を作成および編集する
- 委任されたサブスクリプション内で顧客が定義したポリシー定義を割り当てる
- 顧客には、サービス プロバイダーが作成したポリシーと顧客自身が作成したポリシーが並べて表示される
- [顧客のテナント内で deployIfNotExists の修復または割り当ての変更を行う](../how-to/deploy-policy-remediation.md)ことができる

[Azure Resource Graph](../../governance/resource-graph/index.yml):

- 返されるクエリ結果にテナント ID が含まれるため、サブスクリプションが顧客のテナントとサービス プロバイダーのテナントのどちらに属しているかを特定できる

[Azure Security Center](../../security-center/index.yml):

- テナント間の表示
  - セキュリティ ポリシーへの準拠を監視し、セキュリティの適用範囲がすべてのテナントのリソースになるようにする
  - 1 つのビューで複数の顧客の規制への順守を継続的に監視する
  - セキュリティ スコアの計算を使用して実行可能なセキュリティの推奨事項の監視、トリアージ、優先度付けを行う
- テナント間のセキュリティ体制の管理
  - セキュリティ ポリシーの管理
  - 実行可能なセキュリティの推奨事項に準拠していないリソースに対処する
  - セキュリティ関連のデータを収集して保存する
- テナント間の脅威の検出と保護
  - テナントのリソース全体で脅威を検出する
  - Just-In-Time (JIT) VM アクセスなど、高度な脅威保護コントロールを適用する
  - アダプティブ ネットワーク強化を使用してネットワーク セキュリティ グループの構成を強化する
  - サーバーで、適応型アプリケーション制御の対象とすべきアプリケーションとプロセスのみが実行されるようにする
  - ファイルの整合性の監視 (FIM) を使用して、重要なファイルとレジストリ エントリに対する変更を監視する

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- [顧客テナントで](../../sentinel/multiple-tenants-service-providers.md) Azure Sentinel リソースを管理する
- [複数の顧客テナントにわたる攻撃を追跡し、セキュリティ アラートを表示する](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- 顧客テナント間に分散している複数の Sentinel ワークスペースにわたる[インシデントを表示する](../../sentinel/multiple-workspace-view.md)

[Azure Service Health](../../service-health/index.yml):

- Azure Resource Health を使用して、顧客のリソースの正常性を監視する
- 顧客が使用している Azure サービスの正常性を追跡する

[Azure Site Recovery](../../site-recovery/index.yml):

- 顧客のテナント内にある Azure 仮想マシンのディザスター リカバリー オプションを管理する (VM 拡張機能のコピーには RunAs アカウントを使用できないことに注意してください)

[Azure Virtual Machines](../../virtual-machines/index.yml):

- 仮想マシン拡張機能を使用して、顧客のテナント内にある Azure VM のデプロイ後の構成と自動タスクを提供する
- ブート診断を使用して、顧客のテナント内にある Azure VM のトラブルシューティングを行う
- 顧客のテナントでシリアルコンソールを使用して VM にアクセスする
- [ポリシーによるマネージド ID](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret) を使用して、ディスク暗号化のためのパスワード、シークレット、または暗号化キー用に VM を Azure KeyVault と統合して、シークレットが顧客のテナントの Key Vault に確実に保存されるようにする
- 顧客のテナントの VM へのリモート ログインに Azure Active Directory を使用できないことに注意する

サポート リクエスト:

- Azure portal の **[ヘルプとサポート]** ブレードで、委任されたリソースに対するサポート リクエストを開く (委任されたスコープで利用可能なサポート プランを選択する)

## <a name="current-limitations"></a>現在の制限
すべてのシナリオで、次に示す現在の制限事項に注意してください。

- Azure Resource Manager で処理される要求は、Azure の委任されたリソース管理を使用して実行できます。 これらの要求の操作 URI は、`https://management.azure.com` で始まります。 ただし、リソースの種類のインスタンス (KeyVault のシークレット アクセスやストレージのデータ アクセスなど) によって処理される要求は、Azure の委任されたリソース管理ではサポートされていません。 これらの要求の操作 URI は、通常、`https://myaccount.blob.core.windows.net` や `https://mykeyvault.vault.azure.net/` など、実際のインスタンスに固有のアドレスで始まります。 また、通常、後者は管理操作ではなくデータ操作です。 
- ロールの割り当てでは、ロールベースのアクセス制御 (RBAC) の[組み込みロール](../../role-based-access-control/built-in-roles.md)を使用する必要があります。 現在、組み込みロールはすべて、Azure の委任されたリソース管理によってサポートされています。ただし、所有者または [DataActions](../../role-based-access-control/role-definitions.md#dataactions) アクセス許可を持つ組み込みロールは除きます。 [マネージド ID へのロールの割り当て](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)において、ユーザー アクセス管理者ロールは、限定された用途のみに対してサポートされています。  カスタム ロールと[従来のサブスクリプション管理者ロール](../../role-based-access-control/classic-administrators.md)はサポートされていません。
- Azure Databricks を使用するサブスクリプションをオンボードすることはできますが、現時点では、管理テナントのユーザーは、委任されたサブスクリプションで Azure Databricks ワークスペースを起動することはできません。
- リソース ロックがある Azure の委任されたリソース管理のサブスクリプションとリソース グループをオンボードすることはできますが、このようなロックがあっても、管理テナントのユーザーによるアクションの実行は妨げられません。 Azure マネージド アプリケーションまたは Azure Blueprints (システム割り当ての拒否割り当て) によって作成されたものなど、システムの管理対象リソースを保護する[拒否割り当て](../../role-based-access-control/deny-assignments.md)がある場合、管理テナントのユーザーはそれらのリソースを操作できません。ただし、現時点では、顧客テナントのユーザーは自分の拒否割り当て (ユーザー割り当て拒否割り当て) を作成できません。
- 管理テナント内のユーザーは、通常はアクセスを許可する組み込みロールを持っている場合でも、委任された顧客のサブスクリプションの課金情報を表示するためのアクセス権が与えられません。 これは、課金情報にアクセスするには、現在は同じテナント内のユーザーに対してのみサポートされている追加の手順が必要であるためです。

## <a name="next-steps"></a>次のステップ

- [Azure Resource Manager テンプレートを使用する](../how-to/onboard-customer.md)か[プライベートまたはパブリックのマネージド サービスのオファーを Azure Marketplace に公開する](../how-to/publish-managed-services-offers.md)ことで、Azure の委任されたリソース管理に顧客をオンボードします。
- Azure portal の **[マイ カスタマー]** に移動して、[顧客を表示および管理](../how-to/view-manage-customers.md)します。
