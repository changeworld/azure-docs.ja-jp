<properties title="Creating an Oracle WebLogic Server 12c and Oracle Database 12c Virtual Machine in Azure" pageTitle="Azure での Oracle WebLogic Server 12c および Oracle Database 12c の仮想マシンの作成" description="Windows Server 2012 で実行中の Oracle WebLogic Server 12c および Oracle Database 12c のイメージを Microsoft Azure で作成する例の手順を実行します。" services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Azure での Oracle WebLogic Server 11g 仮想マシンの作成
次の例は、Windows Server 2008 R2 で実行しているマイクロソフト提供の WebLogic Server 11g のイメージに基づいて、Azure で仮想マシンを作成する方法を示しています。

##Azure で Oracle WebLogic Server 11g 仮想マシンを作成するには

1. [Azure ポータル](https://ms.portal.azure.com/)にログインします。

2. **[Marketplace]**、[Compute] をクリックし、続いて検索ボックスに**「Oracle」**と入力します。

3. このイメージに関する情報 (最小の推奨サイズなど) を選択または確認し、クリックします。

4. VM の**ホスト名**を指定します。

5. VM の**ユーザー名**を指定します。なお、このユーザーは VM にリモートログインするためのもので、Oracle データベースのユーザー名ではありませんのでご注意ください。

6. VM のパスワードを指定し確認するか、または SSH 公開キーを入力します。

7. 既定では推奨される価格レベルが表示されます。すべての構成オプションを見るには、右上の **[すべて表示]** をクリックします。

8. 必要に応じて [[オプションの構成]](https://msdn.microsoft.com/library/azure/dn763935.aspx) を設定します。このとき、次に注意してください。

	1. VM 名で新しいストレージ アカウントを作成するには、**[ストレージ アカウント]** をそのままにします。

	2. **[可用性セット]** を「未構成」のままにします。

	3. この時点では**エンドポイント**を追加しないでください。

9. [[リソース グループ]](resource-group-portal.md) を選択または作成

10. **[サブスクリプション]** を選択

11. **[場所]** を選択

##Azure で Oracle WebLogic Server 11g 仮想マシンを構成するには

1. [Azure ポータル](https://ms.portal.azure.com/)にログインします。

2. **[仮想マシン]** をクリックします。

3. ログインする仮想マシンの名前をクリックします。

4. **[接続]** をクリックします。

5. 表示される画面で必要に応じて入力して、仮想マシンに接続します。管理者名とパスワードの入力画面が表示されたら、仮想マシンの作成時に指定した値を使用します。

6. **Windows の [スタート]**、**[すべてのプログラム]**、**[Oracle WebLogic]**、**[WebLogic Server 11g R1]**、**[ツール]**、**[構成ウィザード]** の順にクリックします。

7. **[ようこそ]** ダイアログで、**[WebLogic ドメインの新規作成]** を選択し、続いて **[次へ]** をクリックします。

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image30.png)

8. **[ドメイン ソースの選択]** ダイアログで、既定値をそのまま使用し、続いて **[次へ]** をクリックします。

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image31.png)

9. **[ドメイン名と場所の指定]** ダイアログで、既定値をそのまま使用し、続いて **[次へ]** をクリックします。

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image32.png)

10. **[管理者ユーザー名とパスワードの設定]** ダイアログで、次のことを行います。

	1. [オプション] ユーザー名を **weblogic** から任意の値に変更します。

	2. WebLogic Server 管理者のパスワードを指定し確認します。

	3. **[次へ]** をクリックします。

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image33.png)

11. **[サーバー開始モードと JDK の構成]** ダイアログで、**[実稼働モード]** を選択し、入手できる JDK (または必要に応じて JDK に対応するブラウザー) を選択し、**[次へ]** をクリックします。

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image34.png)

12.	**[オプション構成の選択]** ダイアログでは、どのオプションも選択せずに **[次へ]** をクリックします。

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image35.png)

13.	**[構成の概要]** ダイアログで、**[作成]** をクリックします。
	
	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image35.png)

14.	**[ドメインの作成]** ダイアログで、**[管理サーバーの起動]** を確認し、続いて **[完了]** をクリックします。

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image37.png)

15.	コマンド プロンプト **startWebLogic.cmd** が起動します。メッセージが表示されたら、WebLogic のユーザー名とパスワードを入力します。

