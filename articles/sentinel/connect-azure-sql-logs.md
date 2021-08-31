---
title: すべての Azure SQL Database の診断と監査のログを Azure Sentinel に接続する
description: Azure Policy を使用して、Azure SQL Database の診断ログとセキュリティ監査ログを強制的に Azure Sentinel に接続する方法について説明します。
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 0f1cc19aac18f22d86e0ed56eded168c78a26631
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731027"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Azure SQL Database の診断と監査のログを接続する

Azure SQL は、アップグレード、修正プログラムの適用、バックアップ、監視などのほとんどのデータベース管理機能を、ユーザーの介入を必要とせずに処理する、フル マネージドの PaaS (サービスとしてのプラットフォーム) データベース エンジンです。 

Azure SQL Database コネクタを使用すると、データベースの監査と診断のログを Azure Sentinel にストリーミングできます。これにより、すべてのインスタンスのアクティビティを継続的に監視できます。

- 診断ログを接続すると、さまざまなデータ型のデータベース診断ログを Azure Sentinel ワークスペースに送信できます。

- 監査ログを接続すると、サーバー レベルですべての Azure SQL データベースからセキュリティ監査ログをストリーミングできます。

[Azure SQL Database 診断テレメトリ](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)と [Azure SQL サーバー監査](../azure-sql/database/auditing-overview.md)に関する詳細をご覧ください。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- 監査ログを接続するには、Azure SQL Server の監査設定に対する読み取りと書き込みのアクセス許可が必要です。

- Azure Policy を使用して Azure SQL データベースおよびサーバー リソースにログ ストリーミング ポリシーを適用するには、ポリシー割り当てスコープの所有者ロールが必要です。

## <a name="connect-to-an-azure-sql-database"></a>Azure SQL データベースに接続する

このコネクタでは、Azure Policy を使用して、単一の Azure SQL のログ ストリーミング構成が、スコープとして定義されたインスタンスのコレクションに適用されます。 Azure SQL データベース コネクタが Azure Sentinel に送信するログには 2 種類あり、診断ログ (SQL データベースから) と監査ログ (SQL サーバー レベル) です。 **[データ型]** の下のコネクタ ページの左側で、Azure SQL データベースおよびサーバーから取り込まれたログの種類を確認できます。

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーから **[Azure SQL Databases]** を選択し、プレビュー ペインで **[コネクタ ページを開く]** を選択します。

1. コネクタ ページの **[構成]** セクションで、接続できる 2 つのカテゴリのログを確認します。

### <a name="connect-diagnostics-logs"></a>診断ログを接続する

1. **[Azure SQL データベースから診断ログを大規模にストリーミングする]** を展開します。

1. **[Launch Azure Policy Assignment wizard]\(Azure Policy 割り当てウィザードの起動\)** ボタンを選択します。

    ポリシー割り当てウィザードが開き、**デプロイ - SQL データベースの診断設定を Log Analytics ワークスペースに構成する** という名前の新しいポリシーを作成できるようになります。

    1. **[基本]** タブで、 **[スコープ]** の下にある 3 つのドットがあるボタンをクリックして、お使いのサブスクリプション (および必要に応じてリソース グループ) を選択します。 説明を追加することもできます。

    1. **[パラメーター]** タブでは、最初の 2 つの設定はそのままにします。 **[Log Analytics ワークスペース]** ドロップダウン リストからお使いの Azure Sentinel ワークスペースを選択します。 残りのドロップダウン フィールドは、使用可能な診断ログの種類を表します。 取り込み対象のすべてのログの種類を "True" とマークされたままにします。

    1. ポリシーは、今後追加されるリソースに適用されます。 ポリシーを既存のリソースにも適用するには、 **[修復]** タブを選択し、 **[修復タスクを作成する]** チェック ボックスにチェックを付けます。

    1. **[確認と作成]** タブで、 **[作成]** をクリックします。 これで、選択したスコープにポリシーが割り当てられました。

### <a name="connect-audit-logs"></a>監査ログを接続する

1. コネクタ ページに戻り、 **[Azure SQL データベースからサーバー レベルの監査ログを大規模にストリーミングする]** を展開します。

1. **[Launch Azure Policy Assignment wizard]\(Azure Policy 割り当てウィザードの起動\)** ボタンを選択します。

    ポリシー割り当てウィザードが開き、**デプロイ - SQL データベースの監査設定を Log Analytics ワークスペースに構成する** という名前の新しいポリシーを作成できるようになります。

    1. **[基本]** タブで、 **[スコープ]** の下にある 3 つのドットがあるボタンをクリックして、お使いのサブスクリプション (および必要に応じてリソース グループ) を選択します。 説明を追加することもできます。

    1. **[パラメーター]** タブで、 **[Log Analytics ワークスペース]** ドロップダウン リストからお使いの Azure Sentinel ワークスペースを選択します。 **[効果]** の設定はそのままにしておきます。

    1. ポリシーは、今後追加されるリソースに適用されます。 ポリシーを既存のリソースにも適用するには、 **[修復]** タブを選択し、 **[修復タスクを作成する]** チェック ボックスにチェックを付けます。

    1. **[確認と作成]** タブで、 **[作成]** をクリックします。 これで、選択したスコープにポリシーが割り当てられました。

> [!NOTE]
>
> この特定のデータ コネクタを使用すると、過去 14 日間のある時点でデータが取り込まれた場合にのみ、接続状態インジケーター (データ コネクタ ギャラリーのカラー ストライプとデータ型の名前の横にある接続アイコン) が "*接続済み*" (緑) と表示されます。 データ インジェストがないまま 14 日が経過すると、コネクタは切断済みと表示されます。 さらに多くのデータが入ってくると、"*接続済み*" 状態が返されます。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Policy を使用して Azure SQL Database の診断と監査のログを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
