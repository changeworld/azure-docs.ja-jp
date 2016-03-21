## ストレージ接続文字列の設定

Azure .NET 用ストレージ クライアント ライブラリでは、ストレージ接続文字列を使用して、ストレージ サービスにアクセスするためのエンドポイントおよび資格情報を構成できます。ストレージ接続文字列は、アプリケーション内にハードコーディングするのではなく、構成ファイルの中に保持することをお勧めします。接続文字列の保存には、次の 2 つのオプションがあります。

- アプリケーションが Azure クラウド サービスで実行されている場合は、Azure サービス構成システム (`*.csdef` ファイルと `*.cscfg` ファイル) を使用して、接続文字列を保存します。Azure クラウド サービスの構成の詳細については、「[クラウド サービスを作成してデプロイする方法](../articles/cloud-services/cloud-services-how-to-create-deploy.md)」をご覧ください。
- アプリケーションが Azure 仮想マシンで実行されている場合、または Azure の外部で実行される .NET アプリケーションを作成している場合は、.NET 構成システム (`web.config` ファイルや `app.config` ファイルなど) を使用して接続文字列を保存します。

後で、コードから接続文字列を取得する方法について説明します。

### Azure クラウド サービスからの接続文字列の構成

Azure クラウド サービスのサービス構成メカニズムは特有のものであり、これを使用すると、アプリケーションを再デプロイすることなく Azure の管理ポータルから構成設定を動的に変更できます。

Azure サービス構成で接続文字列を構成するには:

1.  Visual Studio のソリューション エクスプローラーで、Azure デプロイメント プロジェクトの **[ロール]** フォルダー内の Web ロールまたは worker ロールを右クリックし、**[プロパティ]** をクリックします。![Visual Studio で、クラウド サービス ロールのプロパティを選択します。][connection-string1]

2.  **[設定]** タブをクリックし、**[設定の追加]** をクリックします。![Visual Studio でのクラウド サービス設定の追加][connection-string2]

    新しい **[Setting1]** エントリが設定グリッドに表示されます。

3.  新しい **[Setting1]** エントリの **[種類]** ボックスの一覧で、**[接続文字列]** をクリックします。![接続文字列の種類の設定][connection-string3]

4.  **[Setting1]** エントリの右端にある **[...]** をクリックします。**[ストレージ アカウント接続文字列]** ダイアログが開きます。

5.  ストレージ エミュレーター (ローカル コンピューターでシミュレートされた Microsoft Azure のストレージ) とクラウド内のストレージ アカウントのどちらをターゲットにするかを選択します。このガイドに記載されているコードは、どちらのオプションにも対応しています。

	> [AZURE.NOTE] ストレージ エミュレーターをターゲットにすると、Azure Storage に関連する利用料金の発生を回避できます。ただし、クラウド内の Azure ストレージ アカウントをターゲットとしても、このチュートリアルを実行するための利用料金はごくわずかです。

	クラウドのストレージ アカウントをターゲットにする場合は、そのストレージ アカウントのプライマリ アクセス キー入力します。Microsoft Azure 管理ポータルを使用してプライマリ アクセス キーをコピーする方法については、「[ストレージ アクセス キーを表示、コピーする](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys)」をご覧ください。

	> [AZURE.NOTE] ストレージ アカウント キーは、ストレージ アカウントの root パスワードに似ています。キーは必ず保護してください。キーを他のユーザーに配布したり、他のユーザーがアクセスできるプレーン テキスト ファイルに保存したりしないでください。キーが侵害されたと思われる場合は、管理ポータルを使用してキーを再生成します。
	
    ![ターゲット環境を選択][connection-string4]

6.  エントリの **[名前]** を **Setting1** から **StorageConnectionString** などのわかりやすい名前に変更します。この接続文字列は、このガイドの後半のコードで参照します。![接続文字列名の変更][connection-string5]
	
### .NET 構成を使用した接続文字列の構成

Azure クラウド サービスではないアプリケーションを作成する場合は (前のセクションを参照)、.NET 構成システム (`web.config` や `app.config` など) を使用することをお勧めします。これには、Azure Websites や Azure Virtual Machines のほか、Azure の外部で実行されるアプリケーションも含まれます。次のように、`<appSettings>` 要素を使用して接続文字列を格納します。`account-name` をストレージ アカウントの名前に置き換え、`account-key` をアカウントのアクセス キーに置き換えます。

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
  		</appSettings>
	</configuration>

たとえば、構成ファイルの構成設定は次のようになります。

	<configuration>
    	<appSettings>
      		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln9fT7bvY+rxu2iWAEyzPNITGkhM88J8HUoyofpK7C8fHcZc2kIZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />
    	</appSettings>
	</configuration>

これで、このガイドのハウツー タスクを実行する準備が整いました。

[connection-string1]: ./media/storage-configure-connection-string-include/connection-string1.png
[connection-string2]: ./media/storage-configure-connection-string-include/connection-string2.png
[connection-string3]: ./media/storage-configure-connection-string-include/connection-string3.png
[connection-string4]: ./media/storage-configure-connection-string-include/connection-string4.png
[connection-string5]: ./media/storage-configure-connection-string-include/connection-string5.png

[Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx

<!---HONumber=AcomDC_0218_2016-->