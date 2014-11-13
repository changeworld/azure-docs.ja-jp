Azure の仮想マシンのディスクの詳細については、「[About Virtual Machine Disks in Azure (Azure の仮想マシンのディスクについて)][About Virtual Machine Disks in Azure (Azure の仮想マシンのディスクについて)]」を参照してください。

## <span id="attachempty"></span></a>方法: Attach an empty disk

空のディスクを接続すると、Azure が .vhd ファイルを作成してそれをストレージ アカウントに保存するため、データ ディスクを比較的簡単に追加できます。

1.  **[仮想マシン]** をクリックし、適切な仮想マシンを選択します。

2.  コマンド バーで **[接続]** をクリックし、**[空のディスクの接続]** を選択します。

    ![Attach an empty disk][Attach an empty disk]

    **[空のディスクの接続]** ダイアログ ボックスが表示されます。

    ![Attach a new empty disk][Attach a new empty disk]

3.  **[ファイル名]** で、自動的に生成される名前を受け入れるか、わかりやすい名前を入力します。.vhd ファイルから作成されたデータ ディスクでは必ず、自動的に生成された名前が使用されます。

4.  **[サイズ]** ボックスに、データ ディスクのサイズを入力します。

5.  チェック マークをクリックして、空のデータ ディスクを接続します。

    データ ディスクが仮想マシンのダッシュボードに表示されます。

    ![Empty data disk successfully attached][Empty data disk successfully attached]

## <span id="attachexisting"></span></a>方法: 既存のディスクの接続

既存のディスクを接続する場合は、ストレージ アカウントで利用できる .vhd を持っている必要があります。.vhd ファイルをストレージ アカウントにアップロードするには、[Add-AzureVhd][Add-AzureVhd] コマンドレットを使用します。.vhd ファイルを作成してアップロードした後は、それを仮想マシンに接続できます。

1.  **[仮想マシン]** をクリックし、適切な仮想マシンを選択します。

2.  コマンド バーで **[接続]** をクリックし、**[ディスクの接続]** を選択します。

    ![Attach data disk][Attach data disk]

    **[ディスクの接続]** ダイアログ ボックスが表示されます。

    ![Enter data disk details][Enter data disk details]

3.  仮想マシンに接続するデータ ディスクを選択します。
4.  チェック マークをクリックして、仮想マシンにデータ ディスクを接続します。

    データ ディスクが仮想マシンのダッシュボードに表示されます。

    ![Data disk successfully attached][Data disk successfully attached]

> [WACOM.NOTE]
> データ ディスクを追加した後、仮想マシンがストレージとしてそのディスクを使用できるように、仮想マシンにログオンしてディスクを初期化する必要があります。

  [About Virtual Machine Disks in Azure (Azure の仮想マシンのディスクについて)]: http://go.microsoft.com/fwlink/p/?LinkId=403697
  [Attach an empty disk]: ./media/howto-attach-disk-window-linux/AttachDiskWindows.png
  [Attach a new empty disk]: ./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png
  [Empty data disk successfully attached]: ./media/howto-attach-disk-window-linux/AttachEmptySuccess.png
  [Add-AzureVhd]: http://go.microsoft.com/FWLink/p/?LinkID=391684
  [Attach data disk]: ./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png
  [Enter data disk details]: ./media/howto-attach-disk-window-linux/AttachExistingDisk.png
  [Data disk successfully attached]: ./media/howto-attach-disk-window-linux/AttachSuccess.png
