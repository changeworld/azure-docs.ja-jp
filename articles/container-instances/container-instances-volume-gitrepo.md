---
title: "Azure Container Instances に gitRepo ボリュームをマウントする"
description: "gitRepo ボリュームをマウントし、Git リポジトリの複製をコンテナー インスタンスに作成する方法について説明します。"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 9acde9259fcb392458e7b2fa7d3369776978285e
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2018
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Azure Container Instances に gitRepo ボリュームをマウントする

*gitRepo ボリューム*をマウントし、Git リポジトリの複製をコンテナー インスタンスに作成する方法について説明します。

> [!NOTE]
> *gitRepo* ボリュームのマウントは現在、Linux コンテナーに限定されています。 すべての機能を Windows コンテナーにも採り入れることに取り組んでいますが、現在のプラットフォームの違いは、「[Quotas and region availability for Azure Container Instances](container-instances-quotas.md)」(Azure Container Instances のクォータとリージョンの可用性) で確認できます。

## <a name="gitrepo-volume"></a>gitRepo ボリューム

*gitRepo* ボリュームはディレクトリをマウントし、コンテナーの起動時、指定の Git リポジトリの複製をそのディレクトリに作成します。 コンテナー インスタンスで *gitRepo* ボリュームを使用することで、そのためのコードを自分のアプリケーションに追加する必要がなくなります。

*gitRepo* ボリュームをマウントするとき、次の 3 つのプロパティを設定し、ボリュームを構成できます。

| プロパティ | 必須 | [説明] |
| -------- | -------- | ----------- |
| `repository` | [はい] | 完全 URL。複製を作成する Git リポジトリの `http://` または `https://` も含まれます。|
| `directory` | いいえ  | リポジトリの複製を作成するディレクトリ。 パスには "`..`" を含めることができません。  "`.`" を指定すると、リポジトリの複製がボリュームのディレクトリに作成されます。 指定しない場合、ボリューム ディレクトリ内の指定の下位ディレクトリに Git リポジトリの複製が作成されます。 |
| `revision` | いいえ  | 複製を作成するリビジョンのコミット ハッシュ。 指定しない場合、`HEAD` リビジョンが複製されます。 |

## <a name="mount-a-gitrepo-volume"></a>gitRepo ボリュームをマウントする

コンテナー インスタンスに *gitRepo* ボリュームをマウントするには、[Azure Resource Manager テンプレート](/azure/templates/microsoft.containerinstance/containergroups)を使用してデプロイを行う必要があります。

最初に、テンプレートのコンテナー グループ `properties` セクションに `volumes` 配列を入力します。 次に、*gitRepo* ボリュームをマウントするコンテナー グループ内の各コンテナーに対して、コンテナー定義の `properties` セクションで `volumeMounts` 配列を設定します。

たとえば、次の Resource Manager テンプレートでは、1 つのコンテナーから構成されるコンテナー グループが作成されます。 このコンテナーによって、*gitRepo* ボリューム ブロックにより指定される 2 つの GitHub リポジトリが複製されます。 2 つ目のボリュームには、複製先のディレクトリを指定する追加プロパティと複製する特定のリビジョンのコミット ハッシュが含まれています。

[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

先のテンプレートに定義されていた 2 つの複製リポジトリのディレクトリ構造は結果的に次のようになります。

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Azure Resource Manager テンプレートによるコンテナー インスタンスのデプロイ例を見るには、[Azure Container Instances での複数コンテナーのデプロイ](container-instances-multi-container-group.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

Azure Container Instances にその他の種類のボリュームをマウントする方法について学習してください。

* [Azure Container Instances に Azure ファイル共有をマウントする](container-instances-volume-azure-files.md)
* [Azure Container Instances に emptyDir ボリュームをマウントする](container-instances-volume-emptydir.md)
* [Azure Container Instances にシークレット ボリュームをマウントする](container-instances-volume-secret.md)
