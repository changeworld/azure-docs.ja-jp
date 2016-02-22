<properties
	pageTitle="Azure Backup ストレージ冗長オプションの決定 | Microsoft Azure"
	description="geo 冗長ストレージとローカル冗長ストレージの違いを理解して、ご自分の Azure Backup ストレージの冗長オプションを決定します。"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="jimpark; markgal"/>


# Azure Backup のストレージ冗長オプション

お客様のビジネスニーズによって、Azure Backup ストレージの適切なストレージ冗長オプションが決まります。Azure をプライマリ バックアップ ストレージ エンドポイントとして使用している場合 (たとえば、Windows Server から Azure にバックアップしている場合) は、(既定の) 地理冗長ストレージ オプションの選択を検討する必要があります。Azure を第 3 のバックアップ ストレージ エンドポイントとして使用している場合 (たとえば、オンプレミスのローカルのバックアップ コピーには SCDPM を使用し、Azure は長期保存に使用する場合) は、ローカル冗長ストレージの選択を検討する必要があります。

## geo 冗長ストレージ (GRS)

地理冗長ストレージでは、データのコピーが 6 つ保持されます。GRS を使用すると、データがプライマリ リージョン内で 3 回複製され、プライマリ リージョンから数百マイル離れたセカンダリ リージョンでも 3 回複製されます。そのため、最も優れたレベルの持続性が実現されます。プライマリ リージョンで障害が発生した場合は、Azure Backup は GRS にデータを格納することによって、2 つの別個のリージョンでデータが持続するようにします。

## ローカル冗長ストレージ (LRS)

ローカル冗長ストレージでは、データのコピーが 3 つ保持されます。LRS は、1 つのリージョンの 1 つの施設内で 3 回複製されます。LRS では、データは通常のハードウェア障害から保護されますが、Azure 施設全体の障害からは保護されません。これにより、Azure でデータを格納するためのコストは削減されますが、データの持続性レベルは低くなります。これは、第 3 のコピーとしてなら許容される可能性があります。

バックアップ コンテナーの **[構成]** オプションから、ニーズに応じて適切なオプションを選択することができます。

## 次のステップ

- [Windows のサーバーまたはクライアント マシン をバックアップする](backup-configure-vault.md)環境を準備します
- 不明点がある場合は「[Azure Backup FAQ](backup-azure-backup-faq.md)」を参照してください。
- [Azure Backup フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=290933)にアクセスします。

<!---HONumber=AcomDC_0211_2016-->