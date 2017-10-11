<!--author=SharS last changed: 02/04/2016-->

#### <a name="to-create-a-volume"></a>ボリュームを作成するには
1. デバイスで**クイック スタート**] ページで [**ボリュームを追加**です。 これは、追加のボリューム ウィザードを起動します。
2. 追加ボリューム ウィザード の下にある**基本設定**、次の操作します。
   
   1. 指定、**名前**ボリュームのです。
   2. 指定して、**プロビジョニングされた容量**GB または TB のボリュームのです。 ボリュームの容量は、物理デバイスの 1 GB ~ 64 TB でなければなりません。
   3. ドロップダウン リストで選択、**使用法の種類**ボリュームのです。 
   4. このボリュームのアーカイブ データを使用している場合は、選択、**このボリュームを使用して、アクセス頻度の低いアーカイブ データ**チェック ボックスをオンします。 その他のすべてのユース ケースでは、単に選択**階層型ボリューム**です。 (階層化ボリュームが旧称プライマリ ボリューム)。
      
        ![ボリュームを追加する](./media/storsimple-create-volume/ScreenshotUpdate1VolumeFlow.png)
      
      1. 矢印アイコンをクリックします。 ![矢印アイコン](./media/storsimple-create-volume/HCS_ArrowIcon-include.png) 次のページに移動します。
3. **追加設定** ダイアログ ボックスで新しいアクセス制御レコード (ACR) を追加します。
   
   1. 指定、**名前**ACR のです。
   2. **ISCSI イニシエーター名**iSCSI を提供する Windows ホストの修飾名 (IQN)。 IQN を持っていない場合に進みます[、Windows Server ホストの IQN の取得](#get-the-iqn-of-a-windows-server-host)です。
   3. 選択すると、既定のバックアップを有効にすることをお勧め、**このボリュームの既定のバックアップを有効にする**チェック ボックスをオンします。 既定のバックアップを毎日 (デバイスの時刻) の 22時 30分に実行をこのボリュームのクラウド スナップショットを作成するポリシーが作成されます。
      
      > [!NOTE]
      > バックアップがここに有効になっているは元に戻せません。 この設定を変更するボリュームを編集する必要があります。
      > 
      > 
      
        ![ボリュームを追加する](./media/storsimple-create-volume/AddVolume2-include.png)
4. チェック アイコンをクリックします。 ![チェック アイコン](./media/storsimple-create-volume/HCS_CheckIcon-include.png)。 ボリュームは、指定された設定で作成されます。

![使用可能なビデオ](./media/storsimple-create-volume/Video_icon.png)**ビデオ**

StorSimple ボリュームを作成する方法を示すビデオを見るには、をクリックして[ここ](https://azure.microsoft.com/documentation/videos/create-a-storsimple-volume/)です。

