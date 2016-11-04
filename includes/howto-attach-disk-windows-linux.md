


## Attach an empty disk
空のディスクを接続すると、Azure が .vhd ファイルを作成してそれをストレージ アカウントに保存するため、データ ディスクを簡単に追加できます。

1. **[仮想マシン]** をクリックし、適切な VM を選択します。
2. コマンド バーで、**[ディスクの接続]**、**[空のディスクの接続]** の順にクリックします。

    ![Attach an empty disk](./media/howto-attach-disk-windows-linux/AttachEmptyDisk.png)

1. **[空のディスクの接続]** ダイアログ ボックスが表示されます。

    ![Attach a new empty disk](./media/howto-attach-disk-windows-linux/AttachEmptyDetail.png)


    以下の手順を実行します。

    - **[ファイル名]** で、.vhd ファイルのファイル名として既定の名前をそのまま使用するか、別の名前を入力します。.vhd ファイルの別の名前を入力した場合でも、データ ディスクには自動的に生成された名前が使用されます。

    - データ ディスクの **[サイズ (GB)]** を入力します。

    - チェック マークをクリックして、完了します。

1. データ ディスクが作成されて接続されると、VM のダッシュボードに表示されます。
   
   ![Empty data disk successfully attached](./media/howto-attach-disk-windows-linux/AttachEmptySuccess.png)

> [!NOTE]
> 新しいデータ ディスクを追加した後、VM にログオンし、ディスクを初期化して使用できるようにする必要があります。
> 
> 

## 方法: 既存のディスクの接続
既存のディスクを接続する場合は、ストレージ アカウントで利用できる .vhd を持っている必要があります。.vhd ファイルをストレージ アカウントにアップロードするには、[Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) コマンドレットを使用します。.vhd ファイルを作成してアップロードした後は、それを VM に接続できます。

1. **[仮想マシン]** をクリックし、適切な仮想マシンを選択します。
2. コマンド バーで **[接続]** をクリックし、**[ディスクの接続]** を選択します。

    ![Attach data disk](./media/howto-attach-disk-windows-linux/AttachExistingDisk.png)


1. データ ディスクを選択し、チェック マークをクリックしてデータ ディスクを接続します。
   
    ![Enter data disk details](./media/howto-attach-disk-windows-linux/AttachExistingDetail.png)
2. データ ディスクが接続されると、VM のダッシュボードに表示されます。

    ![Data disk successfully attached](./media/howto-attach-disk-windows-linux/AttachExistingSuccess.png)

<!---HONumber=AcomDC_0525_2016-->