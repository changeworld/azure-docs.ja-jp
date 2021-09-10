---
title: 配置ポリシーを作成する (プレビュー)
description: Azure VMware Solution で配置ポリシーを作成して、クラスター内のホスト上の仮想マシン (VM) の配置を Azure portal を介して制御する方法について説明します。
ms.topic: how-to
ms.date: 8/18/2021
ms.openlocfilehash: 85146ce86dea0d3cfa7397cdaae6fefc8cf4a23b
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967545"
---
# <a name="create-a-placement-policy-in-azure-vmware-solution-preview"></a>Azure VMware Solution で配置ポリシーを作成する (プレビュー)

>[!IMPORTANT]
>Azure VMware Solution の配置ポリシー (プレビュー) は現在プレビュー段階にあります。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 プレビュー機能を使用するには、["_DRS 配置ポリシー_" と "_早期アクセス_" の両方の機能を登録する必要があります](https://ms.portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade)。  機能を検索して選択し、 **[登録]** を選択します。

Azure VMware Solution では、プライベート クラウド内のクラスターは管理対象リソースです。 結果的に、cloudadmin ロールは、Distributed Resource Scheduler (DRS) ルールの管理など、vSphere クライアントからクラスターに対して特定の変更を行うことはできません。

配置ポリシー機能は、すべての Azure VMware Solution リージョンで利用できます。  配置ポリシーを使用すると、クラスター内のホスト上の仮想マシン (VM) の配置を Azure portal を介して制御できます。 配置ポリシーを作成すると、指定した vSphere クラスターに DRS ルールが含まれます。 また、Azure VMware Solution の運用との相互運用性のための追加のロジックも組み込まれます。

配置ポリシーには、少なくとも 5 つの必須の構成要素があります。 

- **名前** - ポリシーの名前を定義します。これは、[Azure リソース](../azure-resource-manager/management/resource-name-rules.md)の名前付けの制約に従います。

- **種類** - ポリシーに含まれるリソースに適用する制御の種類を定義します。

- **クラスター** - ポリシーのクラスターを定義します。 配置ポリシーのスコープは vSphere クラスターであるため、同じ配置ポリシーに含めることができるのは同じクラスターのリソースのみです。

- **状態** - ポリシーが有効であるか、無効であるかを定義します。 特定のシナリオでは、競合するルールが作成されると、ポリシーが自動的に無効になる場合があります。 詳細については、下記の「[考慮事項](#considerations)」を参照してください。

- **仮想マシン** - ポリシーの VM とホストを定義します。 作成するルールの種類によっては、ポリシーに VM とホストの数を指定することが必要な場合があります。 詳細については、下記の「[配置ポリシーの種類](#placement-policy-types)」を参照してください。


## <a name="prerequisites"></a>前提条件

- 配置ポリシーを管理するには、プライベート クラウドに対する _[共同作成者]_ レベルのアクセス権が必要です。

- "_DRS 配置ポリシー_" と "_早期アクセス_" の[機能が登録されています](https://ms.portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade)。


## <a name="placement-policy-types"></a>配置ポリシーの種類

### <a name="vm-vm-policies"></a>VM 間のポリシー

**VM 間** のポリシーでは、選択した VM を同じホストで実行するか、別のホストに保持するかを指定します。  **VM 間** のポリシーでは、ポリシーの名前とクラスターを選択するだけでなく、割り当てる VM を少なくとも 2 つ選択する必要があります。 このポリシーの種類の場合、ホストの割り当ては必須ではなく、許可もされていません。

- **VM 間のアフィニティ** のポリシーは、指定した VM を同じホストでまとめて保持するように DRS に指示します。 これは、パフォーマンス上の理由などで役に立ちます。

- **VM 間の非アフィニティ** のポリシーは、指定した VM を相互に分離して別個のホストで保持するように DRS に指示します。 これは、1 つのホストの問題が同じポリシー内の複数の VM に影響を及ぼさないシナリオで役立ちます。


### <a name="vm-host-policies"></a>VM とホスト間のポリシー

**VM とホスト間** のポリシーでは、選択した VM を選択したホストで実行できるかどうかを指定します。  ホストのメンテナンス モードやホストの交換など、プラットフォーム マネージド操作に対する干渉を避けるため、Azure VMware Solution の **VM とホスト間** のポリシーは常に優先されます ("必要" ルールとも呼ばれます)。 したがって、**VM とホスト間** のポリシーは、[特定のシナリオで受け入れられない場合があります](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-793013E2-0976-43B7-9A00-340FA76859D0.html)。 詳細については、下記の「[ポリシーの運用を監視する](#monitor-the-operation-of-a-policy)」を参照してください。

特定のプラットフォーム操作により、**VM とホスト間** のポリシーで定義されているホストの一覧が動的に更新されます。 たとえば、配置ポリシーのメンバーであるホストを削除すると、そのポリシーに複数のホストが含まれている場合、そのホストは削除されます。 また、あるホストがポリシーに含まれ、プラットフォーム マネージド操作の一部として置き換えられる必要がある場合、ポリシーは新しいホストで動的に更新されます。

**VM とホスト間** のポリシーでは、ポリシーの名前とクラスターを選択するだけでなく、少なくとも1 つの VM と 1 つのホストを選択してポリシーに割り当てる必要があります。

- **VM とホスト間のアフィニティ** のポリシーは、定義されたホストで、指定した VM の実行を試みるように DRS に指示します。

- **VM とホスト間の非アフィニティ** のポリシーは、定義されたもの以外のホストで、指定した VM の実行を試みるように DRS に指示します。


## <a name="considerations"></a>考慮事項

### <a name="cluster-scale-in"></a>クラスターのスケールイン

Azure VMware Solution では、クラスターのスケールイン操作の実行時、特定の DRS ルールの違反の発生を防ぐよう試みます。

VM とホスト間のポリシーから最後のホストを削除することはできません。 ただし、ポリシーから最後のホストを削除する必要がある場合は、クラスターからホストを削除する前にそのポリシーに別のホストを追加することでこれに対処できます。 または、ホストを削除する前に配置ポリシーを削除することもできます。

クラスター内のホストの数を超える VM を含む、VM 間の非アフィニティのポリシーを設定することはできません。 ホストを削除すると、VM よりもクラスター内のホストの数が少なくなる場合、操作を阻止するエラーが発生します。 これを修復するには、まずルールから VM を削除し、その後クラスターからホストを削除します。


### <a name="rule-conflicts"></a>ルールの競合

VM 間のポリシーを作成するときに DRS ルールの競合が検出された場合、標準の [VMware DRS ルールの動作](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-69C738B6-5FC8-4189-9CB5-DD90A5A05979.html)に従って、そのポリシーが無効な状態で作成されます。 ルールの競合を表示する方法の詳細については、下記の「[ポリシーの運用を監視する](#monitor-the-operation-of-a-policy)」を参照してください。



## <a name="create-a-placement-policy"></a>配置ポリシーを作成する

作成できるポリシーの数に定義された制限はありません。 ただし、配置の制約が多いほど、vSphere DRS でクラスター内の仮想マシンを効果的に移動し、ワークロードに必要なリソースを提供することが難しくなります。      

[ポリシーの種類](#placement-policy-types)の要件を必ず確認してください。

1. Azure VMware Solution プライベート クラウドで、 **[管理]** の下にある **[Placement policies]\(配置ポリシー\)**  >  **[+ 作成]** の順に選択します。

   >[!TIP]
   >配置ポリシーの [概要] ペインでクラスターを選択し、 **[作成]** を選択することもできます。
   >
   >:::image type="content" source="media/placement-policies/create-placement-policy-cluster.png" alt-text="配置ポリシーを作成するための代替オプションを示すスクリーンショット。":::



   :::image type="content" source="media/placement-policies/create-placement-policy.png" alt-text="VM 間の配置ポリシーを作成するプロセスを開始する方法を示すスクリーンショット。" lightbox="media/placement-policies/create-placement-policy.png":::


1. わかりやすい名前を指定し、ポリシーの種類を選択して、ポリシーが作成されるクラスターを選択します。 その後、**[有効化]** を選択します。

   >[!WARNING]
   >ポリシーを無効にした場合、ポリシーと基になる DRS ルールは作成されますが、ポリシーを有効にするまでポリシーのアクションは無視されます。 

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-host-affinity-1.png" alt-text="配置ポリシーのオプションを示すスクリーンショット。" lightbox="media/placement-policies/create-placement-policy-vm-host-affinity-1.png":::   

1. 種類として **[VM-Host affinity]\(VM とホスト間のアフィニティ\)** または **[VM-Host anti-affinity]\(VM とホスト間の非アフィニティ\)** を選択した場合は、 **[+ ホストの追加]** を選択してポリシーに含めるホストを選択します。 複数のホストを選択できます。

   >[!NOTE]
   >[Select hosts]\(ホストの選択\) ペインには、ホストに関連付けられている VM とホスト間ポリシーの数と、それらの関連付けられているポリシーに含まれる VM の合計数が表示されます。
   >
   >:::image type="content" source="media/placement-policies/hosts-associated-policies-vms.png" alt-text="ホストに関連付けられている VM-Host ポリシーの数と、それらに関連付けられているポリシーに含まれる VM の数を示すスクリーンショット。":::


1. **[+ Add virtual machines]\(+ 仮想マシンの追加\)** と、ポリシーに含める VM を選択します。 複数の VM を選択できます。

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-vm-affinity-2.png" alt-text="選択する VM の一覧を示すスクリーンショット。":::
   
   >[!NOTE]
   >[Select hosts]\(ホストの選択\) ペインには、ホストに関連付けられている VM とホスト間ポリシーの数と、それらの関連付けられているポリシーに含まれる VM の合計数が表示されます。 

1. 必要な VM の追加が完了したら、 **[Add virtual machines]\(仮想マシンの追加\)** を選択します。 

1. **[次へ: 確認と作成]** を選択して、ポリシーを確認します。 

1. **[ポリシーの作成]** を選択します。 変更を加える場合は、 **[Back: Basics]\(戻る: 基本\)** を選択します。

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-vm-affinity-3.png" alt-text="作成前の配置ポリシーの設定を示すスクリーンショット。":::

1. 配置ポリシーを作成したら、 **[最新の情報に更新]** を選択して一覧に表示します。

   :::image type="content" source="media/placement-policies/create-placement-policy-8.png" alt-text="作成後、配置ポリシーを [有効] として示すスクリーンショット。" lightbox="media/placement-policies/create-placement-policy-8.png":::



## <a name="edit-a-placement-policy"></a>配置ポリシーを編集する

ポリシーの状態の変更、新しいリソースの追加、または既存のリソースの割り当て解除を行うことができます。

### <a name="change-the-policy-state"></a>ポリシーの状態を変更する

ポリシーの状態を **[有効]** または **[無効]** に変更することができます。 

1. Azure VMware Solution プライベート クラウドで、 **[管理]** の下にある **[Placement policies]\(配置ポリシー\)** を選択します。

1. 編集するポリシーで、 **[詳細]** (...) を選択し、 **[編集]** を選択します。

   >[!TIP]
   >[設定] ドロップダウンから **[無効化]** を選択して、配置ポリシーの概要からポリシーを無効にすることができます。 [設定] ドロップダウンからポリシーを有効にすることはできません。

   :::image type="content" source="media/placement-policies/edit-placement-policy.png" alt-text="配置ポリシーの編集方法を示すスクリーンショット。" lightbox="media/placement-policies/edit-placement-policy.png":::

1.  ポリシーが有効になっていて、無効にする場合は、 **[無効化]** を選択し、確認メッセージで **[無効化]** を選択します。 そうではなく、ポリシーが無効になっていて、有効にする場合は、 **[有効化]** を選択します。

1.  **[確認と更新]** を選択します。 
 
1.  変更内容を確認し、 **[Update policy]\(ポリシーの更新\)** を選択します。 変更を加える場合は、 **[Back: Basics]\(戻る: 基本\)** を選択します。


### <a name="update-the-resources-in-a-policy"></a>ポリシー内のリソースを更新する

新しいリソース (VM やホストなど) をポリシーに追加したり、既存のものを削除したりできます。 

1. Azure VMware Solution プライベート クラウドで、 **[管理]** の下にある **[Placement policies]\(配置ポリシー\)** を選択します。

1. 編集するポリシーで、 **[詳細]** (...)、 **[編集]** の順に選択します。

   :::image type="content" source="media/placement-policies/edit-placement-policy.png" alt-text="配置ポリシー内のリソースを編集する方法を示すスクリーンショット。" lightbox="media/placement-policies/edit-placement-policy.png":::

   - 既存のリソースを削除するには、削除する 1 つ以上のリソースを選択して、 **[割り当て解除]** を選択します。 

      :::image type="content" source="media/placement-policies/edit-placement-policy-unassign.png" alt-text="配置ポリシーから既存リソースを削除する方法を示すスクリーンショット。":::

   - 新しいリソースを追加するには、 **[Edit virtual machines]\(仮想マシンの編集\)** または **[Edit hosts]\(ホストの編集\)** を選択し、追加するリソースを選択して、 **[保存]** を選択します。 

1. **[Next : Review and update]\(次へ: 確認と更新\)** を選択します。 

1. 変更内容を確認し、 **[Update policy]\(ポリシーの更新\)** を選択します。  変更を加える場合は、 **[Back: Basics]\(戻る: 基本\)** を選択します。



## <a name="delete-a-policy"></a>ポリシーの削除

配置ポリシーとそれに対応する DRS ルールを削除できます。 

1. Azure VMware Solution プライベート クラウドで、 **[管理]** の下にある **[Placement policies]\(配置ポリシー\)** を選択します。

1. 編集するポリシーで、 **[詳細]** (...) を選択し、 **[削除]** を選択します。

   :::image type="content" source="media/placement-policies/delete-placement-policy.png" alt-text="配置ポリシーの削除方法を示すスクリーンショット。" lightbox="media/placement-policies/delete-placement-policy.png":::

1. 確認メッセージで **[削除]** を選択します。



## <a name="monitor-the-operation-of-a-policy"></a>ポリシーの運用を監視する

配置ポリシーの対応する DRS ルールの運用を監視するには、vSphere クライアントを使用します。 

cloudadmin ロールの所有者は、クラスターの [構成] タブの [VM/Host Rules]\(VM/ホストのルール\) で、配置ポリシーによって作成された DRS ルールを表示できますが、編集することはできません。 これにより、DRS ルールが競合状態であるかどうかなど、追加情報を表示できます。

さらに、クラスターの [監視] タブから、推奨事項やエラーなど、さまざまな DRS ルールの運用を監視できます。


## <a name="faqs"></a>FAQ

### <a name="are-these-the-same-as-drs-affinity-rules"></a>これらは DRS アフィニティ ルールと同じですか?
場合によります。 vSphere DRS は現在の一連のポリシーを実装しますが、Microsoft ではこのエクスペリエンスを簡素化しました。 VM グループやホスト グループの変更は、特にホストが本質的に一時的なものであり、クラウド環境では置き換えられる可能性があるため、複雑な操作となっています。 オンプレミス環境の vSphere インベントリでホストが置き換えられた場合、vSphere 管理者はホスト グループを修正して、目的の VM-Host 配置制約が有効なままであることを確認する必要があります。 ホストがローテーションまたは変更されると、Azure VMware Solution の配置ポリシーによってホスト グループが更新されます。 同様に、クラスターでスケーリングすると、該当する場合、ホスト グループが自動的に更新されます。 これにより、顧客のホスト グループを管理するオーバーヘッドが解消されます。


### <a name="as-this-is-an-existing-functionality-available-in-vcenter-why-cant-i-use-it-directly"></a>vCenter で利用可能な既存の機能であるにも関わらず、直接使用できないのはなぜしょうか 

Azure VMware Solution は、Azure で VMware プライベート クラウドを提供します。 このマネージド VMware インフラストラクチャでは、Microsoft がプライベート クラウド内のクラスター、ホスト、データストア、および分散仮想スイッチを管理します。 同時に、テナントは、プライベート クラウドにデプロイされたワークロードの管理を担当します。 そのため、プライベート クラウドを管理するテナントには、オンプレミス展開に対して VMware 管理者が[持つような特権はありません](concepts-identity.md)。 

さらに、vSphere 特権に必要な細分性が無い場合、プライベート クラウド上のワークロードの配置を管理する際にいくつかの課題が生じます。 たとえば、アフィニティやアンチアフィニティのルールを定義するためにオンプレミスで一般的に使用されている vSphere DRS のルールは、そのままでは VMware Cloud 環境では使用できません。これは、それらのルールの一部によってプライベート クラウドの日常的な運用がブロックされる可能性があるためです。 配置ポリシーは、Azure VMware Solution ポータルを使用してこれらの規則を定義する方法を提供するため、DRS ルールを使用する必要性を回避することができます。 シンプルな操作性と組み合わさることによって、ルールが日常的なインフラストラクチャの保守や運用活動に影響を与えないようにします。 


###  <a name="what-caveats-should-i-know-about"></a>他に知っておくべき注意事項はありますか

VM-Host の **MUST** ルールはメンテナンス操作をブロックするため、サポートされません。 

VM-Host の **SHOULD** ルールは優先的なルールであり、vSphere DRS は可能な範囲でルールに対応します。 場合によっては、ワークロードで必要なリソースを確保できるようにするために、vSphere DRS から VM-Host の **SHOULD** ルールの対象となる VM に対して vMotion が使用されることがあります。 これは vSphere DRS の標準の動作であり、配置ポリシー機能によって、基になる vSphere DRS の動作が変更されることはありません。

競合するルールを作成すると、それらの競合が vCenter に表示され、新しく定義されたルールが有効にならない場合があります。 これは vSphere DRS の標準の動作であり、そのログは vCenter で確認することができます。
