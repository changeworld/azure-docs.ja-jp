---
title: 仮想マシンのコンテンツの監査を学習する
description: Azure Policy がゲスト構成エージェントを使用して仮想マシン内の設定を監査するしくみについて説明します。
ms.date: 05/20/2020
ms.topic: conceptual
ms.openlocfilehash: 6ff24f14281712497798f2c5231a8d98d7d89055
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684278"
---
# <a name="understand-azure-policys-guest-configuration"></a>Azure Policy のゲストの構成の理解

Azure リソースの監査と[修復](../how-to/remediate-resources.md)以外に、Azure Policy ではマシン内の設定を監査することができます。 検証は、クライアントとゲスト構成拡張機能によって実行されます。 クライアントを介した拡張機能によって、次のような設定が検証されます。

- オペレーティング システムの構成
- アプリケーションの構成または存在
- 環境設定

現在、Azure Policy ゲスト構成のほとんどのポリシーでは、マシン内の設定の監査のみが行われます。
構成は適用されません。 例外は、[以下で参照されている](#applying-configurations-using-guest-configuration) 1 つの組み込みポリシーです。

## <a name="resource-provider"></a>リソース プロバイダー

ゲストの構成を使用するには、リソース プロバイダーを登録する必要があります。 ポータルを使用してゲスト構成ポリシーを割り当てる場合、リソース プロバイダーは自動的に登録されます。 [ポータル](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)、[Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)、または [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) を使用して手動で登録できます。

## <a name="extension-and-client"></a>拡張機能とクライアント

マシン内の設定を監査するには、[仮想マシン拡張機能](../../../virtual-machines/extensions/overview.md)を有効にします。 拡張機能は、適用可能なポリシーの割り当てと、対応する構成定義をダウンロードします。

> [!IMPORTANT]
> Azure の仮想マシンで監査を実行するには、ゲスト構成拡張機能が必要です。 拡張機能を大規模にデプロイするには、次のポリシー定義を割り当てます。 
>  - [Windows VM でゲスト構成ポリシーを有効にするための前提条件をデプロイする。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
>  - [Linux VM でゲスト構成ポリシーを有効にするための前提条件をデプロイする。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

### <a name="limits-set-on-the-extension"></a>拡張機能に設定されている制限

拡張機能のマシン内で実行されているアプリケーションへの影響を制限するために、ゲスト構成が CPU の 5% を超えることは許可されていません。 この制限は、組み込み定義とカスタム定義の両方に存在します。

### <a name="validation-tools"></a>検証ツール

マシン内では、ゲスト構成クライアントによりローカル ツールを使用して監査が実行されます。

次の表では、サポートされている各オペレーティング システムで使用するローカルのツールの一覧を示します。

|オペレーティング システム|検証ツール|Notes|
|-|-|-|
|Windows|[Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby と Python がマシン上にない場合は、ゲスト構成拡張機能によってインストールされます。 |

### <a name="validation-frequency"></a>検証の頻度

ゲスト構成クライアントは、新しいコンテンツを 5 分ごとにチェックします。 ゲスト割り当てを受信すると、その構成の設定が 15 分間隔でチェックされます。 監査が完了すると、結果がゲスト構成リソース プロバイダーに送信されます。 ポリシー[評価トリガー](../how-to/get-compliance-data.md#evaluation-triggers)が発生すると、マシンの状態がゲスト構成リソース プロバイダーに書き込まれます。 この更新により、Azure Policy によって Azure Resource Manager のプロパティが評価されます。 オンデマンドの Azure Policy 評価により、ゲスト構成リソース プロバイダーから最新の値が取得されます。 ただし、マシン内の構成の新しい監査はトリガーされません。

## <a name="supported-client-types"></a>サポートされているクライアントの種類

ゲスト構成ポリシーには、新しいバージョンが含まれます。 ゲスト構成エージェントに互換性がない場合、Azure Marketplace で入手できる古いバージョンのオペレーティング システムは除外されます。
次の表は、Azure イメージでサポートされているオペレーティング システムの一覧を示します。

|Publisher|名前|バージョン|
|-|-|-|
|Canonical|Ubuntu Server|14.04 以降|
|Credativ|Debian|8 以降|
|Microsoft|Windows Server|2012 以降|
|Microsoft|Windows クライアント|Windows 10|
|OpenLogic|CentOS|7.3 以降|
|Red Hat|Red Hat Enterprise Linux|7.4 以降|
|Suse|SLES|12 SP3 以降|

カスタム仮想マシン イメージについては、上記の表にあるいずれかのオペレーティング システムであれば、ゲスト構成ポリシーでサポートされます。

### <a name="unsupported-client-types"></a>サポートされていないクライアントの種類

Windows Server Nano Server はどのバージョンでもサポートされていません。

## <a name="guest-configuration-extension-network-requirements"></a>ゲスト構成拡張機能のネットワーク要件

Azure のゲスト構成リソース プロバイダーと通信するには、マシンはポート **443** で Azure データセンターに対してアウトバウンド アクセスを行う必要があります。 Azure 内のネットワークで送信トラフィックが許可されていない場合は、[ネットワーク セキュリティ グループ](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)の規則で例外を構成します。 [サービス タグ](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" を使用して、ゲスト構成サービスを参照できます。

## <a name="managed-identity-requirements"></a>マネージド ID の要件

仮想マシンに拡張機能を追加する **DeployIfNotExists** ポリシーでは、システム割り当てマネージド ID も有効になります (まで存在しない場合)。

> [!WARNING]
> システム割り当てマネージド ID を有効にするポリシーのスコープで、仮想マシンに対してユーザー割り当てマネージド ID を有効にすることは避けてください。 そのユーザー割り当て ID は置き換えられ、マシンが無応答になることがあります。

## <a name="guest-configuration-definition-requirements"></a>ゲスト構成定義の要件

ゲスト構成によって実行される各監査には、**DeployIfNotExists** 定義と **AuditIfNotExists** 定義の 2 つのポリシー定義が必要です。

**DeployIfNotExists** ポリシー定義が検証し、次の項目を修正します。

- マシンに評価する構成が割り当てられていることを検証します。 割り当てが現在存在しない場合は、割り当てを取得し、次のようにしてマシンを準備します。
  - [マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) を使用して、マシンへの認証を行う
  - **Microsoft.GuestConfiguration** 拡張機能の最新バージョンをインストールする
  - 必要とする場合、[検証ツール](#validation-tools)と依存関係をインストールする

**DeployIfNotExists** の割り当てが準拠していない場合は、[修復タスク](../how-to/remediate-resources.md#create-a-remediation-task)を使用できます。

**DeployIfNotExists** 割り当てが [対応] になったら、**AuditIfNotExists** ポリシー割り当てによって、ゲスト割り当てが [対応] または [非対応] のどちらであるが判定されます。 この検証ツールは、結果をゲスト構成クライアントに提供します。 クライアントは、その結果をゲストの拡張機能に転送します。それにより、その結果がゲスト構成のリソース プロバイダー全体で使用可能になります。

Azure Policy は、ゲスト構成リソースプロバイダーの **complianceStatus** プロパティを使用して**コンプライアンス** ノードでコンプライアンスを報告します。 詳細については、[コンプライアンス データを取得する](../how-to/get-compliance-data.md)を参照してください。

> [!NOTE]
> **AuditIfNotExists** ポリシーから結果を返すには、**DeployIfNotExists** ポリシーが必要です。 **DeployIfNotExists** がない場合、**AuditIfNotExists** ポリシーは状態として"0 of 0" のリソースを示します。

割り当てで使用するための定義をグループ化するためのイニシアティブには、ゲストの構成のすべての組み込みポリシーが含まれます。 " _\[[プレビュー]\]: Linux および Windows マシン内でのパスワードの監査セキュリティ_" という名前の組み込みイニシアティブには 18 のポリシーが含まれています。 Windows のために **DeployIfNotExists** と **AuditIfNotExists** の 6  つのペアがあって、Linux 用に 3 つのペアがあります。 [ポリシー定義](definition-structure.md#policy-rule)のロジックでは、対象のオペレーティング システムのみが評価されることが検証されます。

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>業界の基準に従ってオペレーティング システムの設定を監査する

Azure Policy の 1 つのイニシアティブでは、"ベースライン" に従ってオペレーティング システムの設定を監査する機能が提供されます。 _\[[プレビュー]\]: Azure セキュリティ ベースライン設定と一致しない Windows VM を監査する_という定義には、Active Directory グループ ポリシーに基づく一連の規則が含まれています。

ほとんどの設定は、パラメーターとして使用できます。 パラメーターを使用すると、監査対象をカスタマイズできます。
要件に合わせてポリシーを調整するか、またはポリシーを業界の規制標準などのサード パーティ情報にマップします。

一部のパラメーターは、整数の値範囲をサポートしています。 たとえば、[パスワードの有効期間] の設定では、有効なグループ ポリシー設定を監査できます。 "1,70" の範囲では、ユーザーがパスワードを 1 ～ 70 日ごとに変更する必要があることを確認します。

Azure Resource Manager デプロイ テンプレートを使用してポリシーを割り当てる場合は、パラメーター ファイルを使用して例外を管理します。 これらのファイルを Git などのバージョン管理システムにチェックインします。 ファイル変更に関するコメントによって、なぜ割り当てが予測される値の例外であるかの根拠が示されます。

#### <a name="applying-configurations-using-guest-configuration"></a>ゲスト構成を使用して構成を適用する

Azure Policy の最新の機能によって、コンピューター内の設定が構成されます。 _[Windows コンピューターでタイムゾーンを構成]_ する定義は、タイムゾーンを構成することによって、マシンに変更を加えます。

_[構成]_ で始まる定義を割り当てるとき、 _[前提条件を展開して Windows VM でゲスト構成ポリシーを有効にする]_ 定義も割り当てる必要があります。 必要に応じて、これらの定義をイニシアチブで結合させることができます。

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Azure 外部のコンピューターにポリシーを割り当てる

ゲスト構成に使用できる監査ポリシーには、**Microsoft.HybridCompute/machines** という種類のリソースが含まれます。 ポリシー割り当てのスコープ内にある[サーバー向け Azure Arc](../../../azure-arc/servers/overview.md) にオンボードされているすべてのマシンが自動的に追加されます。

### <a name="multiple-assignments"></a>複数の割り当て

ポリシー割り当てで異なるパラメーターが使用されている場合でも、現在、ゲスト構成ポリシーでは、同じゲスト割り当てはマシンごとに一度の割り当てのみがサポートされています。

## <a name="client-log-files"></a>クライアントのログ ファイル

ゲスト構成拡張機能は、ログ ファイルを次の場所に書き込みます。

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

`<version>` は現在のバージョン番号を示します。

### <a name="collecting-logs-remotely"></a>ログのリモート収集

ゲスト構成の構成またはモジュールのトラブルシューティングでは最初に、[Windows 用のカスタム ゲスト構成監査ポリシーを作成する](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)方法の手順に従って `Test-GuestConfigurationPackage` コマンドレットを使用する必要があります。
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

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>ゲスト構成のサンプル

ゲスト構成の組み込みのポリシー サンプルは、次の場所で入手できます。

- [組み込みのポリシー定義 - ゲスト構成](../samples/built-in-policies.md#guest-configuration)
- [組み込みのイニシアチブ - ゲスト構成](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy サンプルの GitHub リポジトリ](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>次のステップ

- [ゲスト構成のコンプライアンス ビュー](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)から各設定の詳細を表示する方法を学習します。
- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Azure Policy の定義の構造](definition-structure.md)」を確認します。
- 「[Policy の効果について](effects.md)」を確認します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/get-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。
