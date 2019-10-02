---
title: Azure Container Registry でタグなしマニフェストを保持するポリシー
description: Azure コンテナー レジストリで保持ポリシーを有効化して、指定期間後にタグなしマニフェストを自動で削除する方法について説明します。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/25/2019
ms.author: danlep
ms.openlocfilehash: 36d27bc6089bbe3f4ada6862a9c1be1fa0bdbae7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71305922"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>タグなしマニフェストの保持ポリシーの設定

Azure Container Registry では、タグが関連付けられていない格納済みのイメージ マニフェスト ("*タグなしマニフェスト*") に対して、"*保持ポリシー*" を設定できます。 保持ポリシーが有効な場合、レジストリ内にあるタグなしマニフェストは、設定日数の経過後に自動で削除されます。 この機能は、不要なアーティファクトでレジストリがいっぱいになることを防ぐほか、ストレージ コストの削減に役立ちます。 タグなしマニフェストの `delete-enabled` 属性が `false` に設定されている場合、そのマニフェストを削除することはできず、保持ポリシーは適用されません。

この記事のサンプル コマンドは、Azure Cloud Shell または Azure CLI のローカル インストールを使用して実行できます。 これをローカルで使用したい場合は、バージョン 2.0.74 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

> [!IMPORTANT]
> この機能は現在プレビュー段階であり、一定の[制限事項が適用されます](#preview-limitations)。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

> [!WARNING]
> 保持ポリシーの設定は慎重に行ってください。削除したイメージ データは復元できません。 (イメージ名ではなく) マニフェスト ダイジェストによってイメージをプルするシステムを使用している場合は、タグなしマニフェスト用の保持ポリシーは設定しないようにしてください。 このようなシステムでは、タグの付いていないイメージを削除すると、レジストリからイメージをプルできなくなります。 マニフェストでプルするのではなく、*一意のタグ付け*スキームの[推奨されるベスト プラクティス](container-registry-image-tag-version.md)を採用することを検討してください。

Azure CLI コマンドを使用して 1 つのイメージ タグまたはマニフェストを削除する場合は、「[Azure Container Registry のコンテナー イメージを削除する](container-registry-delete.md)」を参照してください。

## <a name="preview-limitations"></a>プレビューの制限事項

* 保持ポリシーを構成できるのは、**Premium** コンテナー レジストリだけです。 レジストリ サービス レベルについては、[「Azure Container Registry SKU」](container-registry-skus.md)をご覧ください。
* 保持ポリシーは、タグなしマニフェストに対してのみ設定できます。

## <a name="set-a-retention-policy---cli"></a>保持ポリシーの設定 - CLI

次の例では、Azure CLI を使用して、レジストリ内のタグなしマニフェストの保持ポリシーを設定する方法を示します。

### <a name="enable-a-retention-policy"></a>保持ポリシーの有効化

既定では、コンテナー レジストリに保持ポリシーは設定されていません。 保持ポリシーを設定または更新するには、Azure CLI で [az acr config retention update][az-acr-config-retention-update] コマンドを実行します。 タグなしマニフェストを保持する期間は、0 日から 365 日までの範囲で指定できます。 日数を指定しない場合、コマンドによって既定値の 7 日に設定されます。 この保持期間の後、レジストリ内にあるすべてのタグなしマニフェストが自動的に削除されます。

次の例では、レジストリ *myregistry* 内のタグなしマニフェストに対して、30 日間の保持ポリシーを設定しています。

```azurecli
az acr config retention update --name myregistry --status enabled --days 30 --type UntaggedManifests
```

次の例では、レジストリ内にあるマニフェストのタグ付けがなくなると即座にそのマニフェストを削除するように、ポリシーを設定します。 このようなポリシーを作成するには、保持期間を 0 日に設定します。

```azurecli
az acr config retention update --name myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="disable-a-retention-policy"></a>保持ポリシーの無効化

レジストリに設定されている保持ポリシーを確認するには、[az acr config retention show][az-acr-config-retention-show] コマンドを実行します。

```azurecli
az acr config retention show --name myregistry
```

レジストリの保持ポリシーを無効にするには、[az acr config retention update][az-acr-config-retention-update] コマンドを実行して `--status disabled` を設定します。

```azurecli
az acr config retention update --name myregistry --status disabled
```

## <a name="set-a-retention-policy---portal"></a>保持ポリシーの設定 - ポータル

レジストリの保持ポリシーは、[Azure portal](https://portal.azure.com) でも設定できます。 次の例では、ポータルを使用して、レジストリ内にあるタグなしマニフェストの保持ポリシーを設定する方法を示します。

### <a name="enable-a-retention-policy"></a>保持ポリシーの有効化

1. 自分の Azure コンテナー レジストリに移動します。 **[ポリシー]** の下にある **[Retention (Preview)]\(保持 (プレビュー)\)** を選択します。
1. **[状態]** で **[有効]** を選択します。
1. タグなしマニフェストの保持期間を、0 日から 365 日までの範囲で選択します。 **[保存]** を選択します。

![Azure portal での保持ポリシーの有効化](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>保持ポリシーの無効化

1. 自分の Azure コンテナー レジストリに移動します。 **[ポリシー]** の下にある **[Retention (Preview)]\(保持 (プレビュー)\)** を選択します。
1. **[状態]** で **[無効]** を選択します。 **[保存]** を選択します。

## <a name="next-steps"></a>次の手順

* Azure Container Registry の[イメージとリポジトリを削除](container-registry-delete.md)する方法を確認します

* 選択したイメージとマニフェストをレジストリから[自動的に消去](container-registry-auto-purge.md)する方法を確認します

* レジストリの[イメージとマニフェストをロック](container-registry-image-lock.md)する方法を確認します

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
