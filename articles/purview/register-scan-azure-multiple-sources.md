---
title: Azure Purview で複数のソースをスキャンする
description: Azure Purview Data Catalog で Azure サブスクリプションまたはリソース グループ全体をスキャンする方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: d141ac2be690a0cb788285a773208e0d930f5e67
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209891"
---
# <a name="register-and-scan-multiple-sources-in-azure-purview"></a>Azure Purview で複数のソースを登録してスキャンする

この記事では、複数のソース (Azure サブスクリプションまたはリソース グループ) を Azure Purview に登録し、それらに対してスキャンを設定する方法を概説します。

## <a name="supported-capabilities"></a>サポートされる機能

複数のソースをスキャンして、Azure Purview でサポートされるほとんどの種類の Azure リソースでメタデータおよびスキーマを取り込むことができます。 Azure Purview では、システムおよびカスタムの分類規則に基づいてデータが自動的に分類されます。

## <a name="prerequisites"></a>[前提条件]

- データ ソースを登録する前に、Azure Purview アカウントを作成します。 詳細については、[クイック スタート: Azure Purview アカウントの作成](create-catalog-portal.md)に関するページを参照してください。
- Azure Purview データ ソース管理者であることを確認します。 また、サブスクリプションまたはリソース グループにロールを追加するには、所有者またはユーザー アクセス管理者である必要があります。
- 以下のセクションの説明に従って認証を設定します。

### <a name="set-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>サブスクリプションまたはリソース グループの下にあるリソースを列挙するための認証を設定する

1. Azure portal でサブスクリプションまたはリソース グループに移動します。  
1. 左側のメニューから  **[アクセス制御 (IAM)]**   を選択します。 
1. **[+追加]** を選択します。 
1. **[入力の選択]** ボックスで、 **[閲覧者]** ロールを選択し、Azure Purview のアカウント名 (その MSI ファイル名を表すもの) を入力します。 
1. **[保存]** を選択して、ロールの割り当てを終了します。

### <a name="set-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>サブスクリプションまたはリソース グループの下にあるリソースをスキャンするための認証を設定する

Azure で複数のソースの認証を設定するには、次の 2 つの方法があります。

- マネージド ID
- サービス プリンシパル

登録してスキャンするサブスクリプションまたはリソース グループ内にある各リソースに対して、認証を設定する必要があります。 Azure Storage リソースの種類 (Azure Blob Storage と Azure Data Lake Storage Gen2) の場合、ストレージ BLOB データ閲覧者として、サブスクリプションまたはリソース グループ レベルで MSI ファイルまたはサービス プリンシパルを追加できるため、これを簡単に行うことができます。 その後、アクセス許可が、そのサブスクリプションまたはリソース グループ内の各ストレージ アカウントに継承されます。 他のすべてのリソースの種類の場合は、各リソースに MSI ファイルまたはサービス プリンシパルを適用するか、スクリプトを作成してこれを行う必要があります。 

サブスクリプションまたはリソース グループ内のリソースの種類ごとにアクセス許可を追加する方法を学習する場合は、以下のリソースを参照してください。
    
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Managed Instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-multiple-sources"></a>複数のソースを登録する

新しい複数のソースをデータ カタログに登録するには、次の操作を行います。

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


## <a name="create-and-run-a-scan"></a>スキャンを作成して実行する

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
    
    各資格情報は、特定の種類の下にあるすべてのリソースの認証方法と見なされます。 [この記事で前述した](#set-up-authentication-to-scan-resources-under-a-subscription-or-resource-group)とおり、リソースを正常にスキャンするために、選択された資格情報をリソースに設定する必要があります。
1. 各種類では、すべてのリソースをスキャンするか、それらのサブセットを名前でスキャンするかを選択できます。
    - オプションを **[すべて]** のままにすると、その種類の将来のリソースも、将来のスキャンの実行でスキャンされます。
    - 特定のストレージ アカウントまたは SQL データベースを選択した場合、将来スキャンが明示的に編集されない限り、このサブスクリプションまたはリソース グループ内に将来作成されるその種類のリソースはスキャンの対象に含まれません。
 
1. **[続行]** をクリックして先に進みます。 サブスクリプションまたはリソース グループの閲覧者として Azure Purview MSI ファイルを適用したかどうかを確認するために、Azure Purview によってアクセスがテストされます。 エラー メッセージが表示された場合は、[これらの手順](#set-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group)に従って解決してください。

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

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)    
