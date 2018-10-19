---
title: Azure (Large Instances) 上に SAP HANA の SMT サーバーを設定する方法 | Microsoft Docs
description: Azure (Large Instances) 上に SAP HANA の SMT サーバーを設定する方法。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f387c1afe88f2bba476309b2e2e01942d2b7ae5b
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982627"
---
# <a name="setting-up-smt-server-for-suse-linux"></a>SUSE Linux 用 SMT サーバーの設定
SAP HANA L インスタンスは、インターネットに直接接続していません。 そのため、このようなユニットを OS プロバイダーに登録し、パッチをダウンロードして適用するのは簡単なプロセスではありません。 SUSE Linux の場合、1 つの解決策として、Azure VM に SMT サーバーを設定します。 一方、Azure VM は、HANA L インスタンスに接続されている Azure VNet でホストする必要があります。 HANA L インスタンス ユニットは、このような SMT サーバーを使用して登録やパッチのダウンロードを実行できます。 

SUSE では、[Subscription Management Tool for SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf) に関する包括的なガイドを提供しています。 

HANA L インスタンスのタスクを実行する SMT サーバーのインストールの前提条件として、次のものが必要です。

- HANA L インスタンスの ER 回線に接続された Azure VNet。
- 組織に関連付けられている SUSE アカウント。 組織には、有効な SUSE サブスクリプションが必要です。

## <a name="installation-of-smt-server-on-azure-vm"></a>Azure VM への SMT サーバーのインストール

この手順では、Azure VM に SMT サーバーをインストールします。 まず、[SUSE Customer Center](https://scc.suse.com/) にサインインします。

ログインしたら、[Organization]\(組織\)、[Organization Credentials]\(組織の資格情報\) の順に移動します。 このセクションで、SMT サーバーを設定するために必要な資格情報を確認します。

次に、Azure VNet で SUSE Linux VM をインストールします。 VM をデプロイするには、Azure の SLES 12 SP2 ギャラリー イメージを取得します (BYOS SUSE イメージを選択します)。 デプロイ プロセスでは、DNS 名を定義しないでください。また、次のスクリーンショットに示すように、静的 IP アドレスを使用しないでください。

![SMT サーバー用の VM のデプロイ](./media/hana-installation/image3_vm_deployment.png)

デプロイされた VM は小さなサイズの VM であり、Azure VNet の内部 IP アドレス (10.34.1.4) を取得しています。 VM の名前は smtserver です。 インストール後、HANA L インスタンス ユニットへの接続がチェックされます。 名前解決の構成方法によっては、Azure VM の etc/hosts で HANA L インスタンス ユニットの解決を構成することが必要な場合があります。 パッチを保持するために使用するディスクを VM に追加します。 ブート ディスク自体が小さすぎる場合があります。 次のスクリーンショットに示すように、このデモでは、/srv/www/htdocs にディスクがマウントされています。 100 GB のディスクで十分です。

![SMT サーバー用の VM のデプロイ](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

HANA Large Instances ユニットにサインインして /etc/hosts を維持し、SMT サーバーを実行することになっている Azure VM にネットワーク経由で到達できるかどうかを確認します。

このチェックが正常に完了したら、SMT サーバーを実行する Azure VM にサインインする必要があります。 PuTTY を使用して VM にサインインする場合は、Bash ウィンドウで、次の一連のコマンドを実行する必要があります。

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

これらのコマンドを実行したら、Bash を再起動して設定をアクティブにします。 次に YaST を起動します。

VM (smtserver) を SUSE サイトに接続します。

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

VM が SUSE サイトに接続されたら、smt パッケージをインストールします。 smt パッケージをインストールするには、次の putty コマンドを使用します。

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


また、YAST ツールを使用して smt パッケージをインストールすることもできます。 YaST で [Software Maintenance] に移動し、smt を検索します。 smt を選択すると、次のように yast2-smt に自動的に切り替わります。

![YaST 内の SMT](./media/hana-installation/image5_smt_in_yast.PNG)


smtserver にインストールするための選択内容をそのまま使用します。 インストールが完了したら、SMT サーバー構成に移動し、SUSE Customer Center で先ほど取得した組織の資格情報を入力します。 また、SMT サーバーの URL として、Azure VM のホスト名を入力します。 このデモでは、次の画像に示すように https://smtserver を使用しています。

![SMT サーバーの構成](./media/hana-installation/image6_configuration_of_smtserver1.png)

次に、SUSE Customer Center への接続が機能しているかどうかをテストする必要があります。 次の画像でおわかりのように、このデモでは接続は機能しています。

![SUSE Customer Center への接続のテスト](./media/hana-installation/image7_test_connect.png)

SMT セットアップが開始されたら、データベースのパスワードを入力する必要があります。 これは新規インストールであるため、次の画像のように、パスワードを定義する必要があります。

![データベースのパスワードの定義](./media/hana-installation/image8_define_db_passwd.PNG)

次の対話式操作は、証明書が作成されるときに発生します。 次のようなダイアログが表示され、手順が進みます。

![SMT サーバーの証明書の作成](./media/hana-installation/image9_certificate_creation.PNG)

構成の最後の "同期チェックの実行" には数分かかることがあります。 SMT サーバーのインストールと構成が完了すると、マウント ポイント /srv/www/htdocs/ の下に repo ディレクトリが作成され、repo に複数のサブディレクトリが作成されていることがわかります。 

次のコマンドを使用して、SMT サーバーと関連サービスを再起動します。

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-of-packages-onto-smt-server"></a>SMT サーバーへのパッケージのダウンロード

すべてのサービスが再起動したら、YaST を使用して SMT Management の適切なパッケージを選択します。 パッケージの選択は、SMT サーバーを実行している VM の SLES リリースまたはバージョンではなく、HANA L インスタンス サーバーの OS イメージによって異なります。 選択画面の例を次に示します。

![パッケージの選択](./media/hana-installation/image10_select_packages.PNG)

パッケージの選択が終わったら、設定した SMT サーバーへの選択したパッケージの初期コピーを開始する必要があります。 このコピーは、次のようにシェルで smt-mirror コマンドを使用することでトリガーされます。


![SMT サーバーへのパッケージのダウンロード](./media/hana-installation/image11_download_packages.PNG)

上記のように、マウント ポイント /srv/www/htdocs の下に作成されたディレクトリにパッケージがコピーされます。 このプロセスにはしばらく時間がかかることがあります。 選択したパッケージの数によっては、最大 1 時間以上かかる場合があります。
このプロセスが完了したら、SMT クライアントの設定に移ります。 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>HANA L インスタンス ユニットでの SMT クライアントの設定

この例のクライアントは、HANA L インスタンス ユニットです。 SMT サーバー セットアップによって、clientSetup4SMT.sh スクリプトが Azure VM にコピーされています。 SMT サーバーに接続する HANA L インスタンス ユニットにこのスクリプトをコピーします。 -h オプションを使用してスクリプトを開始し、SMT サーバーの名前をパラメーターとして渡します。 この例では smtserver です。

![SMT クライアントの構成](./media/hana-installation/image12_configure_client.PNG)

クライアントによるサーバーからの証明書の読み込みは成功しても、次のように登録に失敗する場合があります。

![クライアントの登録の失敗](./media/hana-installation/image13_registration_failed.PNG)

登録に失敗した場合は、こちらの [SUSE サポート ドキュメント](https://www.suse.com/de-de/support/kb/doc/?id=7006024)を参照し、ドキュメントに記載されている手順を実行します。

> [!IMPORTANT] 
> サーバー名として、完全修飾ドメイン名なしで VM の名前 (この例では smtserver) を指定する必要があります。 VM 名だけが有効です。 

これらの手順の実行後、HANA L インスタンス ユニットで次のコマンドを実行する必要があります。

```
SUSEConnect –cleanup
```

> [!Note] 
> テストでは、この手順の後、常に数分待たなければなりませんでした。 SUSE の記事に記載されている是正措置を講じた後、すぐに clientSetup4SMT.sh を実行すると、証明書がまだ有効になっていないというメッセージで終了しました。 通常は 5 ～ 10 分待ってから clientSetup4SMT.sh を実行すると、クライアント構成が正常に完了しました。

SUSE の記事の手順に基づいて修正する必要がある問題が発生した場合は、HANA L インスタンス ユニットで clientSetup4SMT.sh をもう一度開始する必要があります。 これで、次のようにスクリプトが正常に完了します。

![クライアント登録の成功](./media/hana-installation/image14_finish_client_config.PNG)

この手順では、Azure VM にインストールした SMT サーバーに接続するように、HANA L インスタンス ユニットの SMT クライアントを構成しました。 "zypper up" または "zypper in" を使用して、HANA L インスタンスへの OS パッチのインストールや、追加パッケージのインストールを実行できるようになりました。 取得できるのは、SMT サーバーで以前にダウンロードしたパッチだけであることに注意してください。

**次のステップ**
- [HLI への HANA のインストール](hana-example-installation.md)に関するページを参照してください。











