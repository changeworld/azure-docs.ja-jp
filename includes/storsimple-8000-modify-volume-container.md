---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4cffbb1aaa438bac08ec77f576b32a901abacabd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "67181221"
---
> [!NOTE] 
> 暗号化の設定と、ボリューム コンテナーに関連付けられているストレージ アカウントの資格情報は、ボリューム コンテナーの作成後に変更することはできません。

#### <a name="to-modify-a-volume-container"></a>ボリューム コンテナーを変更するには

1. StorSimple デバイス マネージャー サービスに移動し、 **[管理]、[ボリューム コンテナー]** の順に移動します。

2. ボリューム コンテナーの表形式の一覧で、変更するボリューム コンテナーを選択します。 **[デバイス]** ページでデバイスを選択し、ダブルクリックします。次に、 **[ボリューム コンテナー]** タブをクリックします。

2. ボリューム コンテナーの表形式の一覧で、変更するボリューム コンテナーを選択します。 表示されたブレードで、コマンド バーから **[変更]** をクリックします。

    ![ボリューム コンテナーの変更](./media/storsimple-8000-modify-volume-container/modify-vol-container1.png)

3. **[ボリューム コンテナーの変更]** ブレードで、次の操作を行います。
   
   1. ボリューム コンテナーに関連付けられている名前、暗号化キー、ストレージ アカウントは、指定後に変更することはできません。 関連付けられている帯域幅の設定を変更します。
      
       ![帯域幅の設定の変更](./media/storsimple-8000-modify-volume-container/modify-vol-container2.png)

   2.  **[OK]** をクリックします。
4. **[ボリューム コンテナーの変更]** ダイアログ ボックスの次のページで、次の操作を行います。
   
   1. ドロップダウン リストから、既存の帯域幅テンプレートを選択します。
   2. 指定した帯域幅テンプレートのスケジュール設定を確認します。
   3. **[保存]** をクリックして変更を確定します。
      
       ![変更の確定](./media/storsimple-8000-modify-volume-container/modify-vol-container3.png)

   3. **[ボリューム コンテナー]** ブレードが更新され、変更が反映されます。

