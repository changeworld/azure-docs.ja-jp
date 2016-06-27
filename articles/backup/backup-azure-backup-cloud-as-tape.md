<properties
   pageTitle="Azure Backup を使用してテープのインフラストラクチャを置換する | Microsoft Azure"
   description="Azure でのデータのバックアップと復元を可能にするテープのようなセマンティクスを Azure Backup が提供する方法について説明します。"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="06/08/2016"
   ms.author="jimpark;"/>

# Azure Backup を使用してテープのインフラストラクチャを置換する
Azure Backup と System Center Data Protection Manager のユーザーは以下を実行できます。

- 組織のニーズに最適なスケジュールでのデータのバックアップ
- 長期間にわたるバックアップ データの保持
- 長期間保有する必要があるものを (テープの代わりに) Azure で保持

この記事では、顧客がバックアップと保有ポリシーを有効にする方法について説明します。その長期間保有のニーズに対処するためにこれまでテープ使用してきた顧客にとっては、この機能を使用するための強力かつ有効な代替方法を活用できるようになりました。Azure Backup の最新のリリース ([ここ](http://aka.ms/azurebackup_agent)からアクセスできます) では、この機能が有効になっています。SCDPM の顧客は、この機能を使用する前に、UR5 に移動する必要があります。

## Backup スケジュールとは
Backup スケジュールは、バックアップ操作の頻度を示します。たとえば、次の画面の設定は、毎日午後 6 時と、午前 0 時にバックアップが実行されることを示しています。

![毎日のスケジュール](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

顧客は週ごとのバックアップをスケジュールすることもできます。たとえば、以次の画面の設定は、隔週の日曜日と水曜日の午前 9 時 30 分と午前 1 時にバックアップが実行されることを示しています。

![週単位のスケジュール](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## 保有ポリシーとは
保有ポリシーは、バックアップを格納する必要がある期間を指定します。顧客は、すべてのバックアップ ポイントの「フラット ポリシー」を指定するのでなく、バックアップが作成された時期に基づいて別の保有ポリシーを指定できます。たとえば、各四半期の最後に実行されるバックアップ ポイントは、監査のために長期間保持する必要がありますが、毎日実行されるバックアップ ポイント (運用上の復旧ポイントとして機能する) は、90 日間保持する必要があります。

![保有ポリシー](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

このポリシーで指定された「保有ポイント」の合計数は、90 (1 日のポイント) + 40 (10 年間の四半期ごとに 1 つ) = 130 です。

## 例 - 両方を一緒にまとめる

![サンプル画面](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **毎日の保有ポリシー**: 毎日作成されるバックアップは 7 日間保存されます。
2. **週ごとの保有ポリシー**: 毎日午前 0 時と土曜日の午後 6 時に作成されるバックアップは 4 週間保存されます。
3. **月ごとの保有ポリシー**: 各月の最後の土曜日の午前 0 時と午後 6 時に作成されるバックアップは 12か月保存されます。
4. **年ごとの保有ポリシー**: 毎年 3 月の最後の土曜日の午前 0 時と午後 6 時に作成されるバックアップは 10 年間保存されます。

上の図にある「保有ポイント」の合計数 (顧客がデータを復元できるポイント) は次のように計算されます。

- 7 日間で 1 日あたり 2 つのポイント = 14 の回復ポイント
- 4 週間で 1 週あたり 2 つのポイント = 8 の回復ポイント
- 12 か月間で 1 月あたり 2 つのポイント = 24 の回復ポイント
- 10 年間で 1 年あたり 1 つのポイント = 10 の回復ポイント

回復ポイントの合計数は 56 です。

> [AZURE.NOTE] Azure のバックアップでは、回復ポイントの数に制限はありません。

## 詳細な構成
上記の画面の **[変更]** をクリックすることによって、顧客はより柔軟に保持スケジュールを指定することができます。

![変更](./media/backup-azure-backup-cloud-as-tape/modify.png)

## 次のステップ
Azure Backup について詳しくは、以下をご覧ください。

- [Azure Backup の概要](backup-introduction-to-azure-backup.md)
- [Azure Backup を試す](backup-try-azure-backup-in-10-mins.md)

<!---HONumber=AcomDC_0615_2016-->