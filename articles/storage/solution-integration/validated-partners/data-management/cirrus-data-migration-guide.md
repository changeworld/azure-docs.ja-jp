---
title: Cirrus Data を使用してブロック データを Azure に移行する
titleSuffix: Azure Storage
description: Cirrus Migrate Cloud を実装して Azure にデータを移行するためのクイック スタート ガイドを提供します。
author: dukicn
ms.author: nikoduki
ms.date: 09/06/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: b9e56df2b4643fabdbdcf0d00baa6fe63c0d9e95
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263232"
---
# <a name="migrate-your-block-data-to-azure-with-cirrus-migrate-cloud"></a>Cirrus Migrate Cloud を使用してブロック データを Azure に移行する

Cirrus Migrate Cloud (CMC) を使用すると、既存のストレージ システム、またはクラウドから Azure にディスクを移行することができます。 移行は元のシステムが稼働中のままで実行されます。 このドキュメントでは、移行を正しく構成して実行する方法について説明します。

## <a name="overview"></a>概要

このソリューションには、ホスト間の直接接続が可能なすべてのホストで実行される分散型の移行エージェントが使用されます。 ホスト間の移行はそれぞれ独立しているため、ソリューションのスケーラビリティは無限であり、どこか 1 か所がデータフローのボトルネックになることもありません。 運用環境への影響をなくすため、移行には cMotion™ テクノロジが使用されます。 

## <a name="use-cases"></a>ユース ケース

このドキュメントでは、Azure 内の仮想マシンに対し、(オンプレミスまたは別のクラウド プロバイダーで実行されている) 仮想マシンからアプリケーションを移行する一般的な事例について取り上げます。 さまざまなユース ケースにおける詳しいステップバイステップ ガイドについては、次のリンク先を参照してください。

- [cMotion を使用して Azure にワークロードを移動する](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-on-premises-to-azure-1xo3nuf/)
- [Premium ディスクから Ultra Disks に移動する](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-migration-between-azure-tiers-sxhppt/)
- [AWS から Azure に移動する](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-migration-from-aws-to-azure-weegd9/.)

## <a name="components"></a>Components

Cirrus Migrate Cloud は複数のコンポーネントから構成されています。

- **cMotion™**: ソース ホストにダウンタイムを生じさせることなく、ソースからターゲット クラウドへのストレージレベルの切り替えを行う CMC の機能。 元の場所である FC または iSCSI の移行元ディスクから移行先の新しい Azure マネージド ディスクにワークロードを切り替えるために cMotion™ が使用されます。
- **Web ベースの管理ポータル**: Web ベースの Management as a Service (サービスとしての管理)。 ユーザーは、これを使用して移行を管理したり、ブロック ストレージを保護したりすることができます。 Web ベースの管理ポータルには、CMC アプリケーションのすべての構成、管理、制御タスクのためのインターフェイスが備わっています。

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-web-portal.jpg" alt-text="CMC ポータルのスクリーンショット":::

## <a name="implementation-guide"></a>実装ガイド

ユーザーは Azure のベスト プラクティスに従って新しい仮想マシンを実装する必要があります。 このプロセスについて詳しくは、[クイック スタート ガイド](../../../../virtual-machines/windows/quick-create-portal.md)を参照してください。

移行を始める前に、次の前提条件を満たしておく必要があります。

- Azure における OS の使用が適切に許諾されていることを確認する
- Azure 仮想マシンにアクセスできることを確認する
- アプリケーションまたはデータベースを Azure で実行するためのライセンスがあることを確認する
- 移行先ディスクのサイズを自動割り当てするためのアクセス許可を確認する
- マネージド ディスクのサイズが移行元ディスクと同じか、それ以上であることを確認する 
- ホスト間接続を可能にするポートが移行元または移行先の仮想マシンで開放されていることを確認する

1. **Azure 仮想マシンを準備します**。 ドキュメントでは、仮想マシンが完全に実装済みであることを前提としています。 そのため、データ ディスクの移行後、移行先ホストはアプリケーションをすぐに起動し、オンラインにできます。 データは、数秒前にシャットダウンされたときのソースと同じ状態になります。 CMC では、OS ディスクは移行先に移行されません。

1. **Azure 仮想マシン内のアプリケーションを準備します**。 この例における移行元は Linux ホストです。 あらゆるユーザー アプリケーションを実行し、それぞれの BSD ストレージにアクセスすることができます。 ここでは、1 GiB のディスクを移行元ストレージ デバイスとして使用しているデータベース アプリケーションを移行元で実行します。 ただし、それ以外にも任意のアプリケーションを使用できます。 仮想マシンを移行先仮想マシンとして Azure で使用できるように設定します。 リソース構成とオペレーティング システムがアプリケーションに適合していること、CMC ポータルを使用して移行元からの移行を受け入れる準備が整っていることを確認してください。 移行先のブロック ストレージ デバイスの割り当てと作成は、移行処理の過程で自動的に行われます。

