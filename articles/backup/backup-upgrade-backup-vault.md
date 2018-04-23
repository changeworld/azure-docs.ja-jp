---
title: Azure Backup のバックアップ コンテナーを Recovery Services コンテナーにアップグレードする | Microsoft Docs
description: バックアップ コンテナーを Recovery Services コンテナーにアップグレードすることによって、Resource Manager VM のバックアップ、強化されたセキュリティ、VMware VM のバックアップ、Windows Server のシステム状態バックアップなど、新しい機能が利用できます。
services: backup
documentationcenter: ''
author: trinadhk
manager: vijayts
editor: ''
keyword: backup vault; upgrade vault; recovery services vault
ms.assetid: d037a8bf-49f2-4578-974a-3471d87ca278
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: trinadhk, sogup
ms.openlocfilehash: ffc5f11a324b5ac65c872ca2c033f039c129c5f8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>バックアップ コンテナーを Recovery Services コンテナーにアップグレードする
この記事では、Recovery Services コンテナーの機能や、既にあるバックアップ コンテナーを Recovery Services コンテナーにアップグレードすることに関してよく寄せられる質問、アップグレード後の手順について取り上げます。 Recovery Services コンテナーは、バックアップ データを格納するバックアップ コンテナーの Azure Resource Manager 版に相当します。 オンプレミスにあるか、Azure 内にあるかに関係なく、データは通常、データのコピーであるか、仮想マシン (VM)、ワークロード、サーバー、ワークステーションのいずれかの構成情報です。

## <a name="what-is-a-recovery-services-vault"></a>Recovery Services コンテナーとは
Recovery Services コンテナーは、バックアップ コピー、復旧ポイント、バックアップ ポリシーなどのデータを保持するために使用される、Azure のオンライン ストレージ エンティティです。 Recovery Services コンテナーを使用すると、IaaS VM (Linux または Windows) や Azure SQL Database などのさまざまな Azure サービスのバックアップ データを保持できます。 Recovery Services コンテナーは、System Center DPM、Windows Server、Azure Backup Server などをサポートします。 Recovery Services コンテナーでは、管理オーバーヘッドを最小限に抑えながら、バックアップ データを簡単に整理できます。

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Recovery Services コンテナーと Backup コンテナーの比較
Recovery Services コンテナーが Azure の Azure Resource Manager モデルに基づいているのに対し、Backup コンテナーは Azure Service Manager モデルに基づいています。 Backup コンテナーを Recovery Services コンテナーにアップグレードする場合、バックアップ データはアップグレード プロセスの実行中でも実行後でもそのまま残ります。 Recovery Services コンテナーには、Backup コンテナーにはない次のような機能があります。

- **強化されたバックアップ データの保護機能**: Recovery Services コンテナーの場合、Azure Backup によってクラウド バックアップを保護するセキュリティ機能が提供されます。 これらのセキュリティ機能により、バックアップをセキュリティで保護することができ、運用サーバーとバックアップ サーバーが侵害された場合でもクラウド バックアップからデータを安全に回復できます。 [詳細情報](backup-azure-security-feature.md)

