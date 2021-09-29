---
title: Azure portal を使用して Azure Data Lake Storage Gen2 で ACL を管理する
description: Azure portal を使用して、階層型名前空間 (HNS) が有効になっているストレージ アカウントでアクセス制御リスト (ACL) を管理します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: bb9404351142e60e242e1e4181961c1ed4609012
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564980"
---
# <a name="use-the-azure-portal-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure portal を使用して Azure Data Lake Storage Gen2 で ACL を管理する

この記事では、[Azure portal](https://ms.portal.azure.com/) を使用して、階層型名前空間機能が有効になっているストレージ アカウント内のディレクトリまたは BLOB のアクセス制御リスト (ACL) を管理する方法について説明します。

ACL の構造の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御リスト (ACL)](data-lake-storage-access-control.md)」を参照してください。

ACL と Azure ロールを一緒に使用する方法については、「[Azure Data Lake Storage Gen2 のアクセス制御モデル](data-lake-storage-access-control-model.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

- 階層型名前空間機能が有効になっているストレージ アカウント。 作成するには、[こちら](create-data-lake-storage-account.md)の手順に従います。

- 次のセキュリティのアクセス許可のいずれかを持っている必要があります。

  - ユーザー ID に、ターゲット コンテナー、ストレージ アカウント、親リソース グループ、またはサブスクリプションのいずれかのスコープで[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)ロールが割り当てられている。

  - ACL 設定を適用する予定のターゲット コンテナー、ディレクトリ、または BLOB の所有ユーザーである。

## <a name="manage-an-acl"></a>ACL を管理する

1. [Azure ポータル](https://portal.azure.com/)にサインインして、作業を開始します。

2. ストレージ アカウントを見つけて、アカウントの概要を表示します。

3. **[データ ストレージ]** の下で **[コンテナー]** を選択します。

   ストレージ アカウント内のコンテナーが表示されます。

   > [!div class="mx-imgBorder"]
   > ![Azure portal 内のストレージ アカウン トコンテナーの場所](./media/data-lake-storage-acl-azure-portal/find-containers-in-azure-portal.png)

5. 任意のコンテナー、ディレクトリ、または BLOB に移動します。 オブジェクトを右クリックし、 **[ACL の管理]** を選択します。

   > [!div class="mx-imgBorder"]
   > ![ACL を管理するためのコンテキスト メニュー](./media/data-lake-storage-acl-azure-portal/manage-acl-menu-item.png)

   **[ACL の管理]** ページの **[アクセス許可]** タブが表示されます。 このタブ内のコントロールを使用して、オブジェクトへのアクセスを管理します。

   > [!div class="mx-imgBorder"]
   > ![[ACL の管理] ページの [アクセス ACL] タブ](./media/data-lake-storage-acl-azure-portal/access-acl-page.png)

7. "*セキュリティ プリンシパル*" を ACL に追加するには、 **[プリンシパルの追加]** ボタンを選択します。

   > [!TIP]
   > セキュリティ プリンシパルは、Azure Active Directory (AD) で定義されたユーザー、グループ、サービス プリンシパル、またはマネージド ID を表すオブジェクトです。

   検索ボックスを使用してセキュリティ プリンシパルを検索し、 **[選択]** ボタンをクリックします。

   > [!div class="mx-imgBorder"]
   > ![ACL へのセキュリティ プリンシパルの追加](./media/data-lake-storage-acl-azure-portal/get-security-principal.png)

   > [!NOTE]
   > Azure AD にセキュリティ グループを作成し、個々のユーザーではなく、グループに対するアクセス許可を保守することをお勧めします。 この推奨事項やその他のベスト プラクティスの詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御モデル](data-lake-storage-access-control-model.md)」を参照してください。

8. "*既定の ACL*" を管理するには、 **[既定のアクセス許可]** タブを選択し、 **[既定のアクセス許可を構成する]** チェックボックスを選択します。

   > [!TIP]
   > 既定の ACL は、ディレクトリの下に作成されるすべての子項目のアクセス ACL を決定する ACL のテンプレートです。 BLOB には既定の ACL がないため、このタブはディレクトリに対してのみ表示されます。

   > [!div class="mx-imgBorder"]
   > ![[ACL の管理] ページの [既定の ACL] タブ](./media/data-lake-storage-acl-azure-portal/default-acl-page.png)

## <a name="apply-an-acl-recursively"></a>ACL を再帰的に適用する

また、親ディレクトリの既存の子項目に対して ACL エントリを再帰的に適用できます。各子項目に対してこれらの変更を個別に行う必要はありません。 ただし、Azure portal を使用して ACL エントリを再帰的に適用することはできません。

ACL を再帰的に適用するには、Azure Storage Explorer、PowerShell、または Azure CLI を使用します。 コードを記述する場合は、.NET、Java、Python、または Node.js API も使用できます。

ガイドの完全な一覧については、「[ACL を設定する方法](data-lake-storage-access-control.md#how-to-set-acls)」を参照してください。

## <a name="next-steps"></a>次のステップ

Data Lake Storage Gen2 アクセス許可モデルについて学びます。

> [!div class="nextstepaction"]
> 「[Azure Data Lake Storage Gen2 のアクセス制御モデル](./data-lake-storage-access-control-model.md)」
