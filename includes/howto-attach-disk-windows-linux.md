Azure の仮想マシンのディスクの詳細については、「[About Virtual Machine Disks in Azure (Azure の仮想マシンのディスクについて)][]」を参照してください。

## <span id="attachempty"></span></a>方法: 空のディスクの接続

空のディスクを接続すると、Azure が .vhd ファイルを作成してそれをストレージ アカウントに保存するため、データ ディスクを比較的簡単に追加できます。

1.  **[仮想マシン]** をクリックし、適切な仮想マシンを選択します。

2.  コマンド バーで **[接続]** をクリックし、**[空のディスクの接続]** を選択します。

    ![空のディスクの接続][]

    **[空のディスクの接続]** ダイアログ ボックスが表示されます。

    ![新しい空のディスクの接続][]

3.  **[ファイル名]** で、自動的に生成される名前を受け入れるか、わかりやすい名前を入力します。.vhd ファイルから作成されたデータ ディスクでは必ず、自動的に生成された名前が使用されます。

4.  **[サイズ]** ボックスに、データ ディスクのサイズを入力します。

5.  チェック マークをクリックして、空のデータ ディスクを接続します。

    データ ディスクが仮想マシンのダッシュボードに表示されます。

    ![空のデータ ディスクの接続に成功][]

## <span id="attachexisting"></span></a>方法: 既存のディスクの接続

既存のディスクを接続する場合は、ストレージ アカウントで利用できる .vhd を持っている必要があります。.vhd ファイルをストレージ アカウントにアップロードするには、[Add-AzureVhd][] コマンドレットを使用します。.vhd ファイルを作成してアップロードした後は、それを仮想マシンに接続できます。

1.  **[仮想マシン]** をクリックし、適切な仮想マシンを選択します。

2.  コマンド バーで **[接続]** をクリックし、**[ディスクの接続]** を選択します。

    ![データ ディスクの接続][]

    **[ディスクの接続]** ダイアログ ボックスが表示されます。

    ![データ ディスク詳細の入力][]

3.  仮想マシンに接続するデータ ディスクを選択します。
4.  チェック マークをクリックして、仮想マシンにデータ ディスクを接続します。

    データ ディスクが仮想マシンのダッシュボードに表示されます。

    ![データ ディスクの接続に成功][]

> [WACOM.NOTE]
> データ ディスクを追加した後、仮想マシンがストレージとしてそのディスクを使用できるように、仮想マシンにログオンしてディスクを初期化する必要があります。

  [About Virtual Machine Disks in Azure (Azure の仮想マシンのディスクについて)]: http://go.microsoft.com/fwlink/p/?LinkId=403697
  [空のディスクの接続]: ./media/howto-attach-disk-window-linux/AttachDiskWindows.png
  [新しい空のディスクの接続]: ./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png
  [空のデータ ディスクの接続に成功]: ./media/howto-attach-disk-window-linux/AttachEmptySuccess.png
  [Add-AzureVhd]: http://go.microsoft.com/FWLink/p/?LinkID=391684
  [データ ディスクの接続]: ./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png
  [データ ディスク詳細の入力]: ./media/howto-attach-disk-window-linux/AttachExistingDisk.png
  [データ ディスクの接続に成功]: ./media/howto-attach-disk-window-linux/AttachSuccess.png