- **ハイブリッド IT 環境の一元監視**: Recovery Services コンテナーの場合、[Azure IaaS VM](backup-azure-manage-vms.md) だけでなく、[オンプレミス資産](backup-azure-manage-windows-server.md#manage-backup-items)も中央ポータルで監視することができます。 [詳細情報](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **ロールベースのアクセス制御 (RBAC)**: RBAC を使用して、Azure のアクセス権を詳細に管理できます。 [Azure にはさまざまな組み込みのロールがあります](../role-based-access-control/built-in-roles.md)。また、Azure Backup には、[復旧ポイントを管理するための 3 つの組み込みのロールがあります](backup-rbac-rs-vault.md)。 Recovery Services コンテナーは、定義されたユーザー ロールのセットに対するバックアップと復元アクセスを制限する RBAC と互換性があります。 [詳細情報](backup-rbac-rs-vault.md)

- **Azure Virtual Machines のあらゆる構成の保護**: Recovery Services コンテナーは、Resource Manager ベースの VM (Premium ディスク、Managed Disks、暗号化された VM など) を保護します。 Backup コンテナーを Recovery Services コンテナーにアップグレードすると、Service Manager ベースの VM を Resource Manager ベースの VM にアップグレードできます。 コンテナーをアップグレードするときに、Service Manager ベースの VM の復旧ポイントを維持し、アップグレードされた (Resource Manager が有効な) VM の保護を構成できます。 [詳細情報](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **IaaS VM のインスタント リストア**: Recovery Services コンテナーを使用すると、VM 全体を復元せずに IaaS VM からファイルとフォルダーを復元できるため、復元時間を短縮できます。 IaaS VM のインスタント リストアは、Windows VM と Linux VM の両方で利用できます。 [詳細情報](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> 2.0.9083.0 未満の MARS エージェントを使ってバックアップ コンテナーに項目を登録した場合、Recovery Services コンテナーの機能をフルに活用するには、[MARS エージェントの最新]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe)バージョンをダウンロードしてください。 
> 

## <a name="managing-your-recovery-services-vaults"></a>Recovery Services コンテナーの管理
次の画面は、Azure ポータルで Backup コンテナーからアップグレードされた新しい Recovery Services コンテナーを示しています。 アップグレード後のコンテナーは、"Default-RecoveryServices-ResourceGroup-geo" という名前の既定のリソース グループに存在します。 たとえば米国西部に存在していたバックアップ コンテナーは、Default-RecoveryServices-ResourceGroup-westus という名前の既定のリソース グループに置かれます。
> [!NOTE]
> CPS Standard ユーザーについては、コンテナーのアップグレード後もリソース グループは変更されません。アップグレード前と同じ状態が維持されます。

最初の画面は、コンテナーの重要なエンティティを表示するコンテナー ダッシュボードを示しています。
![バックアップ コンテナーからアップグレードされた Recovery Services コンテナーの例](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

2 番目の画面は、Recovery Services コンテナーの使用を開始するために役立つリンク ヘルプを示しています。

![[クイック スタート] ブレードのヘルプ リンク](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>アップグレード後の手順
Recovery Services コンテナーでは、バックアップ ポリシーでのタイム ゾーン情報の指定をサポートしています。 コンテナーが正常にアップグレードされたら、コンテナー設定メニューからバックアップ ポリシーに移動し、コンテナーで構成されているポリシーごとにタイム ゾーン情報を更新します。 この画面には、ポリシーの作成時に使用したローカル タイム ゾーンに従って指定されたバックアップのスケジュール時刻が既に表示されています。 

## <a name="enhanced-security"></a>強化されたセキュリティ
Backup コンテナーを Recovery Services コンテナーにアップグレードすると、そのコンテナーのセキュリティ設定が自動的に有効になります。 セキュリティ設定が有効になると、バックアップの削除やパスフレーズの変更などの特定の操作で [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) の PIN が必要になります。 強化されたセキュリティの詳細については、[ハイブリッド バックアップを保護するためのセキュリティ機能](backup-azure-security-feature.md)に関する記事をご覧ください。 強化されたセキュリティを有効にすると、データは、復旧ポイント情報がコンテナーから削除されてから最大 14 日間保持されます。 このセキュリティ データのストレージに対して課金されます。 セキュリティ データのリテンション期間は、Azure Backup エージェント、Azure Backup Server、System Center Data Protection Manager に対して作成された復旧ポイントに適用されます。 

## <a name="gather-data-on-your-vault"></a>コンテナー上のデータの収集
Recovery Services コンテナーにアップグレードしたら、Azure Backup のレポート (IaaS VM および Microsoft Azure Recovery Services エージェント用) を構成し、Power BI を使用してレポートにアクセスします。 データの収集の詳細については、記事「[Azure Backup のレポートを構成する](backup-azure-configure-reports.md)」を参照してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**アップグレード プランは進行中のバックアップに影響を与えますか?**</br>
いいえ。 進行中のバックアップは、アップグレード中もアップグレード後も、中断なく続行されます。

**このアップグレードは、既存のツールにとってどのような意味がありますか?**</br>
既存のオートメーションまたはツールがアップグレード後も正常に機能するためには、Resource Manager デプロイメント モデルへの更新が必要となります。 [Resource Manager デプロイメント モデル](backup-client-automation.md)用の PowerShell コマンドレット リファレンスを参照してください。

**アップグレード後にロールバックすることはできますか。**</br>
いいえ。 リソースが正常にアップグレードされた後のロールバックはサポートされていません。

**アップグレード後に、従来のコンテナーを表示できますか?**</br>
いいえ。 アップグレード後は、従来のコンテナーは、表示することも管理することもできません。 コンテナーのすべての管理操作は、新しい Azure ポータルを使用してのみ実行できます。

**アップグレード後のコンテナーに、MARS エージェントによって保護されているサーバーが見つかりません。**</br>
コンテナーに存在する、MARS エージェントによって保護されたすべてのサーバーを表示するには、最新の MARS エージェントをインストールする必要があります。 最新バージョンのエージェントは[こちら]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe)からダウンロードできます。

**MARS エージェントによって保護されたサーバーのバックアップ ポリシーがアップグレード後に見えなくなりました**</br>
コンテナーのバックアップ ポリシーが古いために、アップグレード後のコンテナーと同期できていない可能性があります。 アップグレード後も引き続きコンテナー内のポリシーが見えるよう、ポリシーを更新してください。
ポリシーを更新するには、MARS エージェントに移動し、構成されているバックアップ ポリシーを更新してください。

**アップグレード後にバックアップ ポリシーを更新できなくなりました。なぜでしょうか。**</br>
これは、以前のバックアップ エージェントで行った最短のリテンション期間の選択が、許可される最小値を下回っている場合に起こります。 Backup コンテナーを Recovery Services コンテナーにアップグレードすると、そのコンテナーのセキュリティ設定が自動的に有効になります。 有効な復旧ポイント数を常に確保するため、セキュリティ機能に従って、保持する必要のある最短リテンション期間が存在します。 詳細については、[こちら](backup-azure-security-feature.md)を参照してください。
また、Azure Backup の最新の機能を活用するためには、Azure Backup エージェントを最新バージョンに更新する必要があります。

**エージェントを更新したのですが、アップグレード後、数日経ってもオブジェクトが同期されていないようです**</br>
複数のコンテナーに対して同じマシンを登録したかどうかを確かめてください。 ご覧になっているコンテナーが、MARS エージェントの登録先と同じであるかを確認します。 ご使用の MARS エージェントがどのコンテナーに登録されているかを調べるには、Windows レジストリを開いて、HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config にある ServiceResourceName キーの値を調べます。その MARS エージェントに登録されているコンテナーが表示されます。 ご使用のシステムに ServiceResourceName キーが表示されない場合は、HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config の ResourceId キーと MachineId キーの値を添えてお問い合わせください。問題を解決できるようお手伝いさせていただきます。

**アップグレード後にリソースのジョブ情報が表示されないのはなぜですか?**</br>
バックアップの監視 (MARS エージェントおよび IaaS) は、バックアップ コンテナーを Recovery Services コンテナーにアップグレードした後で利用できる新しい機能です。 監視情報がサービスと同期されるまで、最大 12 時間かかります。

**問題はどのようにレポートすればよいですか?**</br>
コンテナーのアップグレードの一部が失敗した場合は、エラーに示されている OperationId を書き留めます。 Microsoft サポートが問題の解決に積極的に取り組みます。 サポートを受けるには、サブスクリプション ID、コンテナー名、OperationId を記載した電子メールを rsvaultupgrade@service.microsoft.com にお送りください。 Microsoft は、問題をできるだけ速やかに解決するよう努力します。 Microsoft による明確な指示がない限り、操作をやり直さないでください。

## <a name="next-steps"></a>次の手順
次の記事を参照してください。</br>
[IaaS VM のバックアップ](backup-azure-arm-vms-prepare.md)</br>
[Azure Backup Server のバックアップ](backup-azure-microsoft-azure-backup.md)</br>
[Windows Server のバックアップ](backup-configure-vault.md)
