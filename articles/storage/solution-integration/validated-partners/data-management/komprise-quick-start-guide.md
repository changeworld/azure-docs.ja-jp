---
title: Komprise Intelligent Data Manager を使用してファイル データを分析し、Azure に移行する
titleSuffix: Azure Storage
description: Komprise Intelligent Data Manager を導入するためのファースト ステップ ガイド。 ファイル インフラストラクチャを分析して、Azure Files、Azure NetApp Files、Azure Blob Storage のほか、利用可能な ISV の NAS ソリューションにデータを移行する方法について説明します
author: dukicn
ms.author: nikoduki
ms.date: 05/20/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: a6333ef4385439afa2e2f0000ae3f452357aa958
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856822"
---
# <a name="analyze-and-migrate-to-azure-with-komprise"></a>Komprise を使用してデータを分析し、Azure に移行する

この記事は、Komprise Intelligent Data Management インフラストラクチャを Azure のストレージ サービスに統合する際に役立ちます。 データを分析して移行する方法についての考慮事項や導入に関するガイダンスを取り上げています。

Komprise では、ネットワークに接続されたストレージ システム (NAS) に格納されているファイルおよびオブジェクト データと、オンプレミスとクラウドの両方のオブジェクト ストアに対する分析と分析情報が提供されます。  Azure Files、Azure NetApp Files、Azure Blob Storage をはじめとする Azure のストレージ サービスに加え、その他の ISV の NAS ソリューションへのデータの移行が可能になります。 [プライマリ ストレージとセカンダリ ストレージの検証済みパートナー ソリューション](../primary-secondary-storage/partner-overview.md)についての詳しい情報をご覧ください。

Komprise の一般的なユースケースは次のとおりです。

- 構造化されていないファイルおよびオブジェクト データを分析して、データの管理、移動、配置、アーカイブ、保護、制限に関する分析情報を得る
- ファイル データを Azure Files、Azure NetApp Files、ISV の NAS ソリューションに移行する
- 元の NAS ソリューションからの透過的なアクセスを維持し、Azure でのネイティブなオブジェクト アクセスを可能にすると同時に、ポリシーに基づいてファイル データを Azure Blob Storage で階層化してアーカイブする
- Azure でのネイティブなオブジェクト アクセスを維持しながら、構成可能なスケジュールに基づいてファイル データを Azure Blob Storage にコピーする
- オブジェクト データを Azure Blob Storage に移行する
- 最終アクセス時刻に基づいて、Azure Blob Storage のホット、クール、アーカイブ層にオブジェクトを階層化してデータ ライフサイクル管理を行う

## <a name="reference-architecture"></a>参照アーキテクチャ

次の図は、オンプレミスから Azure および Azure 内のデプロイに関する参照アーキテクチャを示しています。

:::image type="content" source="./media/komprise-quick-start-guide/komprise-architecture.png" alt-text="Komprise Intelligent Data Manager の基本的な構成を表すリファレンス アーキテクチャ":::

次の図は、クラウドおよびオンプレミスのオブジェクト ワークロードを Azure Blob Storage に移行するための参照アーキテクチャを示しています。

:::image type="content" source="./media/komprise-quick-start-guide/komprise-architecture-blob.png" alt-text="クラウドおよびオンプレミスのオブジェクト ワークロードを Azure Blob Storage に移行するためのセットアップを示す参照アーキテクチャ":::

Komprise は、仮想環境に簡単にデプロイできるソフトウェア ソリューションです。 このソリューションの構成要素は次のとおりです。
- **Director** - Komprise Grid の管理コンソール。 環境の構成、アクティビティの監視、レポートとグラフの表示、ポリシーの設定に使用されます。
- **Observer** - 共有の管理と分析、レポートの要約、Director との通信、オブジェクトおよび NFS データ トラフィックの処理を行います。
- **プロキシ** - SMB または CIFS のデータ フローを単純化してスピードアップすると共に、容易にスケーリングして、拡大する環境のパフォーマンス要件を満たします。

## <a name="before-you-begin"></a>開始する前に

事前に計画を立てることが、リスクの少ないデータ移行につながります。

### <a name="get-started-with-azure"></a>Azure を使用して作業を開始する

