---
title: 匿名プル アクセスを有効にする
description: Azure コンテナー レジストリ内のコンテンツを一般公開するには、必要に応じて匿名プル アクセスを有効にします。
ms.topic: how-to
ms.date: 09/17/2021
ms.custom: ''
ms.openlocfilehash: e82bb43ee8865642ae7939a94291e0c9f5432198
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161960"
---
# <a name="make-your-container-registry-content-publicly-available"></a>コンテナー レジストリのコンテンツを一般公開する

匿名 (認証されていない) プル アクセスのために Azure コンテナー レジストリを設定することは、インターネットにアクセスできる任意のユーザーに対してレジストリから任意の内容をプルすることを許可するオプション機能です。

匿名プル アクセスはプレビュー機能であり、Standard と Premium [サービス レベル](container-registry-skus.md)で使用できます。 匿名プル アクセスを構成するには、Azure CLI (バージョン 2.21.0 以降) を使用してレジストリを更新します。 インストールまたはアップグレードする必要には、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。

## <a name="about-anonymous-pull-access"></a>匿名プル アクセスの概要

既定では、Azure コンテナー レジストリからコンテンツをプルまたはプッシュするためのアクセスは、[認証済み](container-registry-authentication.md)ユーザーのみが使用できます。 匿名 (認証されていない) プル アクセスを有効にすると、すべてのレジストリ コンテンツは読み取り (プル) アクションとして一般公開されます。 匿名プル アクセスは、パブリック コンテナー イメージの配布など、ユーザー認証を必要としないシナリオで使用できます。

- 匿名プル アクセスを有効にするには、既存のレジストリのプロパティを更新します。
- 匿名プル アクセスを有効にした後は、いつでもそのアクセスを無効にすることができます。
- 認証されていないクライアントは、データ プレーン操作のみを使用できます。
- 認証されていない要求の割合が高いと、レジストリによって調整される場合があります。
- 以前にレジストリに対して認証を受けたことがある場合は、必ず匿名プル操作を試行する前に資格情報をクリアしてください。

> [!WARNING]
> 現在、匿名プル アクセスは、レジストリ内のすべてのリポジトリに適用されます。 [リポジトリをスコープとしたトークン](container-registry-repository-scoped-permissions.md)を使用してリポジトリ アクセスを管理する場合は、すべてのユーザーが、匿名プルが有効になっているレジストリ内のリポジトリからプルする可能性があります。 匿名プル アクセスが有効になっている場合は、トークンを削除することをお勧めします。

## <a name="configure-anonymous-pull-access"></a>匿名プル アクセスを構成する 

### <a name="enable-anonymous-pull-access"></a>匿名プル アクセスを有効にする
[az acr update](/cli/azure/acr#az_acr_update) コマンドを使用してレジストリを更新し、`--anonymous-pull-enabled` パラメーターを渡します。 既定では、レジストリの匿名プルは無効になっています。
          
```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

> [!IMPORTANT]
> 以前にレジストリに対して Docker の資格情報を使用して認証を受けたことがある場合は、必ず `docker logout` を実行して既存の資格情報をクリアしてから匿名プル操作を試行してください。 そうしないと、"pull access denied" (プル アクセスが拒否されました) のようなエラー メッセージが表示される可能性があります。

### <a name="disable-anonymous-pull-access"></a>匿名プル アクセスを無効にする
匿名プル アクセスを無効にするには、`--anonymous-pull-enabled` を `false` に設定します。

```azurecli
az acr update --name myregistry --anonymous-pull-enabled false
```

## <a name="next-steps"></a>次のステップ

* [リポジトリスコープのトークン](container-registry-repository-scoped-permissions.md)について確認します。
* Azure コンテナー レジストリに対して[認証を受ける](container-registry-authentication.md)オプションについて確認します。
