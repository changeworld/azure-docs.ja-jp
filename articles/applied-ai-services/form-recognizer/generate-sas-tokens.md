---
title: Azure portal でコンテナーと BLOB の SAS トークンを生成する
description: Azure portal でコンテナーと BLOB の Shared Access Signature (SAS) トークンを生成する方法について説明します。
ms.topic: how-to
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.date: 09/23/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 70ab13f1ca1eee2d5c01d3aa1af6255bc06aeb3d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233660"
---
# <a name="generate-sas-tokens-for-storage-containers"></a>ストレージ コンテナーの SAS トークンを生成する

この記事では、Azure Blob Storage コンテナーのユーザー委任 Shared Access Signature (SAS) トークンを生成する方法について説明します。 ユーザー委任 SAS トークンは、Azure ストレージ キーではなく Azure Active Directory (Azure AD) 資格情報を使用して署名されます。 これにより、Azure ストレージ アカウント内のリソースへのより安全な委任アクセスが可能になります。

大まかには、次のようなしくみです。アプリケーションが要求の一部として Azure ストレージに SAS トークンを提供します。 ストレージ サービスによって Shared Access Signature が有効であることが確認されると、要求が承認されます。 Shared Access Signature が無効と見なされた場合、要求はエラー コード 403 (許可されていません) で拒否されます。

Azure Blob Storage には、3 種類のリソースがあります。

* **ストレージ** アカウントは、データ用の一意の名前空間を Azure 内に提供します。
* **コンテナー** はストレージ アカウントにあり、BLOB のセットを整理します。
* **BLOB** はコンテナーにあり、テキストとバイナリのデータを格納します。

> [!NOTE]
>
> * Azure ストレージ アカウントが仮想ネットワークまたはファイアウォールで保護されている場合、SAS トークンを使用してアクセスを許可することはできません。 ストレージ リソースへのアクセスを許可するには、[マネージド ID](managed-identity-byos.md) を使用する必要があります。
> * [マネージド ID](managed-identity-byos.md) では、プライベートとパブリックの両方のアクセスが可能な Azure Blog Storage アカウントがサポートされます。
>

## <a name="when-to-use-a-shared-access-signature"></a>Shared Access Signature を使用するタイミング

* パブリック アクセスでストレージ コンテナーを使用している場合は、SAS トークンの使用を選択して、ストレージ リソースへの制限付きアクセスを許可することができます。
* カスタム モデルをトレーニングする場合は、組み立てられたトレーニング ドキュメントのセットを Azure Blob Storage コンテナーにアップロードする "*必要があります*"。 ユーザー委任 SAS トークンを使用して、トレーニング リソースにアクセス許可を付与できます。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

