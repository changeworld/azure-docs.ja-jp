---
title: Azure Policy を使用して Azure Monitor for VMs を有効にする | Microsoft Docs
description: この記事では、Azure Policy を使用して、複数の Azure 仮想マシンまたは仮想マシン スケール セットで Azure Monitor for VMs を有効にする方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: magoedte
ms.openlocfilehash: 9664ad5272ffeb55bd85e3d4c4f4b70d05e97702
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524047"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-policy"></a>Azure Policy を使用して Azure Monitor for VMs (プレビュー) を有効にする

この記事では、Azure Policy を使用して、Azure 仮想マシンまたは仮想マシン スケール セットで Azure Monitor for VMs (プレビュー) を有効にする方法について説明します。 このプロセスの最後には、Log Analytics エージェントと依存関係エージェントの有効化が正常に構成され、準拠していない仮想マシンが識別されています。

すべての Azure 仮想マシンまたは仮想マシン スケール セットで Azure Monitor for VMs を検出、管理、および有効化するには、Azure Policy または Azure PowerShell のどちらかを使用できます。 Azure Policy は、新しくプロビジョニングされた VM の一貫性のあるコンプライアンスと自動的な有効化が保証されるようにサブスクリプションを効果的に制御するポリシー定義を管理できるため、これが推奨される方法です。 これらのポリシー定義は、次のことを行います。

* Log Analytics エージェントと Dependency Agent をデプロイします。
* コンプライアンスの結果を報告します。
* 準拠していない VM を修復します。

Azure PowerShell または Azure Resource Manager テンプレートを使用してこれを実行することに関心がある場合は、[Azure PowerShell または Resource Manager テンプレートを使用した有効化](vminsights-enable-at-scale-powershell.md)に関するページを参照してください。 

## <a name="manage-policy-coverage-feature-overview"></a>[Manage Policy Coverage] (ポリシー対象範囲の管理) 機能の概要

当初、Azure Monitor for VMs のポリシー定義を管理およびデプロイするための Azure Policy によるエクスペリエンスは Azure Policy からのみ実行されました。 **[Manage Policy Coverage] (ポリシー対象範囲の管理)** 機能を使用すると、前に説明したポリシー定義を含む **[Azure Monitor for VMs の有効化]** イニシアチブを大規模に検出、管理、および有効化することがより単純かつ簡単になります。 この新機能には、Azure Monitor for VMs の **[Get started] (開始する)** タブから **[Manage Policy Coverage] (ポリシー対象範囲の管理)** を選択してアクセスできます。 それにより、VM の [Policy Coverage] (ポリシー対象範囲) ページが開かれます。 