1. **CMC アカウントにサインアップします**。 CMC アカウントを取得するには、サポート ページを確認し、アカウントの取得方法についての正確な手順に従ってください。 詳しい情報は、[こちら](https://support.cirrusdata.cloud/en/article/licensing-m4lhll/)でご覧いただけます。

1. **移行プロジェクトを作成** し、具体的な移行の特性、種類、移行の所有者など、操作を定義するうえで必要な詳細をすべて反映します。 

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-create-project.jpg" alt-text="新しいプロジェクトの作成のスクリーンショット":::

1. **移行プロジェクトのパラメーターを定義します**。 CMC の Web ベース ポータルを使用し、パラメーター (移行元や移行先などのパラメーター) を定義して移行を構成します。

1. **移行元と移行先のホストに CMC の移行エージェントをインストールします**。 CMC の Web ベースの管理ポータルを使用し、 **[Deploy Cirrus Migrate Cloud]\(Cirrus Migrate Cloud のデプロイ\)** を選択し、 **[New Installation]\(新しいインストール\)** の curl コマンドを取得します。 このコマンドを移行元と移行先のコマンド ライン インターフェイスで実行します。

1. **移行元と移行先のホストの間に双方向接続を作成します**。 CMC の Web ベース管理ポータルで、 **[H2H]\(ホスト間\)** タブの **[Create New Connection]\(新しい接続の作成\)** ボタンを使用します。 Linux オペレーティング システムによって使用されているデバイスではなく、アプリケーションによって使用されているデバイスを選択してください。

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-1.jpg" alt-text="デプロイされたホストを一覧表示するスクリーンショット":::

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-2.jpg" alt-text="ホスト間接続を一覧表示するスクリーンショット":::

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-3.jpg" alt-text="移行されたデバイスを一覧表示するスクリーンショット":::

1. CMC の Web ベースの管理ポータルから **[Migrate Host Volumes]\(ホスト ボリュームの移行\)** を使用して **移行先仮想マシンへの移行を開始** します。 リモートの場所に関する手順に従ってください。 CMC ポータルを使用し、画面の右側で **[Auto allocate destination volumes]\(移行先ボリュームの自動割り当て\)** を選択します。 
 
1. 次に、CMC ポータルの **[Integrations]\(統合\)** タブを使用し、Azure の資格情報を追加して、接続とディスクのプロビジョニングを許可する必要があります。 Azure に関する会社の非公開の値 (**統合名**、**テナント ID**、**クライアント ID またはアプリケーション ID**、**シークレット**) を使用して、必須フィールドに入力します。 **[保存]** をクリックします。 

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-4.jpg" alt-text="Azure の資格情報を入力する画面のスクリーンショット":::

    Azure AD アプリケーションの作成について詳しくは、こちらの[詳細な手順](https://support.cirrusdata.cloud/en/article/creating-an-azure-service-account-for-cirrus-data-cloud-tw2c9n/)を参照してください。 CMC 用の Azure AD アプリケーションを作成、登録することで、移行先仮想マシンに対する Azure Managed Disks の自動作成が有効になります。

    >[!NOTE]
    >前の手順で **[Auto allocate destination volumes]\(移行先ボリュームの自動割り当て\)** を選択したので、新たに割り当てようと再度押すことは避けてください。 そのようにした場合はエラーが発生します。 代わりに **[Continue]\(続行\)** を押してください。

## <a name="migration-guide"></a>移行ガイド

前の手順で **[保存]** を押した後に、 **[New Migration Session]\(新しい移行セッション\)** ウィンドウが表示されます。 フィールドに入力します。
   - **[Session description]\(セッションの説明\)** : わかりやすい説明を入力します
   - **[Auto Resync Interval]\(自動再同期の間隔\)** : 移行のスケジュールを有効にします 
   - iQoS を使用して、移行が運用に及ぼす影響を選択します。
     - **[Minimum]\(最小\)** : 使用可能な帯域幅の 25% に移行速度が抑えられます
     - **[Moderate]\(中程度\)** : 使用可能な帯域幅の 50% に移行速度が抑えられます
     - **[Aggressive]\(積極的\)** : 使用可能な帯域幅の 75% に移行速度が抑えられます。
     - **[Relentless]\(無制限\)** : 移行は抑えられません。

       :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-iqos.jpg" alt-text="iQoS 設定のオプションを示すスクリーンショット":::

**[Create Session]\(セッションの作成\)** を押して移行を開始します。

移行の初期同期の開始から cMotion の開始まで、ユーザーが CMC を対話的に操作する必要はありません。  ただし、進行状況を監視する必要はあります。 ダッシュボードを使用して最新の状態とセッション ボリュームを監視し、変更を追跡することができます。 

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-1.jpg" alt-text="監視の進行状況を示すスクリーンショット":::

移行中は、[Changed Data Map]\(変更データ マップ\) を押すことにより、移行元デバイスで変更されたブロックを観察できます。  

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-2.jpg" alt-text="変更データ マップを示すスクリーンショット":::

iQoS の詳細には、同期済みのブロックと移行の状態が表示されます。 また、これを見ると、運用環境の IO に影響は生じていないことがわかります。

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-3.jpg" alt-text="iQoS の詳細を示すスクリーンショット":::

## <a name="moving-the-workload-to-azure-with-cmotion"></a>cMotion を使用して Azure にワークロードを移動する

初期同期が完了したら、cMotion™ を使用して、ソース ディスクから移行先の Azure マネージド ディスクにワークロードを移動する準備を行います。

### <a name="start-cmotion"></a>cMotion™ を起動する

この時点で、cMotion™ の移行切り替えに向けたシステムの準備は完了しています。 

1. CMS ポータルで Session を使用して **[Trigger cMotion™]\(cMotion™ のトリガー\)** を選択し、移行元から移行先のディスクにワークロードを切り替えます。 処理が完了したかどうかを確認するために、iostat またはそれと同等のコマンドを使用してください。 Azure 仮想マシンのターミナルに移動し、*iostat /dev/<device_name>* (/dev/sdc など) を実行して、Azure クラウド内の移行先ディスクにアプリケーションからの IO が書き込まれていることを確認します。

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-4.jpg" alt-text="現在の監視状態を示すスクリーンショット":::

この状態では随時、ワークロードを切り替えたり、移行元ディスクに戻したりすることが可能です。 運用仮想マシンを元に戻したい場合は、 **[Session Actions]\(セッション アクション\)** ボタンを使用し、 **[Revert cMotion™]\(cMotion™ を元に戻す\)** を選択します。 移行元のホスト (VM) でアプリケーションが実行されている間、何度でも元に戻したり切り替えたりすることができます。

最終的に移行先仮想マシンへの切り替えが必要になった段階で、次の手順に従ってください。
1. **[Session Actions]\(セッション アクション\)** を選択します。
2. **[Finalize Cutover]\(切り替えの確定\)** をクリックすると、新しい Azure 仮想マシンへの切り替えが "確定" され、移行元ディスクを削除するオプションが無効になります。 最終的なホスト切り替えを実施するため、移行元ホストで実行されている他のアプリケーションを停止してください。 

### <a name="move-the-application-to-the-destination-virtual-machine"></a>移行先仮想マシンにアプリケーションを移動する

切り替えが完了したら、アプリケーションを新しい仮想マシンに切り替える必要があります。 そのためには、次の手順を実行します。

1. アプリケーションを停止します
2. 移行されたデバイスのマウントを解除します
3. 新しく移行したデバイスを Azure 仮想マシンでマウントします。 
4. 新しく移行したディスク上の Azure 仮想マシンで同じアプリケーションを起動します。 
 
移行元ホストで iostat コマンドを実行して、移行元ホスト デバイスへの IO がないことを確認します。 Azure 仮想マシンで iostat を実行すると、IO が実行中であることが、Azure 仮想マシンのターミナルに示されます。

### <a name="complete-the-migration-session-in-cmc-gui"></a>CMC GUI で移行セッションを完了する 

cMotion™ のトリガー後、移行先のデバイスにすべての IO がリダイレクトされた時点で移行手順は完了しています。 これで、 **[Session Actions]\(セッション アクション\)** を使用してセッションを閉じることができます。 **[Delete Session]\(セッションの削除\)** をクリックすると、移行セッションが終了します。 最後に、移行元ホストと Azure 仮想マシンの両方から **[Cirrus Migrate Cloud Agents]\(Cirrus 移行クラウド エージェント\)** を削除します。 アンインストールを実行するには、 **[Deploy Cirrus Migrate Cloud]\(Cirrus Migrate Cloud のデプロイ\)** ボタンから **[Uninstall curl command]\(アンインストール curl コマンド\)** を取得します。 ポータルの **[Hosts]\(ホスト\)** セクションにオプションがあります。 

エージェントが削除されたら、移行はすべて完了です。 移行元アプリケーションは、ディスクがローカルにマウントされた移行先 Azure 仮想マシンの運用環境で実行されるようになりました。    

## <a name="support"></a>サポート

### <a name="how-to-open-a-case-with-azure"></a>Azureでケースを開く方法

[Azure portal](https://portal.azure.com) で、上部にある検索バーで「サポート」を検索します。 **[ヘルプとサポート]**  ->  **[新しいサポート要求]** の順に選択します。

### <a name="engaging-cirrus-support"></a>Cirrus にサポートを依頼する

CMC ポータルの **[Help Center]\(ヘルプ センター\)** タブを選択して Cirrus Data Solutions サポートに連絡するか、[CDSI Web サイト](https://support.cirrusdata.cloud/en/)にアクセスしてサポート リクエストを送信します。

## <a name="next-steps"></a>次のステップ
- [Azure 仮想マシン](../../../../virtual-machines/windows/overview.md)の詳細情報
- [Azure Managed Disks](../../../../virtual-machines/managed-disks-overview.md) の詳細情報
- [ストレージ移行](../../../common/storage-migration-overview.md)の詳細情報
- [Cirrus Data の Web サイト](https://www.cirrusdata.com/)
- [cMotion](https://support.cirrusdata.cloud/en/category/howtos-1un623w/) のステップバイステップ ガイド