<properties
   pageTitle="Azure Backup のパブリック プレビュー リリースの FAQ | Microsoft Azure"
   description="このバージョンの FAQ は、Azure Backup サービスのパブリック プレビュー リリースをサポートしています。バックアップ エージェント、バックアップと保持、復元、セキュリティと Azure Backup ソリューションに関する一般的な質問に対する回答。"
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="バックアップ ソリューション、バックアップ サービス"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="07/01/2016"
	 ms.author="trinadhk; markgal; jimpark;"/>

# Azure Backup サービスのパブリック プレビュー リリース - FAQ

> [AZURE.SELECTOR]
- [クラシック モードでの Backup に関する FAQ](backup-azure-backup-faq.md)
- [ARM モードでの Backup に関する FAQ](backup-azure-backup-ibiza-faq.md)

この記事では、Azure Backup サービスのパブリック プレビュー リリースに固有の情報を提供します。この記事は、[Azure Backup FAQ](backup-azure-backup-faq) を補完するものであり、新しい FAQ が到着すると更新されます。Azure Backup FAQ は、Azure Backup サービスに関する質問と回答の完全なセットを提供します。

この記事や関連の記事の「DISQUS」セクションでも Azure Backup について質問できます。また、[ディスカッション フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)でも、Azure Backup サービスに関する質問を投稿できます。

## パブリック プレビュー リリースの内容
パブリック プレビュー リリースでは、Recovery Services コンテナーと、Azure VM を保護する場合の ARM サポートを導入しています。Recovery Services コンテナーは、次世代のコンテナーです。Azure Backup サービスおよび Azure Site Recovery (ASR) サービスによって使用されます。これは v.2 のコンテナーとして考えてください。

## Recovery Services コンテナーと Backup コンテナー

**Q1.Recovery Services コンテナーは v.2 ですが、Backup コンテナー (v.1) もまだサポートされていますか。** <br/> A1.はい。Backup コンテナーは引き続きサポートされます。Backup コンテナーは、クラシック ポータルで作成しますが、Recovery Services コンテナーは、Azure ポータルで作成します。

**Q2.Backup コンテナーを Recovery Services コンテナーに移行できますか。** <br/> A2.残念ながら、現時点では、Backup コンテナーの内容を Recovery Services コンテナーに移行できません。この機能の追加に取り組んでいますが、パブリック プレビューの一部としては使用できません。

**Q3.Recovery Services コンテナーは、v.1 または v.2 の VM をサポートしていますか。** <br/> A3.Recovery Services コンテナーは、v.1 と v.2 の VM をサポートしています。クラシック ポータルで作成された VM (つまり V.1) または Azure ポータルで作成された VM (つまり V.2) を Recovery Services コンテナーにバックアップすることができます。


## Azure VM に対する ARM サポート

**Q1.Azure VM に対する ARM サポートに制限はありますか。** <br/> A1.ARM 用の PowerShell コマンドレットは、現在、使用できません。Azure ポータル UI を使用して、リソース グループにリソースを追加する必要があります。

<!---HONumber=AcomDC_0706_2016-->