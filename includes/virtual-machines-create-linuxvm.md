
1. 「[Azure コマンド ライン インターフェイス (CLI) からの Azure サブスクリプションへの接続](../articles/xplat-cli-connect.md)」で示されている手順を使用して、Azure サブスクリプションにサインインします。

2. 以下を使用して、クラシック デプロイメント モードであることを確認します。

        azure config mode asm

3. 利用可能なイメージからロードする Linux イメージを探します。

        azure vm image list | grep "Linux"

   Windows コマンド プロンプト ウィンドウでは、grep ではなく **find** を使用します。

4. `azure vm create` を使用して、上記のリストの Linux イメージで新しい仮想マシンを作成します。この手順では、新しいクラウド サービスだけでなくストレージ アカウントも作成されます。`-c` オプションを使用して、この仮想マシンを既存のクラウド サービスに接続することもできます。`-e` オプションを使用すると、Linux 仮想マシンにログインするための SSH エンドポイントも作成されます。

        ~$ azure vm create "MyTestVM" b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-ja-JP-30GB -g adminUser -p P@ssw0rd! -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-ja-JP-30GB
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating VM
        info:    vm create command OK

    >[AZURE.NOTE] Linux 仮想マシンの場合は、`vm create` で `-e` オプションを指定する必要があります。仮想マシンを作成した後に SSH を有効にすることはできません。SSH の詳細については、「[Azure 上の Linux における SSH の使用方法](virtual-machines-linux-mac-create-ssh-keys.md)」をご覧ください

    イメージ *b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-ja-JP-30GB* は、上記の手順でイメージ リストから選択したものです。*MyTestVM* は新しい仮想マシンの名前であり、*adminUser* は仮想マシンに SSH するために使用するユーザー名です。これらの変数は、必要に応じて置き換えることができます。このコマンドの詳細については、「[Using the Azure CLI with Classic deployment model (クラシック デプロイメント モデルでの Azure CLI コマンド)](virtual-machines-command-line-tools.md)」を参照してください。

5. 新しく作成された Linux 仮想マシンが、次のものによって指定される一覧に表示されます。

        azure vm list

6. 次のコマンドを使用して、仮想マシンの属性を確認できます。

        azure vm show MyTestVM

7. 新しく作成された仮想マシンは、`azure vm start` コマンドで開始できます。

## 次のステップ
これらすべての Azure CLI 仮想マシン コマンドの詳細については、「[Using the Azure CLI with the Classic deployment API (クラシック デプロイメント API での Azure CLI コマンド)](../articles/virtual-machines-command-line-tools.md)」を参照してください。

<!---HONumber=AcomDC_0824_2016-->