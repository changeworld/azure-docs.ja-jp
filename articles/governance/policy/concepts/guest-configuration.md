---
title: Azure Policy のゲスト構成機能について
description: Azure Policy によりゲスト構成機能を使用して仮想マシン内の設定が監査または構成されるしくみについて説明します。
ms.date: 07/15/2021
ms.topic: conceptual
ms.openlocfilehash: 6a40469b6cd391672ba953bac37402285ac4a097
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040418"
---
# <a name="understand-the-guest-configuration-feature-of-azure-policy"></a>Azure Policy のゲスト構成機能について

Azure Policy のゲスト構成機能を使用すると、Azure で実行されているコンピューターとハイブリッド [Arc 対応のコンピューター](../../../azure-arc/servers/overview.md)の両方で、オペレーティングシステムの設定をコードとして監査または構成することができます。
この機能は、コンピューターごとに直接使用することも、Azure Policy によって調整することもできます。

Azure の構成リソースは、[拡張リソース](../../../azure-resource-manager/management/extension-resource-types.md)として設計されています。
各構成は、コンピューターのプロパティの追加のセットと考えることができます。 構成には、次のような設定を含めることができます。

- オペレーティング システムの設定
- アプリケーションの構成または存在
- 環境設定

構成は、ポリシー定義とは別です。 ゲスト構成では Azure Policy を利用して、構成をコンピューターに動的に割り当てます。 [手動](guest-configuration-assignments.md#manually-creating-guest-configuration-assignments)で、あるいは [AutoManage](../../../automanage/automanage-virtual-machines.md) など他の Azure サービスを利用して、コンピューターに設定を割り当てることもできます。

各シナリオの例を次の表に示します。

| 型 | 説明 | ストーリーの例 |
| - | - | - |
| [構成管理](guest-configuration-assignments.md) | ソース管理のコードとして、サーバーの完全な表現が必要です。 展開には、サーバーのプロパティ (サイズ、ネットワーク、記憶域) とオペレーティングシステムとアプリケーションの設定の構成が含まれている必要があります。 | "このコンピューターは、web サイトをホストするように構成された web サーバーである必要があります。" |
| [コンプライアンス](../assign-policy-portal.md) | スコープ内のすべてのコンピューターの設定を、既存のコンピューターにリアクティブに適用するか、展開時に新しいコンピューターに事前に展開することをお勧めします。 | "すべてのコンピューターは TLS 1.2 を使用する必要があります。 既存のマシンを監査して、必要に応じて大規模な変更を行うことができます。 新しいコンピューターの場合は、展開時に設定を適用します。 |

構成による設定の結果は、[ [ゲストの割り当て] ページ](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) で確認できます。または、構成が Azure Policy 割り当てによって調整されている場合は、[ [対応の詳細] ページ](../how-to/determine-non-compliance.md#view-configuration-assignment-details-at-scale)の [最後に評価されたリソース] リンクをクリックすると表示されます。

[このドキュメントのビデオ チュートリアルを利用できます](https://youtu.be/t9L8COY-BkM)。 (近日公開予定)

## <a name="enable-guest-configuration"></a>ゲスト構成を有効にする

環境内のマシン (Azure 内のマシンと Arc 対応サーバーを含む) の状態を管理するには、以下の詳細を確認します。

## <a name="resource-provider"></a>リソース プロバイダー

Azure Policy のゲスト構成機能を使用するには、その前に、`Microsoft.GuestConfiguration` リソース プロバイダーを登録する必要があります。 ゲスト構成ポリシーの割り当てがポータルを通じて行われた場合、またはサブスクリプションが Azure Security Center に登録されている場合は、リソース プロバイダーが自動的に登録されます。 [ポータル](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)、[Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)、または [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) を使用して手動で登録できます。

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Azure 仮想マシンの要件をデプロイする

マシン内の設定を管理するには、[仮想マシン拡張機能](../../../virtual-machines/extensions/overview.md)を有効にします。また、マシンにはシステム マネージド ID が必要です。 拡張機能によって、適用可能なゲスト構成割り当てと、対応する依存関係がダウンロードされます。 ID は、マシンによるゲスト構成サービスの読み取りと書き込みを認証するために使用されます。 この拡張機能は Arc 接続マシン エージェントに含まれているため、Arc 対応サーバーには必要ありません。

> [!IMPORTANT]
> Azure 仮想マシンを管理するには、ゲスト構成拡張機能とマネージド ID が必要です。

多くのマシンに拡張機能を大規模にデプロイするには、ポリシー イニシアティブ `Deploy prerequisites to enable guest configuration policies on virtual machines` を割り当てます
対象は、管理しようとしているマシンが含まれる管理グループ、サブスクリプション、またはリソース グループです。

拡張機能とマネージド ID を 1 台のマシンにデプロイしたい場合は、それぞれのガイダンスに従ってください。

- [Azure Policy ゲスト構成拡張機能の概要](../../../virtual-machines/extensions/guest-configuration.md)
- [Azure portal を使用して Azure VM で Azure リソースのマネージド ID を構成する](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

構成を適用するゲスト構成パッケージを使用するには、Azure VM ゲスト構成拡張機能のバージョン **1.29.24** 以降が必要です。

### <a name="limits-set-on-the-extension"></a>拡張機能に設定されている制限

マシン内で実行されているアプリケーションへの拡張機能の影響を制限するため、ゲスト構成エージェントが CPU の 5% を超えることは許可されません。 この制限は、組み込み定義とカスタム定義の両方に存在します。 これは、Arc 接続マシン エージェントのゲスト構成サービスにも当てはまります。

### <a name="validation-tools"></a>検証ツール

マシン内では、ゲスト構成エージェントによりローカル ツールを使用してタスクが実行されます。

次の表では、サポートされている各オペレーティング システム上で使用されるローカル ツールの一覧を示します。 組み込みコンテンツの場合は、ゲスト構成によってこれらのツールの読み込みが自動的に処理されます。

|オペレーティング システム|検証ツール|Notes|
|-|-|-|
|Windows|[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v3| Azure Policy でのみ使用されるフォルダーにサイドローディングされます。 Windows PowerShell DSC と競合しません。 PowerShell Core はシステム パスに追加されません。|
|Linux|[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v3| Azure Policy でのみ使用されるフォルダーにサイドローディングされます。 PowerShell Core はシステム パスに追加されません。|
|Linux|[Chef InSpec](https://www.chef.io/inspec/) | Chef InSpec バージョン 2.2.61 が既定の場所にインストールされ、システム パスに追加されます。 Ruby と Python を含む InSpec パッケージの依存関係もインストールされます。 |

### <a name="validation-frequency"></a>検証の頻度

ゲスト構成エージェントによって、新しい、または変更されたゲスト割り当てが、5 分ごとにチェックされます。 ゲスト割り当てを受信すると、その構成の設定が 15 分間隔でチェックされます。 複数の構成が割り当てられている場合は、1 つずつ順番に評価されます。 前の構成が完了するまで、次の構成は実行されないため、実行時間の長い構成は、すべての構成の間隔に影響します。

監査が完了すると、結果がゲスト構成サービスに送信されます。
ポリシー[評価トリガー](../how-to/get-compliance-data.md#evaluation-triggers)が発生すると、マシンの状態がゲスト構成リソース プロバイダーに書き込まれます。 この更新により、Azure Policy によって Azure Resource Manager のプロパティが評価されます。 オンデマンドの Azure Policy 評価により、ゲスト構成リソース プロバイダーから最新の値が取得されます。 ただし、マシン内で新しいアクティビティがトリガーされることはありません。 その後、状態は Azure Resource Graph に書き込まれます。

## <a name="supported-client-types"></a>サポートされているクライアントの種類

ゲスト構成ポリシー定義には、新しいバージョンが含まれます。 ゲスト構成クライアントに互換性がない場合、Azure Marketplace で入手できる古いバージョンのオペレーティング システムは除外されます。 次の表に、Azure イメージでサポートされているオペレーティング システムを示します。
テキスト ".x" は、Linux ディストリビューションの新しいマイナー バージョンを表すシンボルです。

|Publisher|名前|バージョン|
|-|-|-|
|Amazon|Linux|2|
|Canonical|Ubuntu Server|14.04 - 20.x|
|Credativ|Debian|8 - 10.x|
|Microsoft|Windows Server|2012 - 2019|
|Microsoft|Windows クライアント|Windows 10|
|Oracle|Oracle-Linux|7.x - 8.x|
|OpenLogic|CentOS|7.3 -8.x|
|Red Hat|Red Hat Enterprise Linux\*|7.4 - 8.x|
|SUSE|SLES|12 SP3-SP5、15.x|

\* Red Hat CoreOS はサポートされていません。

カスタム仮想マシン イメージは、上の表のいずれかのオペレーティング システムである限り、ゲスト構成ポリシー定義によってサポートされます。

## <a name="network-requirements"></a>ネットワークの要件

Azure の仮想マシンは、ローカル ネットワーク アダプターまたは Private Link を使用して、ゲスト構成サービスと通信できます。

Azure Arc マシンは、オンプレミスのネットワーク インフラストラクチャを使用して接続し、Azure サービスにアクセスして、コンプライアンスの状態を報告します。

### <a name="communicate-over-virtual-networks-in-azure"></a>Azure の仮想ネットワークを介して通信する

Azure のゲスト構成リソース プロバイダーと通信するには、マシンはポート **443** で Azure データセンターに対してアウトバウンド アクセスを行う必要があります。 Azure 内のネットワークで送信トラフィックが許可されていない場合は、[ネットワーク セキュリティ グループ](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)の規則で例外を構成します。 Azure データセンターの [IP 範囲の一覧](https://www.microsoft.com/download/details.aspx?id=56519)を手動で管理するのではなく、[サービス タグ](../../../virtual-network/service-tags-overview.md) "AzureArcInfrastructure" と "Storage" を使用して、ゲスト構成とストレージのサービスを参照できます。 ゲスト構成のコンテンツ パッケージは Azure Storage によってホストされるため、両方のタグが必要です。

### <a name="communicate-over-private-link-in-azure"></a>Azure で Private Link を介して通信する

仮想マシンは、ゲスト構成サービスとの通信に [Private Link](../../../private-link/private-link-overview.md) を使用できます。 この機能を有効にするには、名前 `EnablePrivateNetworkGC` と値 `TRUE` を使用してタグを適用します。 タグは、ゲスト構成ポリシー定義をマシンに適用する前または後に適用できます。

Azure [仮想パブリック IP アドレス](../../../virtual-network/what-is-ip-address-168-63-129-16.md)を使用してトラフィックがルーティングされて、Azure プラットフォーム リソースとの、セキュリティで保護された認証済みチャネルが確立されます。

### <a name="azure-arc-enabled-servers"></a>Azure Arc 対応サーバー

Azure Arc によって接続されている Azure の外部にあるノードには、ゲスト構成サービスへの接続が必要です。 ネットワークとプロキシの要件に関する詳細は、[Azure Arc のドキュメント](../../../azure-arc/servers/overview.md)で提供されています。

プライベート データセンターの Arc 対応サーバーについては、次のパターンを使用してトラフィックを許可します。

- ポート: 送信インターネット アクセスには TCP 443 のみが必要です
- グローバル URL: `*.guestconfiguration.azure.com`

## <a name="assigning-policies-to-machines-outside-of-azure"></a>Azure 外部のコンピューターにポリシーを割り当てる

ゲスト構成に使用できる監査ポリシー定義には、**Microsoft.HybridCompute/machines** リソースの種類が含まれます。 ポリシー割り当てのスコープ内にある[サーバー向け Azure Arc](../../../azure-arc/servers/overview.md) にオンボードされているすべてのマシンが自動的に追加されます。

## <a name="managed-identity-requirements"></a>マネージド ID の要件

イニシアチブ "_仮想マシンでゲスト構成ポリシーを有効にするための前提条件をデプロイする_" のポリシー定義を使用すると、システムによって割り当てられたマネージド ID が有効になります (まだ存在しない場合)。 ID の作成を管理するイニシアチブには、2 つのポリシー定義があります。 ポリシー定義内の IF 条件により、Azure のマシン リソースの現在の状態に基づいて正しい動作が保証されます。

現時点でマシンにマネージド ID がない場合、有効なポリシーは次のようになります: [ID のない仮想マシンでゲスト構成の割り当てを有効にするためにシステム割り当てマネージド ID を追加する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)

現時点でマシンにユーザー割り当てのシステム ID がある場合、有効なポリシーは次のようになります: [ユーザー割り当て ID がある VM でゲスト構成の割り当てを有効にするためにシステム割り当てマネージド ID を追加する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)

## <a name="availability"></a>可用性

ゲスト割り当ては Azure のマシン リソースの拡張機能であるため、高可用性ソリューションを設計しているお客様は、[仮想マシン](../../../virtual-machines/availability.md)の冗長性計画の要件を検討する必要があります。 [ペアになっている](../../../best-practices-availability-paired-regions.md) Azure リージョンにゲスト割り当てリソースがプロビジョニングされている場合、ペアの少なくとも一方のリージョンが使用可能であれば、ゲスト割り当てレポートを使用できます。 ペアになっていない Azure リージョンが使用できなくなった場合は、リージョンが復元されるまで、ゲスト割り当てのレポートにアクセスすることはできません。

高可用性アプリケーションのアーキテクチャを検討する場合 (特に、高可用性を提供するためにロード バランサーソリューションの背後にある[可用性セット](../../../virtual-machines/availability.md#availability-sets)に仮想マシンがプロビジョニングされている場合)、ソリューション内のすべてのマシンに、同じパラメーターを含む同じポリシー定義を割り当てるのがベスト プラクティスです。 可能であれば、すべてのマシンにまたがる単一のポリシー割り当てを採用することにより、管理オーバーヘッドが最小限に抑えられます。

[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) によって保護されているマシンの場合は、プライマリ サイト内のマシンと同じパラメーター値を使用した同じ定義の Azure Policy 割り当てのスコープ内にセカンダリ サイト内のマシンを入れるようにします。

## <a name="data-residency"></a>データの保存場所

ゲスト構成により、顧客データが格納および処理されます。 既定では、顧客データは[ペアになっているリージョン](../../../best-practices-availability-paired-regions.md)にレプリケートされます。
単独配置リージョンの場合は、すべての顧客データがそのリージョンに格納され、処理されます。

## <a name="troubleshooting-guest-configuration"></a>ゲスト構成のトラブルシューティング

ゲスト構成のトラブルシューティングの詳細については、[Azure Policy のトラブルシューティング](../troubleshoot/general.md)に関する記事を参照してください。

### <a name="multiple-assignments"></a>複数の割り当て

ポリシー割り当てで異なるパラメーターが使用されている場合、現在、ゲスト構成ポリシー定義では、同じゲスト割り当てはマシンごとに 1 回だけ割り当てることができます。

### <a name="assignments-to-azure-management-groups"></a>Azure 管理グループへの割り当て

"ゲスト構成" カテゴリの Azure Policy 定義は、効果が "AuditIfNotExists" の場合にのみ管理グループに割り当てることができます。 効果が "DeployIfNotExists" のポリシー定義は、管理グループに割り当てることはできません。

### <a name="client-log-files"></a>クライアントのログ ファイル

ログ ファイルは、ゲスト構成拡張機能によって次の場所に書き込まれます。

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux

- Azure VM: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`
- Arc 対応サーバー: `/var/lib/GuestConfig/arc_policy_logs/gc_agent.log`

### <a name="collecting-logs-remotely"></a>ログのリモート収集

ゲスト構成の構成またはモジュールのトラブルシューティングでは最初に、コマンドレットを使用して、「[ゲスト構成パッケージの成果物をテストする方法](../how-to/guest-configuration-create-test.md)」の手順を実行する必要があります。
それでうまくいかない場合は、クライアント ログを収集すると問題の診断に役立ちます。

#### <a name="windows"></a>Windows

[Azure VM の実行コマンド](../../../virtual-machines/windows/run-command.md)を使用して、ログ ファイルから情報をキャプチャします。次の PowerShell スクリプトの例が役立ちます。

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

[Azure VM の実行コマンド](../../../virtual-machines/linux/run-command.md)を使用して、ログ ファイルから情報をキャプチャします。次の Bash スクリプトの例が役立ちます。

```bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

### <a name="agent-files"></a>エージェント ファイル

ゲスト構成エージェントにより、コンテンツ パッケージがマシンにダウンロードされて、内容が抽出されます。 どのコンテンツがダウンロードされ、保管されたか確認するには、下に示すフォルダーの場所を参照します。

Windows: `c:\programdata\guestconfig\configuration`

Linux: `/var/lib/GuestConfig/Configuration`

## <a name="guest-configuration-samples"></a>ゲスト構成のサンプル

ゲスト構成の組み込みポリシーのサンプルは、次の場所で入手できます。

- [組み込みのポリシー定義 - ゲスト構成](../samples/built-in-policies.md#guest-configuration)
- [組み込みのイニシアチブ - ゲスト構成](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy サンプルの GitHub リポジトリ](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>次のステップ

- カスタム ゲスト構成パッケージの[開発環境](../how-to/guest-configuration-create-setup.md)を設定する。
- ゲスト構成用の[パッケージ成果物を作成する](../how-to/guest-configuration-create.md)。
- 開発環境から[パッケージ成果物をテストする](../how-to/guest-configuration-create-test.md)。
- `GuestConfiguration` モジュールを使用して、環境を大規模に管理するための [Azure Policy の定義を作成する](../how-to/guest-configuration-create-definition.md)。
- Azure portal を使用して[カスタム ポリシー定義を割り当てる](../assign-policy-portal.md)。
- [ゲスト構成ポリシーの割り当てに関するコンプライアンスの詳細](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)を見る方法を学習する。
