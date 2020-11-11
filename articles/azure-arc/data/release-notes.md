---
title: Azure Arc 対応データ サービス - リリース ノート
description: 最新のリリース ノート
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 10/29/2020
ms.topic: conceptual
ms.openlocfilehash: 82dd2f16fa43b52ba4c6dfacd26da5da622523b2
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321706"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>リリースノート - Azure Arc 対応データ サービス (プレビュー)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="october-2020"></a>2020 年 10 月 

Azure データ CLI (`azdata`) バージョン番号:20.2.3。 [https://aka.ms/azdata](https://aka.ms/azdata) でダウンロードします。

### <a name="breaking-changes"></a>重大な変更

このリリースでは、次のような破壊的変更が導入されました。 

* PostgreSQL カスタム リソース定義 (CRD) で、用語 `shards` が `workers` に名称変更されています。 この用語 (`workers`) は、コマンド ライン パラメーター名と一致します。

* `azdata arc postgres server delete` では、postgres インスタンスを削除する前に確認を求めるメッセージが表示されます。  プロンプトをスキップするには `--force` を使用します。

### <a name="additional-changes"></a>追加の変更

* `--volume-claim mounts` という新しい省略可能パラメーターが `azdata arc postgres server create` に追加されました。 値は、ボリューム要求マウントのコンマ区切りの一覧です。 ボリューム要求マウントは、ボリュームの種類と PVC 名のペアです。 現在サポートされているボリュームの種類は `backup` のみです。  PostgreSQL では、ボリュームの種類が `backup` の場合、PVC は `/mnt/db-backups` にマウントされます。  これにより、PostgresSQL インスタンス間でバックアップを共有でき、1 つの PostgresSQL インスタンスのバックアップを別のインスタンスで復元できるようになります。

* PostgresSQL カスタム リソース定義の新しい短い名前: 

  * `pg11` 

  * `pg12`

* テレメトリのアップロードでは、次のいずれかがユーザーに提供されます。

   * Azure にアップロードされたポイントの数

     or 

   * Azure にデータが読み込まれていない場合は、再試行するためのプロンプト。

* `azdata arc dc debug copy-logs` では、`/var/opt/controller/log` フォルダーからの読み取りと、Linux での PostgreSQL エンジン ログの収集も行われるようになりました。

*   PostgreSQL Hyperscale を使用してバックアップを作成および復元するときに、作業インジケーターを表示します。

* `azdata arc postrgres backup list` にバックアップ サイズの情報が含まれるようになりました。

* SQL Managed Instance 管理者名プロパティが Azure portal の概要ブレードの右側の列に追加されました。

* Azure Data Studio では、PostgreSQL Hyperscale のワーカー ノードの数、仮想コア、メモリ設定の構成がサポートされます。 

* プレビューで、Postgres バージョン 11 と 12 のバックアップ/復元がサポートされます。

## <a name="september-2020"></a>2020 年 9 月

Azure Arc 対応データ サービスは、パブリック プレビュー用にリリースされています。 Arc 対応データ サービスを使用すると、どこからでもデータ サービスを管理できます。

- SQL Managed Instance
- PostgreSQL Hyperscale

手順については「[Azure Arc 対応データ サービスとは](overview.md)」を参照してください。

## <a name="known-limitations-and-issues"></a>既知の制限事項と問題

- インスタンス名は 13 文字より長くすることはできません
- Azure Arc データ コントローラーまたはデータベース インスタンスのインプレース アップグレードはありません。
- Arc 対応データ サービスのコンテナー イメージは署名されていません。  署名されていないコンテナー イメージをプルできるように Kubernetes ノードを構成することが必要な場合があります。  たとえば、Docker をコンテナー ランタイムとして使用している場合、DOCKER_CONTENT_TRUST=0 環境変数を設定して再起動することができます。  他のコンテナー ランタイムには、[OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration) などの同様のオプションがあります。
- Azure Arc 対応の SQL Managed インスタンスまたは PostgreSQL Hyperscale サーバー グループは、Azure portal からは作成できません。
- 現時点では、NFS を使用する場合は、Azure Arc データ コントローラーを作成する前に、デプロイ プロファイル ファイルで `allowRunAsRoot` を `true` に設定する必要があります。
- SQL および PostgreSQL ログイン認証のみ。  Azure Active Directory または Active Directory はサポートされていません。
- OpenShift にデータ コントローラーを作成するには、セキュリティ制約の緩和が必要です。  詳細についてはドキュメントを参照してください。
- Azure Arc データ コントローラーとデータベース インスタンスがある Azure Stack Hub 上で Azure Kubernetes Service エンジン (AKS エンジン) を使用している場合、より新しい Kubernetes バージョンへのアップグレードはサポートされていません。 Kubernetes クラスターをアップグレードする前に、Azure Arc データ コントローラーとすべてのデータベース インスタンスをアンインストールしてください。
- Azure Kubernetes Service (AKS) の[複数の可用性ゾーン](../../aks/availability-zones.md)にまたがるクラスターは現在、Azure Arc 対応データ サービスでサポートされていません。 この問題を回避するには、Azure portal で AKS クラスターを作成するときに、ゾーンを使用できるリージョンを選択した場合は、選択コントロールからすべてのゾーンをクリアします。 次の図を参照してください。

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="各ゾーンのチェックボックスをオフにして、[なし] を指定します。":::


### <a name="known-issues-for-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale の既知の問題   

- プレビューでは、PostgreSQL バージョン 11 エンジンのバックアップ/復元はサポートされていません。 PostgreSQL バージョン 12 のバックアップ/復元のみがサポートされています。
- `azdata arc dc debug copy-logs` では Windows 上で PostgreSQL エンジンのログは収集されません。
- 削除したばかりのサーバー グループの名前を使用してサーバー グループを再作成すると、失敗するかハングする可能性があります。 
   - **回避策** サーバー グループを再作成するときは同じ名前を再利用しないようにするか、前に削除したサーバー グループのロード バランサーまたは外部サービスを待機します。 削除したサーバー グループの名前が `postgres01` であり、それが名前空間 `arc` でホストされているとします。この場合、同じ名前のサーバー グループを再作成する前に、kubectl コマンド `kubectl get svc -n arc` の出力に `postgres01-external-svc` が表示されないようになるまで待機してください。
 - Azure Data Studio の [概要] ページと [コンピューティングとストレージ] 構成ページの読み込みに時間がかかります。 



## <a name="next-steps"></a>次のステップ
  
> **試してみたい場合**  
> Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) をすぐに開始できます。

[クライアント ツールをインストールする](install-client-tools.md)

[Azure Arc データ コントローラーを作成する](create-data-controller.md) (先にクライアント ツールをインストールする必要があります)

[Azure Arc で Azure SQL マネージド インスタンスを作成する](create-sql-managed-instance.md) (先に Azure Arc データ コントローラーを作成する必要があります)

[Azure Arc に Azure Database for PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md) (先に Azure Arc データ コントローラーを作成する必要があります)
