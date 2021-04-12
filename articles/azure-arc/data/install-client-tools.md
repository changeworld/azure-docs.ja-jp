---
title: クライアント ツールのインストール
description: azdata、kubectl、Azure CLI、psql、Azure Data Studio (Insider)、および Azure Data Studio 向けの Arc 拡張機能をインストールする
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6f42f712ecca77c00020304b63f5a1b0dbd77ad0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172322"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Azure Arc 対応データ サービスをデプロイおよび管理するためのクライアント ツールをインストールする

> [!IMPORTANT]
> 新しい月次リリースに更新する場合は、最新バージョンの Azure Data Studio、[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] ツール、[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]、および Azure Data Studio 向けの Azure Arc 拡張機能も更新してください。

このドキュメントでは、[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]、Azure Data Studio、Azure CLI (az)、および Kubernetes CLI ツール (kubectl) をクライアント コンピューターにインストールする手順について説明します。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Azure Arc 対応データ サービスを作成および管理するためのクライアント ツール 

次の表では、Azure Arc 対応データ サービスを作成および管理するために必要な一般的なツールと、これらのツールをインストールする方法を示します。

| ツール | 必須 | 説明 | インストール |
|---|---|---|---|
| [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] | はい | ビッグ データ クラスターをインストールして管理するためのコマンドライン ツールです。 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] には、Azure SQL インスタンスと SQL Server インスタンス、および Postgres サーバーに接続してクエリを実行するためのコマンド ライン ユーティリティも含まれています。ここでは、コマンド `azdata sql query` (コマンド ラインから単一のクエリを実行)、`azdata sql shell` (対話型シェル)、`azdata postgres query`、および `azdata postgres shell` を使用します。 | [インストール](/sql/azdata/install/deploy-install-azdata?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json) |
| Azure Data Studio | はい | Azure SQL、SQL Server、PostrgreSQL、MySQL などのさまざまなデータベースに接続してクエリを実行するためのリッチなエクスペリエンスを提供するツールです。 Azure Data Studio の拡張機能は、Azure Arc 対応データ サービスの管理エクスペリエンスを提供します。 | [インストール](/sql/azure-data-studio/download-azure-data-studio) |
| Azure Data Studio 用の [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 拡張機能 | はい | Azure Data Studio の拡張機能。[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] がまだインストールされていない場合にインストールを行います。| Azure Data Studio の拡張機能ギャラリーからインストールします。|
| Azure Data Studio 用の Azure Arc 拡張機能 | はい | Azure Data Studio の拡張機能は、Azure Arc 対応データ サービスの管理エクスペリエンスを提供します。 Azure Data Studio 用の [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 拡張機能に依存しています。 | Azure Data Studio の拡張機能ギャラリーからインストールします。|
| Azure Data Studio の PostgreSQL 拡張機能 | いいえ | PostgreSQL の管理機能を提供する Azure Data Studio 用の PostgreSQL 拡張機能です。 | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Azure Data Studio の拡張機能ギャラリーからインストールします。|
| Azure CLI (az)<sup>1</sup> | はい | Azure のサービスを管理するための最新のコマンドライン インターフェイスです。 AKS デプロイと共に使用し、Azure Arc 対応データ サービスのインベントリと請求先データを Azure にアップロードします。 ([詳細情報](/cli/azure/))。 | [インストール](/cli/azure/install-azure-cli) |
| Kubernetes CLI (kubectl)<sup>2</sup> | はい | Kubernetes クラスターを管理するためのコマンドライン ツールです ([詳細情報](https://kubernetes.io/docs/tasks/tools/install-kubectl/))。 | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| curl <sup>3</sup> | 一部のサンプル スクリプトで必要です。 | URL でデータを転送するためのコマンドライン ツールです。 | [Windows](https://curl.haxx.se/windows/) \| Linux: curl パッケージのインストール |
| oc | Red Hat OpenShift と Azure Redhat OpenShift の展開に必要です。 |`oc` は、Open Shift コマンド ライン インターフェイス (CLI) です。 | [CLI のインストール](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> Azure CLI バージョン 2.0.4 以降を使う必要があります。 バージョンを確認する必要がある場合は、`az --version` を実行します。

<sup>2</sup> バージョン 1.13 以降を`kubectl`使う必要があります。 また、`kubectl` のバージョンは、Kubernetes クラスターより 1 つ上または下のマイナー バージョンにする必要があります。 `kubectl` クライアント上で特定のバージョンをインストールする場合は、[curl を使用した `kubectl` バイナリのインストール](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl)に関するページを参照してください (Windows 10 では、Windows PowerShell ではなく cmd.exe を使って、curl を実行してください)。

<sup>3</sup> PowerShell を使用している場合、curl は Invoke-WebRequest コマンドレットのエイリアスです。

## <a name="next-steps"></a>次のステップ

[Azure Arc データ コントローラーを作成する](create-data-controller.md)