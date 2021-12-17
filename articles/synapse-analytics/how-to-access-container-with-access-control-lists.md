---
title: Azure Synapse Analytics で ACL を使用して ADLS Gen2 フォルダーを参照する
description: Azure Synapse Analytics で ACL を使用して ADLS Gen2 フォルダーを参照する方法について説明します。
author: meenalsri
ms.author: mesrivas
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: how-to
ms.date: 10/28/2021
ms.openlocfilehash: 2d16c9eae1e48c1471eb7c250bd9cf9e71d16d79
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894892"
---
# <a name="browse-adls-gen2-folders-preview-in-an-azure-synapse-analytics-workspace"></a>Azure Synapse Analytics ワークスペースで ADLS Gen2 フォルダー (プレビュー) を参照する
データ ハブ内のコンテナーまたは特定のフォルダーに接続することで、Azure Synapse Analytics ワークスペース内の Azure Data Lake Storage Gen2 (ADLS Gen2) コンテナーまたはフォルダーを参照できるようになりました。 組織がストレージ アカウントの Storage Blob データ共同作成者ロールをユーザーに付与していないが、コンテナーまたは特定のフォルダーに対する POSIX のようなアクセス制御リスト (ACL) の作成を許可している場合は、この記事に記載されている手順に従って ADLS Gen2 内のファイルとフォルダーを参照できます。

>[!Note]
>このプレビュー機能を使用すると、ユーザーは ACL を持つ ADLS Gen2 フォルダーを参照できますが、ファイルまたはフォルダーに対するアップロード、ダウンロード、作成、編集、削除、または名前の変更操作はサポートされません。 Storage Blob データ共同作成者ロールを持つユーザーは、ADLS Gen2 フォルダーに対してすべての操作を実行できます。


## <a name="prerequisites"></a>前提条件
Azure Synapse でコンテナーまたはフォルダーを接続する前に、次の前提条件を満たす必要があります。
* Azure AD ID には、Storage Blob データ共同作成者 (Azure RBAC) ロールまたはアクセス制御リスト (ACL) を付与する必要があります。
* ADLS Gen2 コンテナーへのリンク サービスは、Synapse ワークスペースで作成する必要があります。


## <a name="connect-adls-gen2-folder-to-your-azure-synapse-analytics-workspace"></a>Azure Synapse Analytics ワークスペースに ADLS Gen2 フォルダーを接続する
1. データ ハブの **[リンク]** タブに移動します。
2. **[Data Lake Storage Gen2]** を右クリックし、 **[Connect to Azure Storage (preview)]\(Azure Storage に接続する (プレビュー)\)** を選択します。
    * ADLS Gen2 コンテナーまたはフォルダーの URL を `https://storageaccountname.dfs.core.windows.net/containername/foldername/` の形式で指定します。
    * 接続の一意の名前を指定します。
    * ADLS Gen2 アカウントを含むテナント名を入力します。 空白のままにすると、Synapse ワークスペースに関連付けられているテナントが使用されます。
    ![ACL を使用した Storage フォルダーへの接続](./media/connect-to-azure-storage-with-access-control-lists/connect-to-azure-storage-with-acls.png)
3. **[接続]** をクリックします。 接続されているコンテナーまたはフォルダーが **[アタッチされているコンテナー]** の下に表示されます。


## <a name="next-steps"></a>次のステップ
Azure Data Lake Storage Gen2 で ACL の設定の詳細について確認します。
- [Azure Data Lake Storage Gen2 のアクセス制御リスト](../storage/blobs/data-lake-storage-access-control.md)
- [Azure portal を使用して Azure Data Lake Storage Gen2 で ACL を管理する](../storage/blobs/data-lake-storage-acl-azure-portal.md)
