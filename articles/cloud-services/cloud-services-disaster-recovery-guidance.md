<properties
	pageTitle="Azure Cloud Services に影響を与える Azure サービスの中断が発生した場合の対処方法 | Microsoft Azure"
	description="Azure Cloud Services に影響を与える Azure サービスの中断が発生した場合の対処方法について説明します。"
	services="cloud-services"
	documentationCenter=""
	authors="kmouss"
	manager="drewm"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="cloud-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="kmouss;aglick"/>

#Azure Cloud Services に影響を与える Azure サービスの中断が発生した場合の対処方法

Microsoft では、必要なときにサービスがいつでも使用できるように取り組んでいますが、やむを得ない事情により、計画されていないサービスの中断が発生することがあります。

Microsoft は、稼働時間と接続に関するコミットメントとして、サービスのサービス レベル アグリーメント (SLA) を提供しています。個々の Azure サービスの SLA は、[Azure サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)にあります。

Azure には、可用性の高いアプリケーションをサポートするさまざまなプラットフォーム機能が組み込まれています。こうしたサービスの詳細については、[Azure アプリケーションの障害復旧と高可用性](https://aka.ms/drtechguide)に関するページを参照してください。

このドキュメントでは、大きな自然災害や広範囲にわたるサービス中断により、リージョン全体で障害が発生した場合の真の障害復旧について説明します。こうした状況はほとんど発生しませんが、リージョン全体で停止が発生する可能性に対しても準備する必要があります。リージョン全体でサービス中断が発生した場合、データのローカル冗長コピーは、一時的に使用できなくなります。geo レプリケーションを有効にしてある場合は、Azure Storage の BLOB とテーブルのコピーがさらに 3 つ、別のリージョンに格納されます。地域的な停電や災害が発生し、プライマリ リージョンを復旧できない場合は、すべての DNS エントリが、geo レプリケートされたリージョンに再マッピングされます。
 
>[AZURE.NOTE]このプロセスは、ユーザーが制御することはできません。また、データセンター全体の障害に対してのみ実行されます。そのため、最高レベルの可用性を実現するには、アプリケーション固有の他のバックアップ戦略にも依存する必要があります。詳細については、[障害復旧のためのデータ戦略](https://aka.ms/drtechguide#DSDR)に関するページのセクションを参照してください。独自のフェールオーバーを使用できるようにする場合は、[Read-Access Geo-Redundant Storage (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) の使用を検討してください。この場合は、他のリージョンに、データの読み取り専用のコピーが作成されます。

こうした状況はほとんど発生しませんが、Azure 仮想マシンのアプリケーションがデプロイされているリージョン全体でサービス中断が発生した場合は、Azure 仮想マシンに関する次のガイダンスに従って対応してください。

##オプション 1: 復旧を待つ 
この場合、ユーザーによる操作は必要ありません。Azure チームがサービスを利用できるようにするために鋭意取り組んでいることをご理解ください。現在のサービスの状態は、[Azure サービスの正常性ダッシュボード](https://azure.microsoft.com/status/)で確認できます。

>[AZURE.NOTE]お客様が Azure Site Recovery (ASR) を設定していない場合や別のリージョンにセカンダリ デプロイを持っている場合は、これが最適なオプションです。

デプロイした Cloud Services にすぐにアクセスする必要があるお客様の場合は、以下のオプションをご利用いただけます。

>[AZURE.NOTE]これらのオプションを使用すると、一部のデータが失われる可能性があることに注意してください。

##オプション 2: 新しいリージョンにクラウド サービス構成を再デプロイする 

元のコードがある場合は、アプリケーションおよび関連付けられている構成とリソースを、新しいリージョンの新しいクラウド サービスに簡単に再デプロイすることができます。

クラウド サービス アプリケーションを作成してデプロイする方法の詳細については、「[クラウド サービスを作成してデプロイする方法](./cloud-services-how-to-create-deploy-portal.md)」を参照してください。

アプリケーションのデータ ソースによっては、アプリケーションのデータ ソースの復旧手順を確認することが必要になる場合があります。
  * Azure Storage データ ソースの場合は、「[Azure Storage のレプリケーション](../storage/storage-redundancy.md#read-access-geo-redundant-storage)」を参照して、アプリケーション用に選択したレプリケーション モデルに応じて使用できるオプションについて確認してください。
  * SQL Database ソースの場合は、「[概要: SQL Database を使用したクラウド ビジネス継続性とデータベース障害復旧](../sql-database/sql-database-business-continuity.md)」を参照して、アプリケーション用に選択したレプリケーション モデルに応じて使用できるオプションについて確認してください。 

##オプション 3: Traffic Manager を介してバックアップ デプロイを使用する 
このオプションは、リージョンの障害復旧を考慮に入れて既にアプリケーション ソリューションを設計していることを前提としています。Cloud Services アプリケーションのセカンダリ デプロイを既に別のリージョンで実行していて、Traffic Manager チャネルを介して接続されている場合に、このオプションを使用できます。この場合、セカンダリ デプロイの正常性を確認し、すべてが正常であれば、Azure Traffic Manager を介して、そこにトラフィックをリダイレクトできます。そうすることで、ATM のトラフィック ルーティング方法とフェールオーバー順序構成の利点を活用できます。「[Traffic Manager の設定の構成方法](../traffic-manager/traffic-manager-overview.md#how-to-configure-traffic-manager-settings)」を参照してください。

![Balancing Azure Cloud Services across regions with Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

##参照 

[Azure アプリケーションの障害復旧と高可用性](https://aka.ms/drtechguide)

[Azure のビジネス継続性テクニカル ガイダンス](https://aka.ms/bctechguide)
 
不明な点がある場合、または Microsoft による代理操作をご希望の場合は、[カスタマー サポート](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)までご連絡ください。

<!---HONumber=AcomDC_0525_2016-->