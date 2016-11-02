


## <a name="find-the-virtual-machine"></a>仮想マシンの検索

1. [Azure ポータル](https://portal.azure.com/)にサインインします。

2. ハブ メニューで **[Virtual Machines]**をクリックします。

3.  一覧から仮想マシンを選択します。

4. [仮想マシン] ブレードにアクセスし、**[要点]** で、**[ディスク]** の順にクリックします。

    ![ディスク設定を開く](./media/virtual-machines-common-attach-disk-portal/find-disk-settings.png)

次の手順に従って、新しいディスクまたは既存のディスクを接続します。

## <a name="option-1:-attach-a-new-disk"></a>オプション 1: 新しいディスクを接続する

1.  **[ディスク]** ブレードの **[新しいディスクの接続]** をクリックします。

2.  既定の設定を確認し、必要に応じて更新して、 **[OK]**をクリックします。

    ![ディスク設定を確認する](./media/virtual-machines-common-attach-disk-portal/attach-new.png)

3.  Azure でディスクが作成され、仮想マシンに接続されると、仮想マシンのディスク設定の **[データ ディスク]**に新しいディスクが表示されます。

## <a name="option-2:-attach-an-existing-disk"></a>オプション 2: 既存のディスクを接続する

1.  **[ディスク]** ブレードの **[既存のディスクの接続]** をクリックします。

2.  **[既存のディスクの接続]** の **[VHD ファイル]** をクリックします。

    ![既存のディスクを接続する](./media/virtual-machines-common-attach-disk-portal/attach-existing.png)

3.  **[ストレージ アカウント]**で、アカウントと、.vhd ファイルが格納されているコンテナーを選択します。

    ![VHD の場所を検索する](./media/virtual-machines-common-attach-disk-portal/find-storage-container.png)

4.  .vhd ファイルを選択します。

5.  **[既存のディスクの接続]** の **[VHD ファイル]** に、選択したファイルが表示されます。 **[OK]**をクリックします。

6.  Azure で仮想マシンにディスクが接続されると、仮想マシンのディスク設定の **[データ ディスク]**にそのディスクが表示されます。






<!--HONumber=Oct16_HO2-->


