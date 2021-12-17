---
title: Datadobi DobiMigrate を使用してファイル データを Azure に移行する
titleSuffix: Azure Storage
description: Datadobi DobiMigrate を実装し、データを Azure Files、Azure NetApp Files、または ISV NAS ソリューションに移行するための概要ガイドを提供します
author: dukicn
ms.author: nikoduki
ms.date: 04/27/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 6984230044dc70e6e5e05c7ae0dcb4789065c436
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111955360"
---
# <a name="migrate-data-to-azure-with-datadobi-dobimigrate"></a>Datadobi DobiMigrate を使用した Azure へのデータの移行

この記事では、Datadobi DobiMigrate インフラストラクチャと Azure Storage を統合する方法について説明します。 これには前提条件、考慮事項、実装、および運用に関するガイダンスが含まれます。

DobiMigrate を使用すると、ストレージ プラットフォーム間でファイルとオブジェクトの移行を行うことができます。 オンプレミスから Azure にデータをすばやく、簡単かつコスト効率よく移行できます。

## <a name="reference-architecture"></a>参照アーキテクチャ

次の図は、オンプレミスから Azure および Azure 内のデプロイに関する参照アーキテクチャを示しています。

:::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-reference-architecture.png" alt-text="DobiMigrate の基本的なセットアップについて説明する参照アーキテクチャ":::

Azure 接続を追加して構成することで、既存の DobiMigrate デプロイを Azure と簡単に統合できます。

## <a name="before-you-begin"></a>開始する前に

少し前に計画を立てることで、Azure をオフサイト バックアップ ターゲットおよび復旧サイトとして使用できます。

### <a name="get-started-with-azure"></a>Azure を使用して作業を開始する

Microsoft では、Azure の使用を開始する際に従うフレームワークを提供しています。 [クラウド導入フレームワーク](/azure/architecture/cloud-adoption/) (CAF) は、エンタープライズ デジタル変革に向けた詳細なアプローチであり、運用レベルのクラウド導入を計画するための包括的なガイドです。 CAF には、迅速かつ安全に運用するのに役立つステップバイステップの [Azure セットアップガイド](/azure/cloud-adoption-framework/ready/azure-setup-guide/)が含まれています。 対話型バージョンは [Azure portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)で見つけることができます。 アプリケーションをデプロイするためのサンプル アーキテクチャ、具体的なベスト プラクティス、および Azure の専門知識を活用できる無料のトレーニング リソースが記載されています。

### <a name="considerations-for-migrations"></a>移行に関する考慮事項

Azure へのファイル データの移行を検討する際には、いくつかの点が重要になります。 続行する前に、以下の詳細を確認してください。

- [ストレージの移行の概要](../../../common/storage-migration-overview.md)
- DobiMigrate でサポートされている最新の機能 ([移行ツールの比較マトリックス](./migration-tools-comparison.md))

運用アプリケーションに影響を与えることなく、移行をサポートするには、十分なネットワーク容量が必要になることに注意してください。 このセクションでは、ネットワークのニーズを評価するために使用できるツールと手法について概説します。

#### <a name="determine-unutilized-internet-bandwidth"></a>未使用のインターネット帯域幅を確認する

日常的に利用可能な通常は未使用の帯域幅 (つまり *ヘッドルーム*) がどれだけあるかを把握しておくことが重要です。 以下に関する目標を達成できるかどうかを評価するのに役立ちます。

- オフライン方式の Azure Data Box を使用しない場合の初期の移行時間
- 目的のファイル サービスに最終的に切り替わるまでの増分再同期に要する時間

以下の方法を使用して、無料で利用できる Azure への帯域幅を特定します。

