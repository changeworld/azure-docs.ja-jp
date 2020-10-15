---
title: Azure NetApp Files を使用してスナップショットを管理する | Microsoft Docs
description: Azure NetApp Files を使用してスナップショットを作成して管理する方法について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/04/2020
ms.author: b-juche
ms.openlocfilehash: e9f2a1f9125d25caa9506e954cab3b94dfcb5c24
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932279"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Azure NetApp Files を使用して、スナップショットを管理する

Azure NetApp Files では、オンデマンドのスナップショットの作成と、スナップショット ポリシーを使用した自動スナップショット作成のスケジュール設定がサポートされています。  新しいボリュームにスナップショットを復元したり、クライアントを使用して 1 つのファイルを復元したりすることもできます。  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>ボリュームのオンデマンド スナップショットを作成する

ボリュームのスナップショットをオンデマンドで作成できます。 

1.  スナップショットを作成するボリュームに移動します。 **[スナップショット]** をクリックします。

    ![スナップショットに移動する](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  **[+ スナップショットの追加]** をクリックして、ボリュームのオンデマンド スナップショットを作成します。

    ![スナップショットを追加する](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  [新しいスナップショット] ウィンドウで、作成する新しいスナップショットの名前を指定します。   

    ![新しいスナップショット](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. **[OK]** をクリックします。 

## <a name="manage-snapshot-policies"></a>スナップショット ポリシーを管理する

スナップショット ポリシーを使用して、ボリュームのスナップショットが自動的に取得されるようにスケジュールすることができます。 必要に応じてスナップショット ポリシーを変更したり、不要になったスナップショット ポリシーを削除したりすることもできます。  

### <a name="register-the-feature"></a>機能を登録する

**スナップショット ポリシー**機能は、現在プレビュー段階です。 この機能を初めて使用する場合は、まず機能を登録する必要があります。 

1. 機能を登録します。 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. 機能の登録の状態を確認します。 

    > [!NOTE]
    > **RegistrationState** が `Registering` 状態から `Registered` に変化するまでに最大 60 分間かかる場合があります。 この状態が **Registered** になってから続行してください。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```
また、[Azure CLI のコマンド](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` と `az feature show` を使用して、機能を登録し、登録状態を表示することもできます。 

### <a name="create-a-snapshot-policy"></a>スナップショット ポリシーを作成する 

スナップショット ポリシーを使用すると、スナップショットの作成頻度 (時間単位、日単位、週単位、または月単位) を指定できます。 また、ボリュームに対して保持するスナップショットの最大数も指定する必要があります。  

1.  NetApp アカウント ビューで、 **[スナップショット ポリシー]** をクリックします。

    ![スナップショット ポリシーのナビゲーション](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  [スナップショット ポリシー] ウィンドウで、[ポリシーの状態] を **[有効]** に設定します。 

3.  **[時間単位]** 、 **[日単位]** 、 **[週単位]** 、または **[月単位]** タブをクリックして、時間単位、日単位、週単位、または月単位のスナップショット ポリシーを作成します。 **[保持するスナップショット数]** を指定します。  

    1 つのボリュームで許可されるスナップショットの最大数については、「[Azure NetApp Files のリソースの制限](azure-netapp-files-resource-limits.md)」を参照してください。 

    次の例は、時間単位のスナップショット ポリシーの構成を示しています。 

    ![スナップショット ポリシー (時間単位)](../media/azure-netapp-files/snapshot-policy-hourly.png)

    次の例は、日単位のスナップショット ポリシーの構成を示しています。

    ![スナップショット ポリシー (日単位)](../media/azure-netapp-files/snapshot-policy-daily.png)

    次の例は、週単位のスナップショット ポリシーの構成を示しています。

    ![スナップショット ポリシー (週単位)](../media/azure-netapp-files/snapshot-policy-weekly.png)

    次の例は、月単位のスナップショット ポリシーの構成を示しています。

    ![スナップショット ポリシー (月単位)](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  **[保存]** をクリックします。  

追加のスナップショット ポリシーを作成する必要がある場合は、手順 3 を繰り返します。
作成したポリシーは、[スナップショット ポリシー] ページに表示されます。

ボリュームでスナップショット ポリシーを使用する場合は、[ボリュームにポリシーを適用する](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume)必要があります。 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>ボリュームにスナップショット ポリシーを適用する

作成したスナップショット ポリシーをボリュームで使用する場合は、ボリュームにポリシーを適用する必要があります。 

1.  **[ボリューム]** ページに移動し、スナップショット ポリシーを適用するボリュームを右クリックし、 **[編集]** を選択します。

    ![ボリュームの右クリックメニュー](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  [編集] ウィンドウの **[スナップショット ポリシー]** で、ボリュームに使用するポリシーを選択します。  **[OK]** をクリックしてポリシーを適用します。  

    ![スナップショット ポリシーの編集](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>スナップショット ポリシーを変更する 

既存のスナップショット ポリシーを変更して、ポリシーの状態、スナップショットの頻度 (時間単位、日単位、週単位、または月単位)、または保持するスナップショットの数を変更できます。  
 
1.  NetApp アカウント ビューで、 **[スナップショット ポリシー]** をクリックします。

2.  変更するスナップショット ポリシーを右クリックし、 **[編集]** を選択します。

    ![スナップショット ポリシーの右クリック メニュー](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  表示された [スナップショット ポリシー] ウィンドウで変更を行い、 **[保存]** をクリックします。 

### <a name="delete-a-snapshot-policy"></a>スナップショット ポリシーを削除する 

保持する必要がなくなったスナップショット ポリシーを削除できます。   

1.  NetApp アカウント ビューで、 **[スナップショット ポリシー]** をクリックします。

2.  変更するスナップショット ポリシーを右クリックし、 **[削除]** を選択します。

    ![スナップショット ポリシーの右クリック メニュー](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  **[はい]** をクリックして、スナップショット ポリシーの削除を確認します。   

    ![スナップショット ポリシーの削除の確認](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>スナップショットから新しいボリュームを復元する

現時点では、スナップショットから復元できるのは、新しいボリュームに限られています。 
1. [ボリューム] ブレードから **[スナップショット]** を選択して、スナップショットの一覧を表示します。 
2. 復元するスナップショットを右クリックし、メニュー オプションから **[新しいボリュームに復元]** を選択します。  

    ![スナップショットから新しいボリュームを復元する](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. [ボリュームの作成] ウィンドウで、新しいボリュームの情報を指定します。  
    * **[名前]**    
        作成するボリュームの名前を指定します。  
        
        名前はリソース グループ内で一意である必要があります。 3 文字以上になるようにしてください。  任意の英数字を使用できます。

    * **[クォータ]**  
        ボリュームに割り当てる論理ストレージの量を指定します。  

    ![新しいボリュームに復元](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. **[確認および作成]** をクリックします。  **Create** をクリックしてください。   
    新しいボリュームでは、スナップショットで使用されていたものと同じプロトコルが使用されます。   
    スナップショットから復元された新しいボリュームが [ボリューム] ブレードに表示されます。

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>クライアントを使用してスナップショットからファイルを復元する

[スナップショット全体をボリュームに復元する](#restore-a-snapshot-to-a-new-volume)ことを望まない場合は、ボリュームがマウントされているクライアントを使用してスナップショットからファイルを復元できます。  

マウントされたボリュームには、クライアントからアクセス可能な `.snapshot` (NFS クライアントの場合) または`~snapshot` (SMB クライアントの場合) という名前のスナップショットディレクトリが含まれています。 スナップショット ディレクトリには、ボリュームのスナップショットに対応するサブディレクトリが含まれています。 各サブディレクトリには、スナップショットのファイルが含まれます。 ファイルを誤って削除または上書きした場合、ファイルをスナップショットのサブディレクトリから読み取り/書き込みディレクトリにコピーすることで、ファイルを親の読み取り/書き込みディレクトリに復元できます。 

ボリュームの作成時に [スナップショット パスを非表示にする] チェックボックスをオンにした場合、スナップショット ディレクトリは非表示になります。 ボリュームを選択することで、ボリュームの [スナップショット パスの非表示] 状態を表できます。 ボリュームのページで **[編集]** をクリックすることで、[スナップショット パスを非表示にする] オプションを編集できます。  

![ボリュームのスナップショット オプションを編集する](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Linux NFS クライアントを使用してファイルを復元する 

1. `ls` Linux コマンドを使用して、`.snapshot` ディレクトリから復元するファイルを一覧表示します。 

    次に例を示します。

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. `cp` コマンドを使用して、ファイルを親ディレクトリにコピーします。  

    次に例を示します。 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>Windows クライアントを使用してファイルを復元する 

1. ボリュームの `~snapshot` ディレクトリが非表示になっている場合は、親ディレクトリの[非表示の項目を表示](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10)して、`~snapshot` を表示します。

    ![非表示の項目を表示](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. `~snapshot` 内のサブディレクトリに移動して、復元するファイルを見つけます。  ファイルを右クリックします。 **[コピー]** を選択します。  

    ![ファイルをコピーして復元する](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. 親ディレクトリに戻ります。 親ディレクトリを右クリックし、[`Paste`] を選択して、ファイルをディレクトリに貼り付けます。

    ![ファイルを貼り付けて復元する](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. 親ディレクトリを右クリックし、 **[プロパティ]** を選択します。 **[以前のバージョン]** タブをクリックしてスナップショットの一覧を表示し、 **[復元]** を選択してファイルを復元することもできます。  

    ![[プロパティ] の [以前のバージョン]](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="next-steps"></a>次のステップ

* [スナップショット ポリシーのトラブルシューティング](troubleshoot-snapshot-policies.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files のスナップショット 101 ビデオ](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)