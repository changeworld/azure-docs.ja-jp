<properties
    pageTitle="ソリューション ギャラリーから Log Analytics ソリューションを追加する | Microsoft Azure"
    description="Log Analytics ソリューションには、特定の問題点に関するメトリックを提供するロジックやビジュアライゼーション、データ取得の規則が集約されています。"
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="add-log-analytics-solutions-from-the-solutions-gallery"></a>ソリューション ギャラリーから Log Analytics ソリューションを追加する

Log Analytics ソリューションには、特定の問題点に関するメトリックを提供する**ロジック**や**ビジュアライゼーション**、**データ取得の規則**が集約されています。 この記事では、Log Analytics でサポートされるソリューションを示し、ソリューション ギャラリーを使用してそれらを追加および削除する方法を説明します。

ソリューションを使用すると、次の点についてより深い知識が得られます。

- 運用上の問題を迅速に調査して解決する
- さまざまな種類のマシン データを収集して関連付ける
- 容量計画や修正プログラムの状況報告、セキュリティ監査などのアクティビティに早い段階で対応する


>[AZURE.NOTE] Log Analytics にはログ検索機能が含まれているため、ログ検索を有効にするためにソリューションをインストールする必要はありません。 ただし、ソリューション ギャラリーからソリューションを追加することで、データのビジュアライゼーション、検索候補、洞察を得ることができます。

ソリューションを追加すると、データはインフラストラクチャのサーバーから収集され、OMS サービスに送信されます。 OMS サービスによる処理には、通常、数分から 1 時間かかります。 サービスによってデータが処理されると、OMS でそのデータを表示できます。

不要になったソリューションは、簡単に削除できます。 ソリューションを削除すると、データは OMS に送信されなくなります。それにより、1 日あたりのクォータがある場合は、クォータに計上されるデータの量が減少します。


## <a name="solutions-supported-by-the-microsoft-monitoring-agent"></a>Microsoft Monitoring Agent でサポートされているソリューション

現時点では、Microsoft Monitoring Agent を使用して OMS に直接接続されているサーバーでは、次のような利用可能なソリューションの多くを使用できます。

- Active Directory の評価
- アラートの管理 (SCOM アラートを含まない)
- マルウェア対策
- 変更の追跡
- セキュリティ
- SQL の評価
- システムの更新

ただし、次のソリューションは、Microsoft Monitoring Agent でサポート *されておらず* 、System Center Operations Manager (SCOM) エージェントが必要です。

- アラートの管理 (SCOM アラートを含む)
- 容量管理
- 構成の評価

SCOM エージェントを Log Analytics に接続する方法については、「 [Operations Manager を Log Analytics に接続する](log-analytics-om-agents.md) 」を参照してくだい。

### <a name="to-add-a-solution-using-the-solutions-gallery"></a>ソリューション ギャラリーを使用してソリューションを追加するには

1. OMS の [概要] ページで、 **[Solutions Gallery]** (ソリューション ギャラリー) タイルをクリックします。    
    ![ソリューション ギャラリー](./media/log-analytics-add-solutions/sol-gallery.png)
2. OMS ソリューション ギャラリー ページでは、利用可能な各ソリューションの詳細を確認できます。 OMS に追加するソリューションの名前をクリックします。
3. 選択したソリューションのページに、ソリューションに関する詳細情報が表示されます。 **[追加]**をクリックします。
4. 追加したソリューションの新しいタイルが、OMS の [概要] ページに表示されます。OMS サービスによってデータが処理されると、そのタイルが使用できるようになります。

## <a name="to-configure-solutions"></a>ソリューションを構成するには
1. ソリューションの中には、構成が必要なものもあります。 たとえば、Automation ソリューション、Azure Site Recovery ソリューション、および Backup ソリューションを使用するには、事前に構成する必要があります。
2. [概要] ページで、ソリューションのタイルをクリックします。  
    ![ソリューションの構成](./media/log-analytics-add-solutions/configure-additional.png)
3. 次に、必要な情報を使用してソリューションを構成し、 **[保存]**をクリックします。  
    ![ソリューションの構成](./media/log-analytics-add-solutions/configure.png)

### <a name="to-remove-a-solution-using-the-solutions-gallery"></a>ソリューション ギャラリーを使用してソリューションを削除するには

1. OMS の [概要] ページで、 **[設定]** タイルをクリックします。
2. [設定] ページの [ソリューション] タブで、削除するソリューションの **[削除]** をクリックします。
3. 確認ダイアログで **[はい]** をクリックすると、ソリューションが削除されます。

## <a name="data-collection-details-for-oms-features-and-solutions"></a>OMS の機能とソリューションにおけるデータ収集の詳細

