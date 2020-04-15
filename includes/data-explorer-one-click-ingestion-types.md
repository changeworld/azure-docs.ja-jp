---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 30/03/2020
ms.author: orspodek
ms.openlocfilehash: 9ae820097f8515dc44c67d95366f96c241cb77a1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522820"
---
## <a name="select-an-ingestion-type"></a>インジェストの種類を選択する

**[Ingestion type]\(インジェストの種類\)** には、次のいずれかのオプションを選択します。
   * **ストレージから** - **[Link to storage]\(ストレージにリンク\)** フィールドに、ストレージ アカウントの URL を追加します。 プライベート ストレージ アカウントには [BLOB SAS URL](/azurevs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) を使用します。
   
      ![ストレージからのワンクリックでのインジェスト](media/data-explorer-one-click-ingestion-types/from-storage-blob.png)

    * **ファイルから** - **[Browse]\(参照\)** を選択してファイルを見つけるか、ファイルをフィールドにドラッグします。
  
      ![ファイルからのワンクリックでのインジェスト](media/data-explorer-one-click-ingestion-types/from-file.png)

    * **コンテナーから** - **[Link to storage]\(ストレージにリンク\)** フィールドにコンテナーの [SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) を追加し、必要に応じてサンプル サイズを入力します。

      ![コンテナーからのワンクリックでのインジェスト](media/data-explorer-one-click-ingestion-types/from-container.png)

  データのサンプルが表示されます。 必要に応じて、特定の文字で始まる、または終わるファイルだけを表示するようにフィルター処理することができます。 フィルターを調整すると、プレビューが自動的に更新されます。
  
  たとえば、*data* という単語で始まり、 *.csv.gz* 拡張子で終わるすべてのファイルを表示するようにフィルター処理できます。

  ![ワンクリックでのインジェスト フィルター](media/data-explorer-one-click-ingestion-types/from-container-with-filter.png)
