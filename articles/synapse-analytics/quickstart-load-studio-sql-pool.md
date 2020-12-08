---
title: 'クイックスタート: 専用 SQL プールを使用したデータの一括読み込み'
description: Synapse Studio を使用して、Azure Synapse Analytics の専用 SQL プールにデータを一括読み込みします。
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 11/16/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 312c57c103bf733bc72c5de1d22ab3239d5b5e96
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484661"
---
# <a name="quickstart-bulk-loading-with-synapse-sql"></a>クイックスタート:Synapse SQL を使用したデータの一括読み込み

Synapse Studio の一括読み込みウィザードを使用すると、データを簡単に読み込むことができます。 一括読み込みウィザードの手順に従うと、[COPY ステートメント](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)を使用してデータを一括で読み込むための T-SQL スクリプトを作成できます。 

## <a name="entry-points-to-the-bulk-load-wizard"></a>一括読み込みウィザードへのエントリ ポイント

Synapse Studio 内の次の領域を右クリックするだけで、専用 SQL プールを使用してデータの一括読み込みを簡単に実行できます。

- 自分のワークスペースにアタッチされた Azure ストレージ アカウントのファイルまたはフォルダー ![ストレージ アカウントのファイルまたはフォルダーを右クリックする](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>前提条件

- このウィザードでは、Azure AD パススルーを認証に使用する COPY ステートメントが生成されます。 少なくとも ADLS Gen2 アカウントに対するストレージ BLOB データ共同作成者 Azure ロールがある状態で、ワークスペースに [Azure AD ユーザーがアクセスできる必要があります](
./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication)。 

- [COPY ステートメントを使用するために必要なアクセス許可](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions)が必要です。さらに、読み込み先のテーブルを新しく作成する場合は、テーブルの作成アクセス許可が必要です。

- ADLS Gen2 アカウントに関連付けられている、リンクされたサービスには、読み込む **ファイル**/または **フォルダーへのアクセス権** が必要です。 たとえば、リンクされたサービスの認証メカニズムがマネージド ID の場合、ワークスペースのマネージド ID には、ストレージ アカウントに対するストレージ BLOB 閲覧者アクセス許可が少なくとも必要です。

- お使いのワークスペースで VNet が有効になっている場合は、ソース データとエラー ファイルの場所に対して ADLS Gen2 アカウントのリンクされたサービスに関連付けられている統合ランタイムで、インタラクティブな作成が有効になっていることを確認してください。 インタラクティブな作成は、ウィザード内での自動スキーマ検出、ソース ファイルの内容のプレビュー、および ADLS Gen2 ストレージ アカウントの参照に必要です。

### <a name="steps"></a>手順

1. [Source storage location]\(ソース ストレージの場所\) パネルで、ストレージ アカウントと、読み込み元のファイルまたはフォルダーを選択します。 ウィザードによって自動的に Parquet ファイルの検出が試行されます。 Parquet ファイルの種類が確認できない場合は、区切りテキスト (CSV) が既定で使用されます。

   ![ソースの場所の選択](./sql/media/bulk-load/bulk-load-source-location.png)

2. 拒否された行 (エラー ファイル) の書き込み先のストレージ アカウントを含め、ファイル形式設定を選択します。 現時点では、CSV ファイルと Parquet ファイルのみがサポートされています。

    ![ファイル形式設定の選択](./sql/media/bulk-load/bulk-load-file-format-settings.png)

3. [データのプレビュー] を選択すると、COPY ステートメントによってファイルがどのように解析されるかを確認できるため、ファイル形式設定を構成する際の参考にすることができます。 ファイル形式設定を変更するたびに [データのプレビュー] を選択して、更新された設定で COPY ステートメントによってファイルがどのように解析されるかを確認します。![データのプレビュー](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

> [!NOTE]  
>
> - 複数文字のフィールド ターミネータを使用したデータのプレビューは、一括読み込みウィザードではサポートされません。 複数文字のフィールド ターミネータが指定された場合は、1 列内でデータがプレビューされます。 
> - COPY ステートメントでは、複数文字の行ターミネータを指定できますが、一括読み込みウィザードではサポートされません。指定した場合はエラーがスローされます。

4. 読み込みに使用する専用 SQL プールを選択します (既存のテーブルへの読み込みか、新しいテーブルへの読み込みか)。![ターゲットの場所の選択](./sql/media/bulk-load/bulk-load-target-location.png)

5. [列マッピングの構成] を選択して、列マッピングが適切であることを確認します。 [Infer column names]\(列名の推測\) が有効になっている場合は、列名が自動的に検出されます。 新しいテーブルの場合、ターゲット列のデータ型を更新するには、列マッピングを構成することが非常に重要です。![列マッピングの構成](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)

6. [スクリプトを開く] を選択すると、COPY ステートメントを使用してデータ レイクから読み込むための T-SQL スクリプトが生成されます。![SQL スクリプトを開く](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>次のステップ

- [COPY ステートメント](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)に関する記事を参照して COPY 機能の詳細を確認する
- [データ読み込みの概要](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt)に関する記事を参照する
