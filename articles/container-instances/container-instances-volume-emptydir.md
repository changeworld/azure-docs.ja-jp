---
title: Azure Container Instances に emptyDir ボリュームをマウントする
description: emptyDir ボリュームをマウントし、Azure Container Instances のコンテナー グループに属するコンテナー間でデータを共有する方法について説明します。
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 89289a7a0bb5c486c662d528c5014bdbd8eebaca
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164892"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Azure Container Instances に emptyDir ボリュームをマウントする

*emptyDir* ボリュームをマウントし、Azure Container Instances のコンテナー グループに属するコンテナー間でデータを共有する方法について説明します。

> [!NOTE]
> *emptyDir* ボリュームのマウントは現在、Linux コンテナーに限定されています。 すべての機能を Windows コンテナーにも採り入れることに取り組んでいますが、現在のプラットフォームの違いは、「[Quotas and region availability for Azure Container Instances](container-instances-quotas.md)」(Azure Container Instances のクォータとリージョンの可用性) で確認できます。

## <a name="emptydir-volume"></a>emptyDir ボリューム

*emptyDir* ボリュームによって、あるコンテナー グループに属する各コンテナーにアクセスできる、書き込み可能なディレクトリが与えられます。 グループ内のコンテナーはボリューム内の同じファイルを読み書きできます。ボリュームは同じか別のパスを利用して各コンテナーにマウントできます。

*emptyDir* ボリュームの使用例:

* スクラッチ領域
* 長時間実行されるタスクの間のチェックポイント機能
* サイドカー コンテナーが取得し、アプリケーション コンテナーがサービスを提供するデータの保存

*emptyDir* ボリュームのデータはコンテナーがクラッシュしても存続します。 ただし、再開後のコンテナーが *emptyDir* ボリュームのデータを永続化するかどうかは保証されません。

## <a name="mount-an-emptydir-volume"></a>emptyDir ボリュームをマウントする

コンテナー インスタンスに emptyDir ボリュームをマウントするには、[Azure Resource Manager テンプレート](/azure/templates/microsoft.containerinstance/containergroups)を使用してデプロイを行う必要があります。

最初に、テンプレートのコンテナー グループ `properties` セクションに `volumes` 配列を入力します。 次に、*emptyDir* ボリュームをマウントするコンテナー グループ内の各コンテナーに対して、コンテナー定義の `properties` セクションで `volumeMounts` 配列を設定します。

たとえば、次の Resource Manager テンプレートでは、それぞれが *emptyDir* ボリュームを作成する 2 つのコンテナーから構成されるコンテナー グループが作成されます。

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Azure Resource Manager テンプレートによるコンテナー インスタンスのデプロイ例を見るには、[Azure Container Instances での複数コンテナーのデプロイ](container-instances-multi-container-group.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

Azure Container Instances にその他の種類のボリュームをマウントする方法について学習してください。

* [Azure Container Instances に Azure ファイル共有をマウントする](container-instances-volume-azure-files.md)
* [Azure Container Instances に gitRepo ボリュームをマウントする](container-instances-volume-gitrepo.md)
* [Azure Container Instances にシークレット ボリュームをマウントする](container-instances-volume-secret.md)