Microsoft では、Azure の使用を開始する際に従うフレームワークを提供しています。 [クラウド導入フレームワーク](/azure/architecture/cloud-adoption/) (CAF) は、エンタープライズ デジタル変革に向けた詳細なアプローチであり、運用レベルのクラウド導入を計画するための包括的なガイドです。 CAF には、迅速かつ安全に運用するのに役立つステップバイステップの [Azure セットアップガイド](/azure/cloud-adoption-framework/ready/azure-setup-guide/)が含まれています。 対話型バージョンは [Azure portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)で見つけることができます。 アプリケーションをデプロイするためのサンプル アーキテクチャ、具体的なベスト プラクティス、および Azure の専門知識を活用できる無料のトレーニング リソースが記載されています。

### <a name="considerations-for-migrations"></a>移行に関する考慮事項

Azure へのファイル データの移行を検討する際には、いくつかの点が重要になります。 続行する前に、以下の詳細を確認してください。

- [Storage の移行の概要](../../../common/storage-migration-overview.md)
- Komprise Intelligent Data Management でサポートされる最新の機能 ([移行ツールの比較表](./migration-tools-comparison.md)を参照)。

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

## <a name="migration-planning-guide"></a>移行計画ガイド

Komprise は簡単にセットアップでき、次の 3 つのステップで複数の移行を同時に実行することができます。

1.  データを分析して、移行またはアーカイブの対象となるファイルおよびオブジェクトを特定します。
1.  非構造化データを Azure Storage に移行、移動、コピーするためのポリシーを定義します。
1.  データを自動的に移動するポリシーをアクティブにします。

移行の対象となる適切なデータを見つけ、優先順位を付けるうえで、最初のステップがきわめて重要です。 Komprise の分析からは次の情報が得られます。

- 次のことを明らかにするための、アクセス時刻に関する情報。
  - オンプレミスにキャッシュしたり高速ファイル サービスに保存したりすることができるアクセス頻度の低いファイル
  - Blob Storage にアーカイブできるコールド データ
- 最も大きな影響を受ける組織内のグループと移行の順序を突き止めるための上位のユーザー、グループ、共有に関する情報 (ビジネスへの影響の評価に使用)
- ファイル数、またはファイルの種類ごとの容量。これによって保存対象のファイルの種類が決まるほか、コンテンツをクリーンアップする余地があるかどうかが判断されます。 クリーンアップによって移行作業の負担が軽減され、移行先ストレージのコストも小さくなります。 オブジェクト データに対しても同様の分析を使用できます。
- ファイル数、またはファイル サイズごとの容量。これによって移行の所要時間が決まります。 サイズの小さなファイルが多数存在すると、大きなファイルが少数ある場合よりも、移行にかかる時間が長くなります。 オブジェクト データに対しても同様の分析を使用できます。
- コールド データがコストの高い層に不適切に配置されているかどうか、またはホット データがアクセス コストが高くてコストの安い層に不適切に配置されているかどうかを判断するための、ストレージ層別のオブジェクトのコスト。 アクセス パターンに基づいてデータを適切に配置すると、クラウド ストレージ全体のコストを最適化できます。

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-analyze-1.png" alt-text="ファイルの種類とアクセス時刻に基づく分析":::

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-analyze-shares.png" alt-text="共有の分析の例":::

- 特定のニーズに合った一連のファイルおよびオブジェクトを正確に抽出するためのカスタム クエリ機能フィルター

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-analyze-custom.png" alt-text="カスタム クエリの分析":::

## <a name="deployment-guide"></a>デプロイ ガイド

Komprise をデプロイする前に、移行先のサービスをデプロイする必要があります。 詳細については、次の記事を参照してください。

- [Azure ファイル共有](../../../files/storage-how-to-create-file-share.md)を作成する方法
- Azure NetApp Files で [SMB ボリューム](../../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)または [NFS エクスポート](../../../../azure-netapp-files/azure-netapp-files-create-volumes.md)を作成する方法

Komprise Grid は、速度、スケーラビリティ、回復性を確保するために仮想環境 (Hyper-V、VMware、KVM) にデプロイされます。 または、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management) を使用して、ご自分の Azure サブスクリプションに環境をセットアップすることもできます。

