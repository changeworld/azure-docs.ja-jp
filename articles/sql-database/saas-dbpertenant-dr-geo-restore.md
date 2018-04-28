---
title: Azure SQL Database の geo 冗長バックアップを使用した SaaS アプリのディザースター リカバリー | Microsoft Docs
description: 停止が発生した場合に Azure SQL Database の geo 冗長バックアップを使用して、マルチテナント SaaS アプリを復旧する方法について説明します
keywords: SQL データベース チュートリアル
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/16/2018
ms.author: ayolubek
ms.openlocfilehash: a677e6eb583e293f83df824804aa4cd6f8f5d778
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2018
---
# <a name="recover-a-multi-tenant-saas-application-using-geo-restore-from-database-backups"></a>geo リストアを使用してデータベースのバックアップからマルチテナント SaaS アプリケーションを復旧する

このチュートリアルでは、テナント単位データベース モデルを使用して実装されているマルチテナント SaaS アプリケーションの完全なディザスター リカバリー シナリオを見ていきます。 自動的に保守されている geo 冗長バックアップのカタログおよびテナント データベースを代替復旧リージョンに復元するには、[_geo リストア_](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-recovery-using-backups)を使用します。 停止が解決したら、[_geo レプリケーション_](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview)を使用して、変更されたデータベースを元のリージョンに復帰します。

