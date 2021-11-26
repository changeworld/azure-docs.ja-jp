---
title: 資産を表示、編集、および削除する方法
description: このハウツーガイドでは、資産の詳細を表示および編集する方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 6013811622492f476e65c91d3c028007e2802c27
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485183"
---
# <a name="view-edit-and-delete-assets-in-purview-catalog"></a>Purview カタログで資産を表示、編集、および削除する

この記事では、資産とその関連する詳細を表示する方法について説明します。 また、カタログから資産を編集および削除する方法も説明します。また、

## <a name="prerequisites"></a>前提条件

- データ ソースを設定し、資産をカタログにスキャンします。
- *または* Purview Atlas API を使用して、カタログに資産を取り込みます。 

## <a name="viewing-asset-details"></a>資産の詳細の表示

Purview で資産を検出するには、次のいずれかを使用します。
- [Azure Purview Data Catalog を参照する](how-to-browse-catalog.md)
- [Azure Purview Data Catalog を検索する](how-to-search-catalog.md)

目的の資産を見つけたら、次のセクションで説明するように、すべての詳細を表示したり、編集したり、削除したりできます。

## <a name="asset-details-tabs-explained"></a>[資産の詳細] タブについての説明

:::image type="content" source="media/catalog-asset-details/asset-tabs.png" alt-text="[資産の詳細] タブ":::

- **[概要]** - [概要] タブには、説明、分類、階層、用語集の用語など、資産の基本的な詳細が表示されます。
- **[プロパティ]** - [プロパティ] タブには、資産に関する基本プロパティと詳細プロパティの両方が表示されます。
- **[スキーマ]** - 列名、データ型、列レベルの分類、用語、説明を含む資産のスキーマは、[スキーマ] タブに表示されます。
- **[系列]** - このタブには、使用可能な資産の系列の詳細が表示されます。
- **[連絡先]** - すべての資産には、[連絡先] タブから表示および管理できる所有者とエキスパートを割り当てることができます。
- **[関連]** - このタブを使用して、表示している現在の資産に関連する資産に移動できます。 

## <a name="asset-overview"></a>資産の概要
資産の詳細の概要セクションには、資産の概要ビューが表示されます。 次のセクションでは、概要ページのさまざまな部分について説明します。

### <a name="asset-hierarchy"></a>資産階層

[概要] タブ内で完全な資産階層を表示できます。例として、SQL テーブルに移動すると、スキーマ、データベース、およびテーブルが属するサーバーが表示されます。

:::image type="content" source="media/catalog-asset-details/asset-hierarchy.png" alt-text="資産階層":::

### <a name="asset-classifications"></a>資産の分類

資産の分類は、表されているデータの種類を識別し、手動またはスキャン中に適用されます。 例: 国 ID またはパスポート番号はサポートされている分類です。 (分類の完全なリストについては、「[サポートされている分類ページ](supported-classifications.md)」を参照してください。) [概要] タブには、適用された資産レベルの分類と列レベルの分類の両方が反映されます。これらはスキーマの一部として表示することもできます。

:::image type="content" source="media/catalog-asset-details/asset-classifications.png" alt-text="資産の分類":::

### <a name="asset-description"></a>資産の説明

資産の説明は、概要セクションで確認できます。 [資産の編集](#editing-assets)によって資産の説明を追加できます

:::image type="content" source="media/catalog-asset-details/asset-description.png" alt-text="資産の説明":::

### <a name="asset-glossary-terms"></a>資産の用語集の用語

資産の用語集の用語は、環境全体で資産を分類および関連付けるために使用できるビジネス用語の管理されたボキャブラリです。 たとえば、「顧客」、「購入者」、「コスト センター」などの用語、またはユーザーにデータ コンテキストを提供する用語などです。 詳細については、「[ビジネス用語集のページ](concept-business-glossary.md)」を参照してください。 概要セクションで資産の用語集を表示できます。また、[資産の編集](#editing-assets)によって資産に用語集を追加できます。

:::image type="content" source="media/catalog-asset-details/asset-glossary.png" alt-text="資産の用語集の用語":::

## <a name="editing-assets"></a>資産の編集

資産を編集するには、資産の左上隅にある編集アイコンを選択します。

:::image type="content" source="media/catalog-asset-details/asset-edit-delete.png" alt-text="資産の編集と削除のボタン":::

資産レベルでは、編集画面の [概要] タブにとどまり、説明、分類、または用語集の用語を編集または追加できます。

編集画面の [スキーマ] タブに移動して、列名、データ型、列レベルの分類、用語、または資産の説明を更新できます。

編集画面の [連絡先] タブに移動して、資産の所有者とエキスパートを更新できます。 Azure Active Directory 内のユーザーのフルネーム、電子メール、またはエイリアスで検索できます。

### <a name="scans-on-edited-assets"></a>編集された資産のスキャン

説明、資産レベルの分類、用語集の用語、または連絡先を追加して資産を更新すると、その後のスキャンでさらに資産スキーマ (その後に実行するスキャンでスキャナーによって検出される新しい列と分類) が更新されます。

説明、列レベルの分類、用語集の用語などの追加などのように、何らかの列レベルの更新をするときも、その後のスキャンで資産スキーマ が更新されます (その後に実行するスキャンでスキャナーによって新しい列と分類が検出されます)。 

編集された資産においても、スキャン後に Azure Purview はソース システムの実際の状況をリフレクトします。 たとえば: 列を編集してソースから削除すると、Purview の資産から削除されます。 

>[!NOTE]
> Azure Purview 資産内の **列の名前またはデータ型** を更新した場合は、その後のスキャンでは資産スキーマは更新 **されません**。 新しい列と分類は検出 **されません**。

## <a name="deleting-assets"></a>資産の削除

資産を削除するには、資産の名前の下にある削除アイコンを選択します。

### <a name="delete-behavior-explained"></a>削除動作の説明

削除ボタンを使って削除された資産は Azure Purview 内で永続的に削除されます。 ただし、資産がカタログに取り込まれたソースで **フル スキャン** を実行すると、資産が再取り込みされ、Purview カタログを使用して資産を検出できます。

スケジュールされたスキャン (毎週または毎月) がソース上にある場合、前回のスキャンの実行以降にエンド ユーザーが資産を変更しない限り、**削除された資産はカタログに再取り込みされません**。   たとえば、SQL テーブルが Purview から削除されたものの、テーブルが削除された後に、ユーザーが SQL のテーブルに新しい列を追加した場合、次のスキャンで資産が再スキャンされ、カタログに取り込まれます。

特定の資産を削除すると、その資産だけが削除されます。 Purview では現在、連鎖削除はサポートされていません。 たとえば、カタログ内のストレージ アカウント資産を削除した場合、その中のコンテナー、フォルダー、およびファイルは削除されません。 


## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
