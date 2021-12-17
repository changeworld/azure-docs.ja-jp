---
title: ゲスト構成の修復オプション
description: Azure Policy のゲスト構成機能には、修復タスクを使用した継続的修復または制御のためのオプションがあります。
ms.date: 07/12/2021
ms.topic: how-to
ms.openlocfilehash: 6c16f4a2b20ea753b1ded4e8d389babc613b4b36
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868432"
---
# <a name="remediation-options-for-guest-configuration"></a>ゲスト構成の修復オプション

開始する前に、[ゲスト構成](../concepts/guest-configuration.md)の概要ページを参照することをお勧めします。

> [!IMPORTANT]
> Azure の仮想マシンには、ゲスト構成拡張機能が必要となります。 すべてのマシンに対して大規模な拡張を展開するために、次のようなポリシー イニシアティブを割り当ててください: `Deploy prerequisites to enable guest configuration policies on
> virtual machines`
> 
> ゲスト構成パッケージを使って構成を行うには、Azure VM ゲスト構成拡張のバージョン **1.29.24** 以降、または Arc エージェントのバージョン **1.10.0** 以降が必要です。
>
> **AuditIfNotExists** を用いるカスタム ゲスト構成ポリシー定義は一般公開されていますが、ゲスト構成とともに **DeployIfNotExists** を用いる定義は **プレビュー段階** です。

## <a name="how-remediation-set-is-managed-by-guest-configuration"></a>ゲスト構成による修復 (設定) の管理方法

