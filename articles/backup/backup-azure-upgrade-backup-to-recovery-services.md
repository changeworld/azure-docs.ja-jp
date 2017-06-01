---
title: "Backup コンテナーを Recovery Services コンテナーにアップグレードする (プレビュー) | Microsoft Docs"
description: "Azure Backup コンテナーを Recovery Services コンテナーにアップグレードするためのコマンドとサポート情報です。"
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
ms.assetid: 228fef19-2f6b-4067-acc3-fb6e501afb88
ms.service: required
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/10/2017
ms.author: markgal;arunak
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 00744d70a7644d80cfd99acbcf96966574f4512e
ms.contentlocale: ja-jp
ms.lasthandoff: 05/16/2017


---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Backup コンテナーを Recovery Services コンテナーにアップグレードする

この記事では、Backup コンテナーを Recovery Services コンテナーにアップグレードする方法について説明します。 このアップグレード プロセスは、実行中のバックアップ ジョブに影響を与えず、バックアップ データが失われることもありません。 Backup コンテナーを Recovery Services コンテナーにアップグレードする主な理由は次のとおりです。
- Backup コンテナーのすべての機能は、Recovery Services で保持されます。
- Recovery Services コンテナーは Backup コンテナーよりも多くの機能があり (セキュリティの強化、統合された監視、高速復元、アイテム レベルの復元など)、改良され、簡素化されたポータルからバックアップ項目を管理します。
-  新機能は、Recovery Services コンテナーに対してのみリリースされます。

## <a name="impact-to-operations-during-upgrade"></a>アップグレード中の操作への影響

Backup コンテナーを Recovery Services コンテナーにアップグレードする際、データ プレーンの操作に影響を与えることはありません。 すべてのバックアップ ジョブは通常どおり続行され、アクティブな復元ジョブも中断なしで続行されます。 アップグレード中に、管理操作によって短いダウンタイムが発生します。新しい項目を保護することも、アドホック バックアップ ジョブを作成することもできません。 IaaS VM の復元ジョブは、アップグレード中は実行されません。 <MG>: 通常どおりに開始されたアクティブな復元ジョブと IaaS VM 復元ジョブの相違点
コンテナーのアップグレードは、1 時間以内に完了します。 終了すると、Recovery Services コンテナーが Backup コンテナーに置き換えられます。

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>アップグレード後のオートメーションおよびツールの変更

コンテナーをアップグレードするためにインフラストラクチャーを準備するときに、既存のオートメーションまたはツールがアップグレード後も引き続き動作できるように、それらをアップグレードする必要があります。
[Service Manager デプロイ モデル](backup-client-automation-classic.md)と [Resource Manager デプロイ モデル](backup-client-automation.md)用の PowerShell コマンドレット リファレンスを参照してください。


## <a name="before-you-upgrade"></a>アップグレードする前に

Backup コンテナーを Recovery Service コンテナーにアップグレードする前に、次の問題を調べてください。

- **エージェントの最小バージョン**: コンテナーをアップグレードするには、Microsoft Azure Recovery Services (MARS) エージェントのバージョンが少なくとも 2.0.9070.0 であることを確認します。 MARS エージェントが 2.0.9070.0 よりも古い場合は、アップグレード プロセスを開始する前にエージェントを更新してください。
- **インスタンス ベースの課金モデル**: Recovery Service コンテナーは、インスタンス ベースの課金モデルのみをサポートしています。 古いストレージ ベース課金モデルを使用しているバックアップ コンテナーがある場合は、アップグレード中に課金モデルを変換してください。
- **進行中のバックアップ構成操作がない**: アップグレード中は、管理プレーンへのアクセスは制限されています。 すべての管理プレーンの操作を完了してからアップグレードを開始してください。

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>PowerShell スクリプトを使用したコンテナーのアップグレード

PowerShell スクリプトを使用して、Backup コンテナーを Recovery Services コンテナーにアップグレードできます。 コンテナーのアップグレードを開始するために必要な PowerShell コンポーネントがあることを確認してください。 Backup コンテナー用の PowerShell スクリプトは、Recovery Services コンテナーでは動作しません。 コンテナーをアップグレードできるように環境を準備します。