次の表は、OMS の機能とソリューションにおけるデータの収集手段と、データの収集方法に関する各種情報をまとめたものです。 直接エージェントと SCOM エージェントは基本的には同じものですが、直接エージェントには別途、OMS ワークスペースに接続し、プロキシ経由でルーティングするための機能が備わっています。 SCOM エージェントを使用する場合、OMS とやり取りするためには、その SCOM エージェントを OMS エージェントとしてターゲット指定する必要があります。 この表の SCOM エージェントは、SCOM に接続された OMS エージェントです。 既存の SCOM 環境を OMS に接続する方法については、「[Operations Manager を Log Analytics に接続する](log-analytics-om-agents.md)」を参照してください。

>[AZURE.NOTE] データがどのように OMS に送信されるかは、使用するエージェントの種類によって異なります。また、以下の条件によっても左右されます。

- 使用するのは、直接エージェントか、SCOM に接続された OMS エージェントのどちらか一方です。
- SCOM が必要なとき、ソリューションの SCOM エージェント データは常に SCOM 管理グループを使用して OMS に送信されます。 加えて、SCOM が必要なとき、ソリューションによって使用されるのは SCOM エージェントだけです。
- SCOM が不要であり、なおかつ管理グループを使用して SCOM エージェント データが OMS に送信されることが以下の表に示されているときは、SCOM エージェント データが常に管理グループを使用して OMS に送信されます。 直接エージェントは、管理グループをバイパスして、そのデータを直接 OMS に送信します。
- SCOM エージェント データの送信に管理グループが使用されないときは、データが直接 OMS に送信され、管理グループはバイパスされます。


|データ型| プラットフォーム | 直接エージェント | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
|---|---|---|---|---|---|---|---|
|AD 評価|Windows|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|  7 日|
|AD レプリケーションの状態|Windows|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 日|
|アラート (Nagios)|Linux|![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|着信時|
|アラート (Zabbix)|Linux|![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|1 分|
|アラート (Operations Manager)|Windows|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png)|3 分|
|マルウェア対策|Windows|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)| 時間単位|
|容量管理|Windows|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)| 時間単位|
|変更の追跡|Windows|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)| 時間単位|
|変更の追跡|Linux|![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|時間単位|
|構成の評価 (従来の Advisor)|Windows|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)| 1 日 2 回|
|ETW|Windows|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 分|
|IIS ログ|Windows|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 分|
|キー コンテナー|Windows|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 分|
|ネットワーク アプリケーション ゲートウェイ|Windows|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 分|
|ネットワーク セキュリティ グループ|Windows|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 分|
|Office 365|Windows|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|通知時|
|パフォーマンス カウンター|Windows|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|スケジュールに基づく頻度 (間隔は 10 秒以上)|
|パフォーマンス カウンター|Linux|![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|スケジュールに基づく頻度 (間隔は 10 秒以上)|
|Service Fabric|Windows|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 分|
|SQL の評価|Windows|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)| 7 日|
|SurfaceHub|Windows|![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|着信時|
|Syslog|Linux|![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 分 (Azure Storage からデータを収集する場合) または着信時 (エージェントからデータを収集する場合)|
|システムの更新|Windows|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png)| 1 日に少なくとも 2 回、更新プログラムのインストールの 15 分後|
|Windows セキュリティ イベント ログ|Windows|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)| 10 分 (Azure Storage の場合) または着信時 (エージェントの場合)|
|Windows ファイアウォール ログ|Windows|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)| 着信時|
|Windows イベント ログ|Windows|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)| 1 分 (Azure Storage からデータを収集する場合) または着信時 (エージェントからデータを収集する場合)|
|送信データ|Windows (2012 R2/8.1 以降)|![はい](./media/log-analytics-add-solutions/oms-bullet-green.png)|![あり](./media/log-analytics-add-solutions/oms-bullet-green.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)|![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png)|![なし](./media/log-analytics-add-solutions/oms-bullet-red.png)| 1 分ごと|

## <a name="log-analytics-preview-solutions-and-features"></a>Log Analytics プレビューのソリューションと機能

サービスを実行し、DevOps の実践に従うことで、お客様と協力して、機能やソリューションを開発できます。

プライベート プレビューの期間中は、フィードバックをもらって改良するために、少数のお客様に機能やソリューションの初期実装へのアクセスを提供します。 この初期実装には、最低限の機能や運用上の機能が備わっています。

この目的は、効果的な機能と効果的でない機能を見極めることができるように、さまざま操作をすばやく試すことです。 プライベート プレビューを利用するお客様からのフィードバックでパブリック プレビューの準備ができたと判断できるまで、このプロセスを繰り返します。

