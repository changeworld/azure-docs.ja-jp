#Azure の Web サイトでの HTTPS の有効化

> [WACOM.NOTE]
> より速く進める - 新しい Azure の使用[チュートリアル ガイド](http://support.microsoft.com/kb/2990804)!Azure Cloud Services または Azure Websites を使用したカスタム ドメイン名の関連付けおよび通信 (SSL) のセキュリティ保護がすばやく行えます。

セキュリティで保護された Socket Layer (SSL) 暗号化を使用する HTTPS を使用して、Web サイトとブラウザー間の通信をセキュリティで保護することができます。この SSL 暗号化は、インターネットを介して送信されるデータをセキュリティで保護する際に最もよく使用される方法で、またサイトの訪問者に対し、そのサイト上でのトランザクションの安全性を保証します。この記事では、Azure の Web サイトに対して HTTPS を構成する方法を説明します。 

<a href="bkmk_azurewebsites"></a><h2>\*.azurewebsites.net ドメインの HTTPS</h2>

カスタム ドメイン名を使用する計画がなく、代わりに Azure によってサイトに割り当てられた \*.azurewebsites.net ドメイン (たとえば、contoso.azurewebsites.net) を使用することを計画している場合は、Microsoft の証明書によって HTTPS は既にサイトで有効に設定されています。**https://mywebsite.azurewebsites.net** を使用して Web サイトにアクセスすることができます。ただし、\*.azurewebsites.net はワイルドカード ドメインです。[すべてのワイルドカード ドメイン](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/)は一様に、独自の証明書を持つカスタム ドメインを使用する場合ほど安全ではありません。 

このドキュメントの残りの部分では、**contoso.com**、**www.contoso.com**、または **\*.contoso.com** のようなカスタム ドメインに対して HTTPS を有効にする方法を詳細に説明します。

<a href="bkmk_domainname"></a><h2>カスタム ドメインの SSL を有効にする</h2>

**contoso.com** のようなカスタム ドメインに対して HTTPS を有効にするには、ドメイン名レジストラーにカスタム ドメイン名を登録する必要があります。Azure の Web サイトのカスタム ドメイン名の構成方法の詳細については、「[Azure の Web サイトのカスタム ドメイン名の構成](/ja-jp/develop/net/common-tasks/custom-dns-web-site/)」を参照してください。カスタム ドメイン名を登録し、そのカスタム名に応答するように Web サイトを構成した後、そのドメインに対応する SSL 証明書を要求する必要があります。 

> [WACOM.NOTE] カスタム ドメイン名に対して HTTPS を有効にするには、**標準**モードの Web ホスティング プランで Web サイトを構成する必要があります。現在無料モードまたは共有モードを使用している場合、このモードで構成を行うと追加料金が発生する場合があります。共有モードと**標準**モードの価格の詳細については、「[料金の詳細][pricing]」を参照してください。 

有効なカスタム ドメインの作成後、次の手順を実行して Web サイトに対して HTTPS を有効にします。

1. [SSL 証明書を取得する](#bkmk_getcert)
1. [標準モードの構成](#bkmk_standardmode)
1. [SSL の構成](#bkmk_configuressl)
1. [Azure での Web サイトにおける HTTPS の適用](#bkmk_enforce)

<a href="bkmk_getcert"></a><h2>SSL 証明書を取得する</h2>

SSL 証明書を要求する前に、その証明書により、どのドメイン名をセキュリティで保護するかを最初に決定する必要があります。この結果、どのような種類の証明書を取得する必要があるかが決まります。**contoso.com** または **www.contoso.com** のように、ただ 1 つのドメイン名を保護する必要がある場合は、基本的な証明書で十分です。**contoso.com**、**www.contoso.com**、および **mail.contoso.com** のように複数のドメイン名を保護する必要がある場合は、[ワイルドカード証明書](http://en.wikipedia.org/wiki/Wildcard_certificate)、つまり[サブジェクト代替名](http://en.wikipedia.org/wiki/SubjectAltName) (subjectAltName) を使用した証明書を取得できます。

Azure Websites で使用される SSL 証明書は、[証明機関](http://en.wikipedia.org/wiki/Certificate_authority) (CA) が署名する必要があります。まだ SSL 証明書がない場合は、SSL 証明書を発行する会社から取得する必要があります。証明機関の一覧については、Microsoft TechNet Wiki の「[Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs) (Windows および Windows Phone 8 SSL ルート証明書プログラム (メンバー CA))][ cas]」を参照してください。

証明書は、Azure における SSL 証明書の次の要件を満たす必要があります。

* 証明書は秘密キーを含む必要があります。
* 証明書はキー交換のために作成され、Personal Information Exchange (.pfx) ファイルにエクスポートできる必要があります。
* 証明書の件名は Web サイトへのアクセスに使用されるドメインと一致する必要があります。この証明書で複数のドメインを扱う場合は、前に説明したように、ワイルドカード値を使用するか、subjectAltName 値を指定する必要があります。
* 証明書では、2,048 ビット以上の暗号化を使用する必要があります。
* プライベート CA サーバーから発行された証明書は、Azure Websites ではサポートされていません。

Azure Websites で使用する SSL 証明書を取得するには、証明書署名要求 (CSR) を証明機関に送信し、その後、受け取った証明書で .pfx ファイルを生成します。これは好みのツールを使用して実行できます。証明書を取得する一般的な方法には、以下の数種類があります。

- [Certreq.exe を使用した証明書の取得](#bkmk_certreq)
- [IIS マネージャーを使用した証明書の取得](#bkmk_iismgr)
- [OpenSSL を使用した証明書の取得](#bkmk_openssl)
- [OpenSSL を使用した SubjectAltName 証明書の取得](#bkmk_subjectaltname)
- [自己署名証明書の生成 (テスト目的専用)](#bkmk_selfsigned) 

> [WACOM.NOTE] 手順の途中で、"www.contoso.com" などの**共通名**の入力が必要になります。ワイルドカード証明書の場合、この値は \*.ドメイン名 (\*.contoso.com など) にする必要があります。ワイルドカード名 (\*.contoso.com など) とルート ドメイン名 (contoso.com など) の両方をサポートする必要がある場合、ワイルドカードの subjectAltName 証明書を使用できます。

> [WACOM.NOTE] Azure Websites は楕円曲線暗号 (ECC) 証明書をサポートしています。ただし、この証明書は比較的新しいため、正しい手順で CSR を作成するには証明機関の協力が必要です。

CA が**[中間証明書](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** (チェーン証明書とも呼びます) を使用している場合は、中間証明書も取得する必要があります。中間証明書を使用すると、"チェーンされていない証明書" を使用する場合よりセキュリティが強化されると見なされるため、CA で一般的に使用されています。中間証明書は、多くの場合、CA の Web サイトから個別にダウンロードする形で提供されています。この記事では、Azure の Web サイトにアップロードする証明書に、任意の中間証明書を確実にマージするための手順について説明します。 

<a href="bkmk_certreq"></a>
###Certreq.exe を使用した証明書の取得 (Windows のみ)

Certreq.exe は、証明書の要求を作成するための Windows ユーティリティです。Windows XP または Windows Server 2000 以降の Windows 基本インストールの一部であり、最近の Windows システムで使用できます。Certreq.exe を使用して SSL 証明書を取得するには、次の手順を使用します。

1. **メモ帳**を開き、次の内容を含む新しい文書を作成します。Subject 行の **mysite.com** を、Web サイトに対応するカスタム ドメイン名で置き換えます。たとえば、Subject = "CN=www.contoso.com" と記述します。

		[NewRequest]
		Subject = "CN=mysite.com"
		Exportable = TRUE
		KeyLength = 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		RequestType = CMC

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1

上記で指定したオプションの詳細、および他の使用可能なオプションについては、[Certreq リファレンス ドキュメントに関するページ](http://technet.microsoft.com/library/cc725793.aspx)を参照してください。

2. テキスト ファイル **myrequest.txt** という名前で保存します。

3. **スタート画面**または**[スタート] メニュー**で、**cmd.exe** を実行します。

4. コマンド プロンプトで次のコマンドを使用して、証明書要求ファイルを作成します。

		certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

	ステップ 1. で作成した **myrequest.txt** ファイルに対応するパスと、**myrequest.csr** ファイルを作成するときに使用するパスを指定します。

5. SSL 証明書を取得するために、**myrequest.csr** を証明機関に送信します。この送信には、ファイルをアップロードすること、またはメモ帳でファイルを開き、Web フォームに内容を貼り付ける作業が含まれる可能性があります。

証明機関の一覧については、Microsoft TechNet Wiki の「[Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs) (Windows および Windows Phone 8 SSL ルート証明書プログラム (メンバー CA))][ cas]」を参照してください。

6. 証明機関から証明書 (.CER) ファイルが提供された後、要求を生成したときに使用しコンピューターにこのファイルを保存してから、次のコマンドを使用して要求を受け入れ、証明書の生成プロセスを完了します。

		certreq -accept -user mycert.cer

この例では、証明機関から受け取った **mycert.cer** 証明書を使用して、証明書の署名を完了します。ファイルは作成されません。代わりに、証明書が Windows 証明書ストアに格納されます。

6. CA が中間証明書を使用する場合、次のステップで証明書をエクスポートする前に、それらの中間証明書をインストールする必要があります。通常、これらの証明書は CA から個別のダウンロードとして提供されており、Web サーバーの種類に応じていくつかの形式で提供されています。Microsoft IIS 用に提供されているバージョンを選択します。

証明書をダウンロードした後、エクスプローラーで証明書を右クリックし、**[証明書のインストール]** を選択します。**証明書のインポート ウィザード**で既定値を使用し、インポートが完了するまで、**[次へ]** のクリックを続けます。

7. 証明書ストアから証明書をエクスポートするには、**スタート画面**または **[スタート] メニュー**から **certmgr.msc** を実行します。**証明書マネージャー**が表示された時点で、**[個人用]** フォルダーを展開し、**[証明書]** をクリックします。**[発行先]** フィールドで、証明書を要求したカスタム ドメイン名に対応するエントリを見つけます。**[発行者]** フィールドに、この証明書に関して使用した証明機関が表示されます。

	![insert image of cert manager here][certmgr]

9. 証明書を右クリックし、**[すべてのタスク]** を選択してから **[エクスポート]** を選択します。**証明書のエクスポート ウィザード**で、**[次へ]** をクリックしてから **[はい、秘密キーをエクスポートします]** をクリックします。**[次へ]** をクリックします。

	![Export the private key][certwiz1]

10. **[個人情報の交換 - PKCS #12]**、**[証明書チェーン内にすべての証明書を含める]**、および **[すべての拡張プロパティをエクスポートする]** を選択します。**[次へ]** をクリックします。

	![include all certs and extended properties][certwiz2]

11. **[パスワード]** を選択し、パスワードの入力と確認入力を行います。**[次へ]** をクリックします。

	![specify a password][certwiz3]

12. エクスポートした証明書を格納するファイル名とパスを指定します。ファイル名には、**.pfx** という拡張子を付ける必要があります。**[次へ]** をクリックしてプロセスを完了します。

	![provide a file path][certwiz4]

ここで、エクスポートした PFX ファイルを Azure の Web サイトにアップロードすることができます。

<a href="bkmk_openssl"></a>
###OpenSSL を使用した証明書の取得

1. コマンド ライン、bash、またはターミナル セッションから次を入力し、秘密キーと証明書署名要求を生成します:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. メッセージが表示されたら、適切な情報を入力します。次に例を示します。

 		Country Name (2 letter code) 
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		証明書の要求を送信するために、次の "追加" 属性を入力してください。

A challenge password []: 

この処理を完了すると、**myserver.key** および **server.csr** の 2 つのファイルが生成されます。**server.csr** には、証明書署名要求が含まれます。

3. SSL 証明書を取得するために、CSR を証明機関に送信します。証明機関の一覧については、Microsoft TechNet Wiki の「[Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs) (Windows および Windows Phone 8 SSL ルート証明書プログラム (メンバー CA))][ cas]」を参照してください。

4. CA からの証明書を入手した場合、**myserver.crt** という名前のファイルに保存します。CA がテキスト形式の証明書を提供した場合、**myserver.crt** ファイルに証明書のテキストを貼り付けるだけです。ファイルの内容をテキスト エディターで表示すると、次のようになります。

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

	ファイルを保存します。

5. コマンド ライン、bash、またはターミナル セッションから、次のコマンドを使用して **myserver.key** および **myserver.crt** を Azure Websites で必要とされる形式の **myserver.pfx** に変換します。

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	メッセージが表示されたら、パスワードを入力して .pfx ファイルをセキュリティ保護します。

	<div class="dev-callout"> 
	<b>注</b>
	<p>CA が中間証明書を使用する場合、次のステップで証明書をエクスポートする前に、それらの中間証明書をインストールする必要があります。通常、これらの証明書は CA から個別のダウンロードとして提供されており、Web サーバーの種類に応じていくつかの形式で提供されています。PEM ファイル (ファイル拡張子は .pem) の形で提供されているバージョンを選択します。</p>
	<p>次のコマンドは、<b>intermediate-cets.pem</b> ファイルに格納されている中間証明書を含む .pfx ファイルを作成する方法を示しています。</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

このコマンドを実行すると、Azure Websites での使用に適した **myserver.pfx** ファイルが生成されます。

###<a name="bkmk_iismgr"></a>IIS マネージャーを使用した証明書の取得

IIS マネージャーに慣れている場合は、IIS マネージャーを使用して、Azure の Web サイトで使用できる証明書を生成することができます。

1. IIS マネージャーを使用して、証明機関に送信する CSR を生成します。CSR 生成の詳細については、「[インターネット サーバー証明書を要求する (IIS 7)][ iiscsr]」を参照してください。

2. SSL 証明書を取得するために、CSR を証明機関に送信します。証明機関の一覧については、Microsoft TechNet Wiki の「[Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs) (Windows および Windows Phone 8 SSL ルート証明書プログラム (メンバー CA))][ cas]」を参照してください。

3. 証明機関ベンダーにより提供された証明書で CSR を完了します。CSR の完了手順の詳細については、「[インターネット サーバー証明書をインストールする (IIS 7)][ installcertiis]」を参照してください。

4. CA が中間証明書を使用する場合、次のステップで証明書をエクスポートする前に、それらの中間証明書をインストールする必要があります。通常、これらの証明書は CA から個別のダウンロードとして提供されており、Web サーバーの種類に応じていくつかの形式で提供されています。Microsoft IIS 用に提供されているバージョンを選択します。

証明書をダウンロードした後、エクスプローラーで証明書を右クリックし、**[証明書のインストール]** を選択します。**証明書のインポート ウィザード**で既定値を使用し、インポートが完了するまで、**[次へ]** のクリックを続けます。

4. IIS マネージャーで証明書をエクスポートします。証明書のエクスポートの詳細については、「[サーバー証明書をエクスポートする (IIS 7)][ exportcertiis]」を参照してください。エクスポートしたファイルは、Azure Websites で使用するために、後の手順で Azure にアップロードする場合に使用されます。

	<div class="dev-callout"> 
	<b>注</b>
	<p>エクスポート プロセスでは、オプション <strong>[はい、秘密キーをエクスポートします]</strong> を必ず選択してください。これにより、エクスポートされる証明書に秘密キーが含まれます。</p>
	</div>

	<div class="dev-callout"> 
	<b>注</b>
	<p>エクスポート プロセスでは、オプション <strong>[証明のパスにあるすべての証明書を含める]</strong> と <strong>[すべての拡張プロパティをエクスポートする]</strong> を必ず選択してください。これにより、エクスポートされる証明書にすべての中間証明書が含まれます。</p>
	</div>


###<a href="bkmk_subjectaltname"></a>OpenSSL を使用した SubjectAltName 証明書の取得

OpenSSL を使用して、1 つの証明書で複数のドメイン名をサポートするために SubjectAltName 拡張機能を使用する証明書要求を作成できます。ただし、その場合は構成ファイルが必要になります。次のステップでは、構成ファイルの作成と構成ファイルを使用した証明書の要求について説明します。

1. __sancert.cnf__ という名前の新しいファイルを作成し、このファイルの内容として次のコードを使用します。
 
		# -------------- BEGIN custom sancert.cnf -----
		HOME = .
		oid_section = new_oids
		[ new_oids ]
		[ req ]
		default_days = 730
		distinguished_name = req_distinguished_name
		encrypt_key = no
		string_mask = nombstr
		req_extensions = v3_req # Extensions to add to certificate request
		[ req_distinguished_name ]
		countryName = Country Name (2 letter code)
		countryName_default = 
		stateOrProvinceName = State or Province Name (full name)
		stateOrProvinceName_default = 
		localityName = Locality Name (eg, city)
		localityName_default = 
		organizationalUnitName  = Organizational Unit Name (eg, section)
		organizationalUnitName_default  = 
		commonName              = Your common name (eg, domain name)
		commonName_default      = www.mydomain.com
		commonName_max = 64
		[ v3_req ]
		subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
		# -------------- END custom sancert.cnf -----

	"subjectAltName" で始まる行に注意してください。現在示されているドメイン名は、共通名に加えてサポートする必要があるドメイン名に置き換えてください。次に例を示します。

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	commonName_default フィールドを変更する必要はありません。次のステップのいずれかで、共通名を入力するように求められるためです。

2. __sancert.cnf__ ファイルを保存します。

1. sancert.cnf 構成ファイルを使用して、秘密キーと証明書署名要求を生成します。bash またはターミナル セッションから、次のコマンドを使用します。

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. メッセージが表示されたら、適切な情報を入力します。次に例を示します。

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com
 

この処理を完了すると、**myserver.key** および **server.csr** の 2 つのファイルが生成されます。**server.csr** には、証明書署名要求が含まれます。

3. SSL 証明書を取得するために、CSR を証明機関に送信します。証明機関の一覧については、Microsoft TechNet Wiki の「[Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs) (Windows および Windows Phone 8 SSL ルート証明書プログラム (メンバー CA))][ cas]」を参照してください。

4. CA からの証明書を入手した場合、**myserver.crt** という名前のファイルに保存します。CA がテキスト形式の証明書を提供した場合、**myserver.crt** ファイルに証明書のテキストを貼り付けるだけです。ファイルの内容をテキスト エディターで表示すると、次のようになります。

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

	ファイルを保存します。

5. コマンド ライン、bash、またはターミナル セッションから、次のコマンドを使用して **myserver.key** および **myserver.crt** を Azure Websites で必要とされる形式の **myserver.pfx** に変換します。

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	メッセージが表示されたら、パスワードを入力して .pfx ファイルをセキュリティ保護します。

	<div class="dev-callout"> 
	<b>注</b>
	<p>CA が中間証明書を使用する場合、次のステップで証明書をエクスポートする前に、それらの中間証明書をインストールする必要があります。通常、これらの証明書は CA から個別のダウンロードとして提供されており、Web サーバーの種類に応じていくつかの形式で提供されています。PEM ファイル (ファイル拡張子は .pem) の形で提供されているバージョンを選択します。</p>
	<p>次のコマンドは、<b>intermediate-cets.pem</b> ファイルに格納されている中間証明書を含む .pfx ファイルを作成する方法を示しています。</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

このコマンドを実行すると、Azure Websites での使用に適した **myserver.pfx** ファイルが生成されます。


###<a href="bkmk_selfsigned"></a>自己署名証明書の生成 (テスト目的専用)

テスト用の証明書を取得し、信頼された CA からの証明書の実際の購入は、運用開始時に行いたい場合もあります。この場合、自己署名証明書が有効です。自己署名証明書では、自身が証明機関 (CA) となり、証明書を作成および署名できます。この証明書は Web サイトのセキュリティ保護として使用できますが、証明書に信頼された CA の署名がないため、ほとんどのブラウザーはサイトの参照時にエラーを返します。ブラウザーによっては、サイトの表示を拒否することもあります。

- [makecert を使用した自己署名証明書の生成](#bkmk_ssmakecert)
- [OpenSSL を使用した自己署名証明書の生成](#bkmk_ssopenssl)

<a href="bkmk_ssmakecert"></a>
#### makecert #### を使用した自己署名証明書の生成

Visual Studio がインストールされている Windows システムからテスト証明書を作成する手順は次のとおりです:

1. **[スタート] メニュー**または**スタート画面**で、「**開発者コマンド プロンプト**」を検索します。最後に、**[開発者コマンド プロンプト]** を右クリックし、**[管理者として実行]** をクリックします。

[ユーザー アカウント制御] ダイアログ ボックスが表示された場合、**[はい]** をクリックして続行します。

2. [開発者のコマンド プロンプト] で、新しい自己署名証明書を作成するために次のコマンドを使用します。**serverdnsname** は、Web サイトの DNS に必ず置き換えてください。

		makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

	このコマンドが作成する証明書は、2013 年 1 月 1 日から 2014 年 1 月 1 日まで有効で、CurrentUser の証明書ストア内の位置に格納されます。

3. **[スタート] メニュー**または**スタート画面**で、「**Windows PowerShell**」を検索し、このアプリケーションを開始します。

4. Windows PowerShell プロンプトで、以前に作成された証明書をエクスポートするために、次のコマンドを使用します:

		$mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
		get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

これは $mypwd にセキュリティ保護された文字列として指定したパスワードを格納し、**dnsname** パラメーターによって指定された DNS 名を使用して証明書を検索し、**filepath** パラメーターに指定されたファイルにエクスポートします。パスワードを含むセキュリティ保護された文字列は、をエクスポートしたファイルをセキュリティ保護するために使用されます。

<a href="bkmk_ssopenssl"></a>
####OpenSSL #### を使用した自己署名証明書の生成

1. **serverauth.cnf** という名前の新しいドキュメントを作成し、このファイルの内容として次を使用します。

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName			= Country Name (2 letter code)
        countryName_min			= 2
        countryName_max			= 2
        stateOrProvinceName		= State or Province Name (full name)
        localityName			= Locality Name (eg, city)
        0.organizationName		= Organization Name (eg, company)
        organizationalUnitName		= Organizational Unit Name (eg, section)
        commonName			= Common Name (eg, your website's domain name)
        commonName_max			= 64
        emailAddress			= Email Address
        emailAddress_max		= 40

        [ req_attributes ]
        challengePassword		= A challenge password
        challengePassword_min		= 4
        challengePassword_max		= 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

	これは、Azure の Web サイトで使用できる SSL 証明書の生成に必要な構成設定を指定します。

2. コマンド ライン、bash、またはターミナル セッションから次を入力し、新しい自己署名証明書を生成します:

		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

これは **serverauth.cnf** ファイルで指定された構成設定を使用して、新しい証明書を作成します。

3. Azure の Web サイトにアップロードできる .PFX に証明書をエクスポートするには、次のコマンドを使用します:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	メッセージが表示されたら、パスワードを入力して .pfx ファイルをセキュリティ保護します。

このコマンドによって作成された **myserver.pfx** は、Azure Websites をテスト目的でセキュリティ保護するために使用できます。

<a href="bkmk_standardmode"></a><h2>標準モードの構成</h2>

カスタム ドメインに対して HTTPS を有効にできるのは、Azure Websites の Web ホスティング プランで**標準**モードを使用している場合のみです。**標準**モードに切り替えるには、次のステップを使用します。

> [WACOM.NOTE] Web サイトを無料 Web サイト モードから**標準**モードに切り替える前に、Websites のサブスクリプションに設定されている使用制限を解除する必要があります。この作業を実行しないと、請求期間が終了する前に制限に到達した場合に、サイトが使用できなくなるおそれがあります。共有モードと**標準**モードの価格の詳細については、「[料金の詳細][pricing]」を参照してください。

1. ブラウザーで、[管理ポータル][portal]を開きます。

2. **[Web サイト]** タブで、Web サイトの名前をクリックします。

	![selecting a web site][website]

3. **[規模の設定]** タブをクリックします。

	![The scale tab][scale]

4. **[全般]** セクションで、**[標準]** をクリックして Web ホスティング プランのモードを設定します。

	![standard mode selected][standard]

5. **[保存]** をクリックします。メッセージが表示されたら、「**はい**」をクリックします。

	> [WACOM.NOTE]"Web サイト '&lt;サイト名&gt;' のスケールの構成に失敗しました" エラーが発生する場合は、詳細ボタンを使用して詳細情報を表示できます。"この要求を満たす、利用可能な標準インスタンス サーバーが足りません。" というエラーが発生する場合があります。このエラーが発生した場合は、[Azure のサポート オプション](http://www.windowsazure.com/ja-jp/support/options/)にお問い合わせください。


##<a href="bkmk_configuressl"></a>SSL の構成

このセクションの手順を実行する前に、Azure の Web サイトにカスタム ドメイン名が関連付けられている必要があります。詳細については、「[Azure の Web サイトのカスタム ドメイン名の構成][ customdomain]」を参照してください。

1. ブラウザーで、[Azure の管理ポータル][portal]を開きます。

2. **[Web サイト]** タブで、サイトの名前をクリックし、**[構成]** タブを選択します。

	![the configure tab][configure]

3. **[証明書]** セクションで、**[証明書をアップロードします]** をクリックします。

	![upload a certificate][uploadcert]

4. **[証明書をアップロードします]** ダイアログを使用して、IIS マネージャーまたは OpenSSL によって既に作成されている .pfx 証明書ファイルを選択します。.pfx ファイルをセキュリティ保護するために使用されたパスワードをある場合、それを指定します。最後に、**[チェック]** をクリックして証明書をアップロードします。

	![upload certificate dialog][uploadcertdlg]

5. **[構成]** タブの **[SSL のバインディング]** のセクションで、ドロップダウン リストから SSL でセキュリティ保護するドメイン名、および使用する証明書を選択します。さらに、[[Server Name Indication]][ sni] (SNI) または IP ベースの SSL を使用するかどうか選択できます。

	![ssl bindings][sslbindings]
	
	* IP ベースの SSL は、サーバーの専用パブリック IP アドレスをドメイン名にマッピングすることによって、証明書をドメイン名に関連付けします。これは、サービスに関連付けられている各ドメイン名 (contoso.com、fabricam.com など) の専用の IP アドレスが必要となります。これは SSL 証明書と Web サーバーを関連付ける従来のメソッドです。

	* SNI ベースの SSL は、SSL と[トランスポート層セキュリティ][ tls] (TLS) の拡張です。TLS では、複数のドメインが同じ IP アドレスを共有し、各ドメインが独自のセキュリティ証明書を持つことができます。最新のブラウザー (Internet Explorer、Chrome、Firefox、および Opera を含む) のほとんどが SNI をサポートしていますが、古いブラウザーには、SNI をサポートしていないものもあります。SNI の詳細については、Wikipedia の [Server name Indication][ sni] の記事を参照してください。

6. 変更を保存して SSL を有効にするには、**[保存]** をクリックします。

> [WACOM.NOTE]  **IP ベースの SSL** を選択し、カスタム ドメインが A レコードを使用して構成されている場合、次の追加の手順を実行する必要があります。
>
> 1. IP ベースの SSL バインドを構成すると、専用の IP アドレスが Web サイトに割り当てられます。この IP アドレスは、Web サイトの **[ダッシュボード]** ページの **[概要]** セクションで確認できます。このアドレスは、**仮想 IP アドレス**として示されます。
>    
>     ![Virtual IP address](./media/configure-ssl-web-site/staticip.png)
>    
> この IP アドレスは、ドメイン用の A レコードを構成するために以前使用した仮想 IP アドレスとは異なります。SNI ベースの SSL を使用するように構成する場合、または SSL を使用するように構成しない場合は、このエントリに対してアドレスは表示されません。
>
> 2. ドメイン名レジストラーから提供されるツールを使用して、前の手順の IP アドレスを指定するようにカスタム ドメイン名用の A レコードを変更します。


ここで、証明書が正しく構成されていることを確認するために、"HTTP://" ではなく、"HTTPS://" を使用して Web サイトを参照することができる必要があります。

##<a href="bkmk_enforce"></a>Azure での Web サイトにおける HTTPS の適用

Azure Websites は HTTPS を強制的には適用しません。訪問者は引き続き HTTP を使用してサイトにアクセスできることから、Web サイトのセキュリティが損なわれる可能性があります。Web サイトで HTTPS を適用する場合は、**URL 書き換え**モジュールを使用できます。URL 書き換えモジュールは Azure Websites に組み込まれており、それを使用して、受信した要求をアプリケーションに渡す前に要求に適用されるルールを定義できます。**このモジュールは、Azure Websites がサポートするプログラミング言語で記述されたアプリケーションで使用できます。** 

> [WACOM.NOTE] .NET MVC アプリケーションでは、URL 書き換えの代わりに [RequireHttps](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.requirehttpsattribute.aspx) フィルターを使用する必要があります。RequireHttps の使用方法の詳細については、[安全な ASP.NET MVC 5 アプリケーションを Azure の Web サイトにデプロイする](/ja-jp/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/)方法に関するページを参照してください。
> 
> 他のプログラミング言語とフレームワークを使用して要求をプログラム的にリダイレクトする方法の詳細については、該当するテクノロジのドキュメントを参照してください。

URL 書き換えルールは、アプリケーションのルートに格納されている **web.config** ファイルに定義されます。次の例には、すべての受信トラフィックに HTTPS の使用を強制する基本的な URL 書き換えルールが含まれています。

<a name="example"></a>**Web.Config ファイルの URL 書き換えの例**

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <rewrite>
	      <rules>
	        <rule name="Force HTTPS" enabled="true">
	          <match url="(.*)" ignoreCase="false" />
	          <conditions>
	            <add input="{HTTPS}" pattern="off" />
	          </conditions>
	          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
	        </rule>
	      </rules>
	    </rewrite>
	  </system.webServer>
	</configuration>

このルールは、ユーザーが HTTP を使用してページを要求したときに HTTP ステータス コード 301 (永続的なリダイレクト) を返すことで動作します。301 は、訪問者が要求した URL と同じ URL へ要求をリダイレクトしますが、要求の HTTP 部分は HTTPS で置き換えられます。たとえば、HTTP://contoso.com は、HTTPS://contoso.com にリダイレクトされます。

> [WACOM.NOTE]アプリケーションが **Node.js**、**PHP**、**Python Django**、または **Java** で記述されている場合は、web.config ファイルが含まれていない可能性があります。ただし、**Node.js**、**Python Django**、**Java** はすべて、実際には、Azure Web サイト上でホストされた場合に web.config を使用します。Azure はデプロイメント時に自動的にファイルを作成するため、それを目にすることはありません。アプリケーションの一部としてこのファイルを含めると、Azure が自動的に生成したファイルは上書きされます。

###.NET

.NET アプリケーションの場合、アプリケーションの web.config ファイルを変更して、**例**の [&lt;rewrite>](#example) セクションを、**&lt;system.WebServer>** セクションに追加します。

web.config ファイルに **&lt;rewrite>** セクションが既に含まれている場合は、**例**の [&lt;rule>](#example) を **&lt;rules>** セクションの最初のエントリとして追加します。

###PHP

PHP アプリケーションの場合は、[例](#example)を web.config ファイルとしてアプリケーションのルートに保存するだけです。その後、アプリケーションを Azure Websites に再度デプロイします。

###Node.js、Python Django、および Java

Node.js、Python Django、および Java アプリケーションの場合、web.config ファイルがまだ提供されていない場合は自動的に作成されますが、デプロイメント時に作成されるため、サーバー上にのみ存在します。自動生成されたファイルには、Azure にアプリケーションのホスト方法を伝える設定が含まれます。

自動生成されたファイルを Web サイトから取得して変更するには、次の手順を使用します。

1. FTP を使用してファイルをダウンロードします (「[Uploading/downloading files over FTP and collecting diagnostics logs (FTP を介したファイルのアップロード/ダウンロードおよび診断ログの収集)](http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx)」を参照)。

2. それをアプリケーションのルートに追加します。

3. 次の情報を使用して書き換えルールを追加します。

	* **Node.js および Python Django**

Node.js および Python Django アプリケーション用に生成された web.config ファイルには、既に **&lt;rewrite>** セクションがあり、サイトが適切に機能するために必要な **&lt;rule>** エントリが含まれています。サイトに HTTPS の使用を強制するには、例の **&lt;rule>** を **&lt;rules>** セクションの最初のエントリとして追加します。これにより、その他のルールは未変更のままで、HTTPS が強制的に使用されます。

	* **Java**
	
Apache Tomcat を使用している Java アプリケーションの web.config ファイルには **&lt;rewrite>** セクションが含まれていないため、例の **&lt;rewrite>** セクションを **&lt;system.webServer>** セクションに追加する必要があります。

4. プロジェクト (更新された web.config を含む) を Azure に再度デプロイします。

HTTPS を強制的に使用する書き換えルールを伴う web.config をデプロイすると、すぐに有効となり、すべての要求が HTTPS にリダイレクトされます。

IIS URL 書き換えモジュールの詳細については、[URL 書き換え](http://www.iis.net/downloads/microsoft/url-rewrite)に関するドキュメントを参照してください。 

## その他のリソース ##
- [Microsoft Azure トラスト センター](/ja-jp/support/trust-center/security/)
- [Azure の Web サイトでのロックを解除する構成オプション](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [診断ログの有効化](/ja-jp/documentation/articles/web-sites-enable-diagnostic-log/)
- [Web サイトの構成](/ja-jp/documentation/articles/web-sites-configure/)
- [Azure 管理ポータル](https://manage.windowsazure.com)

[customdomain]: /ja-jp/develop/net/common-tasks/custom-dns-web-site/
[iiscsr]: http://technet.microsoft.com/ja-jp/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/ja-jp/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/ja-jp/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[website]: ./media/configure-ssl-web-site/sslwebsite.png
[scale]: ./media/configure-ssl-web-site/sslscale.png
[standard]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: https://www.windowsazure.com/ja-jp/pricing/details/
[configure]: ./media/configure-ssl-web-site/sslconfig.png
[uploadcert]: ./media/configure-ssl-web-site/ssluploadcert.png
[uploadcertdlg]: ./media/configure-ssl-web-site/ssluploaddlg.png
[sslbindings]: ./media/configure-ssl-web-site/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/configure-ssl-web-site/waws-certmgr.png
[certwiz1]: ./media/configure-ssl-web-site/waws-certwiz1.png
[certwiz2]: ./media/configure-ssl-web-site/waws-certwiz2.png
[certwiz3]: ./media/configure-ssl-web-site/waws-certwiz3.png
[certwiz4]: ./media/configure-ssl-web-site/waws-certwiz4.png