* アクティブな [Azure アカウント](https://azure.microsoft.com/free/cognitive-services/)。 アカウントがない場合は、[無料アカウントを作成する](https://azure.microsoft.com/free/)ことができます。
* [Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) または [Cognitive Services マルチサービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) リソース。
* **標準パフォーマンス** の [Azure Blob Storage アカウント](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 ストレージ アカウント内に BLOB データを格納して整理するためのコンテナーを作成します。 コンテナーを含む Azure ストレージ アカウントを作成する方法がわからない場合は、次のクイックスタートに従ってください。

  * [ストレージ アカウントの作成](../../storage/common/storage-account-create.md)。 ストレージ アカウントを作成するときに、 **[インスタンスの詳細]**  >  **[パフォーマンス]** フィールドで **[Standard]** を選択してください。
  * [コンテナーを作成する](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)。 コンテナーを作成するときに、 **[新しいコンテナー]** ウィンドウの **[パブリック アクセス レベル]** を **[コンテナー]** (コンテナーと BLOB の匿名読み取りアクセス) に設定します。

## <a name="upload-your-documents"></a>ドキュメントをアップロードする

1. [Azure ポータル](https://ms.portal.azure.com/#home)にアクセスします。 **自分のストレージ アカウント** >  **[データ ストレージ]**  >  **[コンテナー]** を選択します。

   :::image type="content" source="media/sas-tokens/data-storage-menu.png" alt-text="Azure portal の [データ ストレージ] メニューを示すスクリーンショット。":::

1. 一覧からコンテナーを選択します。
1. ページ上部のメニューから **[アップロード]** を選択します。

    :::image type="content" source="media/sas-tokens/container-upload-button.png" alt-text="Azure portal のコンテナーの [アップロード] ボタンを示すスクリーンショット。":::

   **[BLOB のアップロード]** ウィンドウが表示されます。
1. アップロードするファイルを選択します。

    :::image type="content" source="media/sas-tokens/upload-blob-window.png" alt-text="Azure portal の [BLOB のアップロード] ウィンドウを示すスクリーンショット。":::

> [!NOTE]
> 既定では、REST API ではコンテナーのルートにあるフォーム ドキュメントが使用されます。 API 呼び出しで指定されている場合は、サブフォルダーに整理されたデータを使用することもできます。 詳細については、「[データをサブフォルダーに整理する](./build-training-data-set.md#organize-your-data-in-subfolders-optional)」を参照してください

## <a name="create-a-shared-access-signature-with-the-azure-portal"></a>Azure portal で Shared Access Signature を作成する

> [!IMPORTANT]
>
> ストレージ アカウント自体ではなく、コンテナーの Shared Access Signature を生成して取得します。

1. [Azure portal](https://ms.portal.azure.com/#home) で、**自分のストレージ アカウント** >  **[コンテナー]** を選択します。
1. 一覧からコンテナーを選択します。
1. メイン ウィンドウの右側に移動し、選択したコンテナーに関連付けられている 3 つの省略記号を選択します。
1. ドロップダウン メニューから **[SAS の生成]** を選択して、 **[SAS の生成]** ウィンドウ を開きます。

    :::image type="content" source="media/sas-tokens/generate-sas.png" alt-text="Azure portal の [SAS トークンの生成] ドロップダウン メニューを示すスクリーンショット":::

1. **[署名方法]**  >  **[ユーザーの委任キー]** を選択します。

1. 該当するチェックボックスをオンまたはオフにして、 **[アクセス許可]** を定義します。 **[読み取り]** 、 **[書き込み]** 、 **[削除]** 、および **[表示]** のアクセス許可がオンになっていることを確認します。

    :::image type="content" source="media/sas-tokens/sas-permissions.png" alt-text="Azure portal の [SAS のアクセス許可] フィールドを示すスクリーンショット。":::

    >[!IMPORTANT]
    >
    > * 次のようなメッセージが表示された場合は、ストレージ アカウント内の BLOB データにアクセス権を割り当てる必要があります。
    >
    >     :::image type="content" source="media/sas-tokens/need-permissions.png" alt-text="アクセス許可の不足の警告を示すスクリーンショット。":::
    >
     > * [Azure ロールベースのアクセス制御](../../role-based-access-control/overview.md) (Azure RBAC) は、Azure のリソースに対するアクセスを管理するために使用する認可システムです。 Azure RBAC は、Azure リソースのアクセスとアクセス許可を管理するのに役立ちます。
    > * 「[BLOB データへのアクセスのための Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md?tabs=current)」には、Azure ストレージ コンテナーの読み取り、書き込み、削除のアクセス許可を許可するロールを割り当てる方法が記載されています。 たとえば、「[Storage Blob データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)」を参照してください。

1. 署名されたキーの **開始** と **有効期限** の時刻を指定します。 有効期限の値は、Shared Access Signature の開始から最大 7 日間です。

1. **[使用できる IP アドレス]** フィールドは省略可能であり、要求を受け入れる IP アドレスまたは IP アドレスの範囲を指定します。 要求の IP アドレスが SAS トークンで指定された IP アドレスまたはアドレス範囲と一致しない場合は、承認されません。

1. **[許可されるプロトコル]** フィールドは省略可能であり、Shared Access Signature を使用して行われた要求で許可されるプロトコルを指定します。 既定値は HTTPS です。

1. **[SAS トークンおよび URL を生成]** を選択します。

1. **BLOB SAS トークン** のクエリ文字列と **BLOB SAS URL** が、ウィンドウの下部に表示されます。 BLOB SAS トークンを使用するには、ストレージ サービス URI にトークンを追加します。

1. **BLOB SAS トークン** と **BLOB SAS URL** の値をコピーして安全な場所に貼り付けます。 これらは 1 回だけ表示され、ウィンドウを閉じた後は取得できません。

## <a name="create-a-shared-access-signature-with-the-azure-cli"></a>Azure CLI で Shared Access Signature を作成する

1. Azure CLI を使用してコンテナーにユーザー委任 SAS を作成するには、バージョン 2.0.78 以降がインストールされていることを確認します。 インストールされているバージョンを確認するには、`az --version` コマンドを使用します。

1. [az storage container generate-sas](/cli/azure/storage/container?view=azure-cli-latest&preserve-view=true#az_storage_container_generate_sas) コマンドを呼び出します。

1. 次のパラメーターが必要です。

    * `auth-mode login`. このパラメーターによって、Azure Storage に対して行われた要求が Azure AD の資格情報で承認されるようになります。
    * `as-user`. このパラメーターは、生成された SAS がユーザー委任 SAS であることを示します。

1. コンテナーでユーザー委任 SAS に対してサポートされているアクセス許可には、追加 (a)、作成 (c)、削除 (d)、一覧表示 (l)、読み取り (r)、書き込み (w) があります。 **r**、**w**、**d**、**l** がアクセス許可パラメーターの一部として含まれている必要があります。

1. Azure CLI を使用してユーザー委任 SAS を作成する場合、ユーザー委任キーが有効な間隔は開始日から最大 7 日間です。 開始時刻から 7 日以内で Shared Access Signature の有効期限を指定します。 詳細については、「[Azure CLI を使用したコンテナーまたは BLOB のユーザー委任 SAS の作成](../../storage/blobs/storage-blob-user-delegation-sas-create-cli.md#use-azure-ad-credentials-to-secure-a-sas)」を参照してください。

### <a name="example"></a>例

ユーザー委任 SAS を生成します。 かっこ内のプレースホルダー値は独自の値に置き換えてください。

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions rwdl \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

## <a name="use-your-blob-sas-url"></a>BLOB SAS URL を使用する

次の 2 つのオプションを使用できます。

* BLOB SAS URL を [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync) で使用するには、SAS URL を要求本文に追加します。

  ```json
  {
      "source":"<BLOB SAS URL>"
  }
  ```

* BLOB SAS URL を [Form Recognizer ラベリング ツール](https://fott-2-1.azurewebsites.net/connections/create)で使用するには、 **[接続の設定]**  >  **[Azure BLOB コンテナー]**  >  **[SAS URI]** フィールドで SAS URL を追加します。

  :::image type="content" source="media/sas-tokens/fott-add-sas-uri.png" alt-text="[SAS URI] フィールドを示すスクリーンショット。":::

これで終了です。 SAS トークンを生成して、クライアントによるデータ アクセスの方法を承認する方法を学習しました。

## <a name="next-step"></a>次のステップ

> [!div class="nextstepaction"]
> [トレーニング データ セットの作成](build-training-data-set.md)