---
title: 複数の Azure ソースに接続して管理する
description: このガイドでは、Azure Purview で複数の Azure ソースに一度に接続する方法と、Purview の機能を使用してソースをスキャンおよび管理する方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: aefb039e17ee75b92829feb9e2f0b06fb5bef99a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076208"
---
# <a name="connect-to-and-manage-multiple-azure-sources-in-azure-purview"></a>Azure Purview で複数の Azure ソースに接続して管理する

この記事では、Azure Purview で、複数の Azure ソースを登録する方法と、それらを認証して操作する方法について説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register) | [あり](#scan) | [あり](#scan) | [あり](#scan)| [あり](#scan)| いいえ| [ソースに依存](catalog-lineage-user-guide.md)|

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

## <a name="register"></a>登録

このセクションでは、[Purview Studio](https://web.purview.azure.com/)を使用して Azure Purview に複数の Azure ソースを登録する方法について説明します。

### <a name="prerequisites-for-registration"></a>登録の前提条件

サブスクリプションまたはリソースグループの下にあるリソースを列挙できるように、なんらかの認証を設定する必要があります。

1. Azure portal でサブスクリプションまたはリソース グループに移動します。  
1. 左側のメニューから  **[アクセス制御 (IAM)]**   を選択します。
1. **[+追加]** を選択します。
1. **[入力の選択]** ボックスで、 **[閲覧者]** ロールを選択し、Azure Purview のアカウント名 (その MSI ファイル名を表すもの) を入力します。 
1. **[保存]** を選択して、ロールの割り当てを終了します。

### <a name="authentication-for-registration"></a>登録の認証

Azure で複数のソースの認証を設定するには、次の 2 つの方法があります。

* マネージド ID
* サービス プリンシパル

登録してスキャンするサブスクリプションまたはリソース グループ内にある各リソースに対して、認証を設定する必要があります。 Azure Storage リソースの種類 (Azure Blob Storage と Azure Data Lake Storage Gen2) の場合、ストレージ BLOB データ閲覧者として、サブスクリプションまたはリソース グループ レベルで MSI ファイルまたはサービス プリンシパルを追加できるため、これを簡単に行うことができます。 その後、アクセス許可が、そのサブスクリプションまたはリソース グループ内の各ストレージ アカウントに継承されます。 他のすべてのリソースの種類の場合は、各リソースに MSI ファイルまたはサービス プリンシパルを適用するか、スクリプトを作成してこれを行う必要があります。

サブスクリプションまたはリソース グループ内のリソースの種類ごとにアクセス許可を追加する方法を学習する場合は、以下のリソースを参照してください。
    
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md#authentication-for-a-scan)
- [Azure SQL Managed Instance](register-scan-azure-sql-database-managed-instance.md#authentication-for-registration)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#authentication-for-registration)

### <a name="steps-to-register"></a>登録する手順

1. Azure Purview アカウントに移動します。
1. 左側のメニューで **[Data Map]** を選択します。
1. **[登録]** を選択します。
1. **[ソースの登録]** で、 **[Azure (multiple)]\(Azure (複数)\)** を選択します。

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="複数のソースを登録するための画面上の Azure Multiple のタイルを示すスクリーンショット。":::

1. **[続行]** をクリックします。
1. **[Register sources (Azure)]\(ソースの登録 (Azure)\)** 画面で、次の操作を行います。

   1. **[名前]** ボックスに、データ ソースがカタログに一覧表示されるときの名前を入力します。 
   1. **[管理グループ]** ボックスで、必要に応じて、フィルターを適用する管理グループを選択します。
   1. **[サブスクリプション]** および **[リソース グループ]** ドロップダウン リスト ボックスで、サブスクリプションまたは特定のリソース グループをそれぞれ選択します。 登録スコープは、選択されたサブスクリプションまたはリソース グループに設定されます。  

      :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="サブスクリプションとリソース グループを選択するためのボックスを示すスクリーンショット。":::

   1. **[コレクションを選択する]** ボックスで、コレクションを選択するか、新しいものを作成します (省略可能)。
   1. **[登録]** を選択してデータ ソースを登録します。

## <a name="scan"></a>スキャン

次の手順に従って、複数の Azure ソースをスキャンし、自動的に資産を識別してデータを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください。

### <a name="create-and-run-scan"></a>スキャンの作成と実行

新しいスキャンを作成して実行するには、次の操作を行います。

1. Purview Studio の左側にあるペインで **[Data Map]** タブを選択します。
1. 登録したデータ ソースを選択します。
1. **[詳細の表示]**  >  **[+ 新しいスキャン]** の順に選択するか、ソース タイルの **[スキャン]** クイック操作アイコンを使用します。
1. **[名前]** に、名前を入力します。
1. **[種類]** で、このソース内でスキャンするリソースの種類を選択します。 次のいずれかのオプションを選択します。

    - **[すべて]** のままにしておきます。 この選択には、そのサブスクリプションまたはリソース グループ内に現在存在しない可能性のある将来のリソースの種類が含まれます。
    - ボックスを使用して、スキャンするリソースの種類を具体的に選択します。 このオプションを選択した場合、将来スキャンが明示的に編集されない限り、このサブスクリプションまたはリソース グループ内に将来作成される可能性のあるリソースの種類はスキャンの対象に含まれません。

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="複数のソースをスキャンするためのオプションを示すスクリーンショット。":::

1. データ ソース内のリソースに接続するための資格情報を選択します。
    - 親レベルで MSI ファイルとして資格情報を選択することも、特定のサービス プリンシパルの種類の資格情報を選択することもできます。 その後、サブスクリプションまたはリソース グループの下にあるすべてのリソースの種類に対して、その資格情報を使用できます。
    - 具体的にリソースの種類を選択し、そのリソースの種類の別の資格情報を適用することができます。

    各資格情報は、特定の種類の下にあるすべてのリソースの認証方法と見なされます。 [この記事で前述した](#authentication-for-registration)とおり、リソースを正常にスキャンするために、選択された資格情報をリソースに設定する必要があります。
1. 各種類では、すべてのリソースをスキャンするか、それらのサブセットを名前でスキャンするかを選択できます。
    - オプションを **[すべて]** のままにすると、その種類の将来のリソースも、将来のスキャンの実行でスキャンされます。
    - 特定のストレージ アカウントまたは SQL データベースを選択した場合、将来スキャンが明示的に編集されない限り、このサブスクリプションまたはリソース グループ内に将来作成されるその種類のリソースはスキャンの対象に含まれません。

1. **[接続テスト]** を選択します。 これにより、サブスクリプションまたはリソース グループの閲覧者として Azure Purview MSI ファイルを適用したかどうかを確認するために、まずアクセスがテストされます。 エラー メッセージが表示された場合は、[これらの手順](#prerequisites-for-registration)に従って解決してください。 次に、選択した各ソースに対する認証と接続がテストされ、レポートが生成されます。 選択したソースの数は、このレポートの生成にかかる時間に影響します。 テスト接続では最初に、サブスクリプション/リソース グループ/synapse ワークスペース レベルで接続とアクセスがテストされます。 続いて、個々のリソースへのアクセスと接続がテストされ、その結果がレポートに表示されます。 一部のリソースでエラーが発生した場合は、 **[X]** アイコンの上にカーソルを合わせると、詳細なエラー メッセージが表示されます。

    :::image type="content" source="media/register-scan-azure-multiple-sources/test-connection.png" alt-text="[テスト接続] ボタンが強調表示されたスキャン設定スライダーのスクリーンショット。":::
    :::image type="content" source="media/register-scan-azure-multiple-sources/test-connection-report.png" alt-text="テスト接続レポートの例を示すスクリーンショット。接続が成功しているものと失敗しているものがあります。失敗した接続のいずれかにカーソルを合わせると、詳細なエラー レポートが表示されます。":::

1. テスト接続に成功した後、 **[続行]** を選択して先に進みます。

1. 前の手順で選択したリソースの種類ごとにスキャン ルール セットを選択します。 また、スキャン ルール セットをインラインで作成することもできます。
  
   :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="リソースの種類ごとのスキャン ルールを示すスクリーンショット。":::

1. スキャン トリガーを選択します。 毎週、毎月または 1 回実行するようにスケジュールできます。

1. スキャンをレビューし、 **[保存]** を選択して設定を完了します。

## <a name="view-your-scans-and-scan-runs"></a>スキャンとスキャンの実行を表示する

1. **[Data Map]** セクションの下にあるタイルで **[詳細の表示]** を選択して、ソースの詳細を表示します。

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="ソースの詳細を示すスクリーンショット。"::: 

1. **[スキャンの詳細]** ページに移動して、スキャン実行の詳細を表示します。
   
    "*ステータス バー*" は、子リソースの実行中の状態の概要を示すものです。 これは、サブスクリプション レベルまたはリソース グループ レベルで表示されます。 色には次の意味があります。
    
    - 緑: スキャンに成功しました。
    - 赤: スキャンに失敗しました。 
    - グレー: スキャンはまだ進行中です。
   
    各スキャンを選択して、さらに詳細を表示することができます。

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="スキャンの詳細を示すスクリーンショット。":::

1. ソースの詳細の下部に最近失敗したスキャン実行の概要を表示します。 これらの実行に関するより詳細な情報を表示することもできます。

## <a name="manage-your-scans-edit-delete-or-cancel"></a>スキャンを管理する: 編集、削除、またはキャンセル

スキャンを管理するには、次の操作を行います。

1. 管理センターに移動します。
1. **[Sources and scanning]\(ソースとスキャン\)** セクションで **[データ ソース]** を選択し、目的のデータ ソースを選びます。
1. 管理するスキャンを選択します。 次のことを行います。 

   - スキャンを編集するには、 **[編集]** を選択します。
   - **[削除]** を選択することで、スキャンを削除できます。
   - スキャンが実行中の場合は、 **[キャンセル]** を選択して取り消すことができます。

## <a name="next-steps"></a>次の手順

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
