---
title: タグなしマニフェストを保持するポリシー
description: Premium Azure コンテナー レジストリで保持ポリシーを有効化して、指定期間後にタグなしマニフェストを自動で削除する方法について説明します。
ms.topic: article
ms.date: 04/26/2021
ms.openlocfilehash: 80e16c4b8cccdda2e171159cca807368b2648e82
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110062832"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>タグなしマニフェストの保持ポリシーの設定

Azure Container Registry では、タグが関連付けられていない格納済みのイメージ マニフェスト ("*タグなしマニフェスト*") に対して、"*保持ポリシー*" を設定できます。 保持ポリシーが有効な場合、レジストリ内にあるタグなしマニフェストは、設定日数の経過後に自動で削除されます。 この機能は、不要なアーティファクトでレジストリがいっぱいになることを防ぐほか、ストレージ コストの削減に役立ちます。 

この記事のサンプル コマンドは、Azure Cloud Shell または Azure CLI のローカル インストールを使用して実行できます。 これをローカルで使用したい場合は、バージョン 2.0.74 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

タグのないマニフェストのアイテム保持ポリシーは現在、**Premium** コンテナー レジストリのプレビュー機能です。 レジストリ サービス レベルについては、[Azure Container Registry のサービス レベル](container-registry-skus.md)に関するページをご覧ください。

> [!WARNING]
> 保持ポリシーの設定は慎重に行ってください。削除したイメージ データは復元できません。 (イメージ名ではなく) マニフェスト ダイジェストによってイメージをプルするシステムを使用している場合は、タグなしマニフェスト用の保持ポリシーは設定しないようにしてください。 このようなシステムでは、タグの付いていないイメージを削除すると、レジストリからイメージをプルできなくなります。 マニフェストでプルするのではなく、*一意のタグ付け* スキームの [推奨されるベスト プラクティス](container-registry-image-tag-version.md)を採用することを検討してください。

## <a name="about-the-retention-policy"></a>アイテム保持ポリシーについて

Azure Container Registry は、レジストリ内のマニフェストのカウントを参照します。 マニフェストがタグ付けされていない場合は、保持ポリシーを確認します。 保持ポリシーが有効になっている場合、マニフェストの削除操作は、ポリシーで設定された日数に従って、特定の日付のキューに登録されます。

個別のキュー管理ジョブは、常にメッセージを処理し、必要に応じてスケーリングします。 例えば、保持ポリシーが 30 日のレジストリで、1 時間間隔の 2 つのマニフェストのタグを解除したとします。 この場合、2 つのメッセージがキューに登録されます。 30 日後、ポリシーが引き続き有効であると見なされ、約 1 時間間隔でメッセージがキューから取得されて処理されます。

タグ付けされていないマニフェストの `delete-enabled` 属性が `false` に設定されている場合、マニフェストはロックされ、ポリシーによって削除されません。

> [!IMPORTANT]
> アイテム保持ポリシーは、ポリシーが有効になった *後に* のタイムスタンプを持つタグ付けされていないマニフェストにのみ適用されます。 以前のタイムスタンプを持つレジストリ内のタグ付けされていないマニフェストは、ポリシーの対象になりません。 イメージ データを削除するその他のオプションについては、「[Azure Container Registry のコンテナー イメージを削除する](container-registry-delete.md)」の例を参照してください。

## <a name="set-a-retention-policy---cli"></a>保持ポリシーの設定 - CLI

次の例では、Azure CLI を使用して、レジストリ内のタグなしマニフェストの保持ポリシーを設定する方法を示します。

### <a name="enable-a-retention-policy"></a>保持ポリシーの有効化

既定では、コンテナー レジストリに保持ポリシーは設定されていません。 保持ポリシーを設定または更新するには、Azure CLI で [az acr config retention update][az-acr-config-retention-update] コマンドを実行します。 タグなしマニフェストを保持する期間は、0 日から 365 日までの範囲で指定できます。 日数を指定しない場合、コマンドによって既定値の 7 日に設定されます。 この保持期間の後、レジストリ内にあるすべてのタグなしマニフェストが自動的に削除されます。

次の例では、レジストリ *myregistry* 内のタグなしマニフェストに対して、30 日間の保持ポリシーを設定しています。

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

次の例では、レジストリ内にあるマニフェストのタグ付けがなくなると即座にそのマニフェストを削除するように、ポリシーを設定します。 このようなポリシーを作成するには、保持期間を 0 日に設定します。 

```azurecli
az acr config retention update \
  --registry myregistry --status enabled \
  --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>アイテム保持ポリシーを検証する

保有期間が 0 日の上記のポリシーを有効にすると、タグなしマニフェストが削除されることをすぐに確認できます。

1. テスト イメージ `hello-world:latest` をレジストリにプッシュするか、別のテストイメージを選択して置き換えます。
1. [az acr repository untag][az-acr-repository-untag] コマンドを使用して、例えば `hello-world:latest` イメージのタグを削除します。 タグなしマニフェストはレジストリに残ります。
    ```azurecli
    az acr repository untag \
      --name myregistry --image hello-world:latest
    ```
1. 数秒で、タグなしマニフェストが削除されます。 削除されたことを確認するには、[az acr repository show-manifests][az-acr-repository-show-manifests] コマンドなどを使用してリポジトリでマニフェストを一覧表示します。 リポジトリにテスト イメージが 1 つしかない場合、リポジトリ自体が削除されます。

### <a name="manage-a-retention-policy"></a>保持ポリシーを管理する

レジストリに設定されている保持ポリシーを表示するには、[az acr config retention show][az-acr-config-retention-show] コマンドを実行します。

```azurecli
az acr config retention show --registry myregistry
```

レジストリの保持ポリシーを無効にするには、[az acr config retention update][az-acr-config-retention-update] コマンドを実行して `--status disabled` を設定します。

```azurecli
az acr config retention update \
  --registry myregistry --status disabled \
  --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>保持ポリシーの設定 - ポータル

レジストリの保持ポリシーは、[Azure portal](https://portal.azure.com) でも設定できます。 

### <a name="enable-a-retention-policy"></a>保持ポリシーの有効化

1. 自分の Azure コンテナー レジストリに移動します。 **[ポリシー]** の下にある **[Retention (Preview)]\(保持 (プレビュー)\)** を選択します。
1. **[状態]** で **[有効]** を選択します。
1. タグなしマニフェストの保持期間を、0 日から 365 日までの範囲で選択します。 **[保存]** を選択します。

![Azure portal での保持ポリシーの有効化](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>保持ポリシーの無効化

1. 自分の Azure コンテナー レジストリに移動します。 **[ポリシー]** の下にある **[Retention (Preview)]\(保持 (プレビュー)\)** を選択します。
1. **[状態]** で **[無効]** を選択します。 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

* Azure Container Registry の[イメージとリポジトリを削除](container-registry-delete.md)する方法を確認します

* 選択したイメージとマニフェストをレジストリから[自動的に消去](container-registry-auto-purge.md)する方法を確認します

* レジストリの[イメージとマニフェストをロック](container-registry-image-lock.md)する方法を確認します

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az_acr_config_retention_update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az_acr_config_retention_show
[az-acr-repository-untag]: /cli/azure/acr/repository#az_acr_repository_untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