パブリック プレビューでは、すべてのユーザーが機能またはソリューションを利用できるようにし、フィードバックの数を増やしてスケールと効率性を検証します。 このフェーズでは、次のようになります。

- プレビュー機能は、[設定] タブに表示され、すべてのユーザーが有効にできます
- プレビュー ソリューションは、ギャラリーまたは公開されたスクリプトを使用して追加できます

### <a name="what-should-i-know-about-preview-features-and-solutions?"></a>プレビューの機能とソリューションについて知っておくべきこと

Microsoft では、新しい機能やソリューションに力を入れており、それらをお客様と協力して開発できることを嬉しく思っています。

ただし、プレビューの機能とソリューションはすべてのユーザーに適しているわけではありません。そのため、プライベート プレビューへの参加を依頼する前やパブリック プレビューを有効にする前に、開発中の機能やソリューションを利用して問題ないかどうかを確認してください。

ポータルからプレビュー機能を有効にする場合は、機能がプレビュー段階であることを示す警告が表示されます。

#### <a name="for-both-*private*-and-*public*-preview"></a>"*プライベート*" プレビューと "*パブリック*" プレビューの両方について

パブリックとプライベートの両方のプレビューに当てはまる項目を次に示します。

- 必ずしも適切に動作するとは限りません。
  - 問題は、少し不快に感じる程度ものから、まったく動作しないものまでさまざまです。
- プレビューによってシステムや環境が悪影響を受ける可能性があります。
  - OMS で使用しているシステムに悪影響が出ないように努めていますが、予期しない問題が発生することがあります。
- データの損失や破損が発生する可能性があります。
- 問題のトラブルシューティングに役立つ、診断ログなどのデータの収集を依頼する場合があります。
- 機能やソリューションは (一時的または永続的に) 削除される可能性があります。
  - プレビュー期間中に得た情報に基づいて、機能またはソリューションをリリースしない決定を下す場合があります。
- プレビューが動作しない構成やテストされていない構成もあるため、次のものが制限される可能性があります。
  - 使用できるオペレーティング システム (たとえば、プレビュー段階では、機能が Linux のみに適用される場合があります)
  - 使用できるエージェントの種類 (MMA、SCOM) (たとえば、プレビュー段階では、機能が SCOM で動作しない場合があります)  
- プレビューのソリューションと機能は、サービス レベル アグリーメントの対象外です。
- プレビュー機能を利用すると、利用料金が発生します。
- 機能やソリューションを活用するために必要な機能が存在しないか、不完全な場合があります。
- 機能やソリューションは、リージョンによっては利用できない場合があります。
- 機能やソリューションがローカライズされていない場合があります。
- 機能やソリューションを利用できるお客様やデバイスの数に制限がある場合があります。
- 構成を実行したり、ソリューションや機能を有効にしたりするスクリプトの使用が必要になる場合があります。
- ユーザー インターフェイス (UI) は不完全で、日々変更される可能性があります。
- パブリック プレビューが運用システムや重要なシステムに適していない場合があります。

#### <a name="for-*private*-preview"></a>*プライベート* プレビューについて

プライベート プレビューには、上記の項目に加え、次の固有の項目も当てはまります。

- 機能やソリューションを改善するために、利用に関するフィードバックをお寄せください
- アンケート、電話、電子メールを使用して、フィードバックを依頼する場合があります。
- 必ずしも適切に動作するとは限りません。
- 参加するために機密保持契約 (NDA) が必要な場合や機密コンテンツが含まれている場合があります。
  - ブログ、ツイート、サード パーティとのやり取りを行う前に、プレビューを担当しているプログラム マネージャーに確認を取り、開示に関する制限を把握してください。
- 運用システムまたは重要なシステムで実行しないでください。


### <a name="how-do-i-get-access-to-private-preview-features-and-solutions?"></a>プライベート プレビューの機能やソリューションにアクセスする方法

Microsoft では、プレビューに応じたさまざまな方法でお客様をプライベート プレビューに招待します。

- 毎月のお客様アンケートに答えたり、追跡調査を許可したりすることで、プライベート プレビューへの招待を受ける機会が増えます。
- Microsoft アカウント チームが推薦する場合があります。
- Twitter の [msopsmgmt](https://twitter.com/msopsmgmt)
- コミュニティ イベントで共有された詳細に基づいてサインアップできます。ミートアップ、カンファレンス、オンライン コミュニティで見つけてください。


## <a name="next-steps"></a>次のステップ

- [ログを検索](log-analytics-log-searches.md) して、ソリューションによって収集された詳細情報を確認します。



<!--HONumber=Oct16_HO2-->


