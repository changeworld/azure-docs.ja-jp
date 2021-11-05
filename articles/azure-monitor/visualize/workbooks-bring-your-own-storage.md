---
title: Azure Monitor ブックで独自のストレージを使用する
description: ブックの内容を自分のストレージに保存して、ブックをセキュリティで保護する方法について説明します
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: af32a91344d2201899757d806019f70eccd736ad
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072277"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>独自のストレージを使用してブックを保存する

クエリやビジネス ロジックをセキュリティで保護することが必要になる場合があります。 ブックでは、ブックの内容を自分のストレージに保存することによってブックをセキュリティで保護するオプションが提供されています。 その後、Microsoft のマネージド キーを使用してストレージ アカウントを暗号化したり、独自のキーを指定して暗号化を管理したりできます。 [Storage Service Encryption に関する Azure のドキュメントを参照してください。](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>マネージド ID を使用したブックの保存

1. ブックを自分のストレージに保存する前に、マネージド ID を作成し ([すべてのサービス] -> [マネージド ID])、それに自分のストレージ アカウントへの `Storage Blob Data Contributor` アクセス権を付与する必要があります。 [マネージド ID に関する Azure のドキュメントを参照してください。](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![ロールの割り当ての追加を示すスクリーンショット](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. 新しいブックを作成します。
3. **[保存]** ボタンを選択してブックを保存します。
4. `Save content to an Azure Storage Account` を実行するオプションがあります。Azure Storage アカウントに保存するチェックボックスをオンにします。

    ![\* * [保存] * * ダイアログボックスが表示されたスクリーンショット。](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. 目的のストレージ アカウントとコンテナーを選択します。 ストレージ アカウントの一覧は、上記で選択したサブスクリプションからのものです。

    ![\* * [保存] * * [ストレージ] オプションを示すスクリーンショット。](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. 次に、 **[変更]** を選択して、前に作成したマネージド ID を選択します。

    [![ID の変更ダイアログを示すスクリーンショット](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. ストレージ オプションを選択したら、 **[保存]** を押してブックを保存します。

## <a name="limitations"></a>制限事項

- カスタム ストレージに保存する場合、ブックの個々の部分をダッシュボードにピン留めすることはできません。これは、個々のピンに、ダッシュボード自体に保護された情報が含まれるからです。 カスタム ストレージを使用する場合は、ブック自体へのリンクのみをダッシュボードにピン留めできます。
- ブックは一旦カスタム ストレージに保存されると、常にカスタム ストレージに保存されます。これをオフにすることはできません。 別の場所に保存するには、[名前を付けて保存] を使用し、コピーをカスタム ストレージに保存しないことを選択できます。
- Application Insights リソース内のブックは "レガシ" ブックであり、カスタム ストレージはサポートされていません。 Application Insights リソース内の最新ブックは、[...More]\(...詳細\) 選択になります。 レガシ ブックでは、保存するときに [サブスクリプション] オプションはありません。

   ![レガシ ブックを示すスクリーンショット](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>次の手順

- ブックで[マップ](workbooks-map-visualizations.md)の視覚化を作成する方法について説明します。
- [ブックでグループ](../visualize/workbooks-groups.md)を使用する方法について説明します。