---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 09/14/2021
ms.author: alkohli
ms.openlocfilehash: 88ef9534c1a0048ef113ce0666ddcdd47171c234
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589395"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>StorSimple のバックアップ ポリシーを追加するには

1. StorSimple デバイスに移動して、 **[バックアップ ポリシー]** をクリックします。

2. **[バックアップ ポリシー]** ブレードのコマンド バーで、 **[ポリシーの追加]** をクリックします。
   
    ![バックアップ ポリシーを追加する](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-01.png)

3. **[バックアップ ポリシーの作成]** ブレードで、次の操作を行います。
   
   1. **[デバイスの選択]** は、選択したデバイスに基づいて自動的に設定されます。
   
   2. バックアップ **ポリシー名** として 3 ～ 150 文字の名前を指定します。 ポリシーを作成した後にポリシーの名前を変更することはできません。
       
   3. このバックアップ ポリシーにボリュームを割り当てるには、 **[ボリュームの追加]** を選択してから、ボリュームの表形式の一覧でチェック ボックスをオンにして、1 つまたは複数のボリュームをこのバックアップ ポリシーに割り当てます。

       ![バックアップ ポリシー 2 の追加](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-02.png)<!--Replacement screen source: create-backup-policy-addvolumes.png-->

   4. このバックアップ ポリシーのスケジュールを定義するには、 **[最初のスケジュール]** をクリックしてから、次のパラメーターを変更します。<!--Do the substeps remain the same? Can they follow without a screenshot?-->

       ![バックアップ ポリシーの追加 3](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-03.png)<!--Replacement screen source: create-backup-policy-first-schedule.png-->

       1. **[スナップショットの種類]** で、 **[クラウド]** または **[ローカル]** を選択します。

       2. バックアップの頻度を指定します (数値を指定し、ドロップダウン リストから **[日]** または **[週間]** を選択します)。

       3. 保持スケジュールを入力します。

       4. バックアップ ポリシーの開始日時を入力します。

       5. **[OK]** をクリックして、スケジュールを定義します。
       
       > [!NOTE]
       > スケジュールにおいてバックアップ数が 64 に達し、それらのバックアップを保持する必要がある場合は、[スケジュールを無効](..\articles\storsimple\storsimple-8000-manage-backup-policies-u2.md#disable-a-schedule)にしてから、最大 64 のバックアップを保持する新しいスケジュールを追加できます。 この回避策は、ボリュームごとにバックアップ数が 256 の上限に達するまで機能します。 そこまで達したら、新しいバックアップを作成する前に、古いバックアップを削除する必要があります。

   5. **[作成]** をクリックして、バックアップ ポリシーを作成します。
   
   6. バックアップ ポリシーが作成されると、その旨が通知されます。 **[バックアップ ポリシー]** ブレードの表形式のビューに、新たに追加したポリシーが表示されます。

       ![バックアップ ポリシーの追加 5](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-07.png)
