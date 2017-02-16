---
title: "Windows Server または Windows コンピューターへの Azure のデータの復元 | Microsoft Docs"
description: "Azure に格納されているデータを Windows Server または Windows コンピューターに復元する方法について説明します。"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 742f4b9e-c0ab-4eeb-8e22-ee29b83c22c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/30/2017
ms.author: trinadhk;jimpark;markgal;
translationtype: Human Translation
ms.sourcegitcommit: bb07ff13e3e4023e334c706d5f9bd95196ee6051
ms.openlocfilehash: 47c5789fc3379b702d8f6ceafd630bece669b11e


---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Resource Manager デプロイメント モデルを使用した Windows Server または Windows Client コンピューターへのファイルの復元
> [!div class="op_single_selector"]
> * [Azure ポータル](backup-azure-restore-windows-server.md)
> * [クラシック ポータル](backup-azure-restore-windows-server-classic.md)
>
>

この記事では、復元操作に必要な&2; 種類の手順を説明します。

* バックアップが実行されたのと同じマシンにデータを復元する
* その他の任意のマシンにデータを復元する

いずれの場合も、データは Azure Recovery Services コンテナーから取得されます。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-restore-data-to-the-same-machine"></a>インスタント リストアを使用して同じコンピューターにデータを復元する
ファイルを誤って削除してしまったため、バックアップを実行したのと同じコンピューターにそのファイルを復元するには、次の手順でデータを回復できます。

1. **Microsoft Azure Backup** スナップインを開きます。 スナップインがインストールされた場所がわからない場合は、コンピューターまたはサーバーで **Microsoft Azure Backup** を検索します。

    デスクトップ アプリが検索結果に表示されます。

2. **[データの回復]** をクリックして、ウィザードを開始します。

    ![データの回復](./media/backup-azure-restore-windows-server/recover.png)

