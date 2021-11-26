---
title: CLI を使用した間接モードの Azure Arc データ コントローラーのアップグレード
description: CLI を使用した間接モードの Azure Arc データ コントローラーのアップグレード
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 5c1afa620fb3b81666aada950c257f38ff5d9a4f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707716"
---
# <a name="upgrade-indirect-mode-azure-arc-data-controller-using-the-cli"></a>CLI を使用した間接モードの Azure Arc データ コントローラーのアップグレード

この記事では、Azure CLI (`az`) を使用して、間接的に接続された Azure Arc 対応データ コントローラーをアップグレードする方法について説明します。

> [!IMPORTANT]
> この記事は、直接接続された Azure Arc 対応データ コントローラーには適用されません。 直接接続されたデータ コントローラーをアップグレードする方法の最新情報については、[リリース ノート](./release-notes.md#data-controller-upgrade)を参照してください。

## <a name="prerequisites"></a>前提条件

imageTag v1.0.0_2021-07-30 以降を使用した間接モード データ コントローラーが必要です。

バージョンを確認するには、次を実行します。

```console
kubectl get datacontrollers -n -o custom-columns=BUILD:.spec.docker.imageTag
```

## <a name="install-tools"></a>ツールをインストールする

この記事のタスクに進む前に、次をインストールする必要があります。

- [Azure CLI (az)](/cli/azure/install-azure-cli)
- [Azure CLI 用の `arcdata` 拡張機能](install-arcdata-extension.md)

[!INCLUDE [azure-arc-angle-bracket-example](../../../includes/azure-arc-angle-bracket-example.md)]

## <a name="view-available-images-and-chose-a-version"></a>使用可能なイメージを表示してバージョンを選択する

次のコマンドを使用して、データ コントローラーで使用可能なイメージの一覧をプルします。

   ```azurecli
   az arcdata dc list-upgrades --k8s-namespace <namespace> –-use-k8s
   ```

上記のコマンドでは、次の例のような出力が返されます。

```output
Found 2 valid versions.  The current datacontroller version is v1.0.0_2021-07-30.
v1.1.0_2021-11-02
v1.0.0_2021-07-30
```

## <a name="upgrade-data-controller"></a>データ コントローラーをアップグレードする

このセクションでは、間接モードでデータ コントローラーをアップグレードする方法について説明します。

> [!NOTE]
> 一部のデータ サービス レベルとモードは一般提供段階であり、一部はプレビュー段階です。
> 同じデータ コントローラーに GA サービスとプレビュー サービスをインストールしている場合、インプレース アップグレードはできません。
> アップグレードするには、GA でないすべてのデータベース インスタンスを削除します。 一般公開されているサービスとプレビュー サービスの一覧は、[リリース ノート](./release-notes.md)で確認できます。

### <a name="indirect-mode"></a>間接モード

Azure Arc データ コントローラーのアップグレードを開始する前に、Kubernetes クラスターに接続して認証し、既存の Kubernetes コンテキストを選択しておく必要があります。

ドライ ランを最初に実行できます。 ドライ ランでは、レジストリの存在、バージョン スキーマ、およびプライベート リポジトリ認可トークン (使用されている場合) が検証されます。 ドライ ランを実行するには、`az arcdata dc upgrade` コマンドで `--dry-run` パラメーターを使用します。 次に例を示します。

```azurecli
az arcdata dc upgrade --desired-version <version> --k8s-namespace <namespace> --dry-run --use-k8s
```

前のコマンドの出力は次のようになります。

```output
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
****Dry Run****
Arcdata Control Plane would be upgraded to: 20211024.1
```

データ コントローラーをアップグレードするには、`az arcdata dc upgrade` コマンドを実行します。 ターゲット イメージを指定しない場合、データ コントローラーは最新バージョンにアップグレードされます。 次の例では、ローカル変数 (`$version`) を使用して、以前に選択したバージョン ([使用可能なイメージを表示してバージョンを選択する](#view-available-images-and-chose-a-version)) を使用します。

```azurecli
az arcdata dc upgrade --desired-version $version --k8s-namespace <namespace> --use-k8s
```

前のコマンドの出力には、手順の状態が表示されます。

```output
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
Creating service account: arc:cr-upgrade-worker
Creating cluster role: arc:cr-upgrade-worker
Creating cluster role binding: arc:crb-upgrade-worker
Cluster role binding: arc:crb-upgrade-worker created successfully.
Cluster role: arc:cr-upgrade-worker created successfully.
Service account arc:cr-upgrade-worker has been created successfully.
Creating privileged job arc-elevated-bootstrapper-job
```

## <a name="monitor-the-upgrade-status"></a>アップグレードの状態を監視する

Kubectl または CLI を使用して、アップグレードの進行状況を監視できます。

### <a name="kubectl"></a>kubectl

```console
kubectl get datacontrollers --namespace <namespace>
kubectl get monitors --namespace <namespace>
```

アップグレードは 2 つの部分からなるプロセスです。 最初にコントローラーがアップグレードされ、次に監視スタックがアップグレードされます。 アップグレード中に、状態を表示するには ```kubectl get monitors -n <namespace> -w``` を使用します。 次のように出力されます。

```output
NAME           STATUS     AGE
monitorstack   Updating   36m
monitorstack   Updating   36m
monitorstack   Updating   39m
monitorstack   Updating   39m
monitorstack   Updating   41m
monitorstack   Ready      41m
```

### <a name="cli"></a>CLI

```azurecli
 az arcdata dc status show --k8s-namespace <namespace> --use-k8s
```

アップグレードは 2 つの部分からなるプロセスです。 最初にコントローラーがアップグレードされ、次に監視スタックがアップグレードされます。 アップグレードが完了すると、出力は次のようになります。

```output
Ready
```

## <a name="troubleshoot-upgrade-problems"></a>アップグレードに関する問題のトラブルシューティング

アップグレードで問題が発生した場合は、「[トラブルシューティング ガイド](troubleshoot-guide.md)」を参照してください。