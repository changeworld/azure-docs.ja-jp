---
title: Azure Container Apps プレビューのコンテナー
description: Azure Container Apps でコンテナーを管理および構成する方法について説明します
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: fdebbbae4cba1577b8f92e0f54821eb72737c2c3
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026770"
---
# <a name="containers-in-azure-container-apps-preview"></a>Azure Container Apps プレビューのコンテナー

Azure Container Apps により、Kubernetes とコンテナー オーケストレーションの詳細が管理されます。 Azure Container Apps のコンテナーでは、任意のランタイム、プログラミング言語、または開発スタックを使用できます。

:::image type="content" source="media/containers/azure-container-apps-containers.png" alt-text="Azure Container Apps: コンテナー":::

Azure Container Apps では、以下がサポートされています。

- 任意の Linux ベースのコンテナー イメージ
- 任意のパブリックまたはプライベート コンテナー レジストリからのコンテナー

次のような機能も含まれています。

- 必須のベース コンテナー イメージはありません。
- `template` ARM 構成セクションを変更すると、新しい[コンテナー アプリのリビジョン](application-lifecycle-management.md)がトリガーされます。
- コンテナーがクラッシュした場合は、自動的に再起動されます。

## <a name="configuration"></a>構成

次の構成例は、コンテナーを設定するときに使用できるオプションを示しています。

```json
{
  ...
  "template": {
    "containers": [
      {
        "image": "myacr.azurecr.io/myrepo/api-service:v1",
        "name": "my-container-image",
        "command": ["/bin/queue"],
        "args": [],
        "env": [
          {
            "name": "HTTP_PORT",
            "value": "8080"
          }
        ],
        "resources": {
            "cpu": 1,
            "memory": "250Mb"
        }
    }]
  }
}
```

| 設定 | 説明 | 解説 |
|---|---|---|
| `image` | コンテナー アプリのコンテナー イメージ名。 | この値は `repository/image-name:tag` の形式になります。 |
| `name` | コンテナーのフレンドリ名。 | レポートと識別に使用されます。 |
| `command` | コンテナーのスタートアップ コマンド。 | Docker の [entrypoint](https://docs.docker.com/engine/reference/builder/) フィールドと同じです。  |
| `args` | コマンド引数を起動します。 | 配列内のエントリが結合され、スタートアップ コマンドに渡すパラメーター リストが作成されます。 |
| `env` | 環境変数を定義するキーと値のペアの配列。 | シークレットを参照するには、`value` フィールドの代わりに `secretRef` を使用します。 |
| `resources.cpu` | コンテナーに割り当てられた CPU の数。 | 値は、次の規則に従う必要があります。値は 0 より大きく、2 未満である必要があります。また、小数点以下 1 桁の任意の 10 進数を指定できます。 たとえば、`1.1` は有効ですが、`1.55` は無効です。 既定値は、コンテナーあたり 0.5 CPU です。 |
| `resources.memory` | コンテナーに割り当てられた RAM の量。 | この値は、最大 `4Gi` までです。 使用できる単位は[ギビバイト](https://simple.wikipedia.org/wiki/Gibibyte) (`Gi`) のみです。 値は、次の規則に従う必要があります。値は 0 より大きく、`4Gi` 未満である必要があります。また、小数点以下 2 桁の任意の 10 進数を指定できます。 たとえば、`1.25Gi` は有効ですが、`1.555Gi` は無効です。 既定値は、コンテナーあたり `1Gi` です。  |

コンテナー アプリ内のすべてのコンテナーに対して要求された CPU とメモリの合計量は、次のいずれかの組み合わせで追加する必要があります。

| vCPU 数 | メモリ (Gi) |
|---|---|
| 0.5 | 1.0 |
| 1.0 | 2.0 |
| 1.5 | 3.0 |
| 2.0 | 4.0 |

- すべてのコンテナーのすべての CPU 要求は、vCPU 列のいずれかの値と一致している必要があります。
- すべてのコンテナーのすべてのメモリ要求は、CPU 列の同じ行にある memory 列のメモリの値と一致している必要があります。

## <a name="multiple-containers"></a>複数のコンテナー

1 つのコンテナー アプリで複数のコンテナーを定義できます。 コンテナーのグループは[ポッド](https://kubernetes.io/docs/concepts/workloads/pods)と呼ばれます。 ポッド内のコンテナーは、ハード ディスクとネットワーク リソースを共有し、同じ[アプリケーション ライフサイクル](application-lifecycle-management.md)を体験します。

構成の `containers` 配列に複数のコンテナーを定義して、複数のコンテナーを同時に実行します。

ポッドでコンテナーを同時に実行する理由は次のとおりです。

- プライマリ アプリのサイドカーとしてコンテナーを使用します。
- 共有ディスク領域と仮想ネットワークの使用。
- コンテナー間でスケーリング ルールを共有します。
- 常に同時に実行する必要がある複数のコンテナーをグループ化します。
- 同じホスト上のコンテナー間の直接通信を有効にします。

## <a name="container-registries"></a>コンテナー レジストリ

Container Apps 構成を使用して資格情報が提供されるプライベート レジストリでホストされているイメージをデプロイできます。

コンテナー レジストリを使用するには、最初に[構成の](azure-resource-manager-api-spec.md) `registries` セクションに必要なフィールドを定義します。

```json
{
  ...
  "registries": {
    "server": "docker.io",
    "username": "my-registry-user-name",
    "passwordSecretRef": "my-password-secretref-name"
  }
}
```

この設定によって、`image` 配列内の `containers` にあるコンテナー イメージを参照するときに、保存された資格情報を使用できます。

次の例は、Azure Container Registry からアプリをデプロイする方法を示しています。

```json
{
  ...
  "configuration": {
      "secrets": [
          {
              "name": "acr-password",
              "value": "my-acr-password"
          }
      ],
      "registries": [
          {
              "server": "myacr.azurecr.io",
              "username": "someuser",
              "passwordSecretRef": "acr-password"
          }
      ]
  }
}
```

## <a name="limitations"></a>制限事項

Azure Container Apps には、次の制限があります。

- **特権コンテナー**: Azure Container Apps では、特権コンテナーを実行できません。 プログラムがルート アクセスを必要とするプロセスを実行しようとすると、コンテナー内のアプリケーションでランタイム エラーが発生します。

- **オペレーティング システム**: Linux ベースのコンテナー イメージが必要です。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [環境](environment.md)
