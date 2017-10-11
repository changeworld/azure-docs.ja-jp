---
title: "サービス マップ ソリューション デモをペース self |Microsoft ドキュメント"
description: "サービス マップは、Operations Management Suite (OMS) に自動的に Windows および Linux のシステム上のアプリケーション コンポーネントを検出し、サービス間の通信にマップされるソリューションです。  これは、順を追ってを特定し、問題を診断するシミュレートされた web アプリケーションでサービス マップを使用して個人のペース デモです。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: bwren
ms.openlocfilehash: c3548d24c74f8ad865b22d6af3490d0b5cc77a84
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="operations-management-suite-oms-self-paced-demo---service-map"></a>Operations Management Suite (OMS) の self ペースのデモ - サービス マップ
これは、順を追ってを使用して個人のペース デモ、[サービス マップ ソリューション](operations-management-suite-service-map.md)で Operations Management Suite (OMS) を特定し、web アプリケーションのシミュレートされた問題を診断します。  サービス マップは自動的に Windows および Linux のシステム上のアプリケーション コンポーネントを検出し、サービス間の通信をマップします。  また、パフォーマンスの分析と問題の特定に役立つその他の OMS サービスによって収集されたデータを統合します。  使用することもあります[ログ分析検索ログイン](../log-analytics/log-analytics-log-searches.md)に問題の根本原因を特定するために収集されたデータにドリル ダウンします。


## <a name="scenario-description"></a>シナリオの説明
ACME カスタマー ポータル アプリケーションには、パフォーマンスの問題があることの通知を受信しただけです。  ある唯一の情報は、これらの問題に約 4時 00分 am (pst) 今日が開始されたことです。  ポータルは、web サーバーのセット以外に依存しているすべてのコンポーネントの完全かが不明なです。  

## <a name="components-and-features-used"></a>コンポーネントと機能の使用
- [サービス マップ ソリューション](operations-management-suite-service-map.md)
- [ログ分析ログ検索](../log-analytics/log-analytics-log-searches.md)


## <a name="walk-through"></a>順を追ってください。

