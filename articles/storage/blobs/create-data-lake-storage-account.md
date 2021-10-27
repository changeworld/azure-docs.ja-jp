---
title: Azure Data Lake Storage Gen2 のストレージ アカウントを作成する
description: Azure Data Lake Storage Gen2 で使用するストレージ アカウントを作成する方法について説明します。
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 10/14/2021
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 35761e10cecf1cb209f004f99f773c09d91dc0e5
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046869"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 で使用するストレージ アカウントを作成する

Data Lake Storage Gen2 の機能を使用するには、階層型名前空間を持つストレージ アカウントを作成します。

ステップ形式のガイドについては、「[ストレージ アカウントを作成する](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json)」を参照してください。

アカウントを作成するときは、この記事で説明するオプションを必ず選択してください。

## <a name="choose-a-storage-account-type"></a>ストレージ アカウントの種類を選択する

Data Lake Storage 機能は、次の種類のストレージ アカウントでサポートされています。

- Standard 汎用 v2
- Premium ブロック BLOB

これらのいずれかを選択する方法の詳細については、「[ストレージ アカウントの概要](../common/storage-account-overview.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json)」を参照してください。

**[ストレージ アカウントの作成]** ページの **[基本]** タブで、これらの 2 種類のアカウントから選択できます。

標準の汎用 v2 アカウントを作成するには、 **[標準]** を選択します。

Premium ブロック BLOB アカウントを作成するには、 **[Premium]** を選択します。 次に、 **[Premium アカウントの種類]** ドロップダウン リストで **[ブロック BLOB]** を選択します。

> [!div class="mx-imgBorder"]
> ![Premium ブロック BLOB オプション](./media/create-data-lake-storage-account/premium-block-blob-option.png)

## <a name="enable-the-hierarchical-namespace"></a>階層型名前空間を有効にする

**[ストレージ アカウントの作成]** ページの **[詳細設定]** タブで **[階層型名前空間を有効にする]** 設定を選択して、Data Lake Storage 機能のロックを解除します。 

次の図は、 **[ストレージ アカウントの作成]** ページのこの設定を示しています。

> [!div class="mx-imgBorder"]
> ![階層型名前空間の設定](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

既存のアカウントで Data Lake Storage 機能を有効にするには、「[Azure Data Lake Storage Gen2 の機能で Azure Blob Storage をアップグレードする](upgrade-to-data-lake-storage-gen2-how-to.md)」を参照してください。

> [!NOTE]
> **データ保護** と階層型名前空間を同時に有効にすることはできません。

## <a name="next-steps"></a>次のステップ

- [ストレージ アカウントの概要](../common/storage-account-overview.md)
- [Azure Data Lake Storage Gen2 の機能で Azure Blob Storage をアップグレードする](upgrade-to-data-lake-storage-gen2-how-to.md)
- [Azure Data Lake Storage Gen2 のアクセス制御](data-lake-storage-access-control.md)
