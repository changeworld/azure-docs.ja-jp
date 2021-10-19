---
title: Azure Purview でのラベル付け
description: Purview 資産を強化するために秘密度ラベルと分類の使用を開始する
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 0cc474ba9566737cb0117cc3a0f2bfb079cdf3d9
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616514"
---
# <a name="labeling-in-azure-purview"></a>Azure Purview でのラベル付け

> [!IMPORTANT]
> Azure Purview 秘密度ラベルは、現在プレビューの段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。
>

組織内のユーザーは、業務を完了するために組織の内外で他のユーザーと共同作業を行います。 データは常にクラウドにとどまっているわけではなく、多くの場合、デバイス、アプリ、サービスなどにどこでもローミングされます。 データがローミングされる場合でも、組織のビジネス ポリシーとコンプライアンス ポリシーを満たす方法で保護する必要があります。</br>

コンテンツに秘密度ラベルを適用すると、組織内で特定のデータがどのくらい機密であるかを指定して、データのセキュリティを確保できます。 また、データ自体が抽象化されるので、別のプラットフォームで機密データを公開することなく、ラベルを使用してデータの種類を追跡することもできます。</br>

たとえば、社会保障番号とクレジット カード番号を含むドキュメントに "極秘" 秘密度ラベルを適用すると、ドキュメント内の実際のデータを知ることなく、ドキュメントの秘密度を識別するために役立ちます。

## <a name="benefits-of-labeling-in-azure-purview"></a>Azure Purview でのラベル付けの利点

Azure Purview を使用すると、資産に秘密度ラベルを適用し、データを分類して保護することができます。

* **ラベルがデータと共に移動:** Purview で使用される秘密度ラベルは、現在、Microsoft 365、SharePoint、Teams、Power BI、および SQL で認識されます。 Purview で資産にラベルが適用され、データが Power BI や Office などの他のプラットフォームに移動される場合、ラベルもデータと共に移動されます。 同様に、Word 文書にラベルが適用され、その後 Purview によってスキャンされた場合、そのラベルが Purview に反映されます。
* **データ資産の概要:** Purview では、事前に用意されたレポートを通じてデータに関する分析情報が提供されます。 Purview でデータをスキャンすると、所有している資産についての情報、スキャン履歴、データで検出された分類、適用されたラベル、用語集の用語などがレポートに含められます。
* **自動ラベル付け:** データの秘密度に基づいて、ラベルが自動的に適用されます。 資産に対して機密データをスキャンすると、どの秘密度ラベルを適用するかを決定するために、自動ラベル付け規則が使用されます。 各秘密度ラベルに対して自動ラベル付け規則を作成し、どの分類および機密情報の種類でラベルが構成されるかを定義できます。
* **ファイルとデータベース列にラベルを適用:** ラベルは、Azure Data Lake、Azure Files などのストレージ内のファイルや、Azure SQL DB、Cosmos DB などでの列のようなスキーマ化されたデータに適用できます。

秘密度ラベルは、データを分類および保護するために資産に適用できるタグです。 秘密度ラベルの詳細については、[こちら](/microsoft-365/compliance/create-sensitivity-labels.md)を参照してください。

## <a name="how-to-apply-labels-to-assets-in-azure-purview"></a>Azure Purview で資産にラベルを適用する方法

:::image type="content" source="media/create-sensitivity-label/apply-label-flow.png" alt-text="Purview フローでの資産へのラベルの適用。ラベルの作成、資産の登録、資産のスキャン、検出された分類、適用されたラベル。":::

Azure Purview で資産にラベルを適用するには、以下の手順を実行する必要があります。

1. Microsoft 365 コンプライアンス センターで、[秘密度ラベルを作成するか、既存の秘密度ラベルを Azure Purview に拡張](how-to-automatically-label-your-content.md)します。 秘密度ラベルの作成には、データ内で検出された分類に基づいてどのラベルを適用するかを指定する、自動ラベル付け規則も含まれます。
1. Azure Purview で[資産を登録してスキャン](how-to-automatically-label-your-content.md#scan-your-data-to-apply-sensitivity-labels-automatically)します。
1. Azure Purview によって分類が適用されます。資産のスキャンをスケジュールすると、Azure Purview によって資産内のデータの種類がスキャンされ、データ カタログ内で分類が適用されます。 分類の適用は、Azure Purview によって自動的に行われます。ユーザーのアクションは必要ありません。
1. Azure Purview によってラベルが適用されます。資産に対する分類が検出されると、Azure Purview によって、自動ラベル付け規則に応じて資産にラベルが適用されます。 ラベルの適用は、Azure Purview によって自動的に行われます。手順 1. で自動ラベル付け規則を使用してラベルを作成している限り、ユーザーのアクションは必要ありません。

> [!NOTE]
> 自動ラベル付けルールは、特定のラベルを適用するタイミングを指定する条件です。 これらの条件が満たされると、ラベルがデータに自動的に割り当てられます。 ラベルを作成するときは、ファイルとデータベースの列の両方に対して自動ラベル付け規則を定義し、各スキャンでラベルが自動的に適用されるようにしてください。
>

## <a name="supported-data-sources"></a>サポートされるデータ ソース

Azure Purview では、次のデータ ソースに対して秘密度ラベルがサポートされます。

|データ型  |変換元  |
|---------|---------|
|ファイルの自動ラベル付け     |    - Azure Blob ストレージ</br>- Azure Files</br>- Azure Data Lake Storage Gen 1 および Gen 2</br>- Amazon S3|
|データベースの列の自動ラベル付け     |  - SQL Server</br>- Azure SQL データベース</br>- Azure SQL Database Managed Instance</br>- Azure Synapse Analytics ワークスペース</br>- Azure Cosmos データベース (SQL API)</br> - Azure Database for MySQL</br> - Azure Database for PostgreSQL</br> - Azure Data Explorer</br>  |
| | |

## <a name="labeling-for-sql-databases"></a>SQL データベースのラベル付け

Microsoft では、データベースの列に対する Purview のラベル付けに加えて、[SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms) の SQL データ分類を使用した SQL データベースの列のラベル付けもサポートされています。 Purview ではグローバルの[秘密度ラベル](/microsoft-365/compliance/sensitivity-labels)が使用されますが、SSMS ではローカルに定義されているラベルのみが使用されます。

Purview でのラベル付けと SSMS でのラベル付けは、現在相互に連携していない別々のプロセスです。 そのため、**SSMS で適用されるラベルは Purview には表示されず、その逆も同様です**。 SQL データベースにラベルを付けるには、Azure Purview を使用することをお勧めします。これは、複数のプラットフォームに適用できるグローバルな MIP ラベルが使用されているからです。

詳しくは、「[SQL データの検出と分類](/sql/relational-databases/security/sql-data-discovery-and-classification)」のドキュメントをご覧ください。 </br></br>

> [!div class="nextstepaction"]
> [コンテンツに自動的にラベルを付ける方法](./how-to-automatically-label-your-content.md)

> [!div class="nextstepaction"]
> [秘密度ラベルの分析情報](sensitivity-insights.md)

> [!div class="nextstepaction"]
> [ラベル付けについてよく寄せられる質問](sensitivity-labels-frequently-asked-questions.yml)