3. 同じサーバーまたはコンピューターにデータを復元するには、**[作業の開始]** ウィンドウで、**[このサーバー] (`<server name>`)** を選択し、**[次へ]** をクリックします。

    ![[このサーバー] オプションを選択すると、データが同じマシンに復元されます](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. **[回復モードの選択]** ウィンドウで、何を復元するかを選択します。
    - **[個々のファイルおよびフォルダー]** - 特定のファイルとフォルダーを復元する場合は、このオプションを使用します。
    - **[ボリューム]** - 選択したボリュームにあるすべてのファイルとフォルダーを復元する場合は、このオプションを使用します。

    次に、**[次へ]** をクリックします。

    ![Browse files](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. **[ボリュームと日付の選択]** ウィンドウで、復元するファイルやフォルダーが格納されているボリュームを選択します。

    カレンダーで回復ポイントを選択します。 任意の時点の回復ポイントから復元できます。 **太字**になっている日付では、少なくとも&1; つの回復ポイントを利用できます。 選択した日付で複数の回復ポイントを利用できる場合は、**[時間]** ドロップダウン メニューから、特定の回復ポイントを選択します。

    ![Volume and Date](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. 復元する回復ポイントを選択したら、**[マウント]** をクリックします。

    Azure Backup がローカルの回復ポイントをマウントし、回復ボリュームとして使用します。

7. **[ファイルの参照と回復]** ウィンドウで、**[参照]** をクリックして Windows エクスプローラーを開き、ファイルとフォルダーを検索します。

    ![Recovery options](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

   * 元の場所に復元することも (ファイルまたはフォルダーが上書きされます)、同じコンピューターの別の場所に復元することもできます。
   * 復元するファイルまたはフォルダーが復元先の場所に存在する場合、コピーを作成するか (同じファイルの&2; つのバージョンが作成されます)、復元先の場所にあるファイルを上書きするか、復元先に存在するファイルの回復をスキップすることができます。
   * 復元されるファイルの ACL を復元するという既定のオプションは、そのままにしておくことを強くお勧めします。
8. Windows エクスプローラーで、復元するファイルやフォルダーをコピーして、サーバーまたはコンピューターの任意のローカルの場所に貼り付けます。 または回復ボリュームから直接ファイルを開くかストリーミングし、正しいバージョンが回復されていることを確認します。

    ![マウントされたボリュームからファイルとフォルダーをコピーしてローカルの保存先に貼り付ける](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. ファイルやフォルダーの復元が完了したら、**[ファイルの参照と回復]** ウィンドウで、**[マウント解除]** をクリックします。 ボリュームのマウントを解除するかどうかを確認するメッセージが表示されたら **[はい]** をクリックします。

    ![ボリュームのマウント解除と確認](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > [マウント解除] をクリックしない場合は、回復ボリュームはマウントされた時刻から&6; 時間、マウントされたままになります。 ボリュームのマウント中は、バックアップ操作が実行されません。 ボリュームのマウント中に実行されるようにスケジュール設定されているバックアップ操作は、回復ボリュームのマウント解除後に実行されます。
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>インスタント リストアを使用してデータを別のコンピューターに復元する
サーバー全体が失われた場合でも、Azure Backup から別のコンピューターにデータを回復できます。 次の手順はそのワークフローを示しています。

> [!NOTE]
> インスタント リストアを使用して別のコンピューターにデータを復元する場合は、[2017 年 1 月の Azure Backup 更新プログラム](https://support.microsoft.com/en-us/help/3216528?preview)が必要です。 また、バックアップ データは、サポート記事に掲載されているロケールの資格情報コンテナーで保護されている必要があります。 現時点では、インスタント リストアを使用できないロケールがあります。
>

この手順で使用される用語は次のとおりです。

* *ソース コンピューター* – バックアップが実行され、現在は使用できなくなっている元のコンピューター。
* *ターゲット コンピューター* – データの回復先となるコンピューター。
* "*サンプルのコンテナー*" – "*ソース コンピューター*" と "*ターゲット コンピューター*" が登録されている Recovery Services コンテナー。 <br/>

> [!NOTE]
> 古いバージョンのオペレーティング システムが実行されているコンピューターには、バックアップを復元できません。 たとえば、Windows 7 コンピューターのバックアップは、Windows 8 以降のコンピューターで復元できます。 Windows 8 コンピューターのバックアップを Windows 7 のコンピューターには復元することはできません。
>
>

1. **ターゲット コンピューター** の *Microsoft Azure Backup*スナップインを開きます。

2. *ターゲット コンピューター*と*ソース コンピューター*が同じ Recovery Services コンテナーに登録されていることを確認します。

3. **[データの回復]** をクリックして、**[データの回復ウィザード]** を開きます。

    ![データの回復](./media/backup-azure-restore-windows-server/recover.png)

4. **[作業の開始]** ウィンドウで、**[別のサーバー]** を選択します。

    ![[別のサーバー]](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. *サンプルの資格情報コンテナー*に対応するコンテナー資格情報ファイルを指定し、**[次へ]** をクリックします。

    コンテナー資格情報ファイルが無効である (または有効期限が切れている) 場合、Azure Portal の *サンプルの資格情報コンテナー*から、新しいコンテナー資格情報ファイルをダウンロードします。 有効なコンテナー資格情報を指定すると、対応する Backup Vault の名前が表示されます。

6. **[バックアップ サーバーの選択]** ウィンドウで、表示されているコンピューターの一覧から*ソース コンピューター*を選択し、パスフレーズを入力します。 その後、 **[次へ]**をクリックします。

    ![List of machines](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. **[回復モードの選択]** ウィンドウで、**[個々のファイルおよびフォルダー]** を選択して、**[次へ]** をクリックします。

    ![Search](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. **[ボリュームと日付の選択]** ウィンドウで、復元するファイルやフォルダーが格納されているボリュームを選択します。

    カレンダーで回復ポイントを選択します。 任意の時点の回復ポイントから復元できます。 **太字**になっている日付では、少なくとも&1; つの回復ポイントを利用できます。 選択した日付で複数の回復ポイントを利用できる場合は、**[時間]** ドロップダウン メニューから、特定の回復ポイントを選択します。

    ![Search items](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. **[マウント]** をクリックして、*ターゲット マシン*の回復ボリュームを回復ポイントとしてローカルでマウントします。

10. **[ファイルの参照と回復]** ウィンドウで、**[参照]** をクリックして Windows エクスプローラーを開き、ファイルとフォルダーを検索します。

    ![Encryption](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. Windows エクスプローラーで、回復ボリュームからファイルやフォルダーをコピーして、*ターゲット コンピューター*の保存先に貼り付けます。 または回復ボリュームから直接ファイルを開くかストリーミングし、正しいバージョンが回復されていることを確認します。

    ![Encryption](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. ファイルやフォルダーの復元が完了したら、**[ファイルの参照と回復]** ウィンドウで、**[マウント解除]** をクリックします。 ボリュームのマウントを解除するかどうかを確認するメッセージが表示されたら **[はい]** をクリックします。

    ![Encryption](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > [マウント解除] をクリックしない場合は、回復ボリュームはマウントされた時刻から&6; 時間、マウントされたままになります。 ボリュームのマウント中は、バックアップ操作が実行されません。 ボリュームのマウント中に実行されるようにスケジュール設定されているバックアップ操作は、回復ボリュームのマウント解除後に実行されます。
    >

## <a name="next-steps"></a>次のステップ
* ファイルとフォルダーを回復したので、 [バックアップを管理](backup-azure-manage-windows-server.md)できます。



<!--HONumber=Jan17_HO5-->


