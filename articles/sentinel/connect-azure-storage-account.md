---
title: Azure Storage アカウントの診断ログを Azure Sentinel に接続する
description: Azure Policy を使用して Azure Storage アカウントの診断ログを Azure Sentinel に接続する方法について説明します。
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: af9f5218b7f7f94ddc73fb67fe82427d1cfbfff2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743072"
---
# <a name="connect-azure-storage-account-diagnostics-logs"></a>Azure Storage アカウント診断ログの接続

> [!IMPORTANT]
> 現在、Azure Storage アカウント コネクタは **プレビュー** の段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Azure Storage アカウントは、最新のデータ ストレージ シナリオ用のクラウド ソリューションです。 すべてのデータ オブジェクト (BLOB、ファイル、キュー、テーブル、ディスク) が含まれます。

このコネクタを使用して Azure Storage アカウントの診断ログを Azure Sentinel にストリーミングすることで、アクティビティを継続的に監視して、組織全体のすべての Azure Storage リソースにおけるセキュリティ上の脅威を検出することができます。

[Azure Storage アカウントの監視](../storage/common/storage-analytics-logging.md)の詳細を確認してください。

## <a name="prerequisites"></a>[前提条件]

Azure Storage アカウントの診断ログを Azure Sentinel に取り込むには:

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- Azure Policy を使用して Azure Storage リソースにログ ストリーミング ポリシーを適用するには、ポリシー割り当てスコープの所有者ロールが必要です。

## <a name="connect-to-azure-storage-account"></a>Azure ストレージ アカウントに接続する

このコネクタでは、Azure Policy を使用して、単一のログ ストリーミング構成が、スコープとして定義された Azure Storage アカウント リソースのコレクションに適用されます。 コネクタ ページの左側の **[データ型]** の下で、使用可能な Azure Storage のログとメトリックの種類を確認できます。

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーから **[Azure Storage アカウント]** を選択し、プレビュー ペインで **[コネクタ ページを開く]** を選択します。

1. コネクタ ページの **[構成]** セクションで、 **[Azure Storage アカウントから診断ログを大規模にストリーミングする]** を展開します。

1. **[Launch Azure Policy Assignment wizard]\(Azure Policy 割り当てウィザードの起動\)** ボタンを選択します。

    ポリシー割り当てウィザードが開き、**ストレージ アカウントの診断設定を Log Analytics ワークスペースに構成する** という名前の新しいポリシーを作成できるようになります。

    1. **[基本]** タブで、 **[スコープ]** の横にある 3 つのドットがあるボタンをクリックして、お使いのサブスクリプション (および必要に応じてリソース グループ) を選択します。 説明を追加することもできます。

    1. **[パラメーター]** タブで、次の操作を行います。
        - **[Log Analytics ワークスペース]** ドロップダウン リストからお使いの Azure Sentinel ワークスペースを選択します。
        - **[デプロイするストレージ サービス]** ドロップダウン リストから、診断設定のデプロイ先となるストレージ リソースの種類 (ファイル、テーブル、キューなど) を選択します。
        - **[設定名]** と **[効果]** のフィールドはそのままにします。
        - 残りのドロップダウン フィールドは、使用可能な診断ログとメトリックの種類を表します。 取り込み対象のすべての種類を "True" とマークされたままにします。

    1. 上記の手順により、ポリシーは今後のすべてのストレージ リソースに適用されます。 既存のリソースにポリシーを適用するには、 **[修復]** タブを選択し、 **[Create a remediation task]\(修復タスクを作成する\)** チェック ボックスにチェックを付けます。

    1. **[確認と作成]** タブで、 **[作成]** をクリックします。 これで、選択したスコープにポリシーが割り当てられました。

> [!NOTE]
>
> この特定のデータ コネクタを使用すると、過去 14 日間のある時点でデータが取り込まれた場合にのみ、接続状態インジケーター (データ コネクタ ギャラリーのカラー ストライプとデータ型の名前の横にある接続アイコン) が "*接続済み*" (緑) と表示されます。 データ インジェストがないまま 14 日が経過すると、コネクタは切断済みと表示されます。 さらに多くのデータが入ってくると、"*接続済み*" 状態が返されます。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Storage アカウントを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
