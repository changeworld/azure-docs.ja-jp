---
title: Azure Sentinel に Azure アクティビティ ログ データを接続する | Microsoft Docs
description: Azure アクティビティ ログ イベントを Azure Sentinel にシングル クリックでストリーミングできます。 アクティビティ ログ サービスにより、Azure 全体のサブスクリプション レベルのイベントが記録および表示されます。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2021
ms.author: yelevin
ms.openlocfilehash: 9638b7935d4af2c53bcd5cd59e1c155da524fa13
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747161"
---
# <a name="connect-data-from-azure-activity-log"></a>Azure アクティビティ ログからデータを接続する

Azure の [**アクティビティ ログ** サービス](../azure-monitor/essentials/activity-log.md)のイベントを Azure Sentinel にシングル クリックでストリーミングできます。 **アクティビティ ログ** は Azure の[プラットフォーム ログ](../azure-monitor/essentials/platform-logs-overview.md)であり、Azure Resource Manager オペレーショナル データから Service Health イベントに関する最新情報まで、サブスクリプション レベルのイベントの分析情報を提供します。 **アクティビティ ログ** を使用して、お使いのサブスクリプションのリソースに対して実行されたすべての書き込み操作 (PUT、POST、DELETE) について、"いつ誰が何を" 行ったのかを確認できます。 また、操作の状態やその他の関連するプロパティを確認することもできます。 このサービスでは、読み取り (GET) 操作や、Azure Resource Manager (ARM) モデルを使用しないリソースに対する操作はログに記録されません。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>前提条件

- Log Analytics ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- Azure Sentinel へのログのストリーミングを開始または停止するサブスクリプションに対する所有者のアクセス許可が必要です。

- Azure Policy を使用して Azure アクティビティ ログ サブスクリプションにログ ストリーミング ポリシーを適用するには、ポリシー割り当てスコープの所有者ロールが必要です。

## <a name="data-structure-changes"></a>データ構造の変更

このコネクタでは、アクティビティ ログ イベントを収集するためにバックエンドで使用する方式が変更されています。 Azure Key Vault コネクタや Azure Kubernetes Service コネクタなどの新しいコネクタで使用されている **診断設定** パイプラインを使用するようになっています。 このコネクタでまだ従来の方式を使用している場合は、新しいバージョンへの *アップグレードを強くお勧めします*。これにより、機能が強化され、リソース ログとの整合性が向上します。 以下の手順を参照してください。

**診断設定** 方式では、従来の方式でアクティビティ ログ サービスから送信していたものと同じデータを送信しますが、**AzureActivity** テーブルの[構造の変更](../azure-monitor/essentials/activity-log.md#data-structure-changes)がいくつかあります。

診断設定パイプラインへの移行によって向上する点の中で重要なものは次のとおりです。
- インジェストの待機時間の短縮 (イベントのインジェストに発生から 15 ～ 20 分かかっていたのが 2 ～ 3 分以内に)。
- 信頼性が向上します。
- パフォーマンスが向上します。
- アクティビティ ログ サービスによってログに記録されるイベントのすべてのカテゴリをサポートします (従来のメカニズムではサブセットのみをサポートしています。たとえば、サービス正常性イベントはサポートされていません)。
- Azure Policy を使用した大規模な管理。

[Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md)と[診断設定パイプライン](../azure-monitor/essentials/diagnostic-settings.md)の詳細については、[Azure Monitor のドキュメント](../azure-monitor/logs/data-platform-logs.md)を参照してください。

## <a name="set-up-the-azure-activity-log-connector"></a>Azure アクティビティ ログ コネクタの設定

新しい Azure アクティビティ ログ コネクタの設定には、次の 2 つの段階があります。
1. 既存のサブスクリプションを従来方式から切断します。

1. **Azure Policy** を使用して、関連するすべてのサブスクリプションを新しい診断設定パイプラインに接続します。

### <a name="disconnect-from-old-pipeline"></a>古いパイプラインからの切断

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。 コネクタの一覧で **[Azure Activity]\(Azure アクティビティ\)** を選択し、右下にある **[Open connector page]\(コネクタ ページを開く\)** ボタンをクリックします。

1. **[Instructions]\(手順\)** タブの **[構成]** セクションの手順 1 で、従来方式に接続している既存のサブスクリプションの一覧を確認して、新しい方式に追加するものを特定し、下にある **[Disconnect All]\(すべて切断\)** ボタンをクリックしてすべてを一度に切断します。

### <a name="connect-to-new-pipeline"></a>新しいパイプラインに接続する

1. 手順 2 で、 **[Launch Azure Policy Assignment wizard]\(Azure Policy 割り当てウィザードの起動\)** ボタンを選択します。 ポリシー割り当てウィザードが開き、「**指定した Log Analytics ワークスペースにストリーミングする Azure アクティビティ ログを構成する**」という名前の新しいポリシーを作成する準備が整います。

1. **[基本]** タブで、 **[スコープ]** の下にある 3 つのドットがあるボタンをクリックして **[スコープ]** パネルを開き、お使いのサブスクリプション (および必要に応じてリソース グループ) を選択します。 説明を追加することもできます。

1. **[パラメーター]** タブで、 **[効果]** および **[Enable logs]\(ログの有効化\)** フィールドはそのままにします。 **[Log Analytics ワークスペース]** ドロップダウン リストからお使いの Azure Sentinel ワークスペースを選択します。

1. ポリシーは、今後追加されるリソースに適用されます。 ポリシーを既存のリソースにも適用するには、 **[修復]** タブを選択し、 **[修復タスクを作成する]** チェック ボックスにチェックを付けます。

1. **[確認と作成]** タブで、 **[作成]** をクリックします。 これで、選択したスコープにポリシーが割り当てられました。

> [!NOTE]
>
> この特定のデータ コネクタを使用すると、過去 14 日間のある時点でデータが取り込まれた場合にのみ、接続状態インジケーター (データ コネクタ ギャラリーのカラー ストライプとデータ型の名前の横にある接続アイコン) が "*接続済み*" (緑) と表示されます。 データ インジェストがないまま 14 日が経過すると、コネクタは切断済みと表示されます。 さらに多くのデータが入ってくると、"*接続済み*" 状態が返されます。

## <a name="find-your-data"></a>データの検索

Log Analytics の関連スキーマから Azure アクティビティのアラートをクエリするには、クエリ ウィンドウの上部で「`AzureActivity`」と入力します。

データの分析と視覚化に役立つサンプルのクエリとワークブック テンプレートについては、コネクタ ページの **[次のステップ]** タブを参照してください。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure アクティビティ ログを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [組み込み](detect-threats-built-in.md)または[カスタム](detect-threats-custom.md)のルールを使用して、Azure Sentinel で脅威の検出を開始する。
