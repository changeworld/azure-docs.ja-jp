---
title: (非推奨) Azure Kubernetes クラスターを監視する - Sysdig
description: Sysdig を使用した Azure Container Service での Kubernetes クラスターの監視
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 68136d5b9ec16c822cb62e4fee85b8ace9b1899a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371101"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(非推奨) Sysdig を使用して Azure Container Service Kubernetes クラスターを監視します

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、[Azure Container Service を使用して Kubernetes クラスターを作成](container-service-kubernetes-walkthrough.md)したことを想定します。

また、azure cli と kubectl ツールをインストールしていることも想定します。

`az` ツールがインストールされていることを確認するには、次を実行します。

```azurecli
az --version
```

`az` ツールをインストールしていないない場合、[ここ](https://github.com/azure/azure-cli#installation)に手順が記載されています。

`kubectl` ツールがインストールされていることを確認するには、次を実行します。

```console
kubectl version
```

`kubectl` をインストールしていない場合、次を実行できます。

```azurecli
az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
Sysdig は、Azure で実行されている Kubernetes クラスター内のコンテナーを監視できる、サービス会社としての外部監視です。 Sysdig を使用するには、アクティブな Sysdig アカウントが必要になります。
アカウントには、その[サイト](https://app.sysdigcloud.com)でサインアップできます。

Sysdig クラウドの Web サイトへのログイン後に自分のユーザー名をクリックすると、該当する "アクセス キー" がそのページに表示されます。 

![Sysdig API key](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Kubernetes への Sysdig エージェントのインストール
コンテナーを監視するには、Sysdig で、Kubernetes `DaemonSet` を使用して各コンピューターでプロセスを実行します。
DaemonSet は、コンピューターごとに 1 つのコンテナーの 1 つのインスタンスを実行する Kubernetes API オブジェクトです。
Sysdig の監視エージェントなどのツールをインストールする場合に最適です。

Sysdig DaemonSet をインストールするには、まず[テンプレート](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes)を sysdig からダウンロードします。 そのファイルを `sysdig-daemonset.yaml` という名前で保存します。

Linux および OS X 上では次のように実行することができます。

```console
curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

PowerShell では次のとおりです。

```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

次に、そのファイルを編集して、Sysdig アカウントから取得したアクセス キーを挿入します。

最後に、DaemonSet を作成します。

```console
kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>監視の表示
インストールして実行したら、エージェントが Sysdig にデータを返します。  [Sysdig ダッシュボード](https://app.sysdigcloud.com)に戻ると、コンテナーに関する情報が表示されます。

[新しいダッシュボード ウィザード](https://app.sysdigcloud.com/#/dashboards/new)を使用して、Kubernetes 固有のダッシュボードをインストールすることもできます。
