<properties
   pageTitle="Recovery Services コンテナーに関する FAQ | Microsoft Azure"
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
	 ms.date="08/21/2016"
	 ms.author="trinadhk; markgal; jimpark;"/>

# Recovery Services コンテナー - FAQ

> [AZURE.SELECTOR]
- [クラシック モードでの Backup に関する FAQ](backup-azure-backup-faq.md)
- [Resource Manager モードでの Backup に関する FAQ](backup-azure-backup-ibiza-faq.md)

この記事では、Recovery Services コンテナーに固有の情報を提供すると共に、[Azure Backup に関する FAQ](backup-azure-backup-faq.md) を補足します。Azure Backup FAQ は、Azure Backup サービスに関する質問と回答の完全なセットを提供します。

この記事や関連の記事の「DISQUS」セクションでも Azure Backup について質問できます。また、[ディスカッション フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)でも、Azure Backup サービスに関する質問を投稿できます。

## Recovery Services コンテナーは Resource Manager に基づいています。Backup コンテナー (クラシック モード) はまだサポートされていますか。<br/>
はい。Backup コンテナーは引き続きサポートされます。Backup コンテナーは[クラシック ポータル](https://manage.windowsazure.com)で作成しますが、Recovery Services コンテナーは [Azure ポータル](https://portal.azure.com)で作成します。ただし、Recovery Services コンテナーを作成することを強くお勧めします。今後強化される機能は Recovery Services コンテナーでしか利用できないためです。

## Backup コンテナーを Recovery Services コンテナーに移行することはできますか。<br/>
残念ながら、現時点では、Backup コンテナーの内容を Recovery Services コンテナーに移行できません。この機能の追加に取り組んでいますが、パブリック プレビューの一部としては使用できません。

## Recovery Services コンテナーでサポートされるのはクラシック VM と Resource Manager ベースの VM のどちらですか。<br/>
Recovery Services コンテナーでは両方のモデルがサポートされています。クラシック ポータルで作成された VM (クラシック モード VM) も、Azure ポータルで作成された VM (Resource Manager ベースのもの) も、Recovery Services コンテナーにバックアップできます。

## 私はクラシック VM を Backup コンテナーにバックアップしてあります。VM をクラシック モードから Resource Manager モードに移行したいのですが、Recovery Services コンテナーにバックアップするにはどうすればよいですか。
Backup コンテナー内のクラシック VM のバックアップは、VM をクラシックから Resource Manager モードに移行しても、Recovery Services コンテナーに自動的には移行されません。VM のバックアップを移行するには、次の手順に従ってください。

1. Backup コンテナーで、**[保護された項目]** タブに移動し、VM を選択します。[[保護の停止]](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines) をクリックします。*[関連付けられたバックアップ データを削除します]* オプションは**オフ**のままにしておきます。
2. 仮想マシンをクラシック モードから Resource Manager モードに移行します。仮想マシンに対応するストレージとネットワークも Resource Manager モードに移行されることを確認してください。
3. コンテナー ダッシュボードの上部にある **[バックアップ]** アクションを使用して、移行先の仮想マシンに Recovery Services コンテナーを作成し、バックアップを構成します。[Recovery Services コンテナーでバックアップを有効にする](backup-azure-vms-first-look-arm.md)方法について、さらに詳しくご確認ください。

<!---HONumber=AcomDC_0928_2016-->