---
title: "Azure Container Instances のコンテナー グループ"
description: "Azure Container Instances のコンテナー グループのしくみを理解します"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 12/19/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: a42c01917926a4297c97cf9c5dfd1333dbef6793
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2018
---
# <a name="container-groups-in-azure-container-instances"></a>Azure Container Instances のコンテナー グループ

Azure Container Instances の最上位のリソースは、*コンテナー グループ*です。 この記事では、コンテナー グループとは何か、およびそれらによってどのような種類のシナリオが有効になるかを説明します。

## <a name="how-a-container-group-works"></a>コンテナー グループのしくみ

コンテナー グループは、同じホスト コンピューター上にスケジュール設定されるコンテナーのコレクションです。 コンテナー グループ内のコンテナーは、ライフサイクル、ローカル ネットワーク、およびストレージ ボリュームを共有します。 それは、[Kubernetes][kubernetes-pod] と [DC/OS][dcos-pod] における "*ポッド*" の概念に似ています。

次の図は、複数のコンテナーを含むコンテナー グループの例を示しています。

![コンテナー グループ ダイアグラム][container-groups-example]

この例のコンテナー グループは:

* 単一のホスト コンピューター上にスケジュール設定されます。
* 単一のパブリック IP アドレスを公開し、1 つの公開ポートを持ちます。
* 次の 2 つのコンテナーから構成されます。 片方のコンテナーはポート 80 でリッスンし、他方のポートはポート 5000 でリッスンします。
* ボリューム マウントとして 2 つの Azure ファイル共有が含まれています。各コンテナーは共有のいずれかをローカルにマウントします。

> [!NOTE]
> 複数コンテナー グループは、現在、Linux コンテナーに限定されています。 すべての機能を Windows コンテナーにも採り入れることに取り組んでいますが、現在のプラットフォームの違いは、「[Quotas and region availability for Azure Container Instances](container-instances-quotas.md)」(Azure Container Instances のクォータとリージョンの可用性) で確認できます。

### <a name="networking"></a>ネットワーク

コンテナー グループは、IP アドレスと、その IP アドレス上のポートの名前空間を共有します。 外部クライアントがグループ内のコンテナーにアクセスできるようにするには、IP アドレスのポートをコンテナーから公開する必要があります。 グループ内のコンテナーがポートの名前空間を共有するため、ポートのマッピングはサポートされません。 グループ内のコンテナーは、ポートの localhost 経由で相互にアクセスできます。これは、これらのポートがグループの IP アドレスで外部的に公開されていない場合でも同じです。

### <a name="storage"></a>Storage

コンテナー グループにマウントする外部ボリュームを指定できます。 これらのボリュームは、グループの個別のコンテナーの特定のパスにマップできます。

## <a name="common-scenarios"></a>一般的なシナリオ

単一の機能タスクを少数のコンテナー イメージに分割する場合は、マルチコンテナー グループが便利です。これにより、異なるチームによる配信が可能になり、別々のリソース要件を適用できます。

次のような使用例が考えられます。

* アプリケーション コンテナーとログ記録コンテナー。 ログ記録コンテナーは、メイン アプリケーションによって出力されるログとメトリックを収集し、長期保存される記憶域に書き込みます。
* アプリケーションと監視コンテナー。 監視コンテナーは、要求を定期的にアプリケーションに送信して、アプリケーションが実行中であり、正常に応答していることを確認します。そうでない場合はアラートを発生させます。
* Web アプリケーションにサービスを提供するコンテナーとソース管理から最新のコンテンツをプルするコンテナー。

## <a name="next-steps"></a>次の手順

Azure Resource Manager テンプレートを使用して[マルチコンテナー グループをデプロイする](container-instances-multi-container-group.md)方法を確認します。

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
