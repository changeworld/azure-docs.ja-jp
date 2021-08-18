---
title: Azure Kubernetes Service (AKS) 診断ログを Azure Sentinel に接続する
description: Azure Policy を使用して Azure Kubernetes Service 診断ログを Azure Sentinel に接続する方法について説明します。
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/22/2021
ms.author: yelevin
ms.openlocfilehash: 96068491bd4be59c34d62375db30edad504823a9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779017"
---
# <a name="connect-azure-kubernetes-service-diagnostics-logs"></a>Azure Kubernetes Service 診断ログを接続する

Azure Kubernetes Service (AKS) は、オープンソースのフル マネージド コンテナー オーケストレーション サービスであり、クラスター環境で Docker コンテナーとコンテナー ベースのアプリケーションをデプロイ、拡張、管理することができます。

このコネクタを使用すると、Azure Kubernetes Service (AKS) の診断ログを Azure Sentinel にストリーミングできます。これにより、すべてのインスタンスでのアクティビティを継続的に監視できます。 

詳細については、[Azure Kubernetes Service の監視](../azure-monitor/containers/container-insights-overview.md)と [AKS 診断テレメトリ](../aks/monitor-aks-reference.md#resource-logs)に関する記事を参照してください。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>前提条件

AKS ログを Azure Sentinel に取り込むには:

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- Azure Policy を使用して AKS リソースにログ ストリーミング ポリシーを適用するには、ポリシー割り当てスコープの所有者ロールが必要です。

## <a name="connect-to-azure-kubernetes-service"></a>Azure Kubernetes Service に接続する

このコネクタでは、Azure Policy を使用して、単一の Azure Kubernetes Service のログ ストリーミング構成が、スコープとして定義されたリソースのコレクションに適用されます。 **[データ型]** の下のコネクタ ページの左側で、Azure Kubernetes Service から取り込まれたログの種類を確認できます。

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーから **[Azure Kubernetes Service (AKS)]** を選択し、プレビュー ペインで **[Open Connector Page]\(コネクタ ページを開く\)** を選択します。

1. コネクタ ページの **[構成]** セクションで、 **[ Azure Kubernetes Service (AKS) から診断ログを大規模にストリーミングする]** を展開します。

1. **[Launch Azure Policy Assignment wizard]\(Azure Policy 割り当てウィザードの起動\)** ボタンを選択します。

    ポリシー割り当てウィザードが開き、**Deploy - Configure diagnostic settings for Azure Kubernetes Service to Log Analytics workspace** いう名前の新しいポリシーを作成できるようになります。

    1. **[基本]** タブで、 **[スコープ]** の下にある 3 つのドットがあるボタンをクリックして、お使いのサブスクリプション (および必要に応じてリソース グループ) を選択します。 説明を追加することもできます。

    1. **[パラメーター]** タブで、 **[効果]** と **[設定名]** のフィールドをそのままにします。 **[Log Analytics ワークスペース]** ドロップダウン リストからお使いの Azure Sentinel ワークスペースを選択します。 残りのドロップダウン フィールドは、使用可能な診断ログの種類を表します。 取り込み対象のすべてのログの種類を "True" とマークされたままにします。

    1. ポリシーは、今後追加されるリソースに適用されます。 ポリシーを既存のリソースにも適用するには、 **[修復]** タブを選択し、 **[修復タスクを作成する]** チェック ボックスにチェックを付けます。

    1. **[確認と作成]** タブで、 **[作成]** をクリックします。 これで、選択したスコープにポリシーが割り当てられました。

> [!NOTE]
>
> この特定のデータ コネクタを使用すると、過去 14 日間のある時点でデータが取り込まれた場合にのみ、接続状態インジケーター (データ コネクタ ギャラリーのカラー ストライプとデータ型の名前の横にある接続アイコン) が "*接続済み*" (緑) と表示されます。 データ インジェストがないまま 14 日が経過すると、コネクタは切断済みと表示されます。 さらに多くのデータが入ってくると、"*接続済み*" 状態が返されます。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Policy を使用して Azure Kubernetes Service を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
