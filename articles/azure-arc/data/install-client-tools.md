---
title: クライアント ツールのインストール
description: azdata、kubectl、Azure CLI、psql、Azure Data Studio (Insider)、および Azure Data Studio 向けの Arc 拡張機能をインストールする
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e328c65eb7453a8e80faa315c0b038e8fa6f13e1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750284"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Azure Arc 対応データ サービスをデプロイおよび管理するためのクライアント ツールをインストールする

この記事では、Arc 対応データ サービスを管理するためのツールをインストールするリソースについて紹介します。

> [!IMPORTANT]
> 新しいリリースに更新する場合は、最新バージョンの Azure Data Studio、Azure Data Studio の Azure Arc 拡張機能、Azure (`az`) コマンド ライン インターフェイス (CLI)、および [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] に更新します。
>
> [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)] 

Azure CLI の [`arcdata` 拡張機能 (`az`) ](reference/reference-az-arcdata-dc.md) は、Arc 対応のデータ サービスの `azdata` に代わるものです。

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Azure Arc 対応データ サービスを作成および管理するためのクライアント ツール

次の表では、Azure Arc 対応データ サービスを作成および管理するために必要な一般的なツールと、これらのツールをインストールする方法を示します。

| ツール | 必須 | 説明 | インストール |
|---|---|---|---|
| Azure CLI (`az`)<sup>1</sup> | はい | Azure のサービスを管理するための最新のコマンドライン インターフェイスです。 Azure サービス全般、特に CLI を使用した Arc 対応データ サービス、または間接的に接続されたモード (現在利用可能) と直接接続モード (近日利用可能) の両方のスクリプトで管理するために使用されます。 ([詳細情報](/cli/azure/))。 | [インストール](/cli/azure/install-azure-cli) |
| Azure (`az`) CLI 向け `arcdata` 拡張機能 | はい | Arc CLI 対応データ サービスを拡張機能として管理するためのコマンド ライン ツール (`az`) | [インストール](install-arcdata-extension.md) |
| Azure Data Studio | はい | Azure SQL、SQL Server、PostrgreSQL、MySQL などのさまざまなデータベースに接続してクエリを実行するためのリッチなエクスペリエンスを提供するツールです。 Azure Data Studio の拡張機能は、Azure Arc 対応データ サービスの管理エクスペリエンスを提供します。 | [インストール](/sql/azure-data-studio/download-azure-data-studio) |
| Azure Data Studio 用の Azure Arc 拡張機能 | はい | Azure Data Studio の拡張機能は、Azure Arc 対応データ サービスの管理エクスペリエンスを提供します。| Azure Data Studio の拡張機能ギャラリーからインストールします。|
| Azure Data Studio の PostgreSQL 拡張機能 | いいえ | PostgreSQL の管理機能を提供する Azure Data Studio 用の PostgreSQL 拡張機能です。 | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Azure Data Studio の拡張機能ギャラリーからインストールします。|
| Kubernetes CLI (kubectl)<sup>2</sup> | はい | Kubernetes クラスターを管理するためのコマンドライン ツールです ([詳細情報](https://kubernetes.io/docs/tasks/tools/install-kubectl/))。 | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| curl <sup>3</sup> | 一部のサンプル スクリプトで必要です。 | URL でデータを転送するためのコマンドライン ツールです。 | [Windows](https://curl.haxx.se/windows/) \| Linux: curl パッケージのインストール |
| oc | Red Hat OpenShift と Azure Redhat OpenShift の展開に必要です。 |`oc` は、Open Shift コマンド ライン インターフェイス (CLI) です。 | [CLI のインストール](https://docs.openshift.com/container-platform/4.6/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> Azure CLI バージョン 2.26.0 以降を使う必要があります。 バージョンを確認する必要がある場合は、`az --version` を実行します。

<sup>2</sup> バージョン 1.19 以降を`kubectl`使う必要があります。 また、`kubectl` のバージョンは、Kubernetes クラスターより 1 つ上または下のマイナー バージョンにする必要があります。 `kubectl` クライアント上で特定のバージョンをインストールする場合は、[curl を使用した `kubectl` バイナリのインストール](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl)に関するページを参照してください (Windows 10 では、Windows PowerShell ではなく cmd.exe を使って、curl を実行してください)。

<sup>3</sup> PowerShell を使用している場合、curl は Invoke-WebRequest コマンドレットのエイリアスです。

## <a name="next-steps"></a>次のステップ

[Azure Arc データ コントローラーを作成する](create-data-controller.md)
