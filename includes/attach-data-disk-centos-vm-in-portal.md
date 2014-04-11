1. [Windows Azure (プレビュー) の管理ポータル][AzurePreviewPortal]で、**[仮想マシン]** をクリックし、先ほど作成した仮想マシン (**testlinuxvm**) を選択します。

2. コマンド バーで、**[ディスクの接続]**、**[空のディスクの接続]** の順にクリックします。

	**[空のディスクの接続]** ダイアログ ボックスが表示されます。


3. **[仮想マシン名]**、**[ストレージの場所]**、**[ファイル名]** の値は既に定義されています。必要なのは、ディスクのサイズを入力することだけです。**[サイズ]** ボックスに「**5**」と入力します。

	![空のディスクの接続][Image2]

	**注:** ディスクはすべて、VHD ファイルから Windows Azure のストレージに作成されます。ストレージに追加する VHD ファイルの名前は指定できますが、ディスクの名前は Windows Azure によって自動的に生成されます。

4. チェック マークをクリックして、仮想マシンにデータ ディスクを接続します。

5. 仮想マシンの名前をクリックしてダッシュボードを表示します。こうすることでデータ ディスクが仮想マシンに正しく接続されたか確認することができます。

	現在の仮想マシンのディスク数は 2 です。接続したディスクは、**[ディスク]** テーブルに表示されます。

	![空のディスクの接続][Image3]

	仮想マシンにデータ ディスクを接続した後も、ディスクはオフラインで初期化されていません。データ ディスクを使ってデータを保存する前に、仮想マシンにログオンして、ディスクを初期化する必要があります。

##SSH または PuTTY を使用して仮想マシンに接続し、セットアップを完了する
仮想マシンに接続したばかりのデータ ディスクはオフラインで、追加後も初期化されません。データ ディスクを使用してデータを保存するには、仮想マシンにログオンし、ディスクを初期化する必要があります。

1. 仮想マシンをプロビジョニングしたら、SSH または PuTTY を使用して接続し、(前の手順に従って) **newuser** としてログインします。	

2. SSH または PuTTY のウィンドウで次のコマンドを入力し、アカウントのパスワードを入力します。

	`$ sudo grep SCSI /var/log/messages`

	表示されたメッセージで、最後に追加されたデータ ディスクの識別子を確認できます (この例では **sdc**)。

	![GREP][Image4]

3. SSH または PuTTY のウィンドウで次のコマンドを入力し、ディスク **/dev/sdc** をパーティション分割します。

	`$ sudo fdisk /dev/sdc`

4. 「**n**」を入力して、新しいパーティションを作成します。

	![FDISK][Image5]

5. パーティションをプライマリ パーティションにするには「**p**」を、最初のパーティションにするには「**1**」を、シリンダーの既定値 (1) をそのまま使用するには Enter キーを押します。

	![FDISK][Image6]

6. 「**p**」を入力すると、パーティション分割されたディスクに関する詳細情報が表示されます。

	![FDISK][Image7]

7. 「**w**」と入力すると、ディスクの設定が書き込まれます。

	![FDISK][Image8]

8. **mkfs.ext3** コマンドを使用して、新しいディスクをフォーマットします。

	`$ sudo mkfs.ext3 /dev/sdc1`

	![Format Disk][Image9]

9. ドライブのマウント ポイントにするディレクトリを作成します。

	`$ sudo mkdir /mnt/datadrive`

10. ドライブをマウントします。

	`$ sudo mount /dev/sdc1 /mnt/datadrive`

11. **/etc/fstab** ファイルを開き、次の行を追加します。

	`/dev/sdc1        /mnt/datadrive      ext3    defaults   1 2`

12. **/etc/fstab** ファイルを保存して閉じます。

13. e2label を使用してパーティションにラベルを付けます。

	`$ sudo e2label /dev/sdc1 /mnt/datadrive`




[Image2]: ./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM2.png
[Image3]: ./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM3.png
[Image4]: ./media/attach-data-disk-centos-vm-in-portal/GrepScsiMessages.png
[Image5]: ./media/attach-data-disk-centos-vm-in-portal/fdisk1.png
[Image6]: ./media/attach-data-disk-centos-vm-in-portal/fdisk2.png
[Image7]: ./media/attach-data-disk-centos-vm-in-portal/fdisk3.png
[Image8]: ./media/attach-data-disk-centos-vm-in-portal/fdisk4.png
[Image9]: ./media/attach-data-disk-centos-vm-in-portal/mkfs.png



