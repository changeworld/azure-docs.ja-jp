---
title: クイックスタート - Azure Storage Explorer を使用して BLOB を作成する
titleSuffix: Azure Storage
description: Azure Storage Explorer を使用してコンテナーと BLOB を作成する方法、その BLOB をローカル コンピューターにダウンロードする方法、およびコンテナーにあるすべての BLOB を表示する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 09/10/2021
ms.author: tamram
ms.openlocfilehash: abf75bb39610deb54b72002cde3dd8e28131ed94
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124839132"
---
# <a name="quickstart-use-azure-storage-explorer-to-create-a-blob"></a>クイック スタート:Azure Storage Explorer を使用して BLOB を作成する

このクイック スタートでは、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して、コンテナーと BLOB を作成する方法を学習します。 次に、ローカル コンピューターに BLOB をダウンロードする方法と、コンテナー内のすべての BLOB を表示する方法について説明します。 また、BLOB のスナップショットを作成する方法、コンテナーのアクセス ポリシーを管理する方法、共有アクセス署名を作成する方法についても学習します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

このクイックスタートでは、Azure Storage Explorer をインストールする必要があります。 Windows、Macintosh、または Linux 用の Azure Storage Explorer をインストールする方法については、「[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)」をご覧ください。

## <a name="log-in-to-storage-explorer"></a>Storage Explorer にログインする

初回の起動時には、 **[Microsoft Azure Storage Explorer - 接続]** ウィンドウが表示されます。 Storage Explorer には、ストレージ アカウントに対する接続方法がいくつか用意されています。 次の表は、さまざまな接続方法を示しています。

|タスク|目的|
|---|---|
|Azure アカウントを追加する | Azure に対する認証を行うための組織のサインイン ページにリダイレクトします。 |
|接続文字列または Shared Access Signature URI を使用する | SAS トークンまたは共有の接続文字列でコンテナーまたはストレージ アカウントに直接アクセスするために使用できます。 |
|ストレージ アカウントの名前とキーを使用する| ストレージ アカウントの名前とキーを使用して、Azure Storage に接続します。|

**[Add an Azure Account\(Azure アカウントの追加\)]** を選択し、 **[サインイン]** をクリックします。画面上のプロンプトに従って Azure アカウントにサインインします。

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-connect.png" alt-text="[Microsoft Azure Storage Explorer - 接続] ウィンドウを示すスクリーンショット":::