1. [Windows Management Framework (WMF) のバージョン 5](https://www.microsoft.com/download/details.aspx?id=50395) 以降をインストールするか、そのバージョンにアップグレードします。
2. [Azure PowerShell 3.7.0](https://github.com/Azure/azure-powershell/releases/download/v3.7.0-March2017/azure-powershell.3.7.0.msi) をダウンロードしてインストールします。
3. Azure PowerShell モジュールを更新します。
  - Windows Server:
    - Install-Module -Name Azure -RequiredVersion 3.8.0
    - Install-Module -Name AzureRM.RecoveryServices -RequiredVersion 2.8.0
    - Install-Module -Name AzureRM.Resources -RequiredVersion 3.8.0
  - Windows 10:
    - Install-Module -Name Azure -RequiredVersion 3.8.0 -AllowClobber
    - Install-Module -Name AzureRM.RecoveryServices -RequiredVersion 2.8.0 -AllowClobber
    - Install-Module -Name AzureRM.Resources -RequiredVersion 3.8.0 -AllowClobber
4. コンテナーをアップグレードするための [PowerShell スクリプト](http://download.microsoft.com/download/1/c/6/1c6ed72e-38f9-4675-9cf9-9b8a06da7cd3/recoveryservicesvaultupgrade.ps1)をダウンロードします。

### <a name="run-the-powershell-script"></a>PowerShell スクリプトの実行

次のスクリプトを使用して、コンテナーをアップグレードします。 次のサンプル スクリプトには、パラメーターの説明があります。

RecoveryServicesVaultUpgrade.ps1 **-SubscriptionID** `<subscriptionID>` **-VaultName** `<vaultname>` **-Location** `<location>` **-ResourceType** `BackupVault` **-TargetResourceGroupName** `<rgname>`


SubscriptionID - アップグレードされるコンテナーのサブスクリプション ID 番号。
VaultName - アップグレードされる Backup コンテナーの名前。
Location - アップグレードされるコンテナーの場所。
ResourceType - BackupVault を使用します。
TargetResourceGroupName - コンテナーは Resource Manager ベースのデプロイにアップグレードされるため、リソース グループを指定します。 既存のリソース グループを使用することも、新しい名前を指定して作成することもできます。 リソース グループの名前のスペルを間違えた場合は、新しいリソース グループが作成されます。 リソース グループの詳細については、[こちら](../azure-resource-manager/resource-group-overview.md#resource-groups)のリソース グループの概要に関する記事を参照してください。

>[!NOTE]
> リソース グループの名前には制約があります。 必ずこのガイダンスに従ってください。従わなかった場合、コンテナーのアップグレードが失敗する可能性があります。
>
>

次のコード スニペットは、PowerShell コマンドの例を示しています。

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

スクリプトはパラメーターなしで実行することもできます。この場合は、すべての必須パラメーターを入力することを求められます。

PowerShell スクリプトによって、資格情報を入力することを求められます。 資格情報は 2 回入力します。1 回目の入力は Service Manager アカウント用であり、2 回目は Resource Manager アカウント用です。

### <a name="pre-requisites-checking"></a>前提条件のチェック
Azure の資格情報を入力すると、Azure によって、環境が次の前提条件を満たしていることがチェックされます。

- **エージェントの最小バージョン** - Backup コンテナーを Recovery Services コンテナーにアップグレードするには、MARS エージェントのバージョンが少なくとも 2.0.9070.0 である必要があります。 2.0.9070 より前のエージェントを使用して Backup コンテナーに登録した項目がある場合、前提条件のチェックは失敗します。 前提条件のチェックが失敗した場合は、エージェントを更新し、コンテナーのアップグレードをやり直してください。 エージェントの最新バージョンは、[http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) からダウンロードできます。
- **進行中の構成ジョブ**: アップグレードするように設定した Backup コンテナーに対するジョブの構成または項目の登録が進行中の場合、前提条件のチェックは失敗します。 構成を完了するか、項目の登録を終了した後、コンテナーのアップグレード プロセスを開始してください。
- **ストレージ ベースの課金モデル**: Recovery Services コンテナーは、インスタンス ベースの課金モデルをサポートします。 ストレージ ベースの課金モデルを使用している Backup コンテナーでコンテナーのアップグレードを実行した場合は、コンテナーと請求モデルを同時にアップグレードすることを求められます。 または、課金モデルを更新した後でコンテナーのアップグレードを実行できます。
- Recovery Services コンテナーのリソース グループを識別します。 Resource Manager デプロイ機能を活用するには、Recovery Services コンテナーをリソース グループ内に配置する必要があります。 使用するリソース グループがわからない場合は、名前を指定すると、アップグレード プロセスによってリソース グループが作成されます。 さらに、アップグレード プロセスは、新しいリソース グループにコンテナーを関連付けます。

アップグレード プロセスで前提条件のチェックが完了すると、コンテナーのアップグレードを開始することに求められます。 確認後に実行されるアップグレード プロセスは、通常は約 15 ～ 20 分で完了しますが、時間はコンテナーのサイズによって異なります。 大規模なコンテナーでは、アップグレードは最大 90 分かかる可能性があります。

## <a name="managing-your-recovery-services-vaults"></a>Recovery Services コンテナーの管理

次の画面は、Azure ポータルで Backup コンテナーからアップグレードされた新しい Recovery Services コンテナーを示しています。 最初の画面は、コンテナーの重要なエンティティを表示するコンテナー ダッシュボードを示しています。

![Backup コンテナーからアップグレードされた Recovery Services コンテナーの例](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

2 番目の画面は、Recovery Services コンテナーの使用を開始するために役立つリンク ヘルプを示しています。

![[クイック スタート] ブレードのヘルプ リンク](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**アップグレード プランは進行中のバックアップに影響を与えますか?**</br>
いいえ。 進行中のバックアップは、アップグレード中もアップグレード後も、中断なく続行されます。

**近日中にアップグレードする予定がない場合、コンテナーはどうなりますか?**</br>
すべての新機能は Recovery Services コンテナーのために計画されており、クラシック ポータルの使用は非推奨になるため、Backup コンテナーと Service Manager モデルは廃止されることになります。 今後、Microsoft では、すべてのコンテナーの Recovery Services コンテナーへの自動アップグレードを開始する予定です。 それまでは、コンテナーをアップグレードするタイミングは、ユーザーが決めることができます。

**アップグレード プランは、既存のツールにとってどのような意味がありますか?**</br>  
Recovery Services コンテナーがベースにしている Resource Manager デプロイ モデルへのツールの更新は、アップグレード プランで考慮する必要がある最も重要な変更の 1 つです。

**ダウンタイム時間はどのくらいかかりますか?**</br>
アップグレードされるリソースの数によって異なります。 小規模なデプロイ (保護されたインスタンスが数十) では、アップグレード全体は 20 分未満で実行されます。 大規模なデプロイでは、最大 1 時間かかります。

**アップグレード後にロールバックすることはできますか?**</br>
いいえ。 リソースが正常にアップグレードされた後のロールバックはサポートされていません。

**サブスクリプションまたはリソースがアップグレードが可能かどうかを確認できますか?**</br>
はい。 アップグレードの最初の手順は、リソースがアップグレード可能であることを検証することです。 前提条件の検証が失敗した場合は、アップグレードを完了できない理由をすべて通知するメッセージが表示されます。

**コンテナーのアップグレードを開始するために必要なアクセス許可は何ですか?**</br>
コンテナーのアップグレードを実行するには、サブスクリプションの共同管理者として Azure クラシック ポータルに追加されている必要があります。 これは、Azure ポータルに所有者として既に追加されている場合でも必要です。 試しに、Azure クラシック ポータルでサブスクリプションの共同管理者の追加を実行して、自分がサブスクリプションの共同管理者になっているかどうかを確認してください。 共同管理者を追加できない場合は、サービス管理者またはサブスクリプションの共同管理者に連絡して、追加してもらってください。

**CSP ベースの Backup コンテナーはアップグレードできますか?**</br>
いいえ。 現時点では、CSP ベースのバックアップ コンテナーはアップグレードできません。 このサポートは、次回のリリースで追加される予定です。

**アップグレード後に、従来のコンテナーを表示できますか?**</br>
いいえ。 アップグレード後は、従来のコンテナーは、表示することも管理することもできません。 コンテナーのすべての管理操作は、新しい Azure ポータルを使用してのみ実行できます。

**アップグレードが失敗しましたが、その原因はもう存在しないコンピューター上のエージェントをアップグレードする必要があるというものでした。このような場合はどうすればよいでしょうか?**</br>
コンピューターに保存されているバックアップを長期にわたって保存する必要がある場合は、コンテナーをアップグレードすることはできません。 今後のリリースで、このようなコンテナーもアップグレードできるサポートが追加される予定です。
コンピューターにバックアップを保存する必要がなくなったら、そのコンピューターのコンテナーへの登録を解除してから、アップグレードをやり直してください。

**アップグレード後に、オンプレミスのリソースに対するジョブ情報が表示されません。なぜですか?**</br>
オンプレミスのバックアップの監視 (MARS エージェント、DPM および MABS) は、Backup コンテナーを Recovery Services コンテナーにアップグレードした後で使用できる新しい機能です。 監視情報がサービスと同期されるまで、最大 12 時間かかります。

**問題はどのようにレポートすればよいですか?**</br>
アップグレード中にエラーが発生した場合は、エラーに示されている OperationId をメモしてください。 Microsoft サポートは、積極的に問題の解決に取り組みます。 サポートを受けるには、rsvaultupgrade@service.microsoft.com にメールをお送りください。その際、サブスクリプション ID、コンテナーの名前、および OperationId をお知らせください。 できるだけ早く問題を解決するように努力します。 Microsoft による明確な指示がない限り、操作をやり直さないでください。


## <a name="next-steps"></a>次のステップ
次に関する記事を参照してください。</br>
[IaaS VM のバックアップ](backup-azure-arm-vms-prepare.md)</br>
[Azure Backup Server のバックアップ](backup-azure-microsoft-azure-backup.md)</br>
[Windows Server のバックアップ](backup-configure-vault.md).

