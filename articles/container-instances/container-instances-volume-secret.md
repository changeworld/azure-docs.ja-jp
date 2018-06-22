---
title: Azure Container Instances にシークレット ボリュームをマウントする
description: シークレット ボリュームをマウントし、コンテナー インスタンスがアクセスする秘密情報を保存する方法について説明します。
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: a030582c885dd0a5680bd23046ea2a9c0329701a
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830073"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Azure Container Instances にシークレット ボリュームをマウントする

コンテナー グループに属するコンテナーで秘密情報を保存したり、取得したりできるように、コンテナー インスタンスに*シークレット* ボリュームをマウントする方法について説明します。

> [!NOTE]
> *シークレット* ボリュームのマウントは現在、Linux コンテナーに限定されています。 Windows と Linux の両方のコンテナーのセキュリティで保護された環境変数を渡す方法の詳細については、「[環境変数の設定](container-instances-environment-variables.md)」を参照してください。 すべての機能を Windows コンテナーにも採り入れることに取り組んでいますが、現在のプラットフォームの違いは、「[Quotas and region availability for Azure Container Instances](container-instances-quotas.md)」(Azure Container Instances のクォータとリージョンの可用性) で確認できます。

## <a name="secret-volume"></a>シークレット ボリューム

*シークレット* ボリュームを利用し、コンテナー グループに属するコンテナーに秘密情報を提供します。 *シークレット* ボリュームは、ボリューム内のファイルに指定の秘密を格納します。コンテナー グループに属するコンテナーは格納された秘密にアクセスできます。 *シークレット* ボリュームのシークレットを利用することで、SSH キーやデータベース資格情報のような取扱注意のデータをアプリケーション コードに入れる必要がなくなります。

*シークレット* ボリュームは、RAM でバックアップされるファイルシステムである [tmpfs][tmpfs] によってバックアップされます。コンテンツが不揮発性の記憶域に書き込まれることはありません。

## <a name="mount-a-secret-volume"></a>シークレット ボリュームをマウントする

コンテナー インスタンスに*シークレット* ボリュームをマウントするには、[Azure Resource Manager テンプレート](/azure/templates/microsoft.containerinstance/containergroups)を使用してデプロイを行う必要があります。

最初に、テンプレートのコンテナー グループ `properties` セクションに `volumes` 配列を入力します。 次に、*シークレット* ボリュームをマウントするコンテナー グループ内の各コンテナーに対して、コンテナー定義の `properties` セクションで `volumeMounts` 配列を設定します。

たとえば、次の Resource Manager テンプレートでは、1 つのコンテナーから構成されるコンテナー グループが作成されます。 このコンテナーは、Base64 で暗号化された 2 つのシークレットから構成される*シークレット* ボリュームをマウントします。

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Azure Resource Manager テンプレートによるコンテナー インスタンスのデプロイ例を見るには、[Azure Container Instances での複数コンテナーのデプロイ](container-instances-multi-container-group.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

Azure Container Instances にその他の種類のボリュームをマウントする方法について学習してください。

* [Azure Container Instances に Azure ファイル共有をマウントする](container-instances-volume-azure-files.md)
* [Azure Container Instances に emptyDir ボリュームをマウントする](container-instances-volume-emptydir.md)
* [Azure Container Instances に gitRepo ボリュームをマウントする](container-instances-volume-gitrepo.md)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs