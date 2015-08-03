
ディスクの詳細については、「[Azure の仮想マシンのディスクについて](http://go.microsoft.com/fwlink/p/?LinkId=403697)」を参照してください。

##<a id="cliattachempty"></a>方法: 空のディスクの接続
空のディスクを接続すると、データ ディスクを簡単に追加できます。新しい空のディスクを接続するには、次のコマンドを実行します。

    vm disk attach-new <vm-name> <size-in-gb> [blob-url]

`vm-name` を仮想マシンの名前に置き換えて、`size-in-gb` を新しいディスクのサイズに置き換えます。必要に応じて、BLOB の URL を最後の引数として使用し、作成するターゲット BLOB を明示的に指定することもできます。BLOB の URL を指定しないと、BLOB オブジェクトが自動的に生成されます。

ディスクが作成されたことを確認するには、次のコマンドを実行します。

    vm disk list <vm-name>

ターミナル出力をはじめ、前のコマンドの例を次に示します。

    ~$ azure vm disk attach-new pinkylinux 20 http://pinkylinux.blob.core.windows.net/vhds/pinkydisk1.vhd
    info:   Executing command vm disk attach-new
    + Getting virtual machines
    + Adding Data-Disk
    info:   vm disk attach-new command OK
    ~$ azure vm disk list pinkylinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        pinkylinux-pinkylinux-2015-02-05.vhd    Linux
    data:    0    5         pinkydisk1.vhd
    data:    1    20        pinkylinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=July15_HO4-->