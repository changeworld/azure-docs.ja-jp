<properties
   pageTitle="StorSimple ボリュームの複製 | Microsoft Azure"
   description="異なる複製の種類と、それらをどのような場合に使用するかについて説明します。また、バックアップ セットを使用して個々のボリュームを複製する方法についても説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/14/2015"
   ms.author="alkohli" />

# StorSimple Manager サービスを使用してボリュームを複製する (Update 2)

[AZURE.INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## 概要

StorSimple Manager サービスの **[バックアップ カタログ]** ページには、手動バックアップまたは自動バックアップを実行したときに作成されたすべてのバックアップ セットが表示されます。このページを使用すると、バックアップ ポリシーまたはボリュームのすべてのバックアップを一覧表示したり、バックアップを選択または削除したりできます。また、バックアップを使用してボリュームを復元または複製することもできます。

![[バックアップ カタログ] ページ](./media/storsimple-clone-volume-u2/backupCatalog.png)

このチュートリアルでは、バックアップ セットを使用して個々のボリュームを複製する方法について説明します。また、*一時的な*複製と*永続的な*複製の違いについても説明します。

>[AZURE.NOTE] 
>
>ローカル固定ボリュームは、階層化ボリュームとして複製されます。複製されたボリュームをローカル固定ボリュームにする必要がある場合は、複製操作が正常に完了した後でローカル固定ボリュームに変換できます。階層化ボリュームをローカル固定ボリュームに変換する方法については、「[ボリュームの種類を変更する](storsimple-manage-volumes-u2.md#change-the-volume-type)」を参照してください。
>
>複製後に (ボリュームがまだ一時的な複製であるときに) 複製されたボリュームを階層化ボリュームからローカル固定ボリュームに変換しようとすると、変換は失敗し、次のエラー メッセージが表示されます。
>
>`Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`
>
>一時的な複製を永続的な複製に変換しておけば、ローカル固定ボリュームに問題なく変換できます。一時的な複製を永続的な複製に変換するには、そのクラウド スナップショットを作成します。

## ボリュームの複製を作成する

複製は、ローカル スナップショットまたはクラウド スナップショットを使用して、同じデバイスや別のデバイス上に作成できるほか、仮想マシン上に作成することもできます。

#### ボリュームを複製するには

1. StorSimple Manager サービスのページで、**[バックアップ カタログ]** タブをクリックし、バックアップ セットを選択します。

2. バックアップ セットを展開して、関連付けられているボリュームを表示します。バックアップ セットからボリュームをクリックして選択します。

     ![ボリュームを複製する](./media/storsimple-clone-volume-u2/CloneVol.png)

3. **[複製]** をクリックして、選択したボリュームの複製を開始します。

4. ボリュームの複製ウィザードの **[名前と場所の指定]** で、次の手順を実行します。

  1. ターゲット デバイスを指定します。これは、複製が作成される場所です。同じデバイスを選択することも、別のデバイスを指定することもできます。別のクラウド サービス プロバイダー (Azure 以外) に関連付けられたボリュームを選択する場合、ターゲット デバイスのドロップダウン リストには物理デバイスのみが表示されます。別のクラウド サービスのプロバイダーに関連付けられたボリュームを仮想デバイス上に複製することはできません。

        >[AZURE.NOTE] Make sure that the capacity required for the clone is lower than the capacity available on the target device.

  2. 複製の一意の [ボリューム名] を指定します。名前は 3 文字以上 127 文字以下にする必要があります。
    
        >[AZURE.NOTE] The **Clone Volume As** field will be **Tiered** even if you are cloning a locally pinned volume. You cannot change this setting; however, if you need the cloned volume to be locally pinned as well, you can convert the clone to a locally pinned volume after you successfully create the clone. For information about converting a tiered volume to a locally pinned volume, go to [Change the volume type](storsimple-manage-volumes-u2.md#change-the-volume-type).

        ![Clone wizard 1](./media/storsimple-clone-volume-u2/clone1.png) 

  3. 矢印アイコン ![矢印アイコン](./media/storsimple-clone-volume-u2/HCS_ArrowIcon.png) をクリックして、次のページに進みます。

5. **[このボリュームを使用できるホストの指定]** で、次の手順を実行します。

  1. 複製のアクセス制御レコード (ACR) を指定します。新しい ACR を追加することも、既存の一覧から選択することもできます。

        ![Clone wizard 2](./media/storsimple-clone-volume-u2/clone2.png) 

  2. 操作を完了するには、チェック マーク アイコン ![チェック マーク アイコン](./media/storsimple-clone-volume-u2/HCS_CheckIcon.png) をクリックします。

6. 複製ジョブが開始され、ジョブが正常に作成されると通知が表示されます。**[ジョブの表示]** をクリックすると **[ジョブ]** ページで複製ジョブを監視できます。複製ジョブが終了すると、次のメッセージが表示されます。

    ![複製メッセージ](./media/storsimple-clone-volume-u2/CloneMsg.png)

7. 複製ジョブが完了したら、次の手順を実行します。

  1. **[デバイス]** ページに移動し、**[ボリューム コンテナー]** タブを選択します。
  2. 複製元のボリュームに関連付けられているボリューム コンテナーを選択します。ボリュームの一覧に、先ほど作成した複製が表示されます。

>[AZURE.NOTE] 複製されたボリュームの監視と既定のバックアップは自動的に無効になっています。

このように作成された複製は一時的な複製です。複製の種類の詳細については、「[一時的な複製と永続的な複製](#transient-vs.-permanent-clones)」を参照してください。

この複製はこれで通常のボリュームとなり、ボリュームに対して実行できるすべての操作を利用できます。バックアップを行う場合は、このボリュームを構成する必要があります。

## 一時的な複製と永続的な複製

バックアップ セットの特定のボリュームを複製できます。このように作成された複製は*一時的な*複製です。一時的な複製は元のボリュームを参照し、そのボリュームを使用して読み取りを行いますが、書き込みはローカルで行います。そのため、特に複製されたボリュームのサイズが大きい場合は、パフォーマンスが低下します。

一時的な複製のクラウド スナップショットを作成すると、その複製は*永続的な*複製になります。永続的な複製は独立していて、複製元のボリュームを参照することはありません。パフォーマンスの高速化のために、永続的な複製を作成することをお勧めします。

## 一時的な複製と永続的な複製のシナリオ

ここでは、一時的な複製と永続的な複製が使用される状況の例について説明します。

### 一時的な複製による項目レベルの回復

1 年前の PowerPoint プレゼンテーション ファイルを回復する場合を考えます。IT 管理者はその期間の特定のバックアップを識別し、ボリュームをフィルター処理します。次に管理者はボリュームを複製し、ユーザーが探している特定のファイルを見つけ、ユーザーに提供します。このシナリオでは、一時的な複製を使用します。
 
![ビデオ](./media/storsimple-clone-volume-u2/Video_icon.png) **ビデオ**

StorSimple の複製機能と復元機能を使用して、削除されたファイルを回復する方法を説明したビデオについては、[こちら](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/)を参照してください。

### 永続的な複製による運用環境でのテスト

運用環境でテストのバグを確認する場合を考えます。運用環境でボリュームの複製を作成します。パフォーマンスを向上させるために、この複製のクラウド スナップショットの作成が必要です。複製されたボリュームは独立しており、パフォーマンスの高速化につながります。このシナリオでは、永続的な複製を使用します。

## 次のステップ
- [バックアップ セットからの StorSimple ボリュームの復元方法](storsimple-restore-from-backup-set-u2.md)

- [StorSimple Manager サービスを使用した StorSimple デバイスの管理方法](storsimple-manager-service-administration.md)

 

<!---HONumber=AcomDC_0224_2016-->