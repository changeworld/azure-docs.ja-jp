---
title: Azure Purview でのラベル付け
description: Purview 資産を強化するために秘密度ラベルと分類の使用を開始する
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: f6769cfc95ff06ef67d9020ff109993772b36bf0
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000841"
---
<<<<<<< HEAD
# <a name="automatically-label-your-data-in-azure-purview"></a>Azure Purview でデータに自動的にラベルを付ける

この記事では、Microsoft Information Protection (MIP) の秘密度ラベルを作成し、Azure Purview の Azure 資産に自動的に適用する方法について説明します。

## <a name="what-are-sensitivity-labels"></a>秘密度ラベルとは 

組織内のユーザーは、業務を完了するために組織の内外で他のユーザーと共同作業を行います。 データは常にクラウドにとどまっているわけではなく、多くの場合、デバイス、アプリ、サービスなどにどこでもローミングされます。 

データをローミングする場合は、組織のビジネス ポリシーとコンプライアンス ポリシーを満たす安全で保護された方法で行う必要があります。

秘密度ラベルを適用すると、組織内の特定の機密データの性質を指定できます。 たとえば、特定のプロジェクト名は組織内で非常に機密性が高いが、同じ用語が他の組織にとっては機密ではない場合があります。 

### <a name="sensitivity-labels-in-azure-purview"></a>Azure Purview の秘密度ラベル

Purview では、分類はサブジェクト タグに似ており、スキャン時にデータ資産内で見つかった特定の種類のデータをマークおよび識別するために使用されます。

Purview では、Microsoft 365 と同じ分類 (機密情報の種類とも呼ばれます) が使用されます。  MIP の秘密度ラベルは、Microsoft 365 セキュリティ/コンプライアンス センター (SCC) で作成されます。 これにより、Azure Purview の資産全体で既存の秘密度ラベルを拡張できます。

**分類** は、直接照合されます。たとえば、社会保障番号には、**社会保障番号** の分類があります。 

これに対して、**秘密度ラベル** は、1 つまたは複数の分類と条件が一緒に検出された場合に適用されます。 このコンテキストにおける [条件](/microsoft-365/compliance/apply-sensitivity-label-automatically)とは、*別の分類との近さ* や *信頼度* など、非構造化データに対して定義できるすべてのパラメーターを指します。 

Azure Purview で秘密度ラベルを使用すると、ファイルおよびデータベースの列にラベルを自動的に適用できます。

詳細については、次を参照してください。

