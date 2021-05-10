---
title: Windows Virtual Desktop 向けの Monitor を使用する - Azure
description: Windows Virtual Desktop 向けの Azure Monitor を使用する方法
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7da35f77dd232e5f523e2bdc3f125c68015ab871
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448187"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment"></a>Windows Virtual Desktop 向けの Azure Monitor を使用してデプロイを監視する

Windows Virtual Desktop 向けの Azure Monitor は、Azure Monitor のブックに基づいて構築されたダッシュボードで、IT プロフェッショナルが Windows Virtual Desktop 環境を理解するのに役立ちます。 このトピックでは、Windows Virtual Desktop 環境を監視するために Windows Virtual Desktop 向けの Azure Monitor を設定する方法について説明します。

## <a name="requirements"></a>必要条件

Windows Virtual Desktop 向けの Azure Monitor の使用を開始する前に、次の項目を設定する必要があります。

- 監視するすべての Windows Virtual Desktop 環境が、Azure Resource Manager と互換性のある最新リリースの Windows Virtual Desktop に基づいている必要がある。
- 少なくとも 1 つの Log Analytics ワークスペースが構成されている。 Windows Virtual Desktop セッション ホストに対して指定された Log Analytics ワークスペースを使用して、パフォーマンス カウンターとイベントが Windows Virtual Desktop 展開のセッション ホストからのみ収集されるようにします。
- Log Analytics ワークスペースで、次の項目のデータ収集を有効にする。
    - Windows Virtual Desktop 環境からの診断
    - Windows Virtual Desktop セッション ホストからの推奨されるパフォーマンス カウンター
    - Windows Virtual Desktop セッション ホストからの推奨される Windows イベント ログ

 この記事で説明されているデータ セットアップ プロセスは、Windows Virtual Desktop を監視するために必要な唯一のものです。 Log Analytics ワークスペースにデータを送信する他のすべてのアイテムを無効にして、コストを節約することができます。

環境に合わせて Windows Virtual Desktop 向けの Azure Monitor を監視するすべてのユーザーには、次の読み取りアクセス許可も必要になります。

- Windows Virtual Desktop リソースを保持する Azure サブスクリプションへの読み取りアクセス
- Windows Virtual Desktop セッション ホストを保持するサブスクリプションのリソース グループへの読み取りアクセス
- Log Analytics ワークスペースへの読み取りアクセス

>[!NOTE]
> 読み取りアクセスでは、管理者はデータの表示のみを行えます。 Windows Virtual Desktop ポータルでリソースを管理するには、さまざまなアクセス許可が必要です。

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Windows Virtual Desktop 向けの Azure Monitor を開く

Windows Virtual Desktop 向けの Azure Monitor は、次のいずれかの方法で開くことができます。

