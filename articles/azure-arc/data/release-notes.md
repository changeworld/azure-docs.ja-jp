---
title: Azure Arc 対応データ サービス - リリース ノート
description: 最新のリリース ノート
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3c20bbd3ab02cd1eccd00e2d36c14eebf2f63205
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360313"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>リリースノート - Azure Arc 対応データ サービス (プレビュー)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>2020 年 9 月

Azure Arc 対応データ サービスは、パブリック プレビュー用にリリースされています。 Arc 対応データ サービスを使用すると、どこからでもデータ サービスを管理できます。

- SQL Managed Instance
- PostgreSQL Hyperscale

手順については「[Azure Arc 対応データ サービスとは](overview.md)」を参照してください。

### <a name="known-issues"></a>既知の問題

今回のリリースには次の問題が適用されます。

* **PostgreSQL Hyperscale サーバー グループを削除する** : サーバー グループまたはインスタンスの構成を変更した場合は、編集操作が完了した後に PostgreSQL Hyperscale サーバー グループを削除します。

* **`azdata notebook run` が失敗する可能性がある** : この問題を回避するには、Python 仮想環境で `azdata notebook run` を実行します。 また、この問題は、Azure Data Studio 配置ウィザードを使用して SQL Managed Instance または PostgreSQL Hyperscale サーバー グループを作成できなかった場合にも発生します。 この場合は、ノートブックを開いて、そのノートブックの上部にある **[すべて実行]** ボタンをクリックします。

## <a name="next-steps"></a>次のステップ

> **試してみたい場合**  
> Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) をすぐに開始できます。

[クライアント ツールをインストールする](install-client-tools.md)

[Azure Arc データ コントローラーを作成する](create-data-controller.md) (先にクライアント ツールをインストールする必要があります)

[Azure Arc で Azure SQL マネージド インスタンスを作成する](create-sql-managed-instance.md) (先に Azure Arc データ コントローラーを作成する必要があります)

[Azure Arc に Azure Database for PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md) (先に Azure Arc データ コントローラーを作成しておく必要があります)

## <a name="known-limitations-and-issues"></a>既知の制限事項と問題

- SQL マネージド インスタンス名は 13 文字より長くすることはできません
- Azure Arc データ コントローラーまたはデータベース インスタンスのインプレース アップグレードはありません。
- Arc 対応データ サービスのコンテナー イメージは署名されていません。  署名されていないコンテナー イメージをプルできるように Kubernetes ノードを構成することが必要な場合があります。  たとえば、Docker をコンテナー ランタイムとして使用している場合、DOCKER_CONTENT_TRUST=0 環境変数を設定して再起動することができます。  他のコンテナー ランタイムには、[OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration) などの同様のオプションがあります。
- Azure Arc 対応の SQL Managed インスタンスまたは PostgreSQL Hyperscale サーバー グループは、Azure portal からは作成できません。
- 現時点では、NFS を使用する場合は、Azure Arc データ コントローラーを作成する前に、デプロイ プロファイル ファイルで allowRunAsRoot を true に設定する必要があります。
- SQL および PostgreSQL ログイン認証のみ。  Azure Active Directory または Active Directory はサポートされていません。
- OpenShift にデータ コントローラーを作成するには、セキュリティ制約の緩和が必要です。  詳細についてはドキュメントを参照してください。
- Postgres Hyperscale ワーカー ノード数のスケール _ダウン_ はサポートされていません。
- Azure Arc データ コントローラーとデータベース インスタンスがある Azure Stack Hub 上で Azure Kubernetes Service エンジン (AKS エンジン) を使用している場合、より新しい Kubernetes バージョンへのアップグレードはサポートされていません。 Kubernetes クラスターをアップグレードする前に、Azure Arc データ コントローラーとすべてのデータベース インスタンスをアンインストールしてください。
- プレビューでは、Postgres バージョン 11 エンジンのバックアップ/復元はサポートされていません。 Postgres バージョン 12 のバックアップ/復元のみがサポートされています。
- Azure Kubernetes Service (AKS) の[複数の可用性ゾーン](../../aks/availability-zones.md)にまたがるクラスターは現在、Azure Arc 対応データ サービスでサポートされていません。 この問題を回避するには、Azure portal で AKS クラスターを作成するときに、ゾーンを使用できるリージョンを選択した場合は、選択コントロールからすべてのゾーンをクリアします。 次の図を参照してください。

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="各ゾーンのチェックボックスをオフにして、[なし] を指定します。":::

  
