---
title: Azure (Large Instances) 上に SAP HANA の SMT サーバーを設定する方法 | Microsoft Docs
description: SAP HANA on Azure (Large Instances) 用の SMT サーバーを設定する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/25/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9350b6d2a8b593e224da817bcbc3142605276248
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599128"
---
# <a name="set-up-smt-server-for-suse-linux"></a>SUSE Linux 用 SMT サーバーの設定

この記事では、SAP HANA on Azure Large Instances (別名 BareMetal インフラストラクチャ) 用の SMT サーバーの設定手順について説明します。

SAP HANA の L インスタンスは、インターネットに直接接続していません。 その結果、このようなユニットをオペレーティング システム プロバイダーに登録し、更新プログラムをダウンロードして適用するのは、簡単ではありません。 SUSE Linux の場合の解決策は、Azure 仮想マシン (VM) に SMT サーバーを設定することです。 この仮想マシンを、HANA L インスタンス (HLI) に接続される Azure 仮想ネットワークでホストします。 SMT サーバーを設定したら、HANA L インスタンスによる登録と更新プログラムのダウンロードを実行できます。 

SUSE の詳細については、[Subscription Management Tool for SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf) に関する記事を参照してください。 

## <a name="prerequisites"></a>前提条件

HANA L インスタンス用の SMT サーバーをインストールするには、まず次のものが必要です。

- HANA L インスタンスの ExpressRoute 回線に接続されている Azure 仮想ネットワーク。
- 組織に関連付けられている SUSE アカウント。 組織には、有効な SUSE サブスクリプションが必要です。

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>SMT サーバーを Azure 仮想マシンにインストールする

1. [SUSE Customer Center](https://scc.suse.com/) にサインインします。 **[Organization]\(組織)**  >  **[Organization Credentials]\(組織の資格情報)** の順に移動します。 このセクションで、SMT サーバーを設定するために必要な資格情報を確認します。

2. Azure 仮想ネットワークに SUSE Linux VM をインストールします。 仮想マシンをデプロイするには、Azure の SLES 12 SP2 ギャラリー イメージを取得します (BYOS SUSE イメージを選択します)。 デプロイ プロセスでは、DNS 名を定義しないでください。また、静的 IP アドレスを使用しないでください。

    ![SMT サーバー用の仮想マシンのデプロイのスクリーンショット。](./media/hana-installation/image3_vm_deployment.png)

    デプロイされた仮想マシンの Azure 仮想ネットワークの内部 IP アドレスは 10.34.1.4 です。 仮想マシンの名前は *smtserver* です。 インストール後、HANA L インスタンスへの接続を確認します。 名前解決の構成方法によっては、Azure 仮想マシンの etc/hosts に HANA L インスタンスの解決を構成することが必要な場合があります。 

3. ディスクを仮想マシンに追加します。 ブート ディスクでは小さすぎる可能性があるため、このディスクを使用して更新プログラムを保持します。 次のスクリーンショットに示すように、ここでは、/srv/www/htdocs にディスクがマウントされています。 100 GB のディスクで十分です。

    ![[PuTTy] ウィンドウに追加されたディスクを示すスクリーンショット。](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

4. HANA L インスタンスにサインインし、/etc/hosts を管理します。 ネットワーク経由で SMT サーバーを実行する Azure 仮想マシンに接続できるかどうかを確認します。

5. SMT サーバーを実行する Azure 仮想マシンにサインインします。 putty を使用して仮想マシンにサインインする場合は、bash ウィンドウで、次の一連のコマンドを実行します。

    ```
    cd ~
    echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
    ```

6. Bash を再起動して、設定をアクティブにします。 次に YaST を起動します。

7. VM (smtserver) を SUSE サイトに接続します。

    ```
    smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
    Registered SLES_SAP 12.2 x86_64
    To server: https://scc.suse.com
    Using E-Mail: email address
    Successfully registered system.
    ```
    
8. 仮想マシンが SUSE サイトに接続されたら、SMT パッケージをインストールします。 SMT パッケージをインストールするには、次の putty コマンドを使用します。

    ```
    smtserver:~ # zypper in smt
    Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
    Loading repository data...
    Reading installed packages...
    Resolving package dependencies...
    ```
    
    また、YAST ツールを使用して SMT パッケージをインストールすることもできます。 YAST で **[Software Maintenance]** に移動し、smt を検索します。 **smt** を選択すると、yast2-smt に自動的に切り替わります。

    [![YAST での SMT のスクリーンショット。](./media/hana-installation/image5_smt_in_yast.PNG)](./media/hana-installation/image5_smt_in_yast.PNG#lightbox)

    smtserver にインストールするための選択内容をそのまま使用します。 


9. インストールが完了したら、SMT サーバー構成に移動します。 SUSE Customer Center で先ほど取得した組織の資格情報を入力します。 また、SMT サーバーの URL として、Azure 仮想マシンのホスト名を入力します。 この例では、https:\//smtserver です。

    [![SMT サーバー構成のスクリーンショット。](./media/hana-installation/image6_configuration_of_smtserver1.png)](./media/hana-installation/image6_configuration_of_smtserver1.png#lightbox)

10. ここで、SUSE Customer Center への接続が機能しているかどうかをテストします。 次のスクリーンショットでおわかりのように、この例では、それは機能しています。

    [![SUSE Customer Center への接続のテストのスクリーンショット。](./media/hana-installation/image7_test_connect.png)](./media/hana-installation/image7_test_connect.png#lightbox)

11. SMT セットアップが開始されたら、データベースのパスワードを入力します。 これは新規インストールであるため、次の画像のように、パスワードを定義する必要があります。

    [![データベースのパスワードの定義のスクリーンショット。](./media/hana-installation/image8_define_db_passwd.PNG)](./media/hana-installation/image8_define_db_passwd.PNG#lightbox)

12. 証明書を作成する。

    [![SMT サーバーの証明書の作成のスクリーンショット。](./media/hana-installation/image9_certificate_creation.PNG)](./media/hana-installation/image9_certificate_creation.PNG#lightbox)

    構成の最後の同期チェックの実行には、数分かかることがあります。 SMT サーバーのインストールと構成が完了すると、マウント ポイント /srv/www/htdocs/ の下に repo ディレクトリが作成されていることがわかります。 repo の下にはいくつかのサブディレクトリもあります。 

13. 次のコマンドを使用して、SMT サーバーと関連サービスを再起動します。

    ```
    rcsmt restart
    systemctl restart smt.service
    systemctl restart apache2
    ```

## <a name="download-packages-onto-the-smt-server"></a>SMT サーバーにパッケージをダウンロードする

1. すべてのサービスが再起動したら、YAST を使用して SMT Management の適切なパッケージを選択します。 パッケージの選択は、HANA L インスタンス サーバーのオペレーティング システム イメージによって異なります。 パッケージの選択は、SMT サーバーを実行している仮想マシンの SLES リリースまたはバージョンには依存しません。 次のスクリーンショットは、この選択画面の例です。

    [![パッケージの選択のスクリーンショット。](./media/hana-installation/image10_select_packages.PNG)](./media/hana-installation/image10_select_packages.PNG#lightbox)

2. 設定した SMT サーバーへの選択したパッケージの初期コピーを開始します。 このコピーは、シェルで smt-mirror コマンドを使用することでトリガーされます。

   [ ![SMT サーバーへのパッケージのダウンロードを示すスクリーンショット](./media/hana-installation/image11_download_packages.PNG)](./media/hana-installation/image11_download_packages.PNG#lightbox)

    マウント ポイント /srv/www/htdocs の下に作成されたディレクトリにパッケージがコピーされます。 このプロセスは、選択したパッケージの数によっては 1 時間以上かかる場合があります。 このプロセスが完了したら、SMT クライアントの設定に移ります。 

## <a name="set-up-the-smt-client-on-hana-large-instances"></a>HANA L インスタンスで SMT クライアントを設定する

この例のクライアントは、HANA L インスタンスです。 SMT サーバーのセットアップによって、clientSetup4SMT.sh スクリプトが Azure 仮想マシンにコピーされています。 

SMT サーバーに接続する HANA L インスタンスにこのスクリプトをコピーします。 -h オプションを使用してスクリプトを開始し、SMT サーバーの名前をパラメーターとして渡します。 この例では、*smtserver* です。

[![SMT クライアントの構成のスクリーンショット。](./media/hana-installation/image12_configure_client.PNG)](./media/hana-installation/image12_configure_client.PNG#lightbox)

クライアントによるサーバーからの証明書の読み込みを成功させることは可能です。 ただし、この例では、次のスクリーンショットに示すように、登録は失敗します。

[![クライアントの登録の失敗のスクリーンショット。](./media/hana-installation/image13_registration_failed.PNG)](./media/hana-installation/image13_registration_failed.PNG#lightbox)

登録に失敗した場合は、[SUSE サポート ドキュメント](https://www.suse.com/de-de/support/kb/doc/?id=7006024)を参照し、そこに記載されている手順を実行します。

> [!IMPORTANT] 
> サーバー名には、完全修飾ドメイン名を指定せずに、仮想マシンの名前 (この例では *smtserver*) を指定します。 
    
これらの手順の実行後、HANA L インスタンスで次のコマンドを実行します。
    
```
SUSEConnect –cleanup
```

> [!Note] 
> この手順の後、数分待ちます。 clientSetup4SMT.sh をすぐに実行すると、エラーが発生する可能性があります。

SUSE の記事の手順に基づいて修正する必要がある問題が発生した場合は、HANA L インスタンスで clientSetup4SMT.sh をもう一度開始します。 これで、正常に完了します。

[![クライアントの登録の成功のスクリーンショット。](./media/hana-installation/image14_finish_client_config.PNG)](./media/hana-installation/image14_finish_client_config.PNG#lightbox)

HLI の SMT クライアントを、Azure VM にインストールされている SMT サーバーに接続するように構成しました。 ここで、HANA L インスタンスに OS の更新プログラムをインストールするか、他のパッケージをインストールするために、"zypper up" または "zypper in" を使用します。 SMT サーバーでダウンロード済みの更新プログラムのみを取得できます。

## <a name="next-steps"></a>次のステップ
SAP HANA on Azure Large Instance の Azure Virtual Machines への移行について確認します。

> [!div class="nextstepaction"]
> [SAP HANA on Azure Large Instance の Azure Virtual Machines への移行](hana-large-instance-virtual-machine-migration.md)