- [aka.ms/azmonwvdi](https://aka.ms/azmonwvdi) にアクセスします。
- Azure portal で **[Windows Virtual Desktop]** を検索して選択し、 **[Insights]** を選択します。
- Azure portal で **[Azure Monitor]** を検索して選択します。 **[Insights]** で **[Insights Hub]** を選択し、 **[Windows Virtual Desktop]** を選択します。
ページが開いたら、監視する環境の **[サブスクリプション]** 、 **[リソース グループ]** 、 **[ホスト プール]** 、 **[時間範囲]** を入力します。

>[!NOTE]
>現在、Windows Virtual Desktop では、一度に 1 つのサブスクリプション、リソース グループ、ホスト プールの監視のみがサポートされています。 監視する環境が見つからない場合は既知の機能の要求と問題に関する「[トラブルシューティング ドキュメント](troubleshoot-azure-monitor.md)」をご覧ください。

## <a name="log-analytics-settings"></a>Log Analytics の設定

Windows Virtual Desktop 向けの Azure Monitor の使用を開始するには、少なくとも 1 つの Log Analytics ワークスペースが必要です。 Windows Virtual Desktop セッション ホストに対して指定された Log Analytics ワークスペースを使用して、パフォーマンス カウンターとイベントが Windows Virtual Desktop 展開のセッション ホストからのみ収集されるようにします。 ワークスペースが既にセットアップされている場合は、「[構成ブックを使用してセットアップする](#set-up-using-the-configuration-workbook)」に進んでください。 ワークスペースをセットアップするには、「[Azure portal で Log Analytics ワークスペースを作成する](../azure-monitor/logs/quick-create-workspace.md)」をご覧ください。

>[!NOTE]
>Log Analytics に対する通常のデータ ストレージ料金が適用されます。 まず、従量課金制モデルを選択し、より多くのデータを取得するよう、デプロイのスケーリングするときに調整することをお勧めします。 詳細については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」をご覧ください。

## <a name="set-up-using-the-configuration-workbook"></a>構成ブックを使用してセットアップする

Windows Virtual Desktop 向けの Azure Monitor を初めて開くときは、Windows Virtual Desktop 環境用に Azure Monitor をセットアップする必要があります。 配置を構成するには、次の手順に従います。

1. [aka.ms/azmonwvdi](https://aka.ms/azmonwvdi) の Azure portal で Windows Virtual Desktop 向けの Azure Monitor を開き、 **[configuration workbook]\(構成ブック\)** を選択します。
2. **[サブスクリプション]** 、 **[リソース グループ]** 、および **[ホスト プール]** で構成する環境を選択します。

構成ブックによって監視環境が設定され、設定プロセスの完了後に構成を確認できます。 ダッシュボード内の項目が正しく表示されない場合、または新しい設定を必要とする更新プログラムが製品グループによって発行される場合は、構成を確認することが重要です。

### <a name="resource-diagnostic-settings"></a>リソース診断設定

Windows Virtual Desktop インフラストラクチャに関する情報を収集するには、Windows Virtual Desktop ホスト プールとワークスペースでいくつかの診断設定を有効にする必要があります (これは、Log Analytics ワークスペースではなく、Windows Virtual Desktop ワークスペースです)。 ホスト プール、ワークスペース、およびその他の Windows Virtual Desktop リソース オブジェクトの詳細については、「[環境ガイド](environment-setup.md)」をご覧ください。

Windows Virtual Desktop 診断およびサポートされている診断テーブルの詳細については、「[Log Analytics への Windows Virtual Desktop 診断の送信](diagnostics-log-analytics.md)」をご覧ください。

構成ブックにリソースの診断設定を設定するには、次のようにします。 

1. 構成ブックの **[リソース診断設定]** タブを選択します。 
2. Windows Virtual Desktop 診断を送信するには、 **[Log Analytics ワークスペース]** を選択します。 

#### <a name="host-pool-diagnostic-settings"></a>ホスト プールの診断設定

構成ブックの [リソース診断設定] セクションを使用してホスト プール診断をセットアップするには、次の手順を実行します。

1. **[ホスト プール]** で、Windows Virtual Desktop 診断が有効になっているかどうかを確認します。 有効になっていない場合は、"選択されたホスト プールの既存の診断構成が見つかりませんでした" というエラー メッセージが表示されます。 次のサポートされている診断テーブルを有効にする必要があります。

    - Checkpoint
    - エラー
    - 管理
    - Connection
    - HostRegistration
    - AgentHealthStatus
    
    >[!NOTE]
    > エラー メッセージが表示されない場合、手順 2 から 4 を実行する必要はありません。

2. **[Configure host pool]\(ホスト プールを構成する\)** を選択します。
3. **[デプロイ]** を選択します。
4. 構成ブックを最新の状態に更新します。

#### <a name="workspace-diagnostic-settings"></a>ワークスペース診断設定 

構成ブックの [リソース診断設定] セクションを使用してワークスペース診断をセットアップするには、次の手順を実行します。

 1. **[ワークスペース]** で、Windows Virtual Desktop ワークスペースに対して Windows Virtual Desktop 診断が有効になっているかどうかを確認します。 有効になっていない場合は、"選択されたワークスペースの既存の診断構成が見つかりませんでした" というエラー メッセージが表示されます。 次のサポートされている診断テーブルを有効にする必要があります。
 
    - Checkpoint
    - エラー
    - 管理
    - フィード
    
    >[!NOTE]
    > エラー メッセージが表示されない場合、手順 2 から 4 を実行する必要はありません。

2. **[ワークスペースを構成する]** を選択します。
3. **[デプロイ]** を選択します。
4. 構成ブックを最新の状態に更新します。

### <a name="session-host-data-settings"></a>セッション ホスト データの設定

Windows Virtual Desktop セッション ホストの情報を収集するには、ホスト プール内のすべてのセッション ホストに Log Analytics エージェントをインストールし、セッション ホストが Log Analytics ワークスペースに送信されていることを確認し、パフォーマンス データと Windows イベント ログを収集するように Log Analytics エージェントの設定を構成する必要があります。

セッション ホスト データを送信する Log Analytics ワークスペースは、診断データの送信先と同じである必要はありません。 Windows Virtual Desktop 環境の外部に Azure セッション ホストがある場合は、Windows Virtual Desktop セッション ホスト用に指定された Log Analytics ワークスペースを用意することをお勧めします。 

セッション ホスト データを収集する Log Analytics ワークスペースを設定するには、次のようにします。 

1. 構成ブックで **[セッション ホスト データの設定]** タブを選択します。 
2. セッション ホスト データの送信先とする **[Log Analytics ワークスペース]** を選択します。 

#### <a name="session-hosts"></a>セッション ホスト

ホストから、選択した Log Analytics ワークスペースにデータを送信するために、ホスト プール内のすべてのセッション ホストに Log Analytics エージェントをインストールする必要があります。 ホスト プール内のすべてのセッション ホストに対して Log Analytics が構成されていない場合は、 **[セッション ホスト データの設定]** 上部の **[セッション ホスト]** セクションに "ホスト プール内の一部のホストで、選択した Log Analytics ワークスペースにデータが送信されていません" というエラー メッセージが表示されます。

>[!NOTE]
> **[セッション ホスト]** セクションまたはエラー メッセージが表示されない場合は、すべてのセッション ホストが正しくセットアップされています。 [[ワークスペース パフォーマンス カウンター]](#workspace-performance-counters) をセットアップする手順に進んでください。

構成ブックを使用して残りのセッション ホストをセットアップするには、次の手順を実行します。

1. **[ワークスペースにホストを追加する]** を選択します。 
2. 構成ブックを最新の状態に更新します。

>[!NOTE]
>Log Analytics 拡張機能をインストールするには、ホスト マシンが実行されている必要があります。 自動デプロイがうまくいかない場合は、代わりにホストに拡張機能を手動でインストールできます。 拡張機能を手動でインストールする方法については、「[Windows 用の Log Analytics 仮想マシン拡張機能](../virtual-machines/extensions/oms-windows.md)」をご覧ください。

#### <a name="workspace-performance-counters"></a>ワークスペース パフォーマンス カウンター

セッション ホストからパフォーマンス情報を収集し、Log Analytics ワークスペースに送信するには、特定のパフォーマンス カウンターを有効にする必要があります。

既にパフォーマンス カウンターが有効になっていて、それらを削除する場合は、「[パフォーマンス カウンターの構成](../azure-monitor/agents/data-sources-performance-counters.md)」の手順に従います。 同じ場所にパフォーマンス カウンターを追加したり、削除したりできます。

構成ブックを使用してパフォーマンス カウンターをセットアップするには、次の手順に従います。 

1. 構成ブックの **[ワークスペース パフォーマンス カウンター]** の **[現在のカウンター]** で、Log Analytics ワークスペースへの送信が既に有効になっているカウンターを確認します。 **[見つからないカウンター]** を確認して、すべての必要なカウンターが有効になっていることを確認してください。
2. カウンターが見つからない場合は、 **[パフォーマンス カウンターを構成する]** を選択します。
3. **[構成の適用]** を選択します。
4. 構成ブックを最新の状態に更新します。
5. **[見つからないカウンター]** の一覧をチェックして、必要なすべてのカウンターが有効になっていることを確認します。 

#### <a name="configure-windows-event-logs"></a>Windows イベント ログを構成する

また、セッション ホストからエラー、警告、および情報を収集して、Log Analytics ワークスペースに送信するには、特定の Windows イベント ログを有効にする必要があります。

Windows イベント ログを既に有効にしていて、削除する場合は、「[Windows イベント ログの構成](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)」の手順に従ってください。  Windows イベント ログは、同じ場所に追加したり削除したりすることができます。

構成ブックを使用して Windows イベント ログをセットアップするには、次の手順を実行します。

1. **[Windows イベント ログの構成]** で、 **[イベント ログが構成済み]** で、Log Analytics ワークスペースへの送信が既に有効になっているイベント ログを確認します。 **[イベント ログがない]** を確認して、すべての Windows イベント ログが有効になっていることを確認してください。
2. Windows イベント ログがない場合は、 **[イベントの構成]** を選択します。
3. **[デプロイ]** を選択します。
4. 構成ブックを最新の状態に更新します。
5. **[イベント ログがない]** の一覧をチェックして、必要なすべての Windows イベント ログが有効になっていることを確認します。 

>[!NOTE]
>自動イベント デプロイに失敗した場合は、構成ブックで **[エージェント構成を開く]** を選択して、不足している Windows イベント ログを手動で追加します。 

## <a name="optional-configure-alerts"></a>省略可能: アラートを構成する

Windows Virtual Desktop 向けの Azure Monitor を使用すると、選択したサブスクリプション内で発生した Azure Monitor アラートを Windows Virtual Desktop データのコンテキストで監視できます。 Azure Monitor アラートは Azure サブスクリプションのオプション機能であり、Windows Virtual Desktop 向けの Azure Monitor とは別に設定する必要があります。 Windows Virtual Desktop のイベント、診断、およびリソースに対するカスタム アラートを設定するには、Azure Monitor アラート フレームワークを使用します。 Azure Monitor のアラートの詳細については、「[Azure Monitor のアラートを使用してイベントに応答する](../azure-monitor/alerts/tutorial-response.md)」をご覧ください。

## <a name="diagnostic-and-usage-data"></a>診断と使用状況データ

Microsoft は、お客様による Azure Monitor サービスの使用を通して、使用状況とパフォーマンス データを自動的に収集します。 Microsoft はこのデータを使用して、サービスの品質、セキュリティ、および整合性の向上に努めています。

正確で効率的なトラブルシューティング機能を提供するために、収集されたデータには、ポータル セッション ID、Azure Active Directory ユーザー ID、およびイベントが発生したポータル タブの名前が含まれます。 名前や住所などの連絡先情報は収集されません。

データの収集と使用の詳細については、「[Microsoft オンライン サービスのプライバシーに関する声明](https://privacy.microsoft.com/privacystatement)」を参照してください。

>[!NOTE]
>サービスによって収集された個人データを表示または削除する方法については、[GDPR のための Azure データ サブジェクト要求](/microsoft-365/compliance/gdpr-dsr-azure)に関するページをご覧ください。 GDPR の詳細については、[Service Trust Portal の GDPR に関するセクション](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)をご覧ください。

## <a name="next-steps"></a>次の手順

Windows Virtual Desktop 環境用に Azure Monitor を構成したので、環境の監視を開始するために役立つリソースを次に示します。

- Windows Virtual Desktop 向けの Azure Monitor に関連する用語と概念の詳細については、[用語集](azure-monitor-glossary.md)をご覧ください。
- データ ストレージのコストを見積もり、測定、管理には、[Azure Monitor コストを見積もる](azure-monitor-costs.md)をご覧ください。
- 問題が発生した場合のヘルプや既知の問題については、[トラブルシューティング ガイド](troubleshoot-azure-monitor.md)をご覧ください。