## Azure で Oracle WebLogic Server 11g 仮想マシンにアプリケーションをインストールするには

1. 仮想マシンにログインしたまま、[http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war](http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war) で手に入る shoppingcart.war の例をローカルにコピーします。たとえば、**c:\\mywar** という名前のフォルダーを作成し、[http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war](http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war) で WAR を **c:\\mywar** に保存します。

2. **WebLogic Server 管理コンソール**、[http://localhost:7001/console](http://localhost:7001/console) を開きます。メッセージが表示されたら、WebLogic のユーザー名とパスワードを入力します。

3. **[WebLogic Server 管理コンソール]**で、**[ロックと編集]**、**[デプロイ]**、**[インストール]** の順にクリックします。

4. **[パス]** に「`c:\mywar\shoppingcart.war`」と入力します。

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image38.png)

	**[次へ]** をクリックします。

5. **[このデプロイをアプリケーションとしてインストールする]** を選択し、続いて **[次へ]** をクリックします。

6. **[完了]** をクリックします。

7. **WebLogic Server 管理コンソール**で、**[変更を有効にする]** をクリックします。

8. **[デプロイ]** をクリックし、**[ショッピングカート]** を選択し、**[開始]** をクリックし、続いて **[すべての要求を処理]** をクリックします。確認メッセージが表示されたら、**[はい]** をクリックします。

9. ローカルで実行中のショッピングカート アプリケーションを表示するには、ブラウザーを開いて [http://localhost:7001/shoppingcart](http://localhost:7001/shoppingcart) にアクセスします。

10.  ポート 7001 の受信接続をファイアウォールで許可します。

	1. **仮想マシン**にログインしたまま、**Windows の [スタート]**、**[コントロール パネル]**、**[システムとセキュリティ]**、**[Windows ファイアウォール]**、**[詳細設定]** の順にクリックします。**[セキュリティが強化された Windows ファイアウォール]** 管理コンソールが開きます。

	2. ファイアウォール管理コンソール内で、左側のウィンドウの **[受信の規則]** をクリックし (**[受信の規則]**が表示されない場合は、左側のウィンドウの上のノードを展開します)、続いて右側のウィンドウの **[新しい規則]**をクリックします。

	3. **[規則の種類]** は、**[ポート]** を選択して **[次へ]** をクリックします。

	4. **[プロトコルとポート]** では、**[TCP]** を選択し、**[特定のローカル ポート]** を選択し、ポートに**「7001」**と入力し、続いて **[次へ]** をクリックします。

	5. **[接続を許可する]** を選択し、**[次へ]** をクリックします。

	6. 規則を適用するプロファイルの既定値をそのまま使用し、**[次へ]** をクリックします。

	7. 規則の名前とオプションで説明を指定し、続いて **[完了]** をクリックします。

11. 仮想マシンのエンドポイントを作成します。

	1. [Azure ポータル](https://ms.portal.azure.com/)にログインします。

    2. **[参照]** をクリックします

    3. **[仮想マシン]** をクリックします

    4. 仮想マシンを選択します

	5. **[設定]** をクリックします

    6. **[エンドポイント]** をクリックします。

    7. **[追加]** をクリックします。

    8. エンドポイントの名前を指定します

		1. プロトコルに **TCP** を使用します

        2. パブリック ポートに **80** を使用します

        3. プライベート ポートに **7001** を使用します。

    9. 残りのオプションはそのままにします

	10. **[OK]** をクリックします。

12. インターネット上で実行しているショッピングカート アプリケーションを表示するには、ブラウザーを開いて `http://<<unique_domain_name>>/shoppingcart` の形式の URL にアクセスします。(`<<unique_domain_name>>` の値を確認するには、[Azure ポータル](https://ms.portal.azure.com/)内で **[仮想マシン]** をクリックし、続いて Oracle WebLogic Server を実行するために使用している仮想マシンを選択します)。

## その他のリソース

これで Oracle WebLogic Server を実行する仮想マシンのセットアップが終わりました。追加情報については、次のトピックを参照してください。

- [Oracle 仮想マシン イメージ - 他の考慮事項](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

- [Oracle WebLogic Server 製品ドキュメント](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

- [Azure の Oracle 仮想マシン イメージ](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=August15_HO6-->