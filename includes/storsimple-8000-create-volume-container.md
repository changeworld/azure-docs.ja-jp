---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 4ad58078207a90966134ec4d6cf8815f614e4341
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449879"
---
#### <a name="to-create-a-volume-container"></a>ボリューム コンテナーを作成するには

1. StorSimple デバイス マネージャー サービスに移動し、 **[デバイス]** をクリックします。 表形式のデバイス一覧で、デバイスを選択してクリックます。 

    ![[ボリューム コンテナー] ブレード](./media/storsimple-8000-create-volume-container/create-volume-container-01.png)

2. デバイス ダッシュボードで、 **[+ ボリューム コンテナーの追加]** をクリックします。

    ![[ボリューム コンテナー] ブレード 2](./media/storsimple-8000-create-volume-container/create-volume-container-02.png)

3. **[ボリューム コンテナーの追加]** ブレードで、次の操作を行います。
   
   1. デバイスは自動的に選択されます。
   2. ボリューム コンテナーの **[名前]** を指定します。 名前は 3 ～ 32 文字で指定する必要があります。 ボリューム コンテナーの作成後に、その名前を変更することはできません。
   3. **[クラウド ストレージの暗号化を有効にする]** を選択して、デバイス からクラウドに送信されるデータの暗号化を有効にします。
   4. 8 ～ 32 文字の **[クラウド ストレージ暗号化キー]** を指定し、確定します。 このキーは、デバイス が暗号化されたデータにアクセスするために使用されます。
   5. このボリューム コンテナーに関連付ける **[ストレージ アカウント]** を選択します。 既存のストレージ アカウントか、サービス作成時に生成される既定のアカウントを選択できます。 **[新規追加]** オプションを使用して、このサービス サブスクリプションにリンクしないストレージ アカウントを指定することもできます。
   6. 利用可能な帯域幅をすべて使用する場合は、 **[帯域幅の指定]** ボックスの一覧で **[無制限]** を選択します。
   
      帯域幅の使用状況に関する情報がある場合は、 **[帯域幅テンプレートの選択]** を指定して、スケジュールに基づいて帯域幅を割り当てることもできます。 詳細な手順については、[帯域幅テンプレートの追加](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template)に関する記事をご覧ください。

      ![[ボリューム コンテナー] ブレード 3](./media/storsimple-8000-create-volume-container/create-volume-container-06-b.png)<!--New graphic. Source: add-volume-container-bw-setting.-->

   7. **Create** をクリックしてください。

        <!--![Volume container blade 4](./media/storsimple-8000-create-volume-container/create-volume-container-06.png)-->
   
       ボリューム コンテナーが正常に作成されると、通知が表示されます。

       ![ボリューム コンテナーの作成の通知](./media/storsimple-8000-create-volume-container/create-volume-container-08.png)

   新しく作成されたボリューム コンテナーが、デバイスのボリューム コンテナー一覧に表示されます。

   ![[ボリューム コンテナーの追加] ブレード](./media/storsimple-8000-create-volume-container/create-volume-container-09.png)
