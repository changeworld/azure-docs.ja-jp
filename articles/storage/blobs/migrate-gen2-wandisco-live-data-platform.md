---
title: Data Lake Storage と WANdisco LiveData Platform for Azure
description: データ操作を中断したりダウンタイムを必要としたりすることなく、ペタバイト単位のオンプレミスの Hadoop データを Azure Data Lake Storage Gen2 ファイル システムに移行する方法について説明します。
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 10/26/2021
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: b0b78d1233d6e605691e06c69c9d2167c7ecb592
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019222"
---
# <a name="migrate-on-premises-hadoop-data-to-azure-data-lake-storage-gen2-with-wandisco-livedata-platform-for-azure"></a>WANdisco LiveData Platform for Azure を使用してオンプレミスの Hadoop データを Azure Data Lake Storage Gen2 に移行する

[WANdisco LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) を使用すると、データ操作を中断したりダウンタイムを必要とすることなく、ペタバイト単位のオンプレミスの Hadoop データを Azure Data Lake Storage Gen2 ファイル システムに移行することができます。 プラットフォームの継続的チェックにより、変更が行われている場合でも、転送の両端でデータの整合性を維持しながら、データの損失が防止されます。

プラットフォームは 2 つのサービスで構成されています。 アクティブに使用されているデータをオンプレミス環境から Azure Storage に移行する [LiveData Migrateor for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) と、変更または取り込まれたすべてのデータを確実に一貫してレプリケートする [LiveData Plane for Azure](https://www.wandisco.com/products/livedata-plane-for-azure) です。

> [!div class="mx-imgBorder"]
> ![LiveData Platform の概要を示す図](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

両方のサービスとも Azure portal と Azure CLI を使用して管理します。 どちらのサービスも、他のすべての Azure サービスと同じ従量課金制の課金モデルに従います。LiveData Platform for Azure でのデータ使用量は、使用状況メトリックを提供する毎月の Azure 請求書に表示されます。

[静的な情報を Azure Data Box にコピーする](./data-lake-storage-migrate-on-premises-hdfs-cluster.md)か、[DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html) などの Hadoop ツールを使用することによって "*オフライン*" でデータを移行する場合とは異なり、WANdisco LiveData for Azure を使用すると、"*オンライン*" 移行中にビジネス システムの完全な操作を維持することができます。 データを Azure に移行している間も、ビッグ データ環境の稼働を維持します。

## <a name="key-benefits-of-wandisco-livedata-platform-for-azure"></a>WANdisco LiveData Platform for Azure の主な利点

[WaNdisco LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) のワイド エリア ネットワーク対応コンセンサス エンジンにより、データの整合性が維持され、リアルタイム データ レプリケーションが大規模に実行されます。 詳細については、次のビデオをご覧ください。<br><br>

>[!VIDEO https://www.youtube.com/embed/KRrmcYPxEho]

プラットフォームの主な利点は次のとおりです。

- **データの** 精度: データのエンドツーエンドの検証により、データの損失を防ぎ、転送されたデータが用途に適したものとなるように保証します。

- **データの整合性**: 継続的な変更が加えられている場合でも、環境間でデータ ボリュームの整合性を自動的に維持します。

- **データ効率**: 帯域幅の消費をフル コントロールして、大規模なデータ ボリュームを継続的に転送します。

- **ダウンタイムの排除**: 移行中は他のアプリケーションでデータを自由に作成、変更、読み取り、および削除できます。Azure へのデータ転送中にビジネス業務を中断しなくても済みます。 アプリケーション、分析インフラストラクチャ、取り込みジョブ、その他の処理の操作を継続します。

- **シンプルな使用**: プラットフォームの Azure 統合を使用して、自動移行の作成、構成、スケジュール設定、および進行状況の追跡を行います。 さらに、必要に応じて、選択的データ レプリケーション、Hive メタデータ、データ セキュリティ、および機密性を構成できます。

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>WANdisco LiveData Platform for Azure の主な特徴

プラットフォームの主な特徴は次のとおりです。

- **メタデータの移行**: HDFS データに加えて、LiveData Migrator for Azure を使用してメタデータ (Hive や他のストレージから) を移行します。

- **スケジュールされた転送**: LiveData Migrator for Azure を使用して、データ転送を開始するタイミングを制御および自動化します。これにより、データへの変更を手動で移行する必要がなくなります。

- **Kerberos:** LiveData Migrator for Azure では、Kerberized クラスターがサポートされています。

- **除外テンプレート**: 特定のファイル サイズまたはファイル名 (glob パターンを使用して定義) がご利用のターゲット ストレージに移行されるのを防ぐために、LiveData Migrator for Azure でルールを作成します。 除外テンプレートは、Azure portal CLI を使用して作成し、任意の数の移行に適用します。

- **パス マッピング**: 特定のターゲット ファイル システムの代替ターゲット パスを定義します。これにより、転送されたデータはお客様が指定したディレクトリに自動的に移動されます。

- **帯域幅の管理**: LiveData Migrator for Azure で使用できるネットワーク帯域幅の最大量を構成して、帯域幅の過剰消費を防ぎます。

- **除外:** 条件を満たすファイルおよびディレクトリを移行させないようにするテンプレート クエリを定義して、ご利用のソース システムからデータを選択的に移行できるようにします。

- **メトリック**: LiveData Migrator for Azure でのデータ転送に関する詳細 (転送に時間がかかったファイル、除外されたパス、転送に失敗した項目など) を表示します。


   > [!div class="mx-imgBorder"]
   > ![LiveData](./media/migrate-gen2-wandisco-live-data-platform/metrics-1.png)

## <a name="migrate-big-data-faster-without-risk"></a>リスクなしでビッグ データをより迅速に移行する

WANdisco LiveData Platform for Azure に含まれる 1 つ目のサービスは、オンプレミス環境から Azure Storage にデータを移行するための [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) です。 LiveData Migrator をご利用のオンプレミスの Hadoop クラスターにデプロイすると、ファイル システムに最適な構成が自動的に作成されます。 そこから、システムに Kerberos の詳細を提供します。 これで、LiveData Migrator for Azure ではデータを Azure Storage に移行する準備が整います。

> [!div class="mx-imgBorder"]
> ![LiveData Migrator for Azure のアーキテクチャ](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture-1.png)

LiveData Migrator for Azure を開始する前に、次の[前提条件](https://docs.wandisco.com/live-data-platform/docs/prereq/)を確認してください。

移行を実行するには:

1. Azure CLI の場合: 

   - `az provider register --namespace Wandisco.Fusion --consent-to-permissions` を実行して、Azure CLI で WANdisco リソース プロバイダーに登録します。
   - `az vm image terms accept --offer ldma --plan metered-v1 --publisher Wandisco --subscription <subscriptionID>` を実行して、LiveData Platform の従量課金条件に同意します。

2. LiveData Migrator インスタンスを Azure portal からご利用のオンプレミスの Hadoop クラスターにデプロイします。 (クラスターに変更を加える必要も、クラスターを再起動する必要もありません)。

   > [!div class="mx-imgBorder"]
   > ![LiveData Migrator インスタンスを作成する](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

   > [!NOTE]
   > WaNdisco LiveData Migrator for Azure には、Hadoop テスト クラスターを作成するオプションが用意されています。

3. 必要に応じて、Kerberos の詳細を構成します。

4. ターゲットの Azure Data Lake Storage Gen2 対応のストレージ アカウントを定義します。

   > [!div class="mx-imgBorder"]
   > ![LiveData Migrator ターゲットを作成する](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

5. 移行するデータの場所を定義します (例: `/user/hive/warehouse`)。

   > [!div class="mx-imgBorder"]
   > ![LiveData Migrator の移行を作成する](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

6. 移行を開始する。

Azure CLI や Azure portal などの標準の Azure ツールを使用して、移行の進行状況を監視します。

詳細な手順については、[LiveData Migrator for Azure の使用方法に関するビデオ シリーズ](https://fast.wistia.com/embed/channel/qg51p8erky)をご覧ください。

## <a name="bidirectionally-replicate-data-under-active-change-with-livedata-plane-for-azure"></a>LiveData Plane for Azure を使用してアクティブな変更中のデータを双方向にレプリケートする

LiveData プラットフォームに含まれる 2 つ目のサービスは、[LiveData Plane for Azure](https://www.wandisco.com/products/livedata-plane-for-azure) です。 LiveData Plane では WANdisco の調整エンジンを使用して、すべてのシステム上のデータに変更をインテリジェントに適用することで多くのオンプレミス Hadoop クラスターと Azure Storage 間でデータの整合性を維持します。これにより、さまざまな使用ポイントでデータが競合するリスクが取り除かれます。

> [!div class="mx-imgBorder"]
> ![LiveData Migrator for Azure のアーキテクチャ](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

最初の移行が行われたら、LiveData Plane for Azure とのデータの整合性を維持します。

1. Azure portal から開始して、LiveData Plane for Azure をオンプレミスおよび Azure にデプロイします。 アプリケーションの変更は必要ありません。

2. 整合性を維持したいデータの場所を網羅したレプリケーション規則を構成します (例: `/user/contoso/sales/region/WA`)。

3. 必要に応じて、いずれかの場所のデータにアクセスして変更を加えるアプリケーションを実行します。

LiveData Plane for Azure では、クラスターの操作またはアプリケーションのパフォーマンスに大きな影響を与えずに、すべての環境にわたってデータの変更を一貫してレプリケートします。

## <a name="test-drive-or-trial"></a>体験版または試用版

[LiveData Platform for Azure の Marketplace ページ](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma?tab=Overview)から、次の 2 つのオプションを使用できます。

- **[今すぐ取得]** ボタンをクリックすると、ご利用のサブスクリプションでサービスが起動します。 そこから、独自の Hadoop クラスターまたは WANdisco の試用版クラスターを使用できます。

- **[体験版]** をクリックして、お客様用に事前に構成され、ホストされている環境で LiveData Migrator for Azure をテストします。 これにより、LiveData Migrator for Azure を自分のサブスクリプションに追加する前に、使用するデータにコストまたはリスクをかけることなく試すことができます。

[体験版のデモンストレーション ビデオ](https://fast.wistia.net/embed/channel/qg51p8erky?wchannelid=qg51p8erky&wmediaid=ute6gsc60w)をご覧になって、体験版の実際の動作を確認してください。

## <a name="next-steps"></a>次の手順

- [LiveData Migrator for Azure で移行を計画して作成する](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma)。

## <a name="see-also"></a>関連項目

- [Azure Marketplace の LiveData Migrator for Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma?tab=Overview)

- [LiveData Migrator for Azure のプランと価格](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma?tab=PlansAndPricee)

- [LiveData Platform for Azure についてよく寄せられる質問](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [LiveData Platform for Azure に関する既知の問題](https://docs.wandisco.com/live-data-platform/docs/known-issues/)
