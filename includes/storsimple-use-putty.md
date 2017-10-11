<!--author=SharS last changed: 9/17/15-->

#### <a name="to-connect-through-the-serial-console"></a>シリアル コンソールを介して接続するには
1. シリアル ケーブルをデバイスに接続 (直接または USB シリアル アダプターを経由)。
2. 開く、**コントロール パネルの** を開き、**デバイス マネージャー**です。
3. 次の図に示すように、COM ポートを指定します。
   
     ![シリアル コンソールによる接続](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)
4. PuTTY を起動します。 
5. 右側のウィンドウで、変更、**接続の種類**に**シリアル**です。
6. 右側のウィンドウで、適切な COM ポートを入力します。 シリアル構成パラメーターが次のように設定することを確認します。
   
   * 速度: 115,200
   * データ ビット: 8
   * ストップ ビット: 1
   * パリティ: なし
   * フロー制御: なし
     
     これらの設定は、次の図に表示されます。
     
     ![PuTTY 設定](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 
     
     > [!NOTE]
     > 既定のフロー制御の設定が機能しない場合は、フロー制御を XON/XOFF に設定してください。
     > 
     > 
7. をクリックして**開く**シリアル セッションを開始します。

