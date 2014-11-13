Azure .NET 用ストレージ クライアント ライブラリでは、ストレージ接続文字列を使用して、ストレージ サービスにアクセスするためのエンドポイントおよび資格情報を構成できます。ストレージ接続文字列は、アプリケーション内にハードコーディングするのではなく、構成ファイルの中に保持することをお勧めします。接続文字列の保存には、次の 2 つのオプションがあります。

-   アプリケーションが Azure クラウド サービスで実行されている場合は、Azure サービス構成システム (`*.csdef` および `*.cscfg` ファイル) を使用して、接続文字列を保存します。
-   アプリケーションが Azure の仮想マシン上で実行されている場合、または Azure の外部で実行される .NET アプリケーションを作成している場合は、.NET 構成システム (`web.config` または `app.config` ファイルなど) を使用して接続文字列を保存します。

後で、コードから接続文字列を取得する方法について説明します。

### Azure クラウド サービスからの接続文字列の構成

Azure クラウド サービスのサービス構成メカニズムは特有のものであり、これを使用すると、アプリケーションを再デプロイすることなく Azure の管理ポータルから構成設定を動的に変更できます。

Azure サービス構成で接続文字列を構成するには:

1.  Visual Studio のソリューション エクスプローラーで、Azure デプロイ プロジェクトの
    **[ロール]** フォルダー内の Web ロールまたは worker ロールを右クリックし、
    **[プロパティ]** をクリックします。
    ![Select the properties on a Cloud Service role in Visual Studio][Select the properties on a Cloud Service role in Visual Studio]

2.  **[設定]** タブをクリックし、**[設定の追加]** をクリックします。
    ![Add a Cloud Service setting in visual Studio][Add a Cloud Service setting in visual Studio]

    新しい **[Setting1]** エントリが設定グリッドに表示されます。

3.  新しい **[Setting1]** エントリの **[種類]** ボックスの一覧で、
    **[接続文字列]** をクリックします。
    ![Set connection string type][Set connection string type]

4.  **[Setting1]** エントリの右端にある **[...]** ボタンをクリックします。
    **[ストレージ アカウント接続文字列]** ダイアログ ボックスが開きます。

5.  ストレージ エミュレーター (ローカル コンピューターでシミュレートされた
    Windows Azure ストレージ) をターゲットとするか、クラウド内の
    ストレージ アカウントをターゲットとするかを選択します。このガイドに記載されているコードは、
    どちらのオプションにも対応しています。以前に Azure で作成したストレージ アカウントをターゲットとする場合は、
    このチュートリアルの前の手順からコピーした
    **プライマリ アクセス キー**の値を入力します。

    > [WACOM.NOTE] ストレージ エミュレーターをターゲットとすると、Windows Azure Storage に関連する利用料金の発生を回避できます。ただし、クラウド内の Azure ストレージ アカウントをターゲットとしても、このチュートリアルを実行するための利用料金はごくわずかです。

    ![Select target environment][Select target environment]

6.  エントリの **[名前]** を **Setting1** から
    **StorageConnectionString** などのわかりやすい名前に変更します。この接続文字列は、
    このガイドの後半のコードで参照します。
    ![Change connection string name][Change connection string name]

### .NET 構成を使用した接続文字列の構成

Azure のクラウド サービスではないアプリケーション (前のセクションを参照) を記述する場合は、.NET 構成システム (`web.config` または `app.config` など) を使用することをお勧めします。これには、Azure Websites や Azure Virtual Machines のほか、Azure の外部で実行されるアプリケーションも含まれます。次のように、`<appSettings>` 要素を使用して接続文字列を格納します。`account-name` をストレージ アカウントの名前に置き換え、`account-key` をアカウントのアクセス キーに置き換えます。

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

ストレージ接続文字列の詳細については、「[Azure 接続文字列の構成][Azure 接続文字列の構成]」を参照してください。

これで、このガイドのハウツー タスクを実行する準備が整いました。

  [Select the properties on a Cloud Service role in Visual Studio]: ./media/storage-configure-connection-string/connection-string1.png
  [Add a Cloud Service setting in visual Studio]: ./media/storage-configure-connection-string/connection-string2.png
  [Set connection string type]: ./media/storage-configure-connection-string/connection-string3.png
  [Select target environment]: ./media/storage-configure-connection-string/connection-string4.png
  [Change connection string name]: ./media/storage-configure-connection-string/connection-string5.png
  [Azure 接続文字列の構成]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee758697.aspx