- 既存の Azure ExpressRoute を使用している場合は、Azure portal で[回線使用量](../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics)を確認します。
- 既存の日単位および月単位の使用状況を表示するには、ISP にレポートの取得をお問い合わせください。
- ルーターまたはスイッチ レベルでネットワーク トラフィックを監視することで、使用率を測定できるツールがいくつかあります。
  - [Solarwinds Bandwidth Analyzer パック](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco ネットワーク アシスタント](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

## <a name="implementation-guidance"></a>実装ガイダンス

このセクションでは、オンプレミスの Azure DobiMigrate デプロイに Azure Files 共有を追加する方法についての簡単なガイドを提供します。 

1. Azure portal を開き、 **[ストレージ アカウント]** を検索します。 

    :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-locate-storage-account.png" alt-text="Azure portal の検索ボックスに「ストレージ」と入力した状態を示します。":::

    既定の **[ストレージ アカウント]** アイコンをクリックすることもできます。

    :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-portal.png" alt-text="Azure portal でのストレージ アカウントの追加を示します。":::

2. **[作成]** を選択してアカウントを追加します。
   1. 既存のリソース グループを選択するか、**新規作成** します
   2. ストレージ アカウントに一意の名前を指定します
   3. リージョンを選択します
   4. ニーズに応じて、 **[Standard]** または **[Premium]** パフォーマンスを選択します。 **[Premium]** を選択した場合は、 **[Premium account type]\(Premium アカウントの種類\)** で **[ファイル共有]** を選択します。
   5. 必要なデータ保護要件を満たす **[冗長性](../../../common/storage-redundancy.md)** を選択します
   
   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-account-create-1.png" alt-text="ポータル内のストレージ アカウント設定を示します。":::

3. 次に、 **[詳細設定]** 画面の既定の設定を使用することをお勧めします。 Azure Files に移行する場合は、使用可能であれば **[大きいファイルの共有]** を有効にすることをお勧めします。

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-account-create-2.png" alt-text="ポータルの [詳細設定] タブを示します。":::

4. ここでは既定のネットワーク オプションを引き続き使用して、 **[データ保護]** に移ります。 [論理的な削除] を有効にすると、誤って削除されたデータを定義された保有期間内に回復することができます。 論理的な削除を使用すると、不注意または悪意による削除を防ぐことができます。

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-account-create-3.png" alt-text="ポータルでのデータ保護設定を示します。":::

5. タグ付けを使用してアカウントを **作成** する場合は、組織のタグを追加します。
 
6. これでアカウントを DobiMigrate 環境に追加する前に必要なのは、2 つの簡単な手順だけになりました。 Azure portal で作成したアカウントに移動し、ファイル サービス メニューで [ファイル共有] を選択します。 ファイル共有を追加し、わかりやすい名前を選びます。 その後、 [設定] の下にある [アクセス キー] 項目に移動し、 [ストレージ アカウント名] と 2 つのアクセス キーの 1 つをコピーします。

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-access-key.png" alt-text="ポータルでのアクセス キーの設定を示します。":::

7. Azure ファイル共有のプロパティに移動し、URL アドレスを取得します。これは、Azure 接続を DobiMigrate に追加する際に必要になります。

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-files-endpoint.png" alt-text="Azure Files エンドポイントを検索します。":::

8. (_省略可能_) デプロイに追加のセキュリティ層を追加することができます。
 
   1. ストレージ アカウントに変更を加えることができるユーザーを制限するようにロール ベース アクセスを構成します。 詳細については、「[管理操作のための組み込みロール](../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)」を参照してください。
 
   2.  [ストレージ ファイアウォール設定](../../../common/storage-network-security.md)を使用して、アカウントへのアクセスを特定のネットワーク セグメントに限定します。 ファイアウォール設定を構成して、企業ネットワーク外からアクセスが試行されないようにします。

       :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-storage-firewall.png" alt-text="ポータル内のストレージ ファイアウォールの設定を示します。":::

   3.  アカウントに[削除ロック](../../../../azure-resource-manager/management/lock-resources.md)を設定し、ストレージ アカウントが誤って削除されないようにします。

       :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-resource-lock.png" alt-text="ポータルで削除ロックを設定する方法を示します。":::

   4.  追加の[セキュリティに関するベスト プラクティス](../../../blobs/security-recommendations.md)を構成します。

9.  DobiMigrate で、[構成] > [ファイル サーバー] に移動します。 ファイル サーバーの種類として Microsoft Azure Files を追加するには、 **[追加]** をクリックします。

    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-server-type.png" alt-text="サーバーの種類として Microsoft Azure Files を追加します。":::

10. 名前、Azure Files 接続の詳細、ストレージ アカウントの資格情報を指定します。
 
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-connection-details.png" alt-text="Azure Files 接続の詳細を構成します。":::

11. プロキシが Azure Files と通信できることを確認するには、プロキシを Azure Files 接続に割り当て、 **[接続テスト]** をクリックします。
 
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-test-connection.png" alt-text="接続テストの詳細。":::

    接続テストの結果が表示されます。

    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-test-results.png" alt-text="接続テストの結果が表示されます。":::

12. **[SMB Migration Shares]\(SMB 移行共有\)** には、このストレージ アカウントでプロビジョニングされているすべての Azure ファイル共有が表示されます。 移行スコープに含まれる共有については、次の例のように、 **[マッピング]** を **[手動]** に設定します。
 
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobiprotect-azure-files-shares.png" alt-text="使用可能な共有を表示します。":::

13. **[完了]** をクリックして、Azure Files の構成を完了します。 その後、新しい移行タスクを開始できます。

### <a name="start-a-new-migration"></a>新しい移行を開始する

DobiMigrate を使用して新しい移行を設定するには、移行パスを手動で追加するか、一括インポートを使用します。 一括インポートの場合、共通の移行オプションを使用して複数の移行が追加されます。

新しい移行を開始するには:

1. ダッシュボード上の **[新しい移行]** ボタンをクリックします。
   
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-new-migration.png" alt-text="新しい移行ジョブを開始。":::

1. 移行するソースとパスを選択します。

    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-select-source.png" alt-text="移行するソースとパスを選択。":::

1. **移行先** を選択します。
2. プロトコルを確認し、移行オプションを確認します。
3. **[完了]** を クリックして、移行プロセスを完了します。

## <a name="support"></a>サポート 

Azure ソリューションへの移行についてサポートが必要な場合は、Datadobi と Azure の両方でケースを開く必要があります。

### <a name="to-open-a-case-with-datadobi"></a>Datadobi でケースを開くには

[Datadobi サポート サイト](https://support.datadobi.com/s/)で、サインインして、ケースを開きます。

### <a name="to-open-a-case-with-azure"></a>Azure でケースを開くには

[Azure portal](https://portal.azure.com/) の上部にある検索バーで「**サポート**」を検索します。 **[ヘルプとサポート]**  ->  **[新しいサポート要求]** の順に選択します。

## <a name="marketplace"></a>マーケットプレース

Datadobi を使用することで、Azure にソリューションをデプロイし、Azure Virtual Machines やその他の多くの Azure サービスを保護することが簡単になりました。 詳細については、次のリファレンスを参照してください。

- [DobiMigrate を使用してファイル データを Azure に移行する](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=overview)

## <a name="next-steps"></a>次のステップ

詳細については、次のガイドを参照してください。

- [Storage の移行の概要](../../../common/storage-migration-overview.md)
- [DobiMigrate ユーザー マニュアル](https://downloads.datadobi.com/NAS/olh/latest/dobimigrate.html)
- [DobiMigrate の前提条件ガイド](https://downloads.datadobi.com/NAS/guides/latest/prerequisites.html)
- [DobiMigrate のインストール ガイド](https://downloads.datadobi.com/NAS/guides/latest/installguide.html)