Storage Explorer で接続が完了すると、 **[エクスプローラー]** タブが表示されます。このビューには、すべての Azure ストレージ アカウントの分析情報に加えて、[Azure ストレージ エミュレーター](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)、[Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) アカウント、または [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 環境を介して構成されたローカル ストレージの分析情報も表示されます。

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-main-page.png" alt-text="Storage Explorer のメイン ページを示すスクリーンショット":::

## <a name="create-a-container"></a>コンテナーを作成する

コンテナーを作成するには、前の手順で作成したストレージ アカウントを展開します。 **[BLOB コンテナー]** を選択し、 **[BLOB コンテナーの作成]** を右クリックして選択します。 BLOB コンテナーの名前を入力します。 BLOB コンテナーの名前付けに関する規則と制限の一覧については、[コンテナーの作成](storage-quickstart-blobs-dotnet.md#create-a-container)に関するセクションを参照してください。 完了したら、**Enter** キーを押して BLOB コンテナーを作成します。 BLOB コンテナーは、正常に作成されると、選択されたストレージ アカウントの **[BLOB コンテナー]** フォルダーの下に表示されます。

## <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 IaaS VM をバックアップするために使用される VHD ファイルはページ BLOB です。 追加 BLOB は、ファイルに書き込んでから詳細情報を追加し続ける場合などの、ログ記録に使用されます。 BLOB ストレージに格納されているほとんどのファイルはブロック BLOB です。

コンテナー リボンで、 **[アップロード]** を選択します。 この操作によって、フォルダーまたはファイルをアップロードするためのオプションが表示されます。

アップロードするファイルまたはフォルダーを選択します。 **BLOB の種類** を選択します。 **追加**、**ページ**、または **ブロック** の各 BLOB を選択できます。

.vhd または .vhdx ファイルをアップロードする場合は、 **[.vhd/.vhdx ファイルをページ BLOB としてアップロードする (推奨)]** を選択します。

**[アップロード先のフォルダー (オプション)]** フィールドで、ファイルを格納するフォルダーの名前またはコンテナーの下にあるフォルダー内のフォルダーを選択します。 フォルダーが選択されていない場合は、ファイルがコンテナーの下に直接アップロードされます。

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-upload-blob.png" alt-text="Microsoft Azure Storage Explorer - BLOB のアップロード":::

**[OK]** を選択すると、選択したファイルがキューに登録され、各ファイルがアップロードされます。 アップロードが完了すると、 **[アクティビティ]** ウィンドウに結果が表示されます。

## <a name="view-blobs-in-a-container"></a>コンテナー内の BLOB を表示する

**Azure Storage Explorer** アプリケーションで、ストレージ アカウントに基づいてコンテナーを選択します。 選択したコンテナー内の BLOB の一覧がメイン ウィンドウに表示されます。

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-list-blobs.png" alt-text="Microsoft Azure Storage Explorer でのコンテナーの選択方法を示すスクリーンショット":::

## <a name="download-blobs"></a>BLOB をダウンロードする

**Azure Storage Explorer** を使用して BLOB をダウンロードするには、BLOB を選択した状態で、リボンの **[ダウンロード]** を選択します。 ファイル ダイアログが開き、ファイル名を入力するオプションが表示されます。 **[保存]** を選択して、ローカルの場所への BLOB のダウンロードを開始します。

## <a name="manage-snapshots"></a>スナップショットを管理する

Azure Storage Explorer には、BLOB の[スナップショット](./snapshots-overview.md)を作成して管理する機能が用意されています。 BLOB のスナップショットを作成するには、BLOB を右クリックして **[スナップショットの作成]** を選択します。 BLOB のスナップショットを表示するには、BLOB を右クリックして **[Manage Snapshots\(スナップショットの管理\)]** を選択します。 BLOB のスナップショットの一覧が現在のタブに表示されます。

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-snapshots.png" alt-text="BLOB スナップショットを取得および管理する方法を示すスクリーンショット":::

## <a name="generate-a-shared-access-signature"></a>共有アクセス署名 (SAS) を生成する

共有アクセス署名 (SAS) を生成するには、Storage Explorer を使用できます。 ストレージ アカウント、コンテナー、または BLOB を右クリックして **[Get Shared Access Signature...\(Shared Access Signature の取得...\)]** を選択します。SAS URL の開始時刻と有効期限、およびアクセス許可を選択し、 **[作成]** を選択します。 Storage Explorer によって、自身が指定したパラメーターを使用して SAS トークンが生成され、コピーのために表示されます。

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-shared-access-signature.png" alt-text="SAS の生成方法を示すスクリーンショット":::

ストレージ アカウントの SAS を作成すると、Storage Explorer によりアカウント SAS が生成されます。 アカウント SAS の詳細については、「[アカウント SAS を作成する](/rest/api/storageservices/create-account-sas)」を参照してください。

コンテナーまたは BLOB の SAS を作成すると、Storage Explorer によりサービス SAS が生成されます。 サービス SAS の詳細については、「[サービス SAS を作成する](/rest/api/storageservices/create-service-sas)」を参照してください。

> [!NOTE]
> Storage Explorer を使用して SAS を作成すると、SAS には必ずストレージ アカウント キーが割り当てられます。 Storage Explorer では現在、ユーザー委任 SAS (Azure AD の資格情報を使用して署名される SAS) の作成はサポートされていません。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、**Azure Storage Explorer** を使用してローカル ディスクと Azure Blob Storage との間でファイルを転送する方法について学習しました。 BLOB ストレージの操作の詳細を学習するには、BLOB ストレージの概要に進みます。

> [!div class="nextstepaction"]
> [Azure Blob Storage の概要](./storage-blobs-introduction.md)