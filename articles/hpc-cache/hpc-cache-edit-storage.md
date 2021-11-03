---
title: Azure HPC Cache ストレージ ターゲットを更新する
description: Azure HPC Cache ストレージ ターゲットを編集する方法
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/30/2021
ms.author: femila
ms.openlocfilehash: dd7c65e80df04172adb7b59d304ff8d616edf9cd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082571"
---
# <a name="edit-storage-targets"></a>ストレージ ターゲットを編集する

Azure portal か Azure CLI を使用すれば、ストレージ ターゲットを編集できます。 たとえば、既存のストレージ ターゲットのアクセス ポリシー、使用量モデル、名前空間パスを変更できます。

> [!TIP]
> ストレージ ターゲットを削除または中断するか、またはストレージ ターゲットでキャッシュ データをバックエンド ストレージに書き込む方法については、「[ストレージ ターゲットの表示と管理](manage-storage-targets.md)」を参照してください。

ストレージの種類に応じて、次のストレージ ターゲット値を変更できます。

* BLOB ストレージ ターゲットの場合は、名前空間のパスとアクセス ポリシーを変更できます。

* NFS ストレージ ターゲットの場合は、次の値を変更できます。

  * 名前空間パス
  * アクセス ポリシー
  * 名前空間パスに関連付けられているストレージ エクスポートまたはエクスポート サブディレクトリ
  * 使用モデル

* ADLS-NFS ストレージ ターゲットの場合は、名前空間のパス、アクセス ポリシー、使用モデルを変更できます。

ストレージ ターゲットの名前、種類、バックエンド ストレージ システムは編集できません。 これらのプロパティを変更する必要がある場合は、ストレージ ターゲットを削除し、新しい値で置換を作成します。

[Azure HPC Cache の管理ビデオ](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)では、Azure portal でストレージ ターゲットを編集する方法を確認できます。

## <a name="change-a-blob-storage-targets-namespace-path-or-access-policy"></a>Blob ストレージ ターゲットの名前空間パス、アクセス ポリシーを変更する

名前空間パスは、クライアントがこのストレージ ターゲットをマウントするために使用するパスです。 (詳細については、「[集約された名前空間を計画する](hpc-cache-namespace.md)」および「[集約された名前空間を設定する](add-namespace-paths.md)」をご覧ください。)

Azure portal か Azure CLI で名前空間パス、アクセス ポリシーを変更します。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure HPC Cache の **[Namespace]\(名前空間\)** ページで、名前空間パスとアクセス ポリシーを更新できます。 名前空間のページについては、記事「[集約された名前空間を設定する](add-namespace-paths.md)」で詳細に説明しています。

1. 変更したいパスをクリックします。
   ![[Namespace]\(名前空間\) ページのスクリーンショット。[Namespace path]\(名前空間パス\) 列 (1 番左側の列) の項目にマウス カーソルを重ねている。 その名前はハイパーリンクになっていて、カーソルの形状により、それがクリック可能であることが分かる。](media/edit-select-namespace.png)

1. 編集ウィンドウで新しい仮想パスを入力するか、アクセス ポリシーを更新する。

   ![Blob 名前空間パスをクリックした後の、名前空間ページのスクリーンショット。右側のペインに編集欄が表示されている。](media/update-namespace-blob.png)

変更を行った後、 **[OK]** をクリックしてストレージ ターゲットを更新するか、 **[キャンセル]** をクリックして変更を破棄します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache 向けに Azure CLI を設定します](./az-cli-prerequisites.md)。

BLOB ストレージ ターゲットの名前空間を Azure CLI を使用して変更するには、コマンド [az hpc-cache blob-storage-target update](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update) を使用します。 `--virtual-namespace-path` 値のみを変更できます。

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>NFS ストレージ ターゲットを更新する

NFS ストレージ ターゲットの場合は、仮想名前空間パスを変更または追加したり、名前空間パスが指す NFS エクスポートまたはサブディレクトリの値を変更したり、使用モデルを変更したりできます。