1. Azure portal を開き、 **[ストレージ アカウント]** を検索します。 

    :::image type="content" source="./media/komprise-quick-start-guide/azure-locate-storage-account.png" alt-text="Azure portal の検索ボックスに「ストレージ」と入力した状態を示します。":::

    既定の **[ストレージ アカウント]** アイコンをクリックすることもできます。

    :::image type="content" source="./media/komprise-quick-start-guide/azure-portal.png" alt-text="Azure portal でのストレージ アカウントの追加を示します。":::

2. **[作成]** を選択してアカウントを追加します。
   1. 既存のリソース グループを選択するか、**新規作成** します
   2. ストレージ アカウントに一意の名前を指定します
   3. リージョンを選択します
   4. ニーズに応じて、 **[Standard]** または **[Premium]** パフォーマンスを選択します。 **[Premium]** を選択した場合は、 **[Premium account type]\(Premium アカウントの種類\)** で **[ファイル共有]** を選択します。
   5. 必要なデータ保護要件を満たす **[冗長性](../../../common/storage-redundancy.md)** を選択します
   
   :::image type="content" source="./media/komprise-quick-start-guide/azure-account-create-1.png" alt-text="ポータル内のストレージ アカウント設定を示します。":::

3. 次に、 **[詳細設定]** 画面の既定の設定を使用することをお勧めします。 Azure Files に移行する場合は、使用可能であれば **[大きいファイルの共有]** を有効にすることをお勧めします。

   :::image type="content" source="./media/komprise-quick-start-guide/azure-account-create-2.png" alt-text="ポータルの [詳細設定] タブを示します。":::

4. ここでは既定のネットワーク オプションを引き続き使用して、 **[データ保護]** に移ります。 [論理的な削除] を有効にすると、誤って削除されたデータを定義された保有期間内に回復することができます。 論理的な削除を使用すると、不注意または悪意による削除を防ぐことができます。

   :::image type="content" source="./media/komprise-quick-start-guide/azure-account-create-3.png" alt-text="ポータルでのデータ保護設定を示します。":::

5. タグ付けを使用してアカウントを **作成** する場合は、組織のタグを追加します。
 
6. これでアカウントをご自分の Komprise 環境に追加する前に必要なのは、2 つの簡単な手順だけになりました。 Azure portal で作成したアカウントに移動し、ファイル サービス メニューで [ファイル共有] を選択します。 ファイル共有を追加し、わかりやすい名前を選びます。 その後、 [設定] の下にある [アクセス キー] 項目に移動し、 [ストレージ アカウント名] と 2 つのアクセス キーの 1 つをコピーします。 キーが表示されていない場合は、 **[Show keys]\(キーの表示\)** をクリックします。

   :::image type="content" source="./media/komprise-quick-start-guide/azure-access-key.png" alt-text="ポータルでのアクセス キーの設定を示します。":::

7. Azure ファイル共有の **[プロパティ]** に移動します。 URL アドレスを書き留めてください。Komprise の移行先ファイル共有への Azure 接続を追加する際に必要となります。

   :::image type="content" source="./media/komprise-quick-start-guide/azure-files-endpoint.png" alt-text="Azure Files エンドポイントを検索します。":::

8. (_省略可能_) デプロイに追加のセキュリティ層を追加することができます。
 
   1. ストレージ アカウントに変更を加えることができるユーザーを制限するようにロール ベース アクセスを構成します。 詳細については、「[管理操作のための組み込みロール](../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)」を参照してください。
 
   2.  [ストレージ ファイアウォール設定](../../../common/storage-network-security.md)を使用して、アカウントへのアクセスを特定のネットワーク セグメントに限定します。 ファイアウォール設定を構成して、企業ネットワーク外からアクセスが試行されないようにします。

       :::image type="content" source="./media/komprise-quick-start-guide/azure-storage-firewall.png" alt-text="ポータル内のストレージ ファイアウォールの設定を示します。":::

   3.  アカウントに[削除ロック](../../../../azure-resource-manager/management/lock-resources.md)を設定し、ストレージ アカウントが誤って削除されないようにします。

       :::image type="content" source="./media/komprise-quick-start-guide/azure-resource-lock.png" alt-text="ポータルで削除ロックを設定する方法を示します。":::

   4.  追加の[セキュリティに関するベスト プラクティス](../../../blobs/security-recommendations.md)を構成します。