![geo-restore-architecture](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

geo リストアは、SQL Database 向けの最もコストが低いディザスター リカバリー ソリューションです。  ただし、geo 冗長バックアップから復元すると、最大 1 時間のデータ損失が発生する可能性があります。各データベースのサイズによっては、長時間かかることがあります。 **可能な限り低い RPO と RTO でアプリケーションを復旧するには、geo リストアではなく geo レプリケーションを使用します**。

このチュートリアルでは、復元ワークフローと復帰ワークフローの両方について説明します。 学習内容は次のとおりです。
> [!div class="checklist"]

>* データベースとエラスティック プールの構成情報をテナントのカタログに同期する
>* アプリケーション、サーバー、プールから構成されるミラー イメージ環境を "復旧" リージョンに設定する    
>* _geo リストア_を使用してカタログおよびテナント データベースを復旧する
>* 停止が解決したら、_geo レプリケーション_を使用して、テナント カタログと変更されたテナント データベースを復帰します。
>* 各データベースが復元 (または復帰) されるときにカタログを更新して、各テナントのデータベースのアクティブなコピーがある現在の場所を追跡する
>* 待機時間を減らすため、アプリケーションとテナント データベースが常に同じ Azure リージョンに併置されるようにする  
 

このチュートリアルを始める前に、次の前提条件を満たしておく必要があります。
* Wingtip Tickets SaaS テナント単位データベース アプリをデプロイする。 5 分未満でデプロイするには、[テナントごとのデータベースを使用する Wingtip Tickets SaaS アプリケーションのデプロイと探索](saas-dbpertenant-get-started-deploy.md)に関する記事を参照してください。  
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>geo リストア復旧パターンの概要

ディザスター リカバリー (DR) は、コンプライアンス上の理由またはビジネス継続性のため、多くのアプリケーションにとって重要な考慮事項です。 長時間にわたるサービス停止が発生しても、適切に準備された DR 計画があればビジネスの中断を最小限にできます。 geo リストアに基づく DR 計画では、いくつかの目標を達成する必要があります。
 * テナント データベースの復元に利用できるように、選択した復旧リージョン内に、すべての必要な容量を可能な限り早く確保します。
 * 元のプールとデータベースの構成を反映するミラー イメージ復旧環境を確立します 
 * 元のリージョンがオンラインに戻った場合に、復元プロセスの途中で取り消すことができるようにする必要があります。
 * 新しいテナントのオンボードをできるだけ早く再開できるように、テナントのプロビジョニングをすばやく有効にします  
 * 優先順でテナントが復元されるように最適化します
 * 可能であれば、並列で手順を実行して、できる限り早くテナントがオンラインになるように最適化します
 * 停止に対する回復力があり、再起動可能かつべき等であるようにします
 * 停止が解決したら、テナントに与える影響を最小限に抑えて元のリージョンにデータベースを復帰します。  

> [!Note]
> アプリケーションは、アプリケーションが展開されたリージョンの_ペア リージョン_に復旧されます。 詳細については、[Azure のペアになっているリージョン](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions)に関するページを参照してください。   



このチュートリアルでは、Azure SQL Database と Azure プラットフォームの以下の機能を使って、これらの課題に対応します。

* [Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)。すべての必要な容量を可能な限り早く確保します。 復旧リージョン内に元のサーバーとエラスティック プールのミラー イメージをプロビジョニングするために、Azure Resource Manager テンプレートを使用します。 新しいテナントをプロビジョニングするために、別のサーバーとプールも作成されます。 
* [Elastic Database Client Library](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-elastic-database-client-library) (EDCL)。テナント データベース カタログを作成および保守します。  カタログは、定期的に更新されるプールとデータベース構成情報を含むように拡張されています。
* EDCL の[シャード管理復旧機能](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager)。復旧と復帰の間に、カタログに含まれるデータベースの場所エントリを保守します。  
* [geo リストア](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery)。自動的に保守されている geo 冗長バックアップのカタログおよびテナント データベースを復旧します。 
* テナントの優先順で送信される[非同期の復元操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。プールが過負荷にならないように、システムによってプールごとにキューへ格納され、バッチ処理されます。 これらの操作は、必要に応じて実行前または実行中に取り消すことができます。    
* [geo レプリケーション](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)。停止の解決後に元のリージョンにデータベースを復帰します。 geo レプリケーションを使用すると、データの損失を起こさず、テナントに与える影響を最小限に抑えることができます。
* [SQL Server の DNS エイリアス](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)。カタログの場所に関係なく、カタログ同期プロセスがアクティブなカタログに接続できるようにします。  

## <a name="get-the-disaster-recovery--scripts"></a>ディザスター リカバリー スクリプトを取得する 

このチュートリアルで使用されている DR スクリプトは、[Wingtip Tickets SaaS のテナントごとのデータベースの GitHub リポジトリ](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)で入手できます。 Wingtip Tickets 管理スクリプトをダウンロードしてブロック解除する手順に関する[一般的なガイダンス](saas-tenancy-wingtip-app-guidance-tips.md)を参照してください。
> [!IMPORTANT]
> すべての Wingtip Tickets 管理スクリプトと同様に、DR スクリプトはサンプル品質なので、運用環境では使わないでください。   

## <a name="review-the-healthy-state-of-the-application"></a>アプリケーションの正常性状態を確認する
復旧プロセスを始める前に、アプリケーションの通常の正常な状態を確認します。
1. Web ブラウザーで、Wingtip Tickets イベント ハブ (http://events.wingtip-dpt.&lt;ユーザー&gt;.trafficmanager.net - &lt;ユーザー&gt; は実際の展開でのユーザーの値に置き換えます) を開きます。
    * ページの下部までスクロールし、フッターでカタログ サーバー名と場所を確認します。  場所は、アプリを展開したリージョンです。    
        ヒント: マウス ポインターをその場所に置くと、ディスプレイが拡大表示されます。

    ![元のリージョンのイベント ハブの正常状態](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

1. Contoso Concert Hall テナントをクリックして、そのイベント ページを開きます。
    * フッターでテナントのサーバー名を確認します。 場所は、カタログ サーバーの場所と同じです。

    ![Contoso Concert Hall の元のリージョン](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png)    
1. [Azure Portal](https://portal.azure.com) で、アプリが展開されているリソース グループを確認して開きます。
    * アプリ サービス コンポーネントと SQL Database サーバーが展開されているリソースとリージョンを確認します。

## <a name="sync-tenant-configuration-into-catalog"></a>テナントの構成をカタログに同期する

このタスクでは、サーバー、エラスティック プール、およびデータベースの構成をテナント カタログに同期するプロセスを開始します。  この情報は、復旧リージョンでミラー イメージ環境を構成するために後で使用されます。

> [!IMPORTANT]
> わかりやすくするため、これらのサンプルでは、同期プロセスおよびその他の実行時間の長い復旧プロセスと復帰プロセスは、クライアント ユーザーのログインで実行されるローカル Powershell ジョブまたはセッションとして実装されています。 ログイン時に発行される認証トークンは、数時間で有効期限が切れ、ジョブは失敗するようになります。 運用のシナリオでは、実行時間の長いプロセスは、サービス プリンシパルで実行される、何らかの信頼性の高い Azure サービスとしてを実装する必要があります。 「[Azure PowerShell を使用して資格情報でのサービス プリンシパルを作成する](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)」を参照してください。 

1. _PowerShell ISE_ で、...\Learning Modules\UserConfig.psm1 ファイルを開きます。 10 行目と 11 行目の `<resourcegroup>` および `<user>` は、アプリを展開したときに使った値に置き換えます。  ファイルを保存します。

2. *PowerShell ISE* で、...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 スクリプトを開きます。
    *  このチュートリアルでは、この PowerShell スクリプトの各シナリオを実行するので、このファイルは開いたまましてください。

3. 次のように設定します。
    * **$DemoScenario = 1**: テナント サーバーとプールの構成をカタログに同期するバックグラウンド ジョブを開始します

3. **F5** キーを押して、同期スクリプトを実行します。 
    *  この情報は、後で復旧リージョンにサーバー、プール、およびデータベースのミラー イメージを作成するために使用されます。  
![同期プロセス](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

PowerShell ウィンドウはバックグラウンドで実行させたままにして、チュートリアルの残りの部分を続けます。

> [!Note]
> 同期プロセスは、DNS エイリアスを使ってカタログに接続します。 このエイリアスは、復元および復帰の間に、アクティブなカタログを指すように変更されます。 同期プロセスは、復旧リージョンで行われたデータベースまたはプールの構成の変更を使って、カタログを最新の状態に維持します。  復帰では、これらの変更が元のリージョンの同等のリソースに適用されます。

## <a name="geo-restore-recovery-process-overview"></a>geo リストア復旧プロセスの概要

geo リストア復旧プロセスでは、アプリケーションを展開し、バックアップのデータベースを復旧リージョンに復元します。

復旧プロセスでは、次の処理が行われます。

1. 元のリージョンで Web アプリの Traffic Manager エンドポイントを無効にします。 エンドポイントを無効にすることで、復旧の間に元のリージョンがオンラインになったときに、ユーザーが無効な状態のアプリに接続するのを防ぎます。

1. 復旧リージョンに復旧カタログ サーバーをプロビジョニングし、カタログ データベースの geo リストアを実行し、復元されたカタログ サーバーを指すように別名 _activecatalog_ を更新します。  
    * カタログの別名を変更すると、カタログ同期プロセスは常にアクティブなカタログに同期されます。

1. 復元する前に、復旧カタログの既存のすべてのテナントをオフラインとマークして、テナント データベースにアクセスできないようにします。

1. 復旧リージョンにアプリのインスタンスをプロビジョニングし、復旧リージョン内の復元されたカタログを使用するように構成します。
    * 待機時間を最小限に抑えるため、サンプル アプリは同じリージョン内のテナント データベースに常に接続するように設計されています。

1. 新しいテナントがプロビジョニングされるサーバーとエラスティック プールをプロビジョニングします。 このようなリソースを作成して、新しいテナントのプロビジョニングが既存のテナントの復旧を妨げないようにします。

1. 復旧リージョン内の新しいテナント データベースのサーバーを指すように、別名 new-tenant を更新します。 この別名を変更することで、新しいテナントのデータベースが復旧リージョンでプロビジョニングされるようにします。
        
1. テナント データベースを復元するために、復旧リージョン内にサーバーとエラスティック プールをプロビジョニングします。 これらのサーバーとプールは、元のリージョン内の構成のミラー イメージです。  プロビジョニング プールは、すべてのデータベースを復元するために必要な容量を事前に確保します。
    * あるリージョンで停止が発生すると、ペアのリージョンで利用できるリソースに大きな負荷がかかる可能性があります。  DR の geo リストアに依存している場合は、迅速にリソースを確保することをお勧めします。 特定のリージョン内でアプリケーションを復旧する必要がある場合は、geo レプリケーションの使用を検討してください。 

1. 復旧リージョンで Web アプリの Traffic Manager エンドポイントを有効にします。 このエンドポイントを有効にすると、アプリケーションは新しいテナントをプロビジョニングできるようになります。 この段階では、既存のテナントはまだオフラインです。

1. 要求のバッチを送信し、優先順位に従ってデータベースを復元します。 
    * バッチは、データベースがすべてのプールに並列に復元されるように編成されています。  
    * 復元要求は非同期に送信されるので、すばやく送信され、実行のキューは各プールに格納されます。
    * 復元要求はすべてのプールで並列に処理されるので、重要なテナントを多数のプールに分散する場合に適しています。 

1. SQL Database サービスを監視して、データベースを復元するタイミングを判断します。 テナント データベースが復元されると、カタログでオンラインとマークされ、テナント データベースの rowversion の合計が記録されます。 
    * テナントがカタログでオンラインとマークされるとすぐに、アプリケーションはテナント データベースにアクセスできるようになります。
    * テナント データベースでの rowversion 値の合計が、カタログに格納されます。 この合計はフィンガープリントとして機能し、復帰プロセスはこの値を使うことにより、データベースが復旧リージョンで更新されているかどうかを判断できます。      

## <a name="run-the-recovery-script"></a>復旧スクリプトを実行する

> [!IMPORTANT]
> このチュートリアルでは、geo 冗長バックアップからデータベースを復元します。 geo 冗長バックアップは通常 10 分以内に使用できるようになりますが、使用できるようにまでに最大 1 時間かかる場合があります。 使用できるようになるまで、スクリプトは一時停止されます。  この間に休憩を取りましょう。

それでは、アプリケーションが展開されているリージョンで停止が発生したものと想定して、復旧スクリプトを実行します。

1. *PowerShell ISE* で、...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 スクリプトを開き、次の値を設定します。
    * **$DemoScenario = 2**: geo 冗長バックアップから復元することで、アプリを復旧リージョンに復旧します

1. **F5** キーを押して、スクリプトを実行します。  
    * 新しい PowerShell ウィンドウでスクリプトが開き、並列に実行される一連の PowerShell ジョブが開始されます。  これらのジョブで、サーバー、プール、およびデータベースが復旧リージョンに復元されます。 
    * 復旧リージョンは、アプリケーションを展開した Azure リージョンに関連付けられている_ペア リージョン_です。 詳細については、[Azure のペアになっているリージョン](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions)に関するページを参照してください。 

1. PowerShell ウィンドウで復旧プロセスの状態を監視します。

    ![復旧プロセス](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

>復旧ジョブのコードを調べるには、...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\RecoveryJobs フォルダーにある PowerShell スクリプトを確認します。

## <a name="review-the-application-state-during-recovery"></a>復旧中にアプリケーションの状態を確認する
Traffic Manager でアプリケーション エンドポイントが無効になっている間は、アプリケーションを使用できません。 カタログが復元され、すべてのテナントはオフラインとマークされます。  次に復旧リージョン内のアプリケーション エンドポイントが有効になり、アプリケーションはオンラインに戻ります。 アプリケーションは使用できますが、データベースが復元されるまで、イベント ハブ内のテナントはオフラインと表示されます。 オフラインのテナント データベースを処理できるようにアプリケーションを設計することが重要です。

1. カタログ データベースが復旧された後、テナントがオンラインに戻る前に、Web ブラウザーで Wingtip Tickets イベント ハブを更新します。
    * フッターで、カタログ サーバー名に _-recovery_ というサフィックスが付いていて、復旧リージョンに存在することを確認します。
    * まだ復元されていないテナントはオフラインとしてマークされていて、選択できないことを確認します。   
 
    ![復旧プロセス](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

    * テナントがオフラインの間にテナントの [イベント] ページを直接開いた場合は、"テナントはオフライン" であることを示す通知が表示されます。 たとえば、Contoso Concert Hall がオフラインのときに、http://events.wingtip-dpt.&lt;ユーザー&gt;.trafficmanager.net/contosoconcerthall を開いてみます ![復旧プロセス](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>復旧リージョン内に新しいテナントをプロビジョニングする
テナント データベースが復元される前であっても、復旧リージョン内に新しいテナントをプロビジョニングできます。 復旧リージョン内にプロビジョニングされた新しいテナント データベースは、復旧されたデータベースに後で復帰されます。   

1. *PowerShell ISE* で、...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 スクリプトを開き、次のプロパティを設定します。
    * **$DemoScenario = 3**: 復旧リージョンで新しいテナントをプロビジョニングします

1. **F5** キーを押して、スクリプトを実行します。 

1. プロビジョニングが完了すると、Hawthorn Hall のイベント ページがブラウザーで開きます。 
    * Hawthorn Hall データベースが復旧リージョン内にあることを確認します。
    ![復旧リージョン内にプロビジョニングされている Hawthorn Hall](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

1. ブラウザーで、Wingtip Tickets イベント ハブ ページを更新して、Hawthorn Hall が含まれることを確認します。 
    * 他のテナントの復元を待たずに Hawthorn Hall をプロビジョニングした場合、他のテナントはまだオフラインになっている可能性があります。

## <a name="review-the-recovered-state-of-the-application"></a>アプリケーションの復旧された状態を確認する

復旧プロセスが完了すると、アプリケーションとすべてのテナントは、復旧リージョンで完全に機能するようになります。 

1. すべてのテナントが復旧されたことが PowerShell コンソール ウィンドウの表示で示されたら、イベント ハブを更新します。  新しいテナント Hawthorn Hall も含めて、すべてのテナントがオンラインと表示されます。

    ![イベント ハブの復旧されたテナントと新しいテナント](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

1. Contoso Concert Hall をクリックし、[イベント] ページを開きます。
    * フッターで、復旧リージョンの復旧サーバー上にデータベースがあることを確認します。

    ![復旧リージョン内の Contoso](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

1. [Azure Portal](https://portal.azure.com) で、リソース グループの一覧を開きます。  
    * 展開したリソース グループに加えて、_-recovery_ というサフィックスが付いた復旧リソース グループが表示されることを確認します。  復旧リソース グループには、復旧プロセスの間に作成されたすべてのリソースと、停止中に作成された新しいリソースが含まれます。  

1. 復旧リソース グループを開き、次の項目を確認します。
    * サフィックス _-recovery_ が付いた、catalog サーバーと tenants1 サーバーの復旧バージョン。  これらのサーバー上の復元されたカタログ データベースとテナント データベースはすべて、元のリージョンで使われていた名前のままです。

    * _tenants2-dpt-&lt;ユーザー&gt;-recovery_ SQL サーバー。  このサーバーは、停止中の新しいテナントのプロビジョニングに使われます。
    *   _events-wingtip-dpt-&lt;復旧リージョン&gt;-&lt;ユーザー>_ という名前の App Service。これは、Events アプリの復旧インスタンスです。 

    ![復旧リージョン内の Contoso](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png)   
    
1. _tenants2-dpt-&lt;ユーザー&gt;-recovery_ SQL サーバーを開きます。  データベース _hawthornhall_ とエラスティック プール _Pool1_ が含まれることを確認します。  _hawthornhall_ データベースは、_Pool1_ エラスティック プール内のエラスティック データベースとして構成されています。

## <a name="change-tenant-data"></a>テナントのデータを変更する 
このタスクでは、復元されたテナント データベースの 1 つを更新します。 復帰プロセスで、変更された復元済みのデータベースが元のリージョンにコピーされます。 

1. ブラウザーで、Contoso Concert Hall のイベント一覧を探し、イベントをスクロールして、最後のイベント _Seriously Strauss_ を書き留めます。

1. *PowerShell ISE* で、...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 スクリプトを開き、次の値を設定します。
    * **$DemoScenario = 4**: 復旧リージョン内のテナントからイベントを削除します
1. **F5** キーを押してスクリプトを実行します。
1. Contoso Concert Hall のイベント ページ (http://events.wingtip-dpt.&lt;ユーザー&gt;.trafficmanager.net/contosoconcerthall) を更新し、イベント Seriously Strauss がないことを確認します。

チュートリアルのこの時点で、アプリケーションを復旧し、復旧リージョン内で実行しています。  新しいテナントは復旧リージョン内にプロビジョニングし、復元されたテナントのいずれかのデータを変更しています。  

> [!Note]
> このサンプルの他のチュートリアルは、復旧状態のアプリを使用して実行するように設計されていません。 他のチュートリアルを調べる場合は、まずアプリケーションを復帰してください。

## <a name="repatriation-process-overview"></a>復帰プロセスの概要

復帰プロセスでは、停止が解決した後に、アプリケーションとそのデータベースを元のリージョンに戻します。

![geo リストアの復帰](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 


このプロセスの内容は次のとおりです。

1. 進行中の復元アクティビティを終了し、未処理または実行中のデータベース復元要求を取り消します。

1. 停止後に変更されていない元のリージョンのテナント データベースを再アクティブ化します。  これには、まだ復旧されていないデータベースと、復旧され、その後に変更されなかったデータベースが含まれます。 再アクティブ化されたデータベースは、テナントが最後にアクセスしたデータベースとまったく同じです。

1. 新しいテナント サーバーとエラスティック プールのミラー イメージを元のリージョンにプロビジョニングします。 このアクションが完了すると、このサーバーを指すように別名 new-tenant が更新されます。 別名を更新すると、復旧リージョンではなく、元のリージョン内で新しいテナントのオンボードが実行されます。

1. geo レプリケーションを使用して、復旧リージョンから元のリージョンにカタログを移動します。

1. 停止中に復旧リージョンに加えられた変更と一致するように、元のリージョンのプール構成を更新します。

1. 停止中に作成された新しいデータベースをホストするために、必要なサーバーとプールを作成します。

1. geo レプリケーションを使用して、復元後に更新された復元済みテナント データベースと、停止中にプロビジョニングされたすべての新しいテナント データベースを復帰します。 

1. 復元プロセス中に復旧リージョン内に作成されたリソースをクリーンアップします。

復帰する必要のあるテナント データベース数を制限するために、手順 1 から 3 はすぐに実行されます。  

手順 4 は、停止中に復旧リージョン内のカタログが変更された場合にのみ実行されます。 新しいテナントが作成された場合、または復旧リージョン内のいずれかのデータベースまたはプール構成が変更された場合、カタログは更新されます。

手順 7 で、テナントの中断を最小限に抑え、データが失われないことが重要です。 この目標を達成するために、このプロセスでは _geo レプリケーション_を使用します。

各データベースの geo レプリケーションが実行される前に、元のリージョン内の対応するデータベースは削除されます。 削除後に復旧リージョン内のデータベースの geo レプリケーションが実行され、元のリージョン内にセカンダリ レプリカが作成されます。 レプリケーションが完了すると、カタログ内のテナントはオフラインとマークされ、復旧リージョン内のデータベースに対するすべての接続は切断されます。 次にデータベースはフェールオーバーされ、すべての保留中のトランザクションはセカンダリで処理されるので、データは失われません。 フェールオーバー時に、データベース ロールは逆になります。  元のリージョン内のセカンダリはプライマリ読み取り/書き込みデータベースになり、復旧リージョン内のデータベースは読み取り専用のセカンダリになります。 カタログ内のテナント エントリは、元のリージョン内のデータベースを指すように更新され、テナントはオンラインとマークされます。  この時点でデータベースの復帰は完了です。 

接続が切断されたときに自動的に再接続するように、再試行ロジックを使用してアプリケーションを作成する必要があります。  接続を仲介するカタログを使用して再接続すると、元のリージョン内の復帰したデータベースに接続されます。 通常、この短い切断が認識されることはありませんが、データベースの復帰は勤務時間外に行うのがよいかもしれません。 

データベースが復帰したら、復旧リージョン内のセカンダリ データベースを削除できます。 元のリージョン内のデータベースは、DR 保護のために geo リストアに再び依存するようになります。

手順 8 では、復旧サーバーとプールを含む復旧リージョン内のリソースが削除されます。

## <a name="run-the-repatriation-script"></a>復帰スクリプトを実行する
停止が解決したものとして、復帰スクリプトを実行します。

このチュートリアルに従っている場合、Fabrikam Jazz Club と Dogwood Dojo は変更されていないため、元のリージョン内ですぐに再アクティブ化されます。 次に、新しいテナントの Hawthorn Hall と、Contoso Concert Hall (変更されているため) が復帰されます。 このスクリプトでは、Hawthorn Hall がプロビジョニングされたときに更新されたカタログも復帰されます。
  
1. *PowerShell ISE* で、...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 スクリプトを開きます。

1. PowerShell インスタンスでカタログ同期プロセスがまだ実行していることを確認します。  必要な場合は、次のように設定して再起動します。
    * **$DemoScenario = 1**: テナント サーバー、プール、およびデータベースの構成情報のカタログへの同期を開始します
    * **F5** キーを押して、スクリプトを実行します。
1.  復帰プロセスを開始するには、次のように設定します。
    * **$DemoScenario = 5**: アプリを元のリージョンに復帰します
    * **F5** キーを押して、新しい PowerShell ウィンドウで復旧スクリプトを実行します。  復帰には数分かかり、PowerShell ウィンドウで監視できます。
1. スクリプトが実行している間に、イベント ハブのページ (http://events.wingtip-dpt.&lt;ユーザー&gt;.trafficmanager.net) を更新します。
    * すべてのテナントがオンラインであり、このプロセス全体を通じてアクセスできることを確認します。
1. Fabrikam Jazz Club をクリックして開きます。 このテナントを変更しなかった場合は、フッターで、サーバーが元のサーバーに戻っていることを確認します。
1. Contoso Concert Hall のイベント ページを開くか更新して、まずフッターでデータベースが _-recovery_ サーバー上にまだあることを確認します。  
1. 復帰プロセスが完了したら、Contoso Concert Hall のイベント ページを更新し、データベースが元のリージョン内にあることを確認します。
1. イベント ハブをもう一度更新して Hawthorn Hall を開き、そのデータベースも元のリージョン内にあることを確認します。 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>復帰後に復旧リージョンのリソースをクリーンアップする
復帰が完了したら、復旧リージョン内のリソースを削除することができます。 

> [!IMPORTANT]
> これらのリソースは、関連するすべての課金を止めるためにすぐに削除します。

復元プロセスで、復旧リソース グループ内にすべての復旧リソースが作成されます。 クリーンアップ プロセスで、このリソース グループは削除され、カタログからリソースへのすべての参照が削除されます。 

1. *PowerShell ISE* で、...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 スクリプトを開き、次のように設定します。
    * **$DemoScenario = 6**: 復旧リージョンから、古いリソースを削除します

1. **F5** キーを押して、スクリプトを実行します。

スクリプトをクリーンアップすると、アプリケーションは開始された場所に戻ります。  この時点で、スクリプトをもう一度実行するか、他のチュートリアルを試すことができます。

## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>アプリとデータベースが併置されるようにアプリケーションを設計する 
このアプリケーションは、テナント データベースと同じリージョン内のインスタンスから常に接続するように設計されています。 このように設計すると、アプリケーションとデータベースの間の待機時間が減少します。 この最適化では、データベースとアプリの間の対話の方が、ユーザーとアプリの間の対話より頻繁に行われるものと想定されています。  

復帰の過程で、しばらくの間、テナント データベースが復旧リージョンと元のリージョンに分散することがあります。  アプリは、データベースごとに、テナント サーバーの名前で DNS 参照を行って、データベースが存在するリージョンを調べます。 SQL Database では、サーバー名は別名です。 別名のサーバー名には、リージョンの名前が含まれています。 アプリケーションとデータベースのリージョンが異なる場合、アプリケーションはデータベース サーバーと同じリージョン内のインスタンスにリダイレクトします。  データベースと同じリージョン内のインスタンスにリダイレクトすることで、アプリとデータベース間の待機時間を最小限に抑えられます。  

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。
> [!div class="checklist"]

>* テナント カタログを使用して定期的に更新された構成情報を保持し、別のリージョン内にミラー イメージの復旧環境を作成できるようにする
>* geo リストアを使用して Azure SQL Database を復旧リージョンに復旧する
>* 復元されたテナント データベースの場所を反映するようにテナント カタログを更新する  
>* DNS エイリアスを使用して、再構成することなくアプリケーションをテナント カタログに接続できるようにする
>* 停止が解決した後に、geo レプリケーションを使用して、復旧したデータベースを元のリージョンに復帰する

次は、「[Disaster recovery for a multi-tenant SaaS application using database geo-replication](saas-dbpertenant-dr-geo-replication.md)」(データベースの geo レプリケーションを使用したマルチテナント SaaS アプリケーションのディザスター リカバリー) を試して、geo レプリケーションを使用して大規模なマルチテナント アプリケーションを復旧するために必要な時間を大幅に短縮する方法を学んでください。

## <a name="additional-resources"></a>その他のリソース

* [Wingtip SaaS アプリケーションに基づく作業のための追加のチュートリアル](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