一部の種類のカスタム DNS 設定が指定されたキャッシュ内のストレージ ターゲットには、IP アドレスを更新するためのコントロールもあります。 (この種類の構成はまれです。) DNS 設定を更新する方法については、「[ストレージ ターゲットの表示と管理](manage-storage-targets.md#update-ip-address-custom-dns-configurations-only)」を参照してください。

詳細については以下にあります。

* 「[集約された名前空間値を変更する](#change-aggregated-namespace-values)」 (仮想名前空間パス、アクセス ポリシー、エクスポート、エクスポート サブディレクトリ)
* 「[使用モデルを変更する](#change-the-usage-model)」

### <a name="change-aggregated-namespace-values"></a>集約された名前空間値を変更する

Azure portal または Azure CLI を使用して、クライアント向けの名前空間パス、ストレージ エクスポート、エクスポート サブディレクトリ (使用されている場合) を変更できます。 アクセス ポリシーの変更が必要な場合は、Azure portal を使用します。

1 つのストレージ ターゲットに複数の有効なパスを作成する方法について再確認する必要がある場合は、[NFS 名前空間パスの追加](add-namespace-paths.md#nfs-namespace-paths)に関するガイドラインを参照してください。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure HPC Cache の **[Namespace]\(名前空間\)** ページで、クライアント アクセス ポリシーなどの、名前空間の値を変更します。 このページについては、記事「[集約された名前空間を設定する](add-namespace-paths.md)」で詳細に説明しています。

![右側に NFS 更新ページが開いているポータルの名前空間ページのスクリーンショット](media/update-namespace-nfs.png)

1. 変更するパスの名前をクリックします。
1. [編集] ウィンドウを使用して、新しい仮想パス、エクスポート、またはサブディレクトリの値を入力するか、別のアクセス ポリシーを選択します。
1. 変更を加えた後、 **[OK]** をクリックしてストレージ ターゲットを更新するか、 **[キャンセル]** で変更を破棄します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache 向けに Azure CLI を設定します](./az-cli-prerequisites.md)。

[az hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target) コマンドで ``--junction`` オプションを使用して、名前空間パス、NFS エクスポート、またはエクスポート サブディレクトリを変更します。

``--junction`` パラメーターでは次の値が使用されます。

* ``namespace-path`` - クライアント側の仮想ファイル パス
* ``nfs-export`` - クライアント側パスに関連付けるストレージ システム エクスポート
* ``target-path`` (省略可能) - 必要な場合、エクスポートのサブディレクトリ

例: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

``--junction`` ステートメントには、各パスの 3 つの値すべてを指定する必要があります。 変更しない値には、既存の値を使用します。

キャッシュ名、ストレージ ターゲット名、およびリソース グループも、すべての更新コマンドで必須です。

コマンドの例:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>使用モデルを変更する

使用モデルは、キャッシュによってデータが保持される方法に影響します。 詳細については、「[キャッシュ使用モデルについて](cache-usage-models.md)」を参照してください。

> [!NOTE]
> **Read heavy, infrequent writes** (高負荷低頻度の書き込みを読む) と他の使用量モデルを切り替えることはできません。 詳しくは「[キャッシュ使用モデルについて](cache-usage-models.md#change-usage-models)」を読んでください。

NFS ストレージ ターゲットの使用モデルを変更するには、次のいずれかの方法を使用します。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal の **[Storage targets]\(ストレージ ターゲット\)** ページを開きます。 リストにあるストレージ ターゲットの名前をクリックして編集ページを開きます。

![NFS ストレージ ターゲットの編集ページのスクリーンショット](media/edit-storage-nfs.png)

ドロップダウン セレクターを使用して、新しい使用モデルを選択します。 **[OK]** をクリックしてストレージ ターゲットを更新するか、 **[キャンセル]** をクリックして変更を破棄します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache 向けに Azure CLI を設定します](./az-cli-prerequisites.md)。

[az hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update) コマンドを使用します。

update コマンドは、NFS ストレージ ターゲットを追加するために使用するコマンドとほぼ同じものです。 詳細と例については、「[NFS ストレージ ターゲットを作成する](hpc-cache-add-storage.md#create-an-nfs-storage-target)」を参照してください。

使用モデルを変更するには、``--nfs3-usage-model`` オプションを更新します。 例: ``--nfs3-usage-model WRITE_WORKLOAD_15``

キャッシュ名、ストレージ ターゲット名、およびリソース グループの値も必須です。

使用モデルの名前を確認するには、コマンド [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage-model-list) を使用します。

キャッシュが停止しているか、正常な状態ではない場合、キャッシュが正常になってから更新が適用されます。

---

## <a name="update-an-adls-nfs-storage-target"></a>ADLS-NFS ストレージ ターゲットを更新する

NFS ターゲットと同様に、ADLS-NFS ストレージ ターゲットの名前空間のパスと使用モデルを変更できます。

### <a name="change-an-adls-nfs-namespace-path"></a>ADLS-NFS 名前空間のパスを変更する

ご利用の Azure HPC Cache の **[名前空間]** ページを使用して、名前空間の値を更新します。 このページについては、記事「[集約された名前空間を設定する](add-namespace-paths.md)」で詳細に説明しています。

![右側に ADS-NFS 更新ページが開いているポータルの名前空間ページのスクリーンショット](media/update-namespace-adls.png)

1. 変更するパスの名前をクリックします。
1. [編集] ウィンドウを使用して、新しい仮想パスを入力するか、アクセス ポリシーを更新します。
1. 変更を加えた後、 **[OK]** をクリックしてストレージ ターゲットを更新するか、 **[キャンセル]** で変更を破棄します。

### <a name="change-adls-nfs-usage-models"></a>ADLS-NFS 使用モデルを変更する

ADLS-NFS 使用モデルの構成は、NFS 使用モデルの選択と同じです。 上記の NFS セクションの「[使用モデルを変更する](#change-the-usage-model)」でポータルでの手順をお読みください。 ADLS-NFS ストレージ ターゲットを更新するための追加のツールは開発中です。

## <a name="next-steps"></a>次のステップ

* 個々のストレージ ターゲットの停止、削除、フラッシュについては、「[ストレージ ターゲットの表示と管理](manage-storage-targets.md)」を参照してください。
* ストレージ ターゲットのオプションの詳細については「[ストレージ ターゲットを追加する](hpc-cache-add-storage.md)」を読んでください。
* 仮想パスの使用に関するその他のヒントについては、「[集約された名前空間を計画する](hpc-cache-namespace.md)」を参照してください。