### <a name="deployment-instructions-for-managing-file-data"></a>ファイル データを管理するためのデプロイ手順

1.  Komprise Observer 仮想アプライアンスを Director から **ダウンロード** してハイパーバイザーにデプロイし、ネットワークとドメインを使用してその構成を行います。 Director は、Komprise によって管理されるクラウド サービスとして提供されます。 Director にアクセスするために必要な情報は、ソリューションの購入後にウェルカム メールで送信されます。

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-setup-1.png" alt-text="Komprise Observer の適切なイメージを Director からダウンロードする":::

1. 分析と移行の対象となる共有を追加するには、次の 2 つの方法があります。
   1. 次の情報を入力して、ストレージ環境内のすべての共有を **検出** します。
       - 移行元 NAS のプラットフォーム
       - ホスト名または IP アドレス
       - 表示名
       - 資格情報 (SMB 共有の場合)

        :::image type="content" source="./media/komprise-quick-start-guide/komprise-setup-2.png" alt-text="検出対象の NAS システムを指定する":::

    1. 次の情報を入力してファイル共有を **指定** します。
       - ストレージ情報
       - プロトコル
       - Path
       - 表示名
       - 資格情報 (SMB 共有の場合)
  
        :::image type="content" source="./media/komprise-quick-start-guide/komprise-setup-3.png" alt-text="検出する NAS ソリューションを指定する":::

    移行元と移行先の共有をさらに追加するには、この手順を繰り返す必要があります。 移行先として Azure Files を追加するには、Azure ストレージ アカウントとファイル共有の詳細を指定する必要があります。

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-azure-files-1.png" alt-text="移行先サービスとして Azure Files を選択する":::

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-azure-files-2.png" alt-text="Azure Files の詳細を入力する":::

### <a name="deployment-instructions-for-managing-object-data"></a>オブジェクト データを管理するためのデプロイ手順

オブジェクトの管理では、異なるエクスペリエンスが提供されます。 Director と Observer は、Komprise によって管理されるクラウド サービスとして提供されます。 Azure Blob Storage のデータを分析およびアーカイブする必要があるだけの場合は、それ以上のデプロイは必要ありません。 Azure Blob Storage への移行を実行する必要がある場合は、ウェルカム メールで送信された Komprise Observer 仮想アプライアンスを取得して、Azure クラウド インフラストラクチャ内の Linux 仮想マシンにデプロイします。 デプロイが完了したら、Komprise Director で次の手順を実行します。

1. **[データ ストア]** 、 **[新しいオブジェクト ストアの追加]** に移動します。 プロバイダーとして **[Microsoft Azure]** を選択します。

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-add-object-store.png" alt-text="新しいオブジェクト ストアの追加を示すスクリーンショット":::