### <a name="1-connect-to-the-oms-experience-center"></a>1.OMS エクスペリエンス センターへの接続します。
このについてのチュートリアルを使用して、 [Operations Management Suite エクスペリエンス Center](https://experience.mms.microsoft.com/)サンプル データで完全な OMS 環境を提供します。 このリンクで開始し、お客様の情報を提供し、、**洞察と分析**シナリオです。


### <a name="2-start-service-map"></a>2.サービス マップを開始します。
クリックして、サービス マップ ソリューションを起動、**サービス マップ**を並べて表示します。

![サービス マップのタイル](media/operations-management-suite-walkthrough-servicemap/tile.png)

サービス マップ コンソールが表示されます。  左側のウィンドウで、環境内で、サービス マップ エージェントがインストールされているコンピューターの一覧を示します。  この一覧から表示するコンピューターを選択します。

![コンピューターの一覧](media/operations-management-suite-walkthrough-servicemap/computer-list.png)


### <a name="3-view-computer"></a>3.コンピューターを表示します。
分かっている web サーバーおよびと呼ばれる AcmeWFE001 AcmeWFE002、ため、これを開始する適切な場所のように見えます。  をクリックして**AcmeWFE001**です。  これには、AcmeWFE001 とその依存関係のすべてのマップが表示されます。  どのプロセスが、選択したコンピューターとで実行されているを参照してください外部のサービスと通信します。

![Web サーバー](media/operations-management-suite-walkthrough-servicemap/web-server.png)

私たちは、web のパフォーマンスを気にのでアプリケーションをクリックして、 **AcmeAppPool (IIS アプリケーション プール)**プロセスです。  これにより、このプロセスの詳細に表示され、その依存関係が強調表示されます。  

![アプリケーション プール](media/operations-management-suite-walkthrough-servicemap/app-pool.png)


### <a name="4-change-time-window"></a>4.変更の時間枠

聞きました問題開始されている午前 4 時にその時点で何が起こっているかを見てみましょう。 をクリックして**時間の範囲**4時 00分 AM (pst) に時刻を変更し、(現在の日付を保持し、ローカル タイム ゾーン調整) の 20 分間の継続時間。

![時刻の選択](./media/operations-management-suite-walkthrough-servicemap/time-picker.png)


### <a name="5-view-alert"></a>5.ビューのアラート

ここでそれが表示されて、 **acmetomcat**潜在的な問題であるために、依存関係が表示されたら、アラートします。  アラート アイコンをクリックして**acmetomcat**アラートの詳細を表示します。  CPU 使用率が重大ながあるしより詳細に展開できますおは確認できます。  これは、このパフォーマンスの低下の原因は可能性があります。 

![アラート](./media/operations-management-suite-walkthrough-servicemap/alert.png)


### <a name="6-view-performance"></a>6.パフォーマンスの表示

詳しく見てみましょうがある**acmetomcat**です。  上部にあるをクリックしての右**acmetomcat**選択**ロード Server マップ**詳細、このマシンの依存関係を表示します。 これらのパフォーマンス カウンターの疑いを確認するにはもう少し詳しく、いきましょう。  選択、**パフォーマンス**を表示するタブ、[ログ分析によって収集されたパフォーマンス カウンター](../log-analytics/log-analytics-data-sources-performance-counters.md)の時間範囲。  プロセッサおよびメモリ内の定期的な急増を準備中ことを確認できます。

![パフォーマンス](./media/operations-management-suite-walkthrough-servicemap/performance.png)


### <a name="7-view-change-tracking"></a>7.ビュー変更の追跡
この使用率が高い原因が可能性がありますを確認できます。 かどうかを見てみましょう。  をクリックして、**概要**タブです。  これにより、OMS がなどのコンピューターから収集された情報には、接続、重大なアラート、およびソフトウェアの変更が失敗しました。  セクション関心のある最新の情報を既に展開されている必要があります、およびそこに含まれる情報を検査するには、他のセクションを展開することができます。


場合**変更の追跡**が開いていないし、それを展開します。  これによって収集された情報を示しています、[変更の追跡ソリューション](../log-analytics/log-analytics-change-tracking.md)です。  この時間枠中に行われるソフトウェアの変更があったようです。  をクリックして**ソフトウェア**の詳細を取得します。  これには、使用されているリソースを過剰に原因が表示されるように、バックアップ プロセスが午前 4時 00分の直後に、マシンに追加されました。

![Change tracking](./media/operations-management-suite-walkthrough-servicemap/change-tracking.png)



### <a name="8-view-details-in-log-search"></a>8.ログ検索で詳細を表示
これは、ログ分析リポジトリに収集した詳細なパフォーマンス情報を参照してさらに確認おできます。  をクリックして、**アラート**タブを再度クリックしのいずれかで、**高い cpu 使用率**アラートです。  をクリックして**ログ検索で表示**です。  どこで実行できるログ検索ウィンドウを開きます。 この[ログ検索](../log-analytics/log-analytics-log-searches.md)リポジトリに格納されているデータに対してです。  サービス マップは、既にに興味がアラートを取得するうえで queriy に入力されます。  

![ログ検索](./media/operations-management-suite-walkthrough-servicemap/log-search.png)


### <a name="9-open-saved-search"></a>9.保存された検索を開く
このアラートを生成するパフォーマンス コレクションでいくつかの詳細を取得したり、疑いでそのバックアップ プロセス中、問題の原因を確認できるかどうかを見てみましょう。  時間範囲を変更**6 時間**です。  をクリックして**お気に入り**をスクロールして 保存された検索の**サービス マップ**です。  これらは、この分析を具体的に作成したクエリです。  をクリックして**acmetomcat の CPU で上位 5 プロセス**です。

![保存済みの検索](./media/operations-management-suite-walkthrough-servicemap/saved-search.png)


このクエリにプロセッサを消費する上位 5 プロセスの一覧を返します**acmetomcat**です。  ログ検索に使用するクエリ言語の概要を取得するクエリを検査することができます。  他のコンピューター上のプロセスを興味場合は、情報を取得するクエリを変更する可能性があります。

この場合、バックアップ プロセスが一貫して、アプリ サーバーの CPU の約 60% を消費していることがわかります。  この新しいプロセスは、パフォーマンスの問題を担当することが明らかであります。  アプリケーション サーバーの電源をこの新しいバックアップ ソフトウェアを削除するソリューションが明らかになります。  Desired State Configuration (DSC) にこのプロセスは、これらの重要なシステムで実行されないことを確認するポリシーを定義するように Azure Automation で管理実際に利用可能性があります。


## <a name="summary-points"></a>要点
- [サービス マップ](operations-management-suite-service-map.md)のすべてのサーバーとの依存関係を理解していない場合でも、アプリケーション全体のビューを提供します。
- サービス マップは、アプリケーションとその基になるインフラストラクチャの問題を識別するのに役立つその他の OMS ソリューションによって収集されたデータを表示します。
- [ログ検索](../log-analytics/log-analytics-log-searches.md)ログ分析リポジトリに収集された特定のデータにドリル ダウンすることはできます。    

## <a name="next-steps"></a>次のステップ
- 詳細については[サービス マップ](operations-management-suite-service-map.md)です。
- [展開して構成する](operations-management-suite-service-map-configure.md)サービス マップします。
- について学習[ログ分析](../log-analytics/log-analytics-overview.md)するサービス マップを必要と、エージェントが格納されているオペレーション データを格納します。