ゲスト構成では、マシン内で変更を配信する定義にポリシー結果 [DeployIfNotExists](../concepts/effects.md#deployifnotexists) が使用されます。
ポリシー割り当てのプロパティを設定して、[評価](../concepts/effects.md#deployifnotexists-evaluation)で構成を自動的またはオンデマンドで配信する方法を制御します。

[このドキュメントのビデオ チュートリアルを利用できます](https://youtu.be/rjAk1eNmDLk)。

### <a name="guest-configuration-assignment-types"></a>ゲスト構成の割り当ての種類

ゲストの割り当ての作成時に使用できる割り当ての種類は 3 つあります。
このプロパティは、**DeployIfNotExists** をサポートするゲスト構成定義のパラメーターとして使用できます。

| 割り当ての種類 | 動作 |
|-|-|
| Audit | マシンの状態は報告されますが、変更は行われません。 |
| ApplyandMonitor | マシンに 1 回適用され、変更が監視されます。 構成がドリフトして NonCompliant になった場合は、修復がトリガーされない限り、自動的に修正されません。 |
| ApplyandAutoCorrect | マシンに適用されます。 ドリフトした場合、マシン内のローカル サービスは次の評価で修正されます。 |

3 種類の割り当てのそれぞれで、新しいポリシー割り当てが既存のマシンに割り当てられると、ゲストの割り当てが自動的に作成され、最初に構成の状態が監査されて、修復が必要なマシンに関する決定を行うための情報が提供されます。

## <a name="remediation-on-demand-applyandmonitor"></a>オンデマンド修復 (ApplyAndMonitor)

既定では、ゲスト構成の割り当ては "オンデマンド修復" シナリオで動作します。 構成が適用され、その後、準拠からドリフトすることが許可されます。 ゲストの割り当てのコンプライアンス状態は、構成の適用中にエラーが発生した場合、または次回の評価時にマシンが望ましい状態でなくなった場合を除き、"Compliant" です。 エージェントによって、状態が "NonCompliant" として報告され、自動的に修復されません。

この動作を有効にするには、ゲスト構成の割り当ての [assignmentType プロパティ](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype)を "ApplyandMonitor" に設定します。 マシン内で割り当てが処理されるたびに、[Test](/powershell/scripting/dsc/resources/get-test-set#test) メソッドで "true" が返された各リソースの場合は、エージェントによって "Compliant" が報告され、このメソッドで "false" が返された場合は、エージェントによって "NonCompliant" が報告されます。

## <a name="continuous-remediation-autocorrect"></a>継続的修復 (AutoCorrect)

ゲスト構成では、"継続的修復" の概念がサポートされています。 マシンが構成のコンプライアンスからドリフトした場合、次回の評価時に構成が自動的に修正されます。 エラーが発生しない限り、マシンでは構成の状態が常に "Compliant" として報告されます。 継続的修復を使用する場合、ドリフトが自動的に修正された時間を報告する方法はありません。

この動作を有効にするには、ゲスト構成の割り当ての [assignmentType プロパティ](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype)を "ApplyandAutoCorrect" に設定します。 マシン内で割り当てが処理されるたびに、[Test](/powershell/scripting/dsc/resources/get-test-set#test) メソッドで "false" が返された各リソースについて、[Set](/powershell/scripting/dsc/resources/get-test-set#set) メソッドが自動的に実行されます。

## <a name="disable-remediation"></a>修復を無効にする

`assignmentType` プロパティに "Audit" が設定されている場合、エージェントではマシンの監査のみが実行され、準拠していない場合に構成の修復は試みられません。

### <a name="disable-remediation-of-custom-content"></a>カスタム コンテンツの修復を無効にする

カスタム コンテンツ パッケージの割り当ての種類プロパティをオーバーライドするには、マシンに **CustomGuestConfigurationSetPolicy** という名前のタグを追加し、値を **disable** にします。 このタグを追加すると、Microsoft によって提供される組み込みコンテンツではなく、カスタム コンテンツ パッケージの修復だけが無効になります。

## <a name="azure-policy-enforcement"></a>Azure Policy の強制

Azure Policy の割り当てには、新しいリソースと既存のリソースの動作を決定する必須のプロパティである[強制モード](../concepts/assignment-structure.md#enforcement-mode)が含まれています。
構成をマシンに自動的に適用するかどうかを制御するには、このプロパティを使用します。

**既定では、強制は "有効" です**。 "ゲスト構成" カテゴリのポリシー定義を持つ Azure Policy の割り当てのスコープ内にマシンがある場合、新しいマシンがデプロイされたとき、または **マシンのプロパティが更新された** ときに、Azure Policy によって構成が自動的に適用されます。 **更新操作には、タグの追加や変更などの Azure Resource Manager で発生するアクションが含まれ**、仮想マシンの場合は、ディスクのサイズ変更や接続などの変更が含まれます。 Azure のマシン リソースに変更が発生したときに構成を修復する必要がある場合は、強制を有効のままにします。 マシン内で発生した変更では、Azure Resource Manager のマシン リソースが変更されない限り、自動修復はトリガーされません。

強制が "無効" に設定されている場合は、構成の割り当てによって、[修復タスク](../how-to/remediate-resources.md)で動作が変更されるまで、マシンの状態が監査されます。 既定では、ゲスト構成定義によって [assignmentType プロパティ](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype)が "Audit" から "ApplyandMonitor" に更新され、構成が 1 回適用されて、修復がトリガーされるまで再度適用されません。

## <a name="optional-remediate-all-existing-machines"></a>オプション: 既存のすべてのマシンを修復する

Azure portal から Azure Policy の割り当てを作成する場合は、[修復] タブの [修復タスクの作成] チェック ボックスを利用できます。 このチェック ボックスをオンにすると、ポリシーの割り当ての作成後、"NonCompliant" と評価されるリソースが、修復タスクによって自動的に修正されます。

ゲスト構成に対するこの設定の効果は、ポリシーを割り当てるだけで、多数のマシンに構成をデプロイできることです。 また、準拠していないマシンに対して修復タスクを手動で実行する必要がありません。

## <a name="manually-trigger-remediation-outside-of-azure-policy"></a>Azure Policy の外部で手動で修復をトリガーする

更新によってリソースのプロパティが変更されない場合でも、ゲスト割り当てのリソースを更新することで、Azure Policy エクスペリエンスの外部で修復を調整することもできます。

ゲスト構成の割り当てが作成されると、[complianceStatus プロパティ](/rest/api/guestconfiguration/guest-configuration-assignments/get#compliancestatus)が "Pending" に設定されます。
マシン内のゲスト構成サービス ([ゲスト構成拡張機能](../../../virtual-machines/extensions/guest-configuration.md)によって Azure 仮想マシンに配信され、Arc 対応サーバーに含められます) では、5 分ごとに割り当ての一覧を要求します。
ゲスト構成の割り当てに "Pending" の `complianceStatus` および "ApplyandMonitor" または "ApplyandAutoCorrect" の `configurationMode` という両方の要件がある場合、マシン内のサービスによって構成が適用されます。 構成の適用後の[次の間隔](./guest-configuration.md#validation-frequency)で、動作をコンプライアンスの状態の報告のみでドリフトを許可するようにするか、自動的に修正されるようにするかが、構成モードによって決定されます。

## <a name="understanding-combinations-of-settings"></a>設定の組み合わせについて理解する

|~| Audit | ApplyandMonitor | ApplyandAutoCorrect |
|-|-|-|-|
| 強制の有効化 | 状態の報告のみ | 構成が VM に適用され、**更新時に作成および再適用される** が、それ以外の場合はドリフトを許可する | 構成が VM に適用されて、更新時に作成および再適用され、ドリフトが発生した場合は次の間隔で修正される |
| 強制の無効化 | 状態の報告のみ | 構成が適用されるが、ドリフトが許可される | 構成が VM に適用され、作成または更新されて、ドリフトが発生した場合に次の間隔で修正される |

## <a name="next-steps"></a>次の手順

- [ゲスト構成の概要](./guest-configuration.md)に関するページを確認する。
- カスタム ゲスト構成パッケージの[開発環境](../how-to/guest-configuration-create-setup.md)を設定する。
- ゲスト構成用の[パッケージ成果物を作成する](../how-to/guest-configuration-create.md)。
- 開発環境から[パッケージ成果物をテストする](../how-to/guest-configuration-create-test.md)。
- `GuestConfiguration` モジュールを使用して、環境を大規模に管理するための [Azure Policy の定義を作成する](../how-to/guest-configuration-create-definition.md)。
- Azure portal を使用して[カスタム ポリシー定義を割り当てる](../assign-policy-portal.md)。
- [ゲスト構成のポリシー割り当てのコンプライアンスの詳細](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)を確認する方法を学ぶ。
