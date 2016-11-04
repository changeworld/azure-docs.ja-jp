## ストレージ接続文字列の設定
Azure .NET 用ストレージ クライアント ライブラリでは、ストレージ接続文字列を使用して、ストレージ サービスにアクセスするためのエンドポイントおよび資格情報を構成できます。ストレージ接続文字列を管理するには、構成ファイルの中に保持することをお勧めします。

接続文字列の詳細については、「[Azure Storage の接続文字列を構成する](../articles/storage/storage-configure-connection-string.md)」を参照してください。

> [!NOTE]
> ストレージ アカウント キーは、ストレージ アカウントの root パスワードに似ています。ストレージ アカウント キーは常に慎重に保護してください。このキーを他のユーザーに配布したり、ハードコーディングしたり、他のユーザーがアクセスできるプレーン テキスト ファイルに保存したりしないでください。キーが侵害されたと思われる場合は、Azure ポータルを使用してキーを再生成してください。
> 
> 

### ターゲット環境の決定
このガイドの例を実行するための環境オプションとして次の 2 つがあります。

* Azure ストレージ エミュレーターに対してコードを実行できます。ストレージ エミュレーターは、クラウド内の Azure ストレージ アカウントをエミュレートするローカル環境です。エミュレーターを使用すると、アプリケーションの開発中にコードのテストとデバッグを無料で実行できます。エミュレーターでは既知のアカウントとキーを使用します。詳細については、「[開発とテストのための Azure のストレージ エミュレーター使用](../articles/storage/storage-use-emulator.md)」を参照してください。
* クラウド内の Azure ストレージ アカウントに対してコードを実行できます。 

クラウドのストレージ アカウントをターゲットにする場合は、Azure ポータルからストレージ アカウントのプライマリ アクセス キーをコピーします。詳細については、「[ストレージ アクセス キーの表示とコピー](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys)」を参照してください。

> [!NOTE]
> ストレージ エミュレーターをターゲットにすると、Azure Storage に関連する利用料金の発生を回避できます。ただし、クラウド内の Azure ストレージ アカウントをターゲットとしても、このチュートリアルを実行するための利用料金はごくわずかです。
> 
> 

### .NET 構成を使用した接続文字列の構成
デスクトップ デバイス、モバイル デバイス、Azure 仮想マシン、Azure Web アプリのいずれかでアプリケーションが実行される場合、.NET 構成を使って接続文字列を保存します (*たとえば*、アプリケーションの `web.config` ファイルまたは `app.config` ファイル内)。次のように `<appSettings>` 要素を使用して接続文字列を保存します。`account-name` をストレージ アカウントの名前に置き換え、`account-key` をアカウントのアクセス キーに置き換えます。

    <configuration>
          <appSettings>
            <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
          </appSettings>
    </configuration>

たとえば、構成設定は次のようになります。

    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=account-key" />

ストレージ エミュレーターをターゲットとする場合、既知のアカウントの名前とキーにマップされるショートカットを使用できます。この場合、接続文字列の設定は次のようになります。

    <add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />

### Azure クラウド サービスの接続文字列の構成
アプリケーションが Azure クラウド サービスで実行されている場合は、Azure サービス構成ファイル (`*.csdef` ファイルと `*.cscfg` ファイル) を使用して、接続文字列を保存します。Azure クラウド サービスの構成の詳細については、「[クラウド サービスを作成してデプロイする方法](../articles/cloud-services/cloud-services-how-to-create-deploy.md)」をご覧ください。

<!---HONumber=AcomDC_0406_2016-->