1. 分析と移行を行う共有を追加します。 ソース、ターゲット共有、またはコンテナーごとに、これらの手順を繰り返す必要があります。 同じアクションを実行するには、次の 2 つのオプションがあります。
    1. 次の情報を入力して、すべてのコンテナーを **検出** します。
        - ストレージ アカウント名
        - プライマリ アクセス キー
        - 表示名
    
        :::image type="content" source="./media/komprise-quick-start-guide/komprise-discover-storage-account.png" alt-text="ストレージ アカウント内のコンテナーを検出する方法を示すスクリーンショット":::

        **[Azure portal](https://portal.azure.com/)** で必要な情報を確認するには、ストレージ アカウントの **[設定]** の下にある **[アクセス キー]** 項目に移動します。 キーが表示されていない場合は、 **[Show keys]\(キーの表示\)** をクリックします。

    1. 次の情報を入力して、コンテナーを **指定** します。
        - コンテナー名
        - ストレージ アカウント名
        - プライマリ アクセス キー
        - 表示名

        :::image type="content" source="./media/komprise-quick-start-guide/komprise-add-container.png" alt-text="ストレージ アカウントにコンテナーを追加する方法を示すスクリーンショット":::

        コンテナー名は移行先のコンテナーを表し、移行前に作成する必要があります。 **[Azure portal](https://portal.azure.com/)** でその他の必要な情報を確認するには、ストレージ アカウントの **[設定]** の下にある **[アクセス キー]** 項目に移動します。 キーが表示されていない場合は、 **[Show keys]\(キーの表示\)** をクリックします。

## <a name="migration-guide"></a>移行ガイド

Komprise はライブ マイグレーションに対応しており、移行中もエンド ユーザーとアプリケーションを中断することなく、データにアクセスし続けることができます。 ディレクトリ、ファイル、リンクは、移行プロセスによって自動的に移行元から移行先に移行されます。 各ステップで、データ整合性がチェックされます。 属性、アクセス許可、アクセスの制御はすべて移行元から適用されます。 オブジェクトの移行では、オブジェクト、プレフィックス、各オブジェクトのメタデータが移行されます。

移行を構成して実行するには、次の手順に従います。

1. Komprise コンソールにログインします。 コンソールにアクセスするために必要な情報は、ソリューションの購入後にウェルカム メールで送信されます。
1. **[Migrate]\(移行\)** に移動し、 **[Add Migration]\(移行の追加\)** をクリックします。

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-new-migrate.png" alt-text="新しい移行ジョブを追加する":::

1. 移行元と移行先の適切な共有を選択して移行タスクを追加します。 移行の名前を指定してください。 構成が済んだら **[Start Migration]\(移行の開始\)** をクリックします。 この手順は、ファイル データの移行とオブジェクト データの移行では若干異なります。
   
    1. ファイルの移行

       :::image type="content" source="./media/komprise-quick-start-guide/komprise-add-migration.png" alt-text="移行ジョブの詳細を指定する":::

       ファイルの移行では、移行先でアクセス時間と SMB ACL を維持するオプションが提供されます。 このオプションは、移行元と移行先の選択したファイル サービスおよびプロトコルに依存します。

    1. オブジェクトの移行

        :::image type="content" source="./media/komprise-quick-start-guide/komprise-add-object-migration.png" alt-text="オブジェクトの移行の追加を示すスクリーンショット":::

        オブジェクトの移行では、移行先の Azure ストレージ層 (ホット、クール、アーカイブ) を選択するオプションが提供されます。 MD5 チェックサムを使用した各データ転送の検証を選択することもできます。 MD5 チェックサムを計算するにはクラウド オブジェクトを取得する必要があるため、MD5 チェックサムでエグレス コストが発生する可能性があります。

2. 移行が開始されたら、 **[Migrate]\(移行\)** に移動して進行状況を監視できます。

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-monitor-migrations.png" alt-text="すべての移行ジョブを監視する":::

3. すべての変更が移行されたら、 **[Actions]\(アクション\)** をクリックし、 **[Start final iteration]\(最後のイテレーションの開始\)** を選択して、最後の 1 回となる移行を実行します。 最終的な移行の前に、移行元のファイル共有へのアクセスを停止するか、それらを (ユーザーとアプリケーションに対して) 読み取り専用モードにすることをお勧めします。 この手順を行うことで、移行元に対する変更を確実に防ぐことができます。

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-final-migration.png" alt-text="切り替え前に最後の 1 回となる移行を実行する":::

    最後の移行が完了したら、すべてのユーザーとアプリケーションを移行先の共有に切り替えます。 通常、新しいファイル サービスに切り替えるためには、DNS サーバーや DFS サーバーの構成を変更したり、マウント ポイントを新しい移行先に変更したりする必要があります。 

4. 最後に、移行を完了済みとしてマークします。

## <a name="support"></a>サポート

Komprise のサポート ケースを開くには、[Komprise サポート サイト](https://komprise.freshdesk.com/)にサインインしてください

## <a name="marketplace"></a>マーケットプレース

Komprise の登録情報は、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview) で入手できます。

## <a name="next-steps"></a>次のステップ

詳細については、各種のリソースを参照してください。

- [Storage の移行の概要](../../../common/storage-migration-overview.md)
- Komprise Intelligent Data Management でサポートされる機能 ([移行ツールの比較表](./migration-tools-comparison.md))
- [Komprise 互換性マトリックス](https://www.komprise.com/partners/microsoft-azure/)
