<properties 
   pageTitle="StorSimple バックアップ ポリシーの管理 | Microsoft Azure"
	description="StorSimple Manager サービスを使用して、手動バックアップ、バックアップのスケジュールを作成して、バックアップの保持期間を管理する方法について説明します。"
	services="storsimple"
	documentationCenter="NA"
	authors="SharS"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="TBD"
	ms.date="08/27/2015"
	ms.author="v-sharos"/>

# StorSimple Manager サービスを使用してバックアップ ポリシーを管理する

## 概要

このチュートリアルでは、StorSimple Manager サービスの **[バックアップ ポリシー]** ページを使用して、StorSimple ボリュームのバックアップ プロセスとバックアップの保持期間を制御する方法について説明します。また、手動バックアップを完了する方法についても説明します。

**[バックアップ ポリシー]** ページでは、バックアップ ポリシーを管理して、ローカル スナップショットとクラウド スナップショットのスケジュールを設定できます (バックアップ ポリシーは、ボリュームのコレクションに対するバックアップのスケジュールとバックアップの保持期間の構成に使用します)。 バックアップ ポリシーを使用すると、複数のボリュームのスナップショットを同時に取得できます。これは、バックアップ ポリシーによって作成されたバックアップがクラッシュ整合コピーになることを意味します。このページには、バックアップ ポリシーとその種類、関連付けられているボリューム、保持されているバックアップの数、これらのポリシーを有効にするオプションが一覧表示されています。

また **[バックアップ ポリシー]** ページでは、次の 1 つ以上のフィールドで既存のバックアップ ポリシーをフィルター処理することもできます。

- **ポリシー名** – ポリシーに関連付けられた名前。次の種類のポリシーがあります。

   - スケジュールされたポリシー。ユーザーが明示的に作成します。
   - 自動ポリシー。ボリュームの作成時にこのボリュームのオプションの既定のバックアップが有効になっている場合に作成されます。これらのポリシーの名前は、"VolumeName\_Default" という形式で指定されます。"VolumeName" は、ユーザーが管理ポータルで構成する StorSimple ボリュームの名前です。自動ポリシーにより、デバイス時刻の 22 時 30分を起点として、1 日のクラウド スナップショットを取得します。
   - インポートされたポリシー。作成元は StorSimple Snapshot Manager です。このポリシーには、インポート元の StorSimple Snapshot Manager ホストについて説明するタグが付いています。

- **ボリューム** – ポリシーに関連付けられているボリューム。バックアップ ポリシーに関連付けられているボリュームは、すべてバックアップの作成時にグループ化されます。

- **前回の正常なバックアップ** – このポリシーで取得した、最後の正常なバックアップの日時。

- **次回のバックアップ** – このポリシーによって開始される、次のスケジュールされたバックアップの日時。

- **スケジュール** – バックアップ ポリシーに関連付けられているスケジュールの数。

このページから実行できる、頻繁に使用される操作は次のとおりです。

- バックアップ ポリシーの追加 
- スケジュールの追加または変更 
- バックアップ ポリシーの削除 
- 手動バックアップの取得 
- 複数のボリュームとスケジュールによるカスタム バックアップ ポリシーの作成 

## バックアップ ポリシーの追加

自動的にバックアップをスケジュールするためのバックアップ ポリシーを追加します。StorSimple デバイスのバックアップ ポリシーを追加するには、管理ポータルで次の手順を実行します。ポリシーを追加した後は、スケジュールを定義できます (「[スケジュールの追加または変更](#add-or-modify-a-schedule)」を参照してください)。

[AZURE.INCLUDE [storsimple-add-backup-policy](../../includes/storsimple-add-backup-policy.md)]


## スケジュールの追加または変更

StorSimple デバイスで、既存のバックアップ ポリシーに関連付けられるスケジュールを追加または変更できます。スケジュールを追加または変更するには、管理ポータルで次の手順を実行します。

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule.md)]

## バックアップ ポリシーの削除

StorSimple デバイスからバックアップ ポリシーを削除するには、管理ポータルで次の手順を実行します。

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## 手動バックアップの取得

1 つのボリュームに対し、オンデマンドの (手動) バックアップを作成するには、管理ポータルで次の手順を実行します。

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## 複数のボリュームとスケジュールによるカスタム バックアップ ポリシーの作成

複数のボリュームとスケジュールを含むカスタム バックアップ ポリシーを作成するには、管理ポータルで次の手順を実行します。

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy.md)]


## 次のステップ

[StorSimple Snapshot Manager を使用したバックアップ ジョブの表示と管理](storsimple-snapshot-manager-manage-backup-jobs.md)について説明します。

<!---HONumber=September15_HO1-->