- Microsoft 365 のドキュメントの「[秘密度ラベルの詳細](/microsoft-365/compliance/sensitivity-labels)」
- [自動ラベル付け規則とは](#what-are-auto-labeling-rules)
- [Azure Purview の秘密度ラベルでサポートされるデータ型](#supported-data-types-for-sensitivity-labels-in-azure-purview)
- [SQL データベースの列のラベル付け](#labeling-for-sql-database-columns)

#### <a name="what-are-auto-labeling-rules"></a>自動ラベル付け規則とは

データは絶えず増加して変化しています。 現在ラベルが付けられていないデータを追跡し、ラベルを手動で適用するアクションを実行するのは、面倒なだけでなく、不必要な頭痛の種でもあります。 

自動ラベル付け規則は、特定のラベルを適用するタイミングを指定する条件です。 これらの条件が満たされると、ラベルがデータに自動的に割り当てられ、一貫性のある秘密度ラベルが大規模なデータで維持されます。

ラベルを作成するときは、[ファイル](#define-auto-labeling-rules-for-files)と[データベースの列](#define-auto-labeling-rules-for-database-columns)の両方に対して自動ラベル付け規則を定義し、各データ スキャンでラベルが自動的に適用されるようにしてください。 

Purview でデータをスキャンすると、自動的に適用されたラベルを Purview Catalog と分析情報レポートに表示できます。
#### <a name="supported-data-types-for-sensitivity-labels-in-azure-purview"></a>Azure Purview の秘密度ラベルでサポートされるデータ型

Azure Purview では、次のデータ型に対して秘密度ラベルがサポートされます。

|データ型  |変換元  |
|---------|---------|
|ファイルの自動ラベル付け     |      - Azure Blob Storage  </br>- Azure Data Lake Storage Gen 1 および Gen 2  |
|データベースの列の自動ラベル付け     |  - SQL Server </br>- Azure SQL データベース </br>- Azure SQL Database Managed Instance   <br> - Azure Synapse  <br> - Azure Cosmos DB <br><br>詳細については、以下の「[SQL データベースの列のラベル付け](#labeling-for-sql-database-columns)」を参照してください。  |
| | |

#### <a name="labeling-for-sql-database-columns"></a>SQL データベースの列のラベル付け

Microsoft では、データベースの列に対する Purview のラベル付けに加えて、[SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms) の SQL データ分類を使用した SQL データベースの列のラベル付けもサポートされています。 Purview ではグローバルの [MIP 秘密度ラベル](/microsoft-365/compliance/sensitivity-labels)が使用されますが、SSMS ではローカルに定義されているラベルのみが使用されます。

Purview でのラベル付けと SSMS でのラベル付けは、現在相互に連携していない別々のプロセスです。 したがって、SSMS で適用されるラベルは Purview には表示されず、その逆も同様です。 SQL データベースにラベルを付けるには、Azure Purview を使用することをお勧めします。これは、複数のプラットフォームに適用できるグローバルな MIP ラベルが使用されているからです。

詳しくは、「[SQL データの検出と分類](/sql/relational-databases/security/sql-data-discovery-and-classification)」のドキュメントをご覧ください。

## <a name="how-to-create-sensitivity-labels-in-microsoft-365"></a>Microsoft 365 で秘密度ラベルを作成する方法

秘密度ラベルがまだない場合は、それを作成して、Azure Purview で使用できるようにする必要があります。 既存の秘密度ラベルを変更して、Azure Purview で使用できるようにすることもできます。

詳細については、次を参照してください。

- [ライセンスの要件](#licensing-requirements)
- [Azure Purview への秘密度ラベルの拡張](#extending-sensitivity-labels-to-azure-purview)
- [新しい秘密度ラベルの作成または既存のラベルの変更](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="licensing-requirements"></a>ライセンスの要件

MIP の秘密度ラベルは、Microsoft 365 セキュリティ/コンプライアンス センターで作成および管理されます。 Azure Purview で使用する秘密度ラベルを作成するには、アクティブな Microsoft 365 E5 ライセンスを保持している必要があります。

必要なライセンスをまだ保持していない場合は、[Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion) の試用版にサインアップできます。

### <a name="extending-sensitivity-labels-to-azure-purview"></a>Azure Purview への秘密度ラベルの拡張

既定では、MIP の秘密度ラベルは Microsoft 365 の資産にのみ使用でき、ファイルや電子メールに適用できます。

Azure Purview の Azure 資産に MIP の秘密度ラベルを適用するには、ラベルの拡張について明示的に同意し、Purview で使用できるようにする特定のラベルを選択する必要があります。

Azure Purview で MIP の秘密度ラベルを拡張することにより、組織は、より幅広いデータ ソースで秘密度の検出、分類、および分析情報の取得を行うことができるようになり、コンプライアンスのリスクを最小限に抑えることができます。

> [!NOTE]
> Microsoft 365 と Azure Purview は別々のサービスであるため、異なるリージョンにデプロイされる可能性があります。 ラベル名とカスタムの機密情報の種類の名前は、お客様のデータと見なされ、データの機密性を保護し、プライバシーについての規則を遵守するために、既定で同じ地域の場所に保持されます。
>
> このため、ラベルとカスタムの機密情報の種類は、既定では Azure Purview に共有されず、Azure Purview での使用に同意する必要があります。

**秘密度ラベルを Purview に拡張するには:**

次の手順では、秘密度ラベルを Azure Purview で使用できるようにします。Azure Purview では、SQL 列、Azure Blob Storage 内のファイルなどの資産に秘密度ラベルを適用できます。

1. Microsoft 365 で、 **[Information Protection]** ページに移動します。 
1. **[Azure Purview の資産へのラベル付けを拡張する]** で、 **[有効にする]** ボタンを選択し、表示される確認ダイアログで **[はい]** を選択します。

例:

:::image type="content" source="media/create-sensitivity-label/extend-sensitivity-labels-to-purview-small.png" alt-text="[有効にする] を選択して、秘密度ラベルを Purview に拡張します" lightbox="media/create-sensitivity-label/extend-sensitivity-labels-to-purview.png":::
 
Azure Purview の資産にラベル付けを拡張すると、Purview で使用できるようにするラベルを選択できます。 詳細については、「[新しい秘密度ラベルの作成または既存のラベルの変更](#creating-new-sensitivity-labels-or-modifying-existing-labels)」を参照してください。
### <a name="creating-new-sensitivity-labels-or-modifying-existing-labels"></a>新しい秘密度ラベルの作成または既存のラベルの変更

Windows、macOS、iOS、および Android で Office アプリの秘密度ラベルを使用すると、ユーザーに対して 4 時間以内に新しいラベルが表示され、Web 上の Office では 1 時間以内に表示されます。 ただし、すべてのアプリとサービスに対して変更をレプリケートするには、最大 24 時間かかります。
=======
# <a name="labeling-in-azure-purview"></a>Azure Purview でのラベル付け
>>>>>>> repo_sync_working_branch

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

秘密度ラベルは、データを分類および保護するために資産に適用できるタグです。 秘密度ラベルの詳細については、[こちら](/microsoft-365/compliance/create-sensitivity-labels)を参照してください。

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
