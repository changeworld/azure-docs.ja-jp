<properties
	pageTitle="Azure Storage との間でのデータの移動 | Microsoft Azure"
	description="この記事では、Azure Storage との間でデータを移動するためのさまざまな方法の概要を説明します。"
	services="storage"
	documentationCenter=""
	authors="micurd"
	manager="jahogg"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2016"
	ms.author="micurd;tamram"/>

# Azure Storage との間でのデータの移動

オンプレミスのデータを Azure Storage に (またはその逆に) 移動する場合は、さまざまな方法があります。最適な方法は、シナリオによって異なります。この記事では、さまざまなシナリオとそれぞれに適した手段の概要を説明します。

## アプリケーションの構築

アプリケーションを構築する場合は、REST API またはいずれかのクライアント ライブラリを使用するのが、Azure Storage との間でデータを移動する優れた方法です。

Azure Storage では、.NET、iOS、Java、Android、Universal Windows Platform (UWP)、Xamarin、C++、Node.JS、PHP、Ruby、Python 用の充実したクライアント ライブラリが用意されています。クライアント ライブラリは、再試行ロジック、ログ、並列アップロードといった高度な機能を提供します。また、REST API を直接使用して開発することもでき、HTTP/HTTPS 要求を行うどの言語からでも呼び出すことができます。

詳細については、「[.NET を使用して Azure Blob Storage を使用する](storage-dotnet-how-to-use-blobs.md)」を参照してください。

さらに、Azure との間で相互に高パフォーマンスのデータ コピーを行うことができるように設計されたライブラリである、[Azure Storage Data Movement Library](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) も用意されています。詳細については、Data Movement Library の[ドキュメント](https://github.com/Azure/azure-storage-net-data-movement)を参照してください。

## データのすばやい表示/操作

Azure Storage のデータを簡単に表示するだけでなく、データのアップロードやダウンロードも行いたい場合は、Azure ストレージ エクスプローラーを使用できます。

詳細については、[Azure ストレージ エクスプローラー](storage-explorers.md)の一覧を確認してください。

## システム管理

コマンドライン ユーティリティが必要な場合、またはコマンドライン ユーティリティの方が使い慣れている場合は (システム管理者など)、次のような選択肢があります。

### AzCopy

AzCopy は、Azure Storage との間で高パフォーマンスのデータ コピーを行うように設計された Windows コマンドライン ユーティリティです。同じストレージ アカウント内または異なるストレージ アカウント間でデータをコピーすることもできます。

詳細については、「[AzCopy コマンド ライン ユーティリティを使用してデータを転送する](storage-use-azcopy.md)」を参照してください。

### Azure PowerShell

Azure PowerShell は、Azure でサービスを管理するためのコマンドレットを提供するモジュールです。タスク ベースのコマンドライン シェルであり、特にシステム管理用に設計されたスクリプト言語です。

詳細については、「[Azure Storage での Azure PowerShell の使用](storage-powershell-guide-full.md)」を参照してください。

### Azure CLI

Azure CLI は、Azure サービスで使用できるオープン ソース、クロスプラットフォームのコマンド群です。Azure CLI は、Windows、OSX、Linux で使用できます。

詳細については、「[Azure Storage での Azure CLI の使用](storage-azure-cli.md)」を参照してください。

## 低速ネットワークでの大量のデータの移動

大量のデータの移動に関する最も大きな課題の 1 つは、転送時間です。ネットワーク コストやコードの作成を心配することなく Azure Storage との間でデータを移動したい場合は、Azure Import/Export が適切なソリューションです。

詳細については、[Azure Import/Export](storage-import-export-service.md) に関する記事を参照してください。

## データのバックアップ

Azure Storage にデータをバックアップするだけの場合は、Azure Backup が適しています。これは、オンプレミスのデータと Azure VM をバックアップするための強力なソリューションです。

詳細については、[Azure Backup](../backup/backup-introduction-to-azure-backup.md) に関する記事を参照してください。

## オンプレミスおよびクラウドからのデータへのアクセス

オンプレミスで、またはクラウドからデータにアクセスするためのソリューションが必要な場合は、Azure のハイブリッド クラウド ストレージ ソリューションである StorSimple を使用できます。このソリューションは、頻繁に使用されるデータを SSD にインテリジェントに格納する物理 StorSimple デバイスと、Azure Storage 上の非アクティブ/バックアップ/アーカイブ データで構成されます。

詳細については、[StorSimple](../storsimple/storsimple-overview.md) に関する記事を参照してください。

## データの復旧

オンプレミスのワークロードとアプリケーションがある場合、障害が発生してもビジネスを継続できるソリューションが必要です。Azure Site Recovery は、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、復旧を処理します。レプリケートされたデータは Azure Storage に格納されるので、オンサイトのセカンダリ データセンターは必要ありません。

詳細については、[Azure Site Recovery](../site-recovery/site-recovery-overview.md) に関する記事を参照してください。

<!---HONumber=AcomDC_0928_2016-->