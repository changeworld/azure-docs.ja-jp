<properties title="Creating an Oracle WebLogic Server 12c cluster in Azure" pageTitle="Azure で Oracle WebLogic Server 12c クラスターを作成する" description="例として Microsoft Azure で Oracle WebLogic Server 12c クラスターを作成します。" services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Azure で Oracle WebLogic Server 12c クラスターを作成する
次の例は、Windows Server 2012 で実行しているマイクロソフト提供の Oracle WebLogic Server 12c のイメージに基づいて、Azure で Oracle WebLogic Server クラスターを作成する方法を示しています。

WebLogic Server クラスターの各インスタンスは、同じバージョンの Oracle WebLogic Server を実行している必要があります。この例では WebLogic Server 12c Enterprise Edition を使います。

##クラスターで使用する仮想マシンを作成する
クラスターの管理サーバーとして使用する仮想マシンを作成し、続いてクラスターの一部として使用する追加の仮想マシンを作成します。

### 管理サーバーとして使用する仮想マシンを作成する

Azure で手に入る **Oracle WebLogic Server 12c Enterprise Edition on Windows Server 2012** のイメージを使って仮想マシンを作成します。この仮想マシンを作成する手順については、[「Azure で Oracle WebLogic Server 12c 仮想マシンを作成する」](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article)を参照してください。このチュートリアルでは、仮想マシンを **MYVM1-ADMIN** と呼びます。VM の仮想ネットワーク名をメモします。この値はクラスターに追加する VM を作成するときに使います。(VM 名と仮想ネットワーク名は Azure 内で一意であればどんな名前でも結構です。)

### クラスターで使用する管理対象の仮想マシンを作成する

Azure で手に入る Oracle WebLogic Server 12c イメージを使って、管理サーバーで管理対象の仮想マシンを追加作成します。このチュートリアルでは、追加の仮想マシンの名前を **MYVM2-MANAGED** と **MYVM3-MANAGED** にします。これらの仮想マシンを作成する手順については、[「Azure で Oracle WebLogic Server 12c 仮想マシンを作成する」](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article)を参照してください。*ただし*、次の変更が必要です。

-  仮想マシンを作成するとき、新しい仮想ネットワークを作成しないでください。具体的には、**[オプションの構成] > [仮想ネットワーク]** ダイアログで、既定の **[新しい仮想ネットワークを作成する]** ではなく、管理サーバーの VM 用に作成された仮想ネットワークを選択します。たとえば、管理サーバーを作成するときに **EXAMPLE** という名前の仮想ネットワークを作成した場合、管理対象クラスターの VM を作成するときに **EXAMPLE** を選択します。

##ドメインを作成する

1. [Azure ポータル](https://ms.portal.azure.com/)にログインします。

2. **[仮想マシン]** をクリックします。

3. クラスターの管理サーバーにするために作成した仮想マシンの名前をクリックします (たとえば、 **MYVM1-ADMIN**)。

4. **[接続]** をクリックします。

5. 表示される画面で必要に応じて入力して、仮想マシンに接続します。管理者名とパスワードの入力画面が表示されたら、仮想マシンの作成時に指定した値を使用します。

6. **[Fusion Middleware 構成ウィザード]** ダイアログの**ページ 1** で、**[新しいドメインを作成する]** をクリックし、続いて **[次へ]** をクリックします。(**[Fusion Middleware 構成ウィザード]** ダイアログがまだ開いていない場合は、**Windows の [スタート]** をクリックし、**「構成ウィザード」**と入力し、続いて**構成ウィザード** アイコンをクリックします。)

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image19.png)

7. **[Fusion Middleware 構成ウィザード]** ダイアログの**ページ 2** で、**[基本 WebLogic Server ドメイン]** テンプレートを選択し、続いて **[次へ]** をクリックします。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image20.png)

8. **[Fusion Middleware 構成ウィザード]** ダイアログの**ページ 3** で:

	1. [オプション] ユーザー名を **weblogic** から任意の値に変更します。
	
	2. WebLogic Server 管理者のパスワードを指定し確認します。
	
	3. **[次へ]** をクリックします。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image21.png)

9. **[Fusion Middleware 構成ウィザード]** ダイアログの**ページ 4** で、ドメイン モードに **[運用]** を選択し、使用可能な JDK を選択し (または、必要な場合は JDK を参照します)、続いて **[次へ]** をクリックします。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image22.png)

10.  **[Fusion Middleware 構成ウィザード]** ダイアログの**ページ 5** で、どのオプションも選択せずに **[次へ]** をクリックします。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image23.png)

11.  **[Fusion Middleware 構成ウィザード]** ダイアログの**ページ 6** で、**[作成]** をクリックします。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image24.png)

12.  **[Fusion Middleware 構成ウィザード]** ダイアログの**ページ 7** で、ドメインが作成された後に **[次へ]** をクリックします。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image25.png)

13.  **[Fusion Middleware 構成ウィザード]** ダイアログの**ページ 8** で、[管理サーバーを開始する] チェック ボックスをオンにし、続いて **[完了]** をクリックします。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image26.png)

14.  コマンド プロンプト **startWebLogic.cmd** が起動します。メッセージが表示されたら、WebLogic のユーザー名とパスワードを入力します。

##クラスターをセットアップする

1. 管理仮想マシンにログインしたまま、**WebLogic Server 管理コンソール**、<http://localhost:7001/console> を実行します。メッセージが表示されたら、WebLogic Server のユーザー名とパスワードを入力します。

2. **WebLogic Server 管理コンソール**で、**[ロックと編集]** をクリックします。

3. **[ドメイン構造]** ウィンドウで **[環境]** を展開し、続いて **[クラスター]** をクリックします。

4. **[クラスターの概要]** ダイアログで **[新規]** をクリックし、続いて **[クラスター]** をクリックします。

5. **[クラスター プロパティ]** ダイアログ:

	1. クラスターの名前を入力します。

	2. **[メッセージング モード]** に **[ユニキャスト]** を選択します。

		![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image001.png)

	
	3. **[OK]** をクリックします。

6. **[ドメイン構造]** ウィンドウで **[環境]** を展開し、続いて **[サーバー]** をクリックします。

7. 最初の管理対象サーバーをクラスターに追加します。

	1. **[新規]** をクリックします。

	2. **[新しいサーバーの作成]** ダイアログ:

		1. **[サーバー名]** に最初の管理対象サーバーの名前を入力します。たとえば**、MYVM2-MANAGED。**

		2. **[サーバー リッスン アドレス]** に同じ名前を入力します。

		3. **[リッスン ポート]** に**「7008」**と入力します。

		4. **[はい、このサーバーを既存のクラスターのメンバーにします]** チェック ボックスをオンにします。

		5. **[クラスターを選択]** ドロップダウン リストで、以前に作成したクラスターを選択します。

			34d27e82-bb2e-4f9c-aaad-ca3e28c0f5fc

		6. **[次へ]** をクリックします。

		7. **[完了]** をクリックします。

8. 上記の手順を使用して、クラスターに 2 番目の管理対象サーバーを追加します。**[サーバー名]** と **[サーバー リッスン アドレス]** には、2 番目の管理対象コンピューターの名前を使用します。**[リッスン ポート]** には**「7008」**を使用します。

9. WebLogic Server 管理コンソールに残ったままで、**[変更を有効にする]** をクリックします。

10. 管理仮想マシン上で**「SERVER\_HOME」**という環境変数を作成し、その値を**「C:\\Oracle\\Middleware\\Oracle\_Home\\wlserver」**に設定します 。 環境変数は次の手順で作成できます。

	1. **Windows の [スタート]** をクリックし、**「コントロール パネル」**と入力し、**コントロール パネル**アイコンをクリックし、続いて **[システムとセキュリティ]**、**[システム]**、**[システムの詳細設定]** の順にクリックします。

	2. **[詳細設定]** タブをクリックし、続いて **[環境変数]** をクリックします。

	3. **[システム環境変数]** セクションの **[新規]** をクリックして変数を作成します。

	4. **[新しいシステム変数]** ダイアログで、変数の名前に**「SERVER\_HOME」**と入力し、値に**「C:\\Oracle\\Middleware\\Oracle\_Home\\wlserver」**と入力します。

	5. **[OK]** クリックすると、新しい環境変数が保存され **[新しいシステム変数]** ダイアログが閉じます。

	6. コントロール パネルで開かれた他のダイアログ ボックスを閉じます。

11. 新しいコマンド プロンプトを開きます (**SERVER\_HOME** 環境変数が有効になるように)。

	>[AZURE.NOTE]残りの手順の一部では、仮想マシンにログオンした後にコマンド プロンプトを使用する必要があります。どのコンピューターにログオンしているかを簡単に把握できるように、コマンド プロンプトを開いた後に **title %COMPUTERNAME%** を実行します。
	>
	>**(%Computername%** は、コンピューター名に自動的に設定されているシステム定義の環境変数です。**title** **%computername%** コマンドを実行すると、コマンド プロンプトのタイトル バーにコンピューターの名前が表示されます。)

12. 次のコマンドを実行します。

		%SERVER\_HOME%\\common\\bin\\pack.cmd -managed=true -domain=C:\\Oracle\\Middleware\\Oracle\_Home\\user\_projects\\domains\\base\_domain -template=c:\\mytestdomain.jar -template\_name="mytestdomain" 

	このコマンドは **c:\\mytestdomain.jar** という名前の jar を作成します。 後で、この jar をクラスターの管理対象仮想マシンにコピーします。

13. ポート 7001 の受信接続をファイアウォールで許可します。

	1. 仮想マシンにログインしたままで、**Windows の [スタート]** をクリックし、**「セキュリティが強化された Windows ファイアウォール」**と入力し、続いて**セキュリティが強化された Windows ファイアウォール** アイコンをクリックします。**[セキュリティが強化された Windows ファイアウォール]** 管理コンソールが開きます。

	2. ファイアウォール管理コンソール内で、左側のウィンドウの **[受信の規則]** をクリックし (**[受信の規則]**が表示されない場合は、左側のウィンドウの上のノードを展開します)、続いて右側のウィンドウの **[新しい規則]** をクリックします。

	3. **[規則の種類]** は、**[ポート]** を選択して **[次へ]** をクリックします。

	4. **[プロトコルとポート]** は、**[TCP]** を選択し、**[特定のローカル ポート]** を選択し、ポートに**「7001」**と入力し、続いて **[次へ]** をクリックします。

	5. **[接続を許可する]** を選択し、**[次へ]** をクリックします。

	6. 規則を適用するプロファイルの既定値をそのまま使用し、**[次へ]** をクリックします。

	7. 規則の名前とオプションで説明を指定し、続いて **[完了]** をクリックします。

14. 各管理対象仮想マシンに対して:

	1. 仮想マシンにログインします。

	2. **「SERVER\_HOME」**という環境変数を作成し、その値を**「C:\\Oracle\\Middleware\\Oracle\_Home\\wlserver」**に設定します 。

	3. 管理仮想マシンの c:\\mytestdomain.jar を管理対象仮想マシンの c:\\mytestdomain.jar にコピーします。

	4. コマンド プロンプトを開きます (また、どのコンピューターがアクセスされているかを明確にするために必ずコマンド プロンプトで **title %COMPUTERNAME%** を実行します)。

	5. 次のコマンドを実行します。

			%SERVER\_HOME%\\common\\bin\\unpack.cmd -domain=C:\\Oracle\\Middleware\\Oracle\_Home\\user\_projects\\domains\\base\_domain -template=c:\\mytestdomain.jar

	6. コマンド プロンプトの現在のディレクトリを **C:\\Oracle\\Middleware\\Oracle\_Home\\user\_projects\\domains\\base\_domain\\bin** に変更します。

	7. start<<*MACHINENAME*>>.cmd を実行します。<<*MACHINENAME*>> には管理対象コンピューターの名前を指定します。たとえば、**startMYVM2-MANAGED** です。

	8. メッセージが表示されたら、WebLogic Server のユーザー名とパスワードを入力します。

	9. ポート 7008 の受信接続をファイアウォールで許可します。(管理サーバーでポート 7001 を開いた手順と同じですが、管理対象サーバーではポートを 7008 にします。)

15. 管理仮想マシンで、**WebLogic Server 管理コンソール**、<http://localhost:7001/console> を開き、サーバーが実行するのを確認します。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image003.png)

16. 管理対象仮想マシンの負荷分散エンドポイント セットを作成します。

	1. [Azure ポータル](https://ms.portal.azure.com/)の **[仮想マシン]** セクションで、最初の管理対象仮想マシン (**MYVM2-MANAGED** など) を選択します。

	2. **[設定]**、**[エンドポイント]**、**[追加]** の順にクリックします。

	3. エンドポイントの名前を指定し、プロトコルに**「TCP」**を指定し、パブリック ポート**「80」**とプライベート ポート**「7008」**を指定します。 残りのオプションはそのままにします。

	4. **[負荷分散セットの作成]** チェック ボックスをオンにし、続いて **[完了]** をクリックします。

	5. 負荷分散セットの名前を指定し、他のパラメーターは既定値をそのまま使用し、続いて **[完了]** をクリックします。

17. 仮想マシンのエンドポイントを作成します。

	1. [Azure ポータル](https://ms.portal.azure.com/)にログインします。

	2. **[参照]** をクリックします

	3. **[仮想マシン]** をクリックします

	4. 仮想マシンを選択します

	5. **[設定]** をクリックします

	6. **[負荷分散セット]** をクリックします。

	7. **[結合]** をクリックします。

	8. 負荷分散セットタイプを **[内部]** に設定します

	9. エンドポイントの名前を指定します

		1. プロトコルに **TCP** を使用します

		2. パブリック ポートに **80** を使用します

		3. プローブ ポートに **7008** を使用します。

	10. 残りのオプションはそのままにします

	11. **[OK]** をクリックします。

	12. 次の手順に進む前に、この仮想マシンが負荷分散セットを結合するまで待機します。

18. [Azure ポータル](https://ms.portal.azure.com/)の **[仮想マシン]** セクションで、2 番目の管理対象仮想マシン (**MYVM3-MANAGED** など) を選択します。最初の管理対象仮想マシン用に作成した負荷分散セットに結合する上記の手順に従います。

##クラスターへのアプリケーションのデプロイ

この時点で、次の手順を使用してアプリケーションをデプロイすることができます。ここでは、<http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war> でダウンロードできる Oracle の ショッピングカート アプリケーションをデプロイしていると仮定します。

1. WebLogic サーバー クラスターの管理者として機能している仮想マシン (たとえば、**MYVM1-ADMIN**) にログインします。 

2. shoppingcart.war をローカルにコピーします。たとえば、**c:\\mywar** という名前のフォルダーを作成し、<http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war> で WAR を **c:\\mywar** に保存します。

3. **[WebLogic Server 管理コンソール]**、<http://localhost:7001/console> を開きます。メッセージが表示されたら、WebLogic のユーザー名とパスワードを入力します。

4. **WebLogic Server 管理コンソール**で、**[ロックと編集]**、**[デプロイ]**、**[インストール]** の順にクリックします。

5. **[パス]** に**「c:\\myway\\shoppingcart.war」**と入力します。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image004.png)

	**[次へ]** をクリックします。

6. **[このデプロイをアプリケーションとしてインストールする]** を選択し、続いて **[次へ]** をクリックします。

7. **[完了]** をクリックします。

8. **[利用可能なターゲット]** に以前作成したクラスターを選択し、**[クラスター内のすべてのサーバー]** が選択されていることを確認し、続いて **[次へ]** をクリックします。

9. **[ソースのアクセシビリティ]** で、**[私のすべてのターゲットにこのアプリケーションをコピーする]** を選択し、続いて **[完了]** をクリックします。

10.  **[WebLogic Server 管理コンソール]**で、**[保存]** をクリックし、続いて **[変更を有効にする]** をクリックします。

11.  **[デプロイ]** をクリックし、**[ショッピングカート]** を選択し、**[開始]** をクリックし、続いて **[すべての要求を処理]** をクリックします。確認メッセージが表示されたら、**[はい]** をクリックします。

12.  インターネット上で実行しているショッピングカート アプリケーションを表示するには、ブラウザーを開いて `http://<<unique_domain_name>>/shoppingcart` の形式の URL にアクセスします。(`<<unique_domain_name>>` の値を確認するには、[Azure ポータル](https://ms.portal.azure.com/)内で [仮想マシン] をクリックし、続いて Oracle WebLogic Server を実行するために使用している仮想マシンを選択します)。

## 次のステップ

さらにクラスターが期待どおりに動作していることを確認するには、ブラウザー セッションを処理するマシン名を表示するように shoppingcart.war プロジェクトを変更し、ブラウザー セッションを開始し、ブラウザー セッションを処理したマシンを停止し、そしてブラウザー セッションを更新するすることで、異なるマシンがブラウザー セッションを処理し続けていることを確認できます。

次に例を示します。

1. ファイルの先頭に次のコードが格納されるよう **DWRHeader1.jspf** ファイルを変更します:

		<table>
		
		<tr><td><CENTER><b><h3><% out.println("Your request is served from " + System.getenv("computername") ); %></h3></b></CENTER></td></tr>
		
		</table>             

2. コメント行 `Insert session descriptor element here` の後に次のコードが格納されるよう **weblogic.xm**l ファイルを変更します。

		<session-descriptor>
			<persistent-store-type>replicated_if_clustered</persistent-store-type>
		</session-descriptor>


3. 更新された shoppingcart.war を再コンパイルして再デプロイします。

4. ブラウザー セッションを開き、ショッピングカート アプリケーションを実行します。いくつかの商品をショッピングカートに追加し、どのマシンがブラウザー セッションを処理しているかを確認します。

5. Azure ポータルの **[仮想マシン]** ユーザー インターフェイスで、ブラウザー セッションを処理した VM を選択し、**[シャットダウン]** をクリックします。VM の状態が **[停止 (割り当て解除)]** になるのを待ってから次に進みます。

6. ショッピングカート アプリケーションを実行しているブラウザー セッションを更新し、異なるマシンがブラウザー セッションを処理していることを確認します。

7. ショッピングカートのリンクをクリックし、以前に追加した商品がまだショッピングカートの中にあることを確認します。

## その他のリソース

これで Oracle WebLogic Server を実行するクラスターのセットアップが終わりました。追加情報については、次のトピックを参照してください。

- [Oracle 仮想マシン イメージ - 他の考慮事項](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

- [Oracle WebLogic Server 製品ドキュメント](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

- [Microsoft Azure で Linux を使用する Oracle WebLogic Server 12c](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

<!---HONumber=August15_HO6-->