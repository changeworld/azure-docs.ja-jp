<properties linkid="manage-linux-common-tasks-manage-certs" urlDisplayName="管理証明書" pageTitle="Azure での Linux 仮想マシン用の管理証明書の作成" metaKeywords="Azure 管理証明書, 管理証明書のアップロード, Azure サービス管理 API" description="Azure で Linux 用の管理証明書を作成してアップロードする方法について説明します。証明書はサービス管理 API を使用する場合に必要です。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure での Linux 用の管理証明書の作成" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />





#Azure での Linux 用の管理証明書の作成

管理証明書は、サービス管理 API を使用して Azure イメージ プラットフォームと対話するときに必要です。

証明書の作成および管理方法に関するドキュメントは [http://msdn.microsoft.com/ja-jp/library/windowsazure/gg551721.aspx](http://msdn.microsoft.com/ja-jp/library/windowsazure/gg551721.aspx) に既に用意されています。または、OpenSSL を使用して管理証明書を作成することもできます。詳細については、[OpenSSL の Web サイト](http://openssl.org/)を参照してください。ただし、このドキュメントでは、Silverlight ポータルの使用に焦点を合わせています。このポータルには一部の Linux ユーザーがアクセスできない場合があります。独自にこれらの証明書にアクセスし、さまざまなツール (パートナー) に統合して使用する方法について説明します。管理証明書の機能が Azure 管理ポータルに追加されるまでは、この方法に従ってください。


##目次##

* [publishsettings ファイルから管理証明書を取得する](#createcert)
* [Azure 管理ポータルを使用して管理証明書をインストールする](#management)

<h2><a id="publishsettings"></a>方法: 管理証明書を作成してアップロードする</h2>


Azure の管理証明書を作成する簡単な方法が用意されています。そのためには [https://windows.azure.com/download/publishprofile.aspx](https://windows.azure.com/download/publishprofile.aspx) にアクセスします。

この Web サイトでは、ポータルの資格情報を使用してログインして管理証明書を生成するように求められます。生成された証明書は subscriptionID と共に publishsettings ファイルにパッケージされ、そのファイルをダウンロードするように求められます。

![linuxcredentials](./media/linux-create-management-cert/linuxcredentials.png)

このファイルは、失うと回復することができず、新しい管理証明書を生成する必要があるため、安全な場所に保存します (システムで使用できる証明書の合計数には制限があります。詳細については、この Web サイトの「証明書の生成の制限」セクションを参照してください)。その後、次の複数の方法でこの証明書を使用できます。

###Visual Studio で使用する###

![VSpublish](./media/linux-create-management-cert/VSpublish.png)


###Linux Azure コマンド ラインで使用する###

Azure の account import コマンドを実行することで、証明書をインポートして使用できるようになります。

![cmdlinepublish](./media/linux-create-management-cert/cmdlinepublish.png)

他のパートナーまたはソフトウェアのツールが必要な場合は、このファイル自体から管理証明書を抽出し、Base64 でデコードする必要があります。ScaleXtreme や SUSE Studio のような一部のパートナーでは、このファイルを現在の形式のまま使用します。

管理証明書を抽出するには、次の手順を実行する必要があります。

このファイルから Base64 でエンコードされた部分を抽出する必要があります。その部分は ManagementCertificate の後で引用符で囲まれています。

	?xml version="1.0" encoding="utf-8"?>
	<PublishData>
	  <PublishProfile
	    PublishMethod="AzureServiceManagementAPI"
	    Url="https://management.core.windows.net/"
	    ManagementCertificate="xxxxx">
	    <Subscription
	      Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
	      Name="hjereztest" />
	  </PublishProfile>
	</PublishData>
	
この部分をファイルにコピーして、次のように Linux の Base64 デコーダーを使用してデコードする必要があります。

	Base64 -d [encodedfile] > [decodedfile].pfx

これにより PFX ファイルが出力されます。必要に応じてこのファイルは OpenSSL を使用して他の形式に変換して、秘密キーを抽出できます。そのためには次のコマンドを実行します。

 	openssl.exe pkcs12 -in publicAndprivate.pfx -nocerts -out privateKey.pem 

Windows では、PowerShell または無料の Windows Base64 デコーダー ([http://www.fourmilab.ch/webtools/base64/base64.zip] など) を使用してデコードして、PFX ファイルを抽出できます。そのためには次のコマンドを実行します。

	base64 -d key.txt ->key.pfx

####証明書の生成の制限####

プラットフォームでこのツールを使用して作成できる証明書の合計数は、最大 10 個に制限されています。
残念ながら、生成されたキーを回復する方法はありません。システム内のいずれの場所にもこれらの秘密キーが保存されていないためです。
システムでの証明書の上限数に達した場合は、Azure フォーラムを通じてサポートに連絡して、証明書の削除を依頼できます。または、以前の Silverlight ポータルを表示可能なブラウザーを使用して、このタスクを実行できます。

####秘密キーが侵害された場合####

秘密キーがいずれかの時点で侵害された場合、Silverlight をサポートするブラウザーを使用して以前の Silverlight ポータルにアクセスして、対応する登録済み管理証明書を削除する必要があります。または、フォーラムを通じてサポートに連絡して、このタスクを依頼する必要があります。
新しい証明書を生成するだけでは不十分です。10 個の証明書がすべて有効であっても、侵害された古いキーはまだ Web サイトにアクセス可能であるためです。

<h2><a id="management"></a>Azure 管理ポータルを使用して管理証明書をインストールする</h2>

管理証明書は、さまざまな方法で作成できます。証明書の作成方法の詳細については、「[Azure の管理証明書の作成とアップロード](http://msdn.microsoft.com/ja-jp/library/windowsazure/gg551722.aspx)」を参照してください。管理証明書を作成した後、それを Azure のサブスクリプションに追加します。

1. Azure 管理ポータルにサインインします。

2. ナビゲーション ウィンドウで **[設定]** をクリックします。

3. **[管理証明書]** で、**[管理証明書のアップロード]** をクリックします。

4. **[管理証明書のアップロード]** で該当する証明書ファイルを見つけ、**[OK]** をクリックします。

### 証明書のサムプリントとサブスクリプション ID を取得する###

Azure に .vhd ファイルをアップロードするには、追加した管理証明書のサムプリントとサブスクリプション ID が必要です。

1. 管理ポータルで、**[設定]** をクリックします。

2. **[管理証明書]** で目的の証明書をクリックし、**[プロパティ]** ウィンドウでサムプリントをコピーして、後で取得できる場所に貼り付けて記録します。

また、.vhd ファイルをアップロードするにはサブスクリプション ID も必要です。

1. 管理ポータルで、**[すべてのアイテム]** をクリックします。

2. 中心のウィンドウの **[サブスクリプション]** でサブスクリプションをコピーして、後で取得できる場所に貼り付けます。

###独自のキーを生成した場合にツールに指定する情報###

####CSUPLOAD の場合

1.	管理者として Azure SDK のコマンド プロンプト ウィンドウを開きます。
2.	次のコマンドで **Subscriptionid** と **CertThumbprint**  を先に取得した値に置き換えて実行して、接続文字列を設定します。


		csupload Set-Connection "SubscriptionID=<Subscriptionid>;CertificateThumbprint=<Thumbprint>;ServiceManagementEndpoint=https://management.core.windows.net"

####Linux 用 Azure コマンド ライン ツールの場合

OpenSSL を使用して作成した PFX ファイルを Base64 でエンコードする必要があります。そのためには次のコマンドを実行します。

 		Base64 [file] > [econded file]

その後、サブスクリプション ID と Base64 でエンコードした PFX を次のような構造になるように 1 つのファイルにマージする必要があります。

		?xml version="1.0" encoding="utf-8"?>
		<PublishData>
		  <PublishProfile
		    PublishMethod="AzureServiceManagementAPI"
		    Url="https://management.core.windows.net/"
		    ManagementCertificate="xxxxx">
		    <Subscription
		      Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
		      Name="hjereztest" />
		  </PublishProfile>
		</PublishData>
		
ここで xxxxx は[エンコードしたファイル]の内容です。この部分は Linux 用 Azure コマンド ライン ツールで、
Azure account import (File) コマンドに使用します。

