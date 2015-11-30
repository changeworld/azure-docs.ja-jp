<properties
	pageTitle="Azure Backup とは | Microsoft Azure"
	description="Azure Backup と復元サービスを利用すれば、Windows Server、Windows クライアント コンピューター、SCDPM サーバー、Azure Virtual Machines のデータとアプリケーションをバックアップし、復元できます。"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor="tysonn"
	keywords="バックアップと復元、復元サービス"/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/17/2015" ms.author="aashishr"; "trinadhk"; "jimpark"/>

# Azure Backup とは
Azure Backup は、オンプレミスまたは Azure にデータをバックアップし、復元できるマルチテナントの Azure サービスです。既存のオンプレミスまたはオフサイトのバックアップ ソリューションを、信頼性の高い、セキュリティで保護された、コスト競争力のあるクラウド バックアップ ソリューションに置き換えます。クラウドで実行されている資産を保護するための柔軟性も提供します。Azure Backup は、拡張性、持続性、高可用性に優れた世界クラスのインフラストラクチャの上に構築されます。このソリューションを使用すると、System Center Data Protection Manager (SCDPM) サーバー、Windows サーバー、Windows クライアント コンピューター、Azure IaaS 仮想マシンからデータやアプリケーションをバックアップできます。Azure Backup と SCDPM は、Microsoft のクラウド統合バックアップ ソリューションを構成する基本的なテクノロジです。

> [AZURE.VIDEO what-is-azure-backup]

## クラウド設計ポイント
従来のバックアップ ソリューションは、クラウドをディスクやテープなどのエンドポイントのように扱えるように進化してきました。この方法は簡単でデプロイしやすく、一貫性のある処理を行いますが、使用が制限され、基になるプラットフォームを最大限に活用できません。これは、エンド ユーザーにとって非効率的でコストの高いソリューションになります。Azure を "単なるストレージ エンドポイント" として扱うため、バックアップ ソリューションはパブリック クラウド プラットフォームの豊富な機能を活用できません。一方、Azure Backup は、クラウドを利用して強力で低コストのバックアップ ソリューションを提供するという真のサービスを提供します。オンプレミスのバックアップ ソリューション (SCDPM) と統合できるため、エンド ツー エンドのハイブリッド ソリューションを提供します。

この方法の利点は、次のとおりです。

- 低コストで回復力のあるデータ ストレージを提供する、効率的なクラウド ストレージ アーキテクチャ
- 高可用性を保証する、非侵入型の自動調整サービス
- 一貫した方法によるオンプレミス、ハイブリッド、IaaS デプロイへのバックアップ

このソリューションの主な機能は、次のとおりです。

1. **信頼性の高いサービス**: Azure Backup を採用することで、可用性の高いバックアップ サービスを受けることができます。このサービスはマルチ テナントであり、基になるコンピューティングやストレージの管理を心配する必要がありません。

2. **信頼性の高いストレージ**: Azure Backup は、高度な SLA に準拠する信頼性の高いクラウド ストレージの上に構築されています。ストレージを維持する上で資本や運用コストを心配する必要がありません。さらに、バックアップ先を LRS (ローカル冗長ストレージ) または GRS (Geo レプリケーション ストレージ) から選択できます。LRS は、同じ geo 内に 3 つのデータをコピーしてローカルのハードウェア障害から保護しますが、GRS は、ペアのデータ センター内にさらに 3 つのコピー (合計 6 つのコピー) を格納します。これにより、バックアップ データの高可用性が保証されます。Azure サイト レベルの障害が発生してもバックアップ データは安全です。

3. **セキュリティ**: Azure のバックアップ データは転送時にソースで暗号化され、暗号化された状態で Azure に保存されます。暗号化キーはソースに保存されます。転送されたり Azure に保存されることはありません。データを復元するには暗号化キーが必要です。お客様のみがサービス内のデータにフル アクセスできます。

