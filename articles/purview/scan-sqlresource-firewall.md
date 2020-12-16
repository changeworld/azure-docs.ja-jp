---
title: ファイアウォールの背後での Azure SQL DB のスキャン
description: Azure Purview ポータルを使用してファイアウォールの背後にあるリソースをスキャンする方法について説明します。
author: sudhandkumar
ms.author: kumarsud
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/16/2020
ms.openlocfilehash: 4e1e76aaae2951e6d65fd1ebaaa5798ff417daf9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550701"
---
# <a name="scan-azure-sql-db-behind-a-firewall-in-azure-purview"></a>Azure Purview での、ファイアウォールの背後にある Azure SQL DB のスキャン

この記事では、Azure Purview を使用してファイアウォールの背後にあるリソースをスキャンする方法について説明します。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 所有する [Azure Active Directory テナント](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

## <a name="set-up-sql-storage-behind-a-firewall"></a>ファイアウォール背後での SQL ストレージのセットアップ

最初の手順は、[Azure SQL Database の登録およびスキャン](register-scan-azure-sql-database.md)によって、カタログの MSI を Azure SQL DB に追加することであう。

## <a name="scan-sql-db-from-purview"></a>Purview からの SQL DB のスキャン

1. Purview のホーム ページに移動します。

1. 左側のナビゲーションで **[管理センター]** を選択します。

1. **[Sources and scanning]\(ソースとスキャン\)** の **[データ ソース]** を選択します。

1. **[+ 新規]** を選択してデータ ソースを追加します。

1. **[Azure SQL Database]** を選択します。

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-source.png" alt-text="SQL Server 選択のスクリーンショット。":::

1. 新しいデータ ソースの名前を指定し、 **[Azure サブスクリプションから]** を選択し、ドロップダウンからサブスクリプションとサーバー名を選択します。

   **[完了]** を選択して登録を完了します。 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-register.png" alt-text="選択完了のスクリーンショット":::

1. ファイアウォールの背後にあるストレージに対して **[Set Up Scan]\(スキャンのセットアップ\)** を選択します。 接続は成功したことを示します。 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-setscan.png" alt-text="スキャン セットアップ選択のスクリーンショット。":::

1. スキャンの頻度を設定し、 **[続行]** を選択します。

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-once.png" alt-text="SQL スキャン起動を選択するスクリーンショット。":::

1.  スキャンが完了し、データ ソースの一覧で状態が更新されます。

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-success.png" alt-text="スキャン完了メッセージのスクリーンショット":::
   
## <a name="next-steps"></a>次のステップ

次に、スキャン ルール セットを作成して、スキャンをグループ化できます (「[スキャン ルール セットを作成する](create-a-scan-rule-set.md)」)。