![Azure Monitor for VMs の [Get Started] (開始する) タブ](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

ここから、管理グループおよびサブスクリプション全体にわたるイニシアチブの対象範囲を確認して管理したり、各管理グループおよびサブスクリプション内に存在する VM の数やそれらのコンプライアンス状態を把握したりすることができます。   

![Azure Monitor for VMs の [Policy Coverage] (ポリシー対象範囲) ページ](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

これらの情報は、1 つの中央の場所から Azure Monitor for VM のためのガバナンス シナリオを計画したり、実行したりするために役立ちます。 Azure Policy では、ポリシー/イニシアチブがスコープに割り当てられているときのコンプライアンス ビューが提供されるのに対して、この新しいページを使用すると、ポリシー/イニシアチブが割り当てられていない場所を検出し、所定の場所に割り当てることができます。 すべてのアクション (割り当て、表示、編集) が Azure Policy に直接リダイレクトされます。 Azure Monitor for VMs の [Policy Coverage] (ポリシー対象範囲) ページは、 **[Azure Monitor for VMs の有効化]** イニシアチブに対してのみ拡張および統合されたエクスペリエンスです。 

このページからは、Azure Monitor for VMs のための Log Analytics ワークスペースを構成することもできます。これにより、次が実行されます。

- サービス マップのインストールおよび Infrastructure Insights ソリューションをインストールします。
- パフォーマンス グラフ、ワークブック、およびカスタム ログ クエリとアラートによって使用されるオペレーティング システムのパフォーマンス カウンターを有効にします。

![Azure Monitor for VMs のワークスペースの構成](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

このオプションはどのポリシー アクションにも関連しておらず、Azure Monitor for VMs を有効にするために必要な[前提条件](vminsights-enable-overview.md)を満たす簡単な方法を提供できます。  

### <a name="what-information-is-available-on-this-page"></a>このページで入手できる情報
次の表は、[Policy Coverage] (ポリシー対象範囲) ページに表示される情報の内訳とその解釈方法を示しています。

| Function | 説明 | 
|----------|-------------| 
| **スコープ** | 現在保有しているか、または管理グループ階層を通してドリル ダウンする能力を使用してアクセスできる対象として継承した管理グループおよびサブスクリプション。|
| **ロール** | スコープに対するロール (閲覧者、所有者、共同作成者のいずれか)。 場合によっては、サブスクリプションにはアクセスできるが、それが属する管理グループにアクセスできないことを示すために空白で表示されることがあります。 他の列の情報は、ロールによって異なります。ロールは、ユーザーが表示できるデータや、イニシアチブ/ポリシー (所有者) の割り当てまたは編集、コンプライアンスの表示などの点から見てユーザーが実行できるアクションを決定する上で重要であるためです。 |
| **[Total VMs] (VM の総数)** | そのスコープの下にある VM の数。 管理グループの場合、これは、サブスクリプションまたは子管理グループ、あるいはその両方の下で入れ子になった VM の総数です。 |
| **[Assignment Coverage] (割り当て対象範囲)** | イニシアチブ/ポリシーの対象となる VM の割合 (%)。 |
| **[Assignment Status] (割り当て状態)** | この列で、ポリシー/イニシアチブの割り当ての状態に関する情報を見つけることができます。 |
| **[Compliant VMs] (準拠している VM)** | ポリシー/イニシアチブの下で準拠している VM の数。 **[Azure Monitor for VMs の有効化]** イニシアチブの場合、これは、Log Analytics エージェントと依存関係エージェントの両方を含む VM の数です。 場合によっては、割り当てがない、VM がない、十分なアクセス許可がないのいずれかの理由により空白で表示されることがあります。 情報は [コンプライアンスの状態] の下に表示されます。 |
| **コンプライアンス** | 全体的なコンプライアンスの数値は、準拠している個別のリソースの総数をすべての個別のリソースの総数で割った値になります。 |
| **コンプライアンスの状態** | ポリシー/イニシアチブの割り当てのためのコンプライアンスの状態に関する情報。|

イニシアチブ/ポリシーを割り当てる場合、割り当てで選択されたスコープは、一覧表示されているスコープまたはそのサブセットである可能性があります。 たとえば、管理グループ (対象範囲スコープ) ではなく、サブスクリプション (ポリシー スコープ) の割り当てを作成している場合があります。 この場合、 **[Assignment Coverage] (割り当て対象範囲)** の値は、ポリシー (またはイニシアチブ) スコープ内の VM 数を対象範囲スコープ内の VM 数で割った値を示します。 あるいは、ポリシー スコープからいくつかの VM、リソース グループ、またはサブスクリプションを除外している可能性があります。 この値が空白である場合は、ポリシー/イニシアチブが存在しないか、またはユーザーにアクセス許可がないことを示します (情報は [Assignment Status] (割り当て状態) に表示されます)。

## <a name="enable-using-azure-policy"></a>Azure Policy を使用して有効にする

テナントで Azure Policy を使用して Azure Monitor for VMs を有効にするには:

- スコープにイニシアティブを割り当てる: 管理グループ、サブスクリプション、またはリソース グループ
- コンプライアンスの結果をレビューおよび修復する

続ける前に、Azure Policy の割り当ての詳細について、[Azure Policy の概要](../../governance/policy/overview.md#policy-assignment)および[管理グループの概要](../../governance/management-groups/index.md)に関する記事をご覧ください。

### <a name="policies-for-azure-vms"></a>Azure VM のポリシー

次の表は、Azure VM のポリシー定義の一覧を示しています。

|Name |説明 |Type |
|-----|------------|-----|
|[プレビュー]:Azure Monitor for VMs の有効化 |指定されたスコープ (管理グループ、サブスクリプション、またはリソース グループ) で、Azure Monitor for VMs を有効にします。 Log Analytics ワークスペースをパラメーターとして受け取ります。 |イニシアティブ |
|[プレビュー]:Audit Dependency Agent のデプロイ - 一覧にない VM イメージ (OS) |一覧で VM イメージ (OS) が定義されておらず、エージェントがインストールされていない場合は、VM を非準拠として報告します。 |ポリシー |
|[プレビュー]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS) |一覧で VM イメージ (OS) が定義されておらず、エージェントがインストールされていない場合は、VM を非準拠として報告します。 |ポリシー |
|[プレビュー]:Linux VM への Dependency Agent のデプロイ |VM イメージ (OS) が一覧で定義されており、エージェントがインストールされていない場合は、Linux VM に Dependency Agent をデプロイします。 |ポリシー |
|[プレビュー]:Windows VM への Dependency Agent のデプロイ |VM イメージ (OS) が一覧で定義されており、エージェントがインストールされていない場合は、Windows VM に Dependency Agent をデプロイします。 |ポリシー |
|[プレビュー]:Linux VM への Log Analytics エージェントのデプロイ |VM イメージ (OS) が一覧で定義されており、エージェントがインストールされていない場合は、Linux VM に Log Analytics エージェントをデプロイします。 |ポリシー |
|[プレビュー]:Windows VM への Log Analytics エージェントのデプロイ |VM イメージ (OS) が一覧で定義されており、エージェントがインストールされていない場合は、Windows VM に Log Analytics エージェントをデプロイします。 |ポリシー |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セットのポリシー

次の表は、Azure 仮想マシン スケール セットのポリシー定義の一覧を示しています。

|Name |説明 |Type |
|-----|------------|-----|
|[プレビュー]:VM スケール セット (VMSS) 用の Azure Monitor を有効にする |指定されたスコープ (管理グループ、サブスクリプション、またはリソース グループ) で仮想マシン スケール セット用の Azure Monitor を有効にします。 Log Analytics ワークスペースをパラメーターとして取得します。 注: スケール セットの upgradePolicy が [手動] に設定されている場合は、そのセット内のすべての VM でアップグレードを呼び出すことにより、それらの VM に拡張機能を適用する必要があります。 CLI では、これは az vmss update-instances になります。 |イニシアティブ |
|[プレビュー]:VMSS 内の依存関係エージェントのデプロイを監査する – VM イメージ (OS) が一覧にない |VM イメージ (OS) が一覧で定義されておらず、エージェントがインストールされていない場合は、仮想マシン スケール セットを非準拠として報告します。 |ポリシー |
|[プレビュー]:VMSS 内の Log Analytics エージェントのデプロイを監査する – VM イメージ (OS) が一覧にない |VM イメージ (OS) が一覧で定義されておらず、エージェントがインストールされていない場合は、仮想マシン スケール セットを非準拠として報告します。 |ポリシー |
|[プレビュー]:Linux VM スケール セット (VMSS) 用の依存関係エージェントをデプロイする |VM イメージ (OS) が一覧で定義されており、エージェントがインストールされていない場合は、Linux 仮想マシン スケール セット用の依存関係エージェントをデプロイします。 |ポリシー |
|[プレビュー]:Windows VM スケール セット (VMSS) 用の依存関係エージェントをデプロイする |VM イメージ (OS) が一覧で定義されており、エージェントがインストールされていない場合は、Windows 仮想マシン スケール セット用の依存関係エージェントをデプロイします。 |ポリシー |
|[プレビュー]:Linux VM スケール セット (VMSS) 用の Log Analytics エージェントのデプロイ |VM イメージ (OS) が一覧で定義されており、エージェントがインストールされていない場合は、Linux 仮想マシン スケール セット用の Log Analytics エージェントをデプロイします。 |ポリシー |
|[プレビュー]:Windows VM Scale Sets (VMSS) 用の Log Analytics エージェントのデプロイ |VM イメージ (OS) が一覧で定義されており、エージェントがインストールされていない場合は、Windows 仮想マシン スケール セット用の Log Analytics エージェントをデプロイします。 |ポリシー |

ここではスタンドアロン ポリシー (イニシアティブには含まれません) について説明します。

|Name |説明 |Type |
|-----|------------|-----|
|[プレビュー]:VM 用 Audit Log Analytics ワークスペース - 不一致の報告 |ポリシー/イニシアティブの割り当てで指定された Log Analytics ワークスペースにログインしていない場合、VM を非準拠として報告します。 |ポリシー |

### <a name="assign-the-azure-monitor-initiative"></a>Azure Monitor のイニシアティブを割り当てる
Azure Monitor for VMs の [Policy Coverage] (ポリシー対象範囲) ページからポリシー割り当てを作成するには、次の手順を実行します。 これらの手順を完了する方法については、 [Azure portal でのポリシー割り当ての作成](../../governance/policy/assign-policy-portal.md)に関する記事を参照してください。

イニシアチブ/ポリシーを割り当てる場合、割り当てで選択されたスコープは、ここに一覧表示されているスコープまたはそのサブセットである可能性があります。 たとえば、管理グループ (対象範囲スコープ) ではなく、サブスクリプション (ポリシー スコープ) の割り当てを作成している場合があります。この場合、対象範囲の % は、ポリシー (またはイニシアチブ) スコープ内の VM 数を対象範囲スコープ内の VM 数で割った値を示します。 あるいは、ポリシー スコープからいくつかの VM、RG、またはサブスクリプションを除外している可能性があります。  これが空白である場合は、ポリシー/イニシアチブが存在しないか、またはユーザーにアクセス許可がないことを示します (情報は [Assignment Status] (割り当て状態) に表示されます)。  

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. Azure portal で、 **[モニター]** を選択します。 

3. **[分析情報]** セクションの **[Virtual Machines (プレビュー)]** を選択します。
 
4. **[Get Started] (開始する)** タブを選択し、そのページで **[Manage Policy Coverage] (ポリシー対象範囲の管理)** を選択します。

5. 表から管理グループまたはサブスクリプションのどちらかを選択し、省略記号ボタン (...) をクリックして **[スコープ]** を選択します。    この例では、スコープにより、ポリシーの割り当てが仮想マシンのグループに制限されて実施されます。

6. **[Azure Policy assignment] (Azure Policy の割り当て)** ページには、 **[Azure Monitor for VMs の有効化]** イニシアチブが事前に入力されています。 
    **[割り当て名]** ボックスにはイニシアチブ名が自動的に入力されますが、これは変更できます。 必要に応じて、説明を追加することもできます。 **[割り当て担当者]** ボックスは、ログインしたユーザーに基づいて自動的に入力されます。この値は省略可能です。

7. (省略可能) スコープから 1 つまたは複数のリソースを削除するには、 **[除外]** を選択します。

8. サポートされているリージョンの **[Log Analytics ワークスペース]** ドロップダウン リストで、ワークスペースを選択します。

   > [!NOTE]
   > ワークスペースが割り当てのスコープの範囲外にある場合は、ポリシー割り当てのプリンシパル ID に "*Log Analytics 共同作成者*" アクセス許可を付与します。 そうしないと、次のようなデプロイ エラーが表示される可能性があります。`The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` アクセス権の付与については、[マネージド ID を手動で構成する方法](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)に関する記事をご覧ください。
   > 
   >  割り当てられるイニシアティブには *deployIfNotExists* 効果のあるポリシーが含まれているため、 **[マネージド ID]** チェック ボックスがオンになります。
    
9. **[マネージド ID の場所]** ドロップダウン リストで、適切なリージョンを選択します。

10. **[割り当て]** を選択します。

割り当てを作成すると、Azure Monitor for VMs の [Policy Coverage] (ポリシー対象範囲) ページでは、[Assignment Coverage] (割り当て対象範囲)、[Assignment Status] (割り当て状態)、[Compliance VMs] (コンプライアンス VM)、および [コンプライアンスの状態] が変更を反映して更新されます。 

次のマトリックスは、イニシアチブに関する可能性のあるコンプライアンスの各状態をマップしています。  

| コンプライアンスの状態 | 説明 | 
|------------------|-------------|
| **[Compliant] (準拠)** | スコープ内のすべての VM に Log Analytics エージェントと依存関係エージェントがデプロイされています。|
| **[Not Compliant] (非準拠)** | スコープ内の一部の VM に Log Analytics エージェントと依存関係エージェントがデプロイされておらず、修復を必要としている可能性があります。|
| **[Not started] (未開始)** | 新しい割り当てが追加されました。 |
| **[Lock] (ロック)** | ユーザーに管理グループに対する十分な特権がありません。<sup>1</sup> | 
| "**空白**" | ポリシーが割り当てられていません。 | 

<sup>1</sup> 管理グループにアクセスできない場合は、所有者にアクセスを提供するよう依頼するか、または子管理グループまたはサブスクリプションを使用してコンプライアンスを表示し、割り当てを管理してください。 

次の表は、イニシアチブに関する可能性のある割り当ての各状態をマップしています。

| 割り当て状態 | 説明 | 
|------------------|-------------|
| **Success** | スコープ内のすべての VM に Log Analytics エージェントと依存関係エージェントがデプロイされています。|
| **Warning** | サブスクリプションが管理グループの下にありません。|
| **[Not started] (未開始)** | 新しい割り当てが追加されました。 |
| **[Lock] (ロック)** | ユーザーに管理グループに対する十分な特権がありません。<sup>1</sup> | 
| "**空白**" | VM が存在しないか、またはポリシーが割り当てられていません。 | 
| **アクション** | ポリシーの割り当てまたは割り当ての編集 | 

<sup>1</sup> 管理グループにアクセスできない場合は、所有者にアクセスを提供するよう依頼するか、または子管理グループまたはサブスクリプションを使用してコンプライアンスを表示し、割り当てを管理してください。 

## <a name="review-and-remediate-the-compliance-results"></a>コンプライアンスの結果をレビューおよび修復する

次の例は Azure VM に対するものですが、仮想マシン スケール セットにも適用されます。 コンプライアンスの結果をレビューする方法については、[非準拠の結果の確認](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)に関する記事を参照してください。 Azure Monitor for VMs の [Policy Coverage] (ポリシー対象範囲) ページで、表から管理グループまたはサブスクリプションのどちらかを選択し、省略記号ボタン (...) をクリックして **[View Compliance] (コンプライアンスの表示)** を選択します。   

![Azure VM のポリシーへの準拠](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

以下のシナリオでは、イニシアティブに含まれるポリシーの結果に基づいて、VM が非準拠として報告されます。

* Log Analytics も Dependency Agent もデプロイされていない。  
    このシナリオは、既存の VM を含むスコープでよくみられます。 これを軽減するには、非準拠ポリシーで[修復タスクを作成する](../../governance/policy/how-to/remediate-resources.md)ことにより、必要なエージェントをデプロイします。  
    - [プレビュー]: Deploy Dependency Agent for Linux VMs
    - [プレビュー]: Deploy Dependency Agent for Windows VMs
    - [プレビュー]: Deploy Log Analytics Agent for Linux VMs
    - [プレビュー]: Deploy Log Analytics Agent for Windows VMs

* VM イメージ (OS) が、ポリシー定義で示されていない。  
    デプロイ ポリシーの基準には、よく知られている Azure VM イメージからデプロイされた VM のみが含まれます。 VM の OS がサポートされているかどうか、ドキュメントで確認します。 サポートされていない場合は、デプロイ ポリシーを複製し、イメージが準拠するようにそれを更新または変更します。  
    - [プレビュー]:Audit Dependency Agent のデプロイ - 一覧にない VM イメージ (OS)
    - [プレビュー]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)

* VM が、指定された Log Analytics ワークスペースにログインしていない。  
    イニシアティブ スコープ内の一部の VM が、ポリシー割り当てで指定されているもの以外の Log Analytics ワークスペースにログインしている可能性があります。 このポリシーは、非準拠のワークスペースに報告を行っている VM を識別するための手段となります。  
    - [プレビュー]: Audit Log Analytics Workspace for VM - Report Mismatch

## <a name="edit-initiative-assignment"></a>イニシアチブの割り当てを編集する

管理グループまたはサブスクリプションにイニシアチブを割り当てた後いつでも、それを編集して次のプロパティを変更できます。

- 割り当て名
- 説明
- 割り当て担当者
- Log Analytics ワークスペース
- 例外

## <a name="next-steps"></a>次の手順

これで仮想マシンに対して監視が有効になったので、これらの情報を Azure Monitor for VMs での分析に使用できます。 正常性機能の使用方法については、[Azure Monitor for VMs の正常性の表示](vminsights-health.md)に関する記事をご覧ください。 検出されたアプリケーションの依存関係を表示するには、[Azure Monitor for VMs のマップの表示](vminsights-maps.md)に関する記事をご覧くださいい。 VM のパフォーマンスのボトルネックや全体的な使用率を識別するには、[Azure VM のパフォーマンスの表示](vminsights-performance.md)に関するページを参照してください。または、検出されたアプリケーションの依存関係を表示するには、[VM 用 Azure Monitor のマップの表示](vminsights-maps.md)に関するページを参照してください。