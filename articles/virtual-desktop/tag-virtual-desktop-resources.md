---
title: Azure Virtual Desktop リソースへのタグ付けをする - Azure
description: タグ付けとは何か、また Azure Virtual Desktop における Azure サービスのコスト管理のためにタグ付けを利用する方法について説明します。
author: heidilohr
ms.topic: conceptual
ms.date: 11/12/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 1c77e7b2ec767cd843cbf34b7f3a44d074d20ab5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404272"
---
# <a name="tag-azure-virtual-desktop-resources-to-manage-costs"></a>Azure Virtual Desktop リソースへのタグ付けをしてコストを管理する

タグ付けは Azure サービス全体で利用できるツールであり、Azure サブスクリプション内のリソースを整理するのに役立ちます。 リソースの整理により、複数のサービスにわたるコストの追跡が簡単になります。 タグは、支払請求サイクルにおける Azure リソースの各グループのコストの把握にも役立ちます。 一般的なタグ付けの詳細については、「[タグを使って Azure リソースと管理階層を整理する](../azure-resource-manager/management/tag-resources.md)」をご覧ください。 Azure タグを使う他の方法について説明した [簡単な動画](https://www.youtube.com/watch?v=dUft4FZ40O8) もあります。

## <a name="how-tagging-works"></a>タグ付けはどのように機能するのか

Azure portal でまたは PowerShell を使って管理している Azure サービスには、タグ付けをすることができます。 タグは、キーと値のペアの文字列として表示されます。 タグ付けされた Azure リソースを使用すると、関連付けられたタグのキーと値のペアがリソースの使用状況にアタッチされます。

デプロイが使用状況に関するタグ付けされた情報を [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) に報告すると、タグ付け構造を用いてコスト データをフィルターにかけることができます。 Azure Cost Management のタグによるフィルター処理については、「[クイック スタート: コスト分析によるコストを調査し分析する](../cost-management-billing/costs/quick-acm-cost-analysis.md)」をご覧ください。

### <a name="add-edit-or-delete-tags"></a>タグの追加、編集、削除

新しいタグをリソースに適用しても、関連付けられている Azure リソースがアクティビティを報告するまでは、Azure Cost Management には表示されません。 既存のタグをリソースに適用した場合、この変更も Azure リソースがアクティビティを報告するまでは、 Azure Cost Management には表示されません。

タグ名を編集すると、関連付けられたリソースは、新しいキーと値のペアにコストを関連付けます。 古いタグを使用してデータをフィルターにかけることはできますが、変更後の新しいデータはすべて新しいタグで報告されます。

タグを削除すると、Azure Virtual Desktop は、削除されたタグに関連付けられたデータは、Azure Cost Management に報告しなくなります。 タグを削除する前に報告されたデータに対しては、その後も削除されたタグを使ってフィルターで抽出することができます。

>[!IMPORTANT]
>新しいタグまたは更新されたタグの適用以降アクティブになっていないタグ付けされた Azure リソースは、変更されたタグに関連付けられたアクティビティに関する Azure Cost Management への報告は行いません。 あるタグに対するフィルター処理は、そのタグに関連付けられたアクティビティがサービスによって Azure Cost Management に報告されてはじめて可能となります。

### <a name="view-all-existing-tags"></a>すべての既存のタグを見る

Azure portal に移動し、[ **[タグ]**  タブ](https://ms.portal.azure.com/#blade/HubsExtension/TagsBlade)を開くと、Azure サービスの既存のタグがすべて表示されます。[タグ] タブには、アクセスできるオブジェクトのすべてのタグが表示されます。 また、同時に多数のタグをすぐに更新する必要がある場合はいつでもキーまたは値でタグを並べ替えることができます。

### <a name="what-tags-can-and-cant-do"></a>タグができることとできないこと

タグは、直接割り当てられている Azure リソースの使用状況とコストデータのみを報告します。 あるリソースにタグを付け、その内部の他のリソースにはタグを付けない場合、Azure Virtual Desktop は、タグが付けられたトップレベルのリソースに関連するアクティビティのみを報告します。 請求データを正確にしたい場合は、トップレベルのリソース以下のすべてのリソースへのタグ付けも必要となります。

Azure Cost Management においてタグがどのように機能するかの詳細については、「[コストと使用状況のデータにおけるタグの使用方法](../cost-management-billing/costs/understand-cost-mgt-data.md#how-tags-are-used-in-cost-and-usage-data)」をご覧ください。

既知の Azure タグの制限事項の一覧については、「[タグを使って Azure リソースと管理階層を整理する](../azure-resource-manager/management/tag-resources.md#limitations)」をご覧ください。

## <a name="using-tags-in-azure-virtual-desktop"></a>Azure Virtual Desktop におけるタグの使用

Azure タグの基本を理解できましたので、Azure Virtual Desktop における Azure タグの使い方について説明します。

Azure タグを使って、顧客やユーザーに向けて仮想化されたエクスペリエンスの作成、管理、デプロイに掛かるコストを整理できます。 またタグ付けは、Azure Virtual Desktop や、Azure Virtual Desktop デプロイに接続されているその他の Azure サービスを通じて直接購入したリソースの追跡にも役立ちます。

## <a name="suggested-tags-for-azure-virtual-desktop"></a>Azure Virtual Desktop で推奨されるタグ

Azure Virtual Desktop は他の Azure サービスと連携してデプロイをサポートできるため、デプロイ リソースへのタグ付けに対する普遍的なシステムというものは存在しません。 最も重要なのは、ご自身と組織に対して機能する戦略を開発することです。 ただし、特に Azure を初めて使う場合に役立つ可能性がある、いくつかの推奨事項があります。 

次の推奨事項は、すべての Azure Virtual Desktop デプロイに当てはまります:

- タグ付けしたい範囲が理解できように、購入した Azure サービスについての理解を深めましょう。 Azure portal の使い方を学習するときは、タグを適用できるサービス グループとオブジェクトのリストを作るようにしましょう。 追跡しておくべきリソースとしては、リソース グループ、仮想マシン、ディスク、ネットワーク インターフェイス カード (NIC) などがあります。 タグを付けることができる、コストが生じるサービス コンポーネントのさらに包括的なリストについては、「[Azure Virtual Desktop デプロイの総コストを理解する](./remote-app-streaming/total-costs.md)」をご覧ください。

- コスト レポートの集計を作成してタグを整理しましょう。 [一般的なタグ付けパターンに従う](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging.md)こともできますし、組織のニーズに合う新しいパターンを作ることもできます。

- どこにタグを適用するかにかかわらず、タグの一貫性を保ち続けましょう。 どんなに小さな入力ミスでもデータのレポートに影響を与える可能性があるため、後に参照することになる正しいキーと値のペアを追加していることを確認してください。

- 更新または編集するタグの記録をつけましょう。 この記録により、必要に応じて各タグの履歴データをまとめることができます。 タグを編集または更新するときは、変更されたタグを含むすべてのリソースに対しても変更を適用しなくてはなりません。

## <a name="suggested-tags-for-azure-virtual-desktop-host-pools"></a>Azure Virtual Desktop ホスト プールで推奨されるタグ

Azure Virtual Desktop ホストプール内のすべての仮想マシンは、コスト生成のコンストラクトを作成します。 ホスト プールは Azure Virtual Desktop デプロイの土台であるため、その VM は通常 Azure Virtual Desktop デプロイの主なコスト源になります。 タグ付けシステムを配置したい場合は、デプロイ内のすべてのホスト プールにタグを付けて VM のコンピューティング コストを追跡することから始めることをお勧めします。 ホスト プールへのタグ付けは、Azure Cost Management でのフィルター処理を使ってこれらの VM のコストを特定する手助けとなります。

「[一般的な提案](#suggested-tags-for-azure-virtual-desktop)」といわれるものと同様に、ホスト プールへのタグ付けに関する普遍的なシステムというものはありません。 ただし、ホスト プール タグの整理に役立ついくつかの推奨事項があります:

- ホスト プール作成中のタグ付けは必須ではありませんが、作成プロセス中にタグ付けをしておくと、後に Azure Cost Management でタグ付けされたすべての使用状況が簡単に表示されるようになります。 ホスト プール タグは、ホスト プール内のセッション ホストのコストを生成するすべてのコンポーネントに従います。 セッション ホスト固有のコストの詳細については、「[Azure Virtual Desktop デプロイの総コストの理解](./remote-app-streaming/total-costs.md)」をご覧ください。

- Azure portal を使用して新しいホスト プールを作成する場合は、作成ワークフローによって既存のタグを追加することができます。 これらのタグは、ホスト プール作成プロセス中に作成したすべてのリソースに渡されます。 タグは、Azure portal 内の既存のホスト プールに追加するすべてのセッション ホストにも適用されます。 ただし、新しいセッション ホストを追加するたびに、手動でタグを入力する必要があります。

- 構成オプションは際限なく存在し各顧客に固有であるため、ホスト プールと連携してサポートを行うすべての Azure サービスの完全なコスト レポートが得られることはほとんどありません。 Azure Virtual Desktop デプロイに関連付けられているすべての Azure サービスのコストをどの程度綿密に追跡するかは、お客様が決定します。 タグ付けによってこれらのコストを入念に追跡すればするほど、月ごとの Azure Virtual Desktop のコスト レポートはより正確なものになるでしょう。

- ホスト プールの周囲にタグ付けシステムを構築する場合は、後に他の Azure サービスに追加することが意味をなすように必ずキーと値のペアを使ってください。

## <a name="suggested-tags-for-other-azure-virtual-desktop-resources"></a>他の Azure Virtual Desktop リソースに向けて推奨されるタグ

ほとんどの Azure Virtual Desktop のお客様は、デプロイをサポートするために他の Azure サービスをデプロイします。 これらの追加サービスのコストをコスト レポートに含める場合は、次の推奨事項を考慮する必要があります:

- Azure Virtual Desktop のデプロイに組み入れたい Azure サービスまたはリソースを既に購入している場合、次の 2 つのオプションがあります:
   
   - 購入した Azure サービスを異なる Azure サブスクリプション間で分離する。
   - 同じサブスクリプションで購入したすべての Azure サービスを Azure Virtual Desktop のタグと結びつける。

   サービスを分離することで、各サービスのコストを明確に把握できますが、最終的にはコストが高くなる可能性があります。 Azure Virtual Desktop が指定されたストレージを持つことを確実にするために、これらのサービス用に追加のストレージを購入する必要がある場合があります。 

   購入したサービスを組み合わせるとコストは低くなりますが、共有リソースの使用状況のデータが正確でないため、コスト レポートが膨らむことがあります。 精度の低さを補うために、リソースに複数のタグを追加して、さまざまな要因を追跡するフィルターを通じて共有コストを見ることができます。

- 別の Azure サービスでタグ付けシステムの構築を開始した場合は、作成したキーと値のペアが、後に Azure Virtual Desktop のデプロイやその他のサービスに適用できることを確認してください。

## <a name="next-steps"></a>次の手順

Azure Virtual Desktop に関連する一般的なコストの詳細については、「[Azure Virtual Desktop デプロイの総コストを把握する](./remote-app-streaming/total-costs.md)」をご覧ください。

Azure タグの詳細については、次のリソースをご覧ください:

- [タグを使用して Azure リソースと管理階層を整理する](../azure-resource-manager/management/tag-resources.md)

- [Azure タグを使用する価値についての説明動画](https://www.youtube.com/watch?v=dUft4FZ40O8)

- [コストと使用状況のデータでのタグの使用方法](../cost-management-billing/costs/understand-cost-mgt-data.md#how-tags-are-used-in-cost-and-usage-data)

- [Azure リソースの名前付けおよびタグ付けの戦略を作成する](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

- [タグ付けの戦略を定義する](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging)

- [リソースの名前付けとタグ付けの意思決定ガイド](/azure/cloud-adoption-framework/decision-guides/resource-tagging)

Azure Cost Management の詳細については、次の記事をご覧ください:

- [Azure Cost Management と Billing とは](../cost-management-billing/cost-management-billing-overview.md)

- [クイック スタート: コスト分析を使用して、コストを調査および分析する](../cost-management-billing/costs/quick-acm-cost-analysis.md)
