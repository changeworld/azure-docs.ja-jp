---
title: Azure Data Factory を使用して複数のコンテナーからファイルをコピーする |Microsoft Docs
description: Azure Data Factory を使用して複数のコンテナーからファイルをコピーするための、ソリューション テンプレート の使用方法について説明します。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: aa5f32594c295ab6a8e60af8359370f64f75a72d
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966814"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Azure Data Factory を使用して複数のコンテナーからファイルをコピーする

この記事で説明しているソリューション テンプレートを使用すると、複数のファイル、コンテナー、またはバケット内にあるファイルを、ファイル ストア間でコピーできます。 たとえば、AWS S3 から Azure Data Lake Store にデータ レイクを移行することもできます。 また、1 つの Azure Blob Storage アカウントに含まれるすべてのファイルを、別の Azure Blob Storage アカウントにレプリケートすることもできます。 このテンプレートは、これらのユース ケースのために設計されています。

1 つのコンテナーやバケットからファイルをコピーする場合は、**データのコピー ツール**を使用して、単一のコピー アクティビティを含んだパイプラインを作成する方が効率的です。 このテンプレートでは、この単純なユース ケースを超える操作を実行できます。

## <a name="about-this-solution-template"></a>このソリューション テンプレートについて

このテンプレートでは、ソース ストレージ ストア内のコンテナーを列挙し、各コンテナーをソース ストレージ ストアからコピー先ストアにコピーします。 

テンプレートには、3 つのアクティビティが含まれています。
-   **GetMetadata** アクティビティ: ソース ストレージ ストアをスキャンし、コンテナーの一覧を取得します。
-   **ForEach** アクティビティ: **GetMetadata** アクティビティからコンテナーの一覧を取得し、その一覧を反復処理して、各コンテナーを Copy アクティビティに渡します。
-   **Copy** アクティビティ: 各コンテナーをソース ストレージ ストアからコピー先ストアにコピーします。

このテンプレートには、次の 2 つのパラメーターが定義されています。
-   *SourceFilePath* パラメーターは、コンテナーやバケットの一覧を取得できるデータ ソース ストアのパスです。 ほとんどの場合、このパスは複数のコンテナー フォルダーを含んだルート ディレクトリです。 このパラメーターの既定値は `/` です。
-   *DestinationFilePath* パラメーターは、コピー先ストア内でファイルがコピーされる場所のパスです。 このパラメーターの既定値は `/` です。

## <a name="how-to-use-this-solution-template"></a>このソリューション テンプレートの使用方法

1. **Copy multiple files containers between File Stores (ファイル ストア間で複数のファイル コンテナーをコピーする)** テンプレートに移動して、ソース ストレージ ストアへの**新しい接続**を作成します。 ソース ストレージ ストアは、複数のコンテナーまたはバケットからファイルをコピーする場所です。

    ![ソースへの新しい接続を作成する](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. コピー先ストレージ ストアへの**新しい接続**を作成します。

    ![コピー先への新しい接続を作成する](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. **[このテンプレートを使用]** をクリックします。

    ![このテンプレートを使用](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. 次の例に示すように、使用可能なパイプラインがパネルに表示されます。

    ![パイプラインを表示する](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. **[デバッグ]** をクリックし、**パラメーター**を入力して **[完了]** をクリックします。

    ![パイプラインを実行する](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. 結果を確認します。

    ![結果を確認する](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>次の手順

- [Azure Data Factory の概要](introduction.md)