4. **オフサイト保護**: お客様は、オフサイトのテープ バックアップ ソリューションの代価を払うよりも、かなり低コストでテープに似た形式の強力なソリューションを提供する Azure にバックアップできます。

5. **簡単**: Azure Backup は、あらゆる規模のデプロイを保護するために拡張できる、使いやすいインターフェイスを提供します。サービスが進化すると、中央管理などの機能を使用して 1 つの場所からバックアップ インフラストラクチャを管理できるようになります。

6. **コスト効率**: Azure Backup の価格には、インスタンスごとのバックアップ管理手数料と Azure で使用中のストレージのコスト (ブロック BLOB 料金) が含まれます。他のクラウド ベースのバックアップ ソリューションとは異なり、Azure Backup では復元操作に対して料金がかかりません。また、復元操作時の出力 (送信) データの転送コストもかかりません。

7. **クラウドでのバックアップ**: Azure Backup では、仮想マシンをシャットダウンせずに、実行中の Azure IaaS 仮想マシンの VSS ベースのアプリケーション整合性バックアップを実行できます。また、ファイルシステムの整合性を維持しながら、Azure で Linux 仮想マシンをバックアップすることもできます。


## デプロイ シナリオ
| コンポーネント | Azure にデプロイできる? | オンプレミスにデプロイできる? | サポートされているターゲット ストレージ|
| --- | --- | --- | --- |
| Azure Backup エージェント | **はい** <br><br>Azure Backup エージェントは、Azure で実行されている任意の Windows Server VM にデプロイできます。 | **はい** <br><br>Azure Backup エージェントは、任意の Windows Server VM または物理マシンにデプロイできます。 | Azure Backup コンテナー |
| System Center Data Protection Manager (SCDPM) | **はい** <br><br>[SCDPM による Azure のワークロードの保護](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx)の詳細を参照してください。 | **はい** <br><br>[データ センターのワークロードと VM の保護](https://technet.microsoft.com/zh-CN/library/hh758173.aspx)の詳細を参照してください。 | ローカルに接続されているディスク、<br>Azure Backup コンテナー、<br>テープ (オンプレミスのみ) |
| Azure Backup (VM 拡張機能) | **はい** <br><br>[Azure IaaS 仮想マシンのバックアップ](backup-azure-vms-introduction.md)に特化しています。 | **いいえ** <br><br>データ センターの仮想マシンをバックアップするには SCDPM を使用します。 | Azure Backup コンテナー |


## アプリケーションとワークロード

| ワークロード | ソース コンピューター | Azure Backup ソリューション |
| --- | --- |---|
| ファイルとフォルダー | Windows Server | [Azure Backup エージェント](backup-configure-vault.md)、<br> [System Center DPM](backup-azure-dpm-introduction.md) |
| ファイルとフォルダー | Windows クライアント | [Azure Backup エージェント](backup-configure-vault.md)、<br> [System Center DPM](backup-azure-dpm-introduction.md) |
| Hyper-V 仮想マシン (Windows) | Windows Server | System Center DPM |
| Hyper-V 仮想マシン (Linux) | Windows Server | System Center DPM |
| Microsoft SQL Server | Windows Server | [System Center DPM](backup-azure-backup-sql.md) |
| Microsoft SharePoint | Windows Server | [System Center DPM](backup-azure-backup-sharepoint.md) |
| Microsoft Exchange | Windows Server | System Center DPM |
| Azure IaaS VM (Windows)| - | [Azure Backup (VM 拡張機能)](backup-azure-vms-introduction.md) |
| Azure IaaS VM (Linux) | - | [Azure Backup (VM 拡張機能)](backup-azure-vms-introduction.md) |


## 次のステップ
- [Azure Backup を試す](backup-try-azure-backup-in-10-mins.md)
- Azure Backup サービスに関してよく寄せられる質問は、[こちら](backup-azure-backup-faq.md)をご覧ください。
- [Azure Backup フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=290933)にアクセスします。

<!---HONumber=Nov15_HO4-->