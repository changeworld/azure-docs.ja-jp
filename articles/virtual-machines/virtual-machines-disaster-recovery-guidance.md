<properties
	pageTitle="Azure Virtual Machines に影響を与える Azure サービス停止が発生した場合の対処方法 | Microsoft Azure"
	description="Azure Virtual Machines に影響を与える Azure サービス停止が発生した場合の対処方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="kmouss"
	manager="drewm"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="virtual-machines"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="kmouss;aglick"/>

#Azure Virtual Machines に影響を与える Azure サービスの停止が発生した場合の対処方法

Microsoft では、必要なときにサービスがいつでも使用できるように取り組んでいますが、やむを得ない事情により、計画されていないサービス停止が発生することがあります。

Microsoft は、稼働時間と接続に関するコミットメントとして、サービスのサービス レベル アグリーメント (SLA) を提供しています。個々の Azure サービスの SLA は、[Azure サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)にあります。

Azure には、可用性の高いアプリケーションをサポートするさまざまなプラットフォーム機能が組み込まれています。こうしたサービスの詳細については、[Azure アプリケーションの障害復旧と高可用性](https://aka.ms/drtechguide)に関するページをご覧ください。

このドキュメントでは、大きな自然災害や広範囲にわたるサービス中断により、リージョン全体で障害が発生した場合の真の障害復旧について説明します。こうした状況はほとんど発生しませんが、リージョン全体で停止が発生する可能性に対しても準備する必要があります。リージョン全体でサービス中断が発生した場合、データのローカル冗長コピーは、一時的に使用できなくなります。geo レプリケーションを有効にしてある場合は、Azure Storage BLOB とテーブルのコピーがさらに 3 つ、別のリージョンに格納されます。地域的な停電や災害が発生し、プライマリ リージョンを復旧できない場合は、すべての DNS エントリが、geo レプリケートされたリージョンに再マッピングされます。
 
>[AZURE.NOTE]このプロセスは、ユーザーが制御することはできません。また、リージョン全体の障害に対してのみ実行されます。そのため、最高レベルの可用性を実現するには、アプリケーション固有の他のバックアップ戦略にも依存する必要があります。詳細については、[障害復旧のためのデータ戦略](https://aka.ms/drtechguide#DSDR)に関するページのセクションをご覧ください。

こうした状況はほとんど発生しませんが、Azure 仮想マシンのアプリケーションがデプロイされているリージョン全体でサービス中断が発生した場合は、Azure 仮想マシンに関する次のガイダンスに従って対応してください。

##オプション 1: 復旧を待つ 
この場合、ユーザーによる操作は必要ありません。サービスを利用できるようにするために鋭意取り組んでいることをご理解ください。現在のサービスの状態は [Azure サービスの正常性ダッシュボード](https://azure.microsoft.com/status/)で確認できます。

>[AZURE.NOTE]停止前に Azure Site Recovery (ASR)、仮想マシン (VM) バックアップ、読み取りアクセス geo 冗長ストレージ (RA-GRS) または geo 冗長ストレージ (GRS) を設定しなかった場合は、このオプションを使用することをお勧めします。VM VHD が格納されているストレージ アカウントに対して GRS (または RA-GRS) ストレージを設定した場合は、基本イメージ VHD の回復を確認し、そこから新しい VM のプロビジョニングを試みることができます。このオプションはお勧めできません。Azure VM バックアップまたは ASR が使用されていない限り、データの同期が保証されず、動作するとは限らないためです。

仮想マシンにすぐにアクセスする必要があるお客様については、次の 2 つのオプションを使用できます。

>[AZURE.NOTE]次のオプションは両方とも、一部のデータが失われる可能性があることに注意してください。

##オプション 2: バックアップから仮想マシン (VM) を復元する 
VM バックアップが構成されているお客様の場合は、そのバックアップと回復ポイントから VM を復元できます。

以下の手順に従って、Azure バックアップから新しい VM を復元します。「Azure での仮想マシンの復元」を参照してください。

Azure Virtual Machines のバックアップ インフラストラクチャの計画については、「[Azure における VM バックアップ インフラストラクチャの計画を立てる](../backup/backup-azure-vms-introduction.md)」を参照してください。

##オプション 3: Azure Site Recovery (ASR) を使用してフェールオーバーを開始する 
影響を受ける Azure Virtual Machines で動作するように Azure Site Recovery を構成した場合は、そのレプリカから VM を復元できます。このレプリカは、Azure またはオンプレミスのいずれかに配置できます。この場合は、その既存のレプリカから新しい VM を作成できます。以下の手順に従って、Azure Site Recovery レプリカから VM を復元します。「[Azure Site Recovery を使用した Azure リージョン間での Azure IaaS 仮想マシンの移行](../site-recovery/site-recovery-migrate-azure-to-azure.md)」を参照してください。

>[AZURE.NOTE]Azure 仮想マシンの OS とデータ ディスクはセカンダリ仮想ハード ドライブ (VHD) にレプリケートされますが、それが GRS または RA-GRS ストレージ アカウント内にある場合は、各 VHD が個別にレプリケートされ、このレプリケーション レベルでは、レプリケートされた VHD 間での一貫性は保証されません。こうしたデータ ディスクを使用しているアプリケーション/データベースが相互に依存している場合は、すべての VHD が 1 つのスナップショットとしてレプリケートされるとは限りません。また、GRS または RA-GRS ストレージの VHD レプリカを使用した場合、VM を起動できるアプリケーション整合性スナップショットが作成されるとは限りません。

##参照 
[Azure アプリケーションの障害復旧と高可用性](https://aka.ms/drtechguide)

[Azure のビジネス継続性テクニカル ガイダンス](http://aka.ms/bctechguide)

[Azure virtual machines のバックアップ](../backup/backup-azure-vms.md)

[Azure Site Recovery](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)

不明な点がある場合、または Microsoft による代理操作をご希望の場合は、[カスタマー サポート](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)までご連絡ください。

<!---HONumber=AcomDC_0525_2016-->