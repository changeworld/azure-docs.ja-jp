---
title: "クラシック デプロイ モデルを使用した Azure からの Windows Server または Windows Client へのデータの復元 | Microsoft Docs"
description: "Windows Server または Windows クライアントからの復元方法について説明します。"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 85585dfc-c764-4e8c-8f0e-40b969640ac2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: trinadhk; jimpark; markgal;
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 8216106cf44016d355885d9117b6ac69127341c0


---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-the-classic-deployment-model"></a>クラシック デプロイメント モデルを使用した Windows Server または Windows Client コンピューターへのファイルの復元
> [!div class="op_single_selector"]
> * [クラシック ポータル](backup-azure-restore-windows-server-classic.md)
> * [Azure ポータル](backup-azure-restore-windows-server.md)
> 
> 

この記事では、復元操作に必要な 2 種類の手順を説明します。

* バックアップが実行されたのと同じマシンにデータを復元する
* その他の任意のマシンにデータを復元する

いずれの場合も、データは Azure Backup コンテナーから取得されます。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

## <a name="recover-data-to-the-same-machine"></a>同じコンピューターへのデータの回復
ファイルを誤って削除してしまったため、バックアップを実行したのと同じコンピューターにそのファイルを復元するには、次の手順でデータを回復できます。

1. **Microsoft Azure Backup** スナップインを開きます。
2. [ **データの回復** ] をクリックして、ワークフローを開始します。
   
    ![データの回復](./media/backup-azure-restore-windows-server-classic/recover.png)
3. バックアップ ファイルを同じコンピューターに復元する場合は、**[このサーバー (コンピューター名)]**** オプションを選択します。
   
    ![Same machine](./media/backup-azure-restore-windows-server-classic/samemachine.png)
4. **[ファイルの参照]** または **[ファイルの検索]** を選択します。
   
    パスがわかっている 1 つまたは複数のファイルを復元する場合は、既定のオプションをそのまま使用します。 フォルダー構造がわからず、ファイルを検索する場合は、 **[ファイルの検索]** オプションを選択します。 ここでは、既定のオプションを使用します。
   
    ![Browse files](./media/backup-azure-restore-windows-server-classic/browseandsearch.png)
5. ファイルの復元元となるボリュームを選択します。
   
    任意の時点から復元できます。 カレンダー コントロールに **太字** で表示される日付は、復元ポイントが存在していることを示しています。 日付を選択すると、バックアップ スケジュールに基づいて (バックアップ操作が成功していれば)、 **[時間]** ドロップダウンから特定の時点を選択できます。
   
    ![Volume and Date](./media/backup-azure-restore-windows-server-classic/volanddate.png)
6. 回復する項目を選択します。 復元するフォルダーまたはファイルを複数選択することができます。
   
    ![Select files](./media/backup-azure-restore-windows-server-classic/selectfiles.png)
7. 回復パラメーターを指定します。
   
    ![Recovery options](./media/backup-azure-restore-windows-server-classic/recoveroptions.png)
   
   * 元の場所に復元することも (ファイルまたはフォルダーが上書きされます)、同じコンピューターの別の場所に復元することもできます。
   * 復元するファイルまたはフォルダーが復元先の場所に存在する場合、コピーを作成するか (同じファイルの 2 つのバージョンが作成されます)、復元先の場所にあるファイルを上書きするか、復元先に存在するファイルの回復をスキップすることができます。
   * 復元されるファイルの ACL を復元するという既定のオプションは、そのままにしておくことを強くお勧めします。
8. これらのうちいずれか 1 つを指定して、[ **次へ**] をクリックします。 このコンピューターにファイルを復元する回復ワークフローが開始します。

## <a name="recover-to-an-alternate-machine"></a>別のコンピューターへの回復
サーバー全体が失われた場合でも、 Azure Backup から別のコンピューターにデータを回復できます。 次の手順はそのワークフローを示しています。  

この手順で使用される用語は次のとおりです。

* *ソース コンピューター* – バックアップが実行され、現在は使用できなくなっている元のコンピューター。
* *ターゲット コンピューター* – データの回復先となるコンピューター。
* "*サンプルの資格情報コンテナー*" – "*ソース コンピューター*" と "*ターゲット コンピューター*" が登録されているバックアップ資格情報コンテナー。 <br/>

> [!NOTE]
> コンピューターのバックアップは、以前のバージョンのオペレーティング システムが実行されているコンピューターには復元できません。 たとえば、バックアップが Windows 7 コンピューターで行われた場合、Windows 8 以上のコンピューターにそのバックアップを復元できます。 ただし、その逆は当てはまりません。
> 
> 

1. **ターゲット コンピューター** の *Microsoft Azure Backup*スナップインを開きます。
2. "*ターゲット コンピューター*" および "*ソース コンピューター*" が同じバックアップ資格情報コンテナーに登録されていることを確認します。
3. [ **データの回復** ] をクリックして、ワークフローを開始します。
   
    ![データの回復](./media/backup-azure-restore-windows-server-classic/recover.png)
4.  **[別のサーバー]**
   
    ![[別のサーバー]](./media/backup-azure-restore-windows-server-classic/anotherserver.png)
5. *サンプルの資格情報コンテナー*に対応するコンテナー資格情報ファイルを指定します。 コンテナー資格情報ファイルが無効である (または有効期限が切れている) 場合は、Azure クラシック ポータルの *サンプルの資格情報コンテナー* から、新しいコンテナー資格情報ファイルをダウンロードします。 資格情報コンテナーの資格情報ファイルを指定すると、その資格情報コンテナーの資格情報ファイルに対するバックアップ資格情報コンテナーが表示されます。
6. 表示されているコンピューターの一覧から *ソース コンピューター* を選択します。
   
    ![List of machines](./media/backup-azure-restore-windows-server-classic/machinelist.png)
7. **[ファイルの検索]** または **[ファイルの参照]** オプションのいずれかを選択します。 ここでは、 **[ファイルを検索]** オプションを使用します。
   
    ![Search](./media/backup-azure-restore-windows-server-classic/search.png)
8. 次の画面で、ボリュームと日付を選択します。 復元するフォルダー名とファイル名を検索します。
   
    ![Search items](./media/backup-azure-restore-windows-server-classic/searchitems.png)
9. ファイルの復元先にする場所を選択します。
   
    ![Restore location](./media/backup-azure-restore-windows-server-classic/restorelocation.png)
10. "*ソース コンピューター*" を "*サンプルの資格情報コンテナー*" に登録する際に指定した暗号化パスフレーズを入力します。
    
    ![暗号化](./media/backup-azure-restore-windows-server-classic/encryption.png)
11. 入力して [ **回復**] ボタンをクリックすると、指定した復元先でバックアップ ファイルの復元が開始します。

## <a name="next-steps"></a>次のステップ
* [Azure Backup FAQ](backup-azure-backup-faq.md)
* [Azure Backup フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=290933)にアクセスします。

## <a name="learn-more"></a>詳細情報
* [Azure Backup の概要](http://go.microsoft.com/fwlink/p/?LinkId=222425)
* [Azure 仮想マシンのバックアップ](backup-azure-vms-introduction.md)
* [Microsoft ワークロードのバックアップ](backup-azure-dpm-introduction.md)




<!--HONumber=Dec16_HO2-->


