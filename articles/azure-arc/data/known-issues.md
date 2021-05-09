---
title: Azure Arc 対応データ サービス - 既知の問題
description: 最新の既知の問題
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: ee652047a33d73ece2d7648905fa590d90b1fb2f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029507"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>既知の問題 - Azure Arc 対応データ サービス (プレビュー)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>2021 年 3 月

### <a name="data-controller"></a>データ コントローラー

- Azure portal を使用して、直接接続モードでデータ コントローラーを作成できます。 その他の Azure Arc 対応データ サービス ツールを使用したデプロイはサポートされていません。 具体的には、このリリースでは、次のツールのいずれかを使用して直接接続モードでデータ コントローラーをデプロイすることはできません。
   - Azure Data Studio
   - Azure Data CLI (`azdata`)
   - Kubernetes ネイティブ ツール

   「[Azure Arc データ コントローラーをデプロイする | 直接接続モード](deploy-data-controller-direct-mode.md)」では、ポータルでデータ コントローラーを作成する方法について説明しています。 

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale

- 直接接続モードが有効になっている Arc データ コントローラーに Azure Arc 対応 Postgres Hyperscale サーバー グループをデプロイすることはサポートされていません。
- サーバー グループの構成を編集して追加の拡張機能を有効にするときに `--extensions` パラメーターに無効な値を渡すと、有効な拡張機能の一覧がサーバー グループの作成時の設定に誤ってリセットされ、ユーザーは追加の拡張機能を作成できなくなります。 そのような場合に使用できる唯一の回避策は、サーバー グループを削除して再デプロイすることです。

## <a name="february-2021"></a>2021 年 2 月

### <a name="data-controller"></a>データ コントローラー

- 直接接続クラスター モードは無効になっています

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale

- 現時点では、ポイントインタイム リストアは NFS ストレージではサポートされていません。
- 同時に、pg_cron 拡張機能を有効にしたり、構成することはできません。 これには、2 つのコマンドを使用する必要があります。 1 つは有効化のコマンドで、もう 1 つは構成するコマンドです。 

   次に例を示します。
   ```console
   § azdata arc postgres server edit -n myservergroup --extensions pg_cron 
   § azdata arc postgres server edit -n myservergroup --engine-settings cron.database_name='postgres'
   ```

   最初のコマンドでは、サーバー グループを再起動する必要があります。 そのため、2 つ目のコマンドを実行する前に、サーバー グループの状態が更新中から準備完了に移行していることを確認します。 再起動が完了する前に 2 つ目のコマンドを実行すると、失敗します。 そのような場合は、しばらく待ってから、2 つ目のコマンドをもう一度実行してください。

## <a name="introduced-prior-to-february-2021"></a>2021 年 2 月より前に確認されたもの

### <a name="data-controller"></a>データ コントローラー

- Azure Kubernetes Service (AKS) では、Kubernetes バージョン 1.19.x はサポートされていません。
- Kubernetes 1.19 では、`containerd` はサポートされていません。
- Azure 内のデータ コントローラー リソースは現在、Azure リソースです。 削除などの更新は、kubernetes クラスターには反映されません。
- インスタンス名は 13 文字より長くすることはできません
- Azure Arc データ コントローラーまたはデータベース インスタンスのインプレース アップグレードはありません。
- Arc 対応データ サービスのコンテナー イメージは署名されていません。  署名されていないコンテナー イメージをプルできるように Kubernetes ノードを構成することが必要な場合があります。  たとえば、Docker をコンテナー ランタイムとして使用している場合、DOCKER_CONTENT_TRUST=0 環境変数を設定して再起動することができます。  他のコンテナー ランタイムには、[OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration) などの同様のオプションがあります。
- Azure Arc 対応の SQL Managed インスタンスまたは PostgreSQL Hyperscale サーバー グループは、Azure portal からは作成できません。
- 現時点では、NFS を使用する場合は、Azure Arc データ コントローラーを作成する前に、デプロイ プロファイル ファイルで `allowRunAsRoot` を `true` に設定する必要があります。
- SQL および PostgreSQL ログイン認証のみ。  Azure Active Directory または Active Directory はサポートされていません。
- OpenShift にデータ コントローラーを作成するには、セキュリティ制約の緩和が必要です。  詳細についてはドキュメントを参照してください。
- Azure Arc データ コントローラーとデータベース インスタンスがある Azure Stack Hub 上で Azure Kubernetes Service (AKS) エンジンを使用している場合、より新しい Kubernetes バージョンへのアップグレードはサポートされていません。 Kubernetes クラスターをアップグレードする前に、Azure Arc データ コントローラーとすべてのデータベース インスタンスをアンインストールしてください。
- [複数の可用性ゾーン](../../aks/availability-zones.md)にまたがる AKS クラスターは現在、Azure Arc 対応データ サービスではサポートされていません。 この問題を回避するには、Azure portal で AKS クラスターを作成するときに、ゾーンを使用できるリージョンを選択した場合は、選択コントロールからすべてのゾーンをクリアします。 次の図を参照してください。

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="各ゾーンのチェックボックスをオフにして、[なし] を指定します。":::


## <a name="next-steps"></a>次のステップ

> **試してみたい場合**  
> AKS、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。

- [クライアント ツールをインストールする](install-client-tools.md)
- [Azure Arc データ コントローラーを作成する](create-data-controller.md) (先にクライアント ツールをインストールする必要があります)
- [Azure Arc で Azure SQL マネージド インスタンスを作成する](create-sql-managed-instance.md) (先に Azure Arc データ コントローラーを作成する必要があります)
- [Azure Arc に Azure Database for PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md) (先に Azure Arc データ コントローラーを作成する必要があります)
- [Azure サービスのリソース プロバイダー](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [リリースノート - Azure Arc 対応データ サービス (プレビュー)](release-notes.md)
