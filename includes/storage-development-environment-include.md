## 開発環境を設定する

次に、このガイドで説明されているコード例を試すことができるように、Visual Studio で開発環境を設定します。

### Windows コンソール アプリケーション プロジェクトの作成

次のように、Visual Studio で新しい Windows コンソール アプリケーションを作成します。

![Create Windows console application](./media/storage-development-environment-include/storage-development-environment-include-1.png)

Azure クラウド サービス、Azure Web アプリ、デスクトップ アプリケーション、モバイル アプリケーションなど、任意の種類の .NET アプリケーションから Azure ストレージ クライアント ライブラリを使用できます。このガイドでは、わかりやすくするためにコンソール アプリケーションを使用します。

### NuGet を使用した必要なパッケージのインストール

このチュートリアルを完了するには、次の 2 つのパッケージをインストールする必要があります。

- [.NET 用 Microsoft Azure ストレージ クライアント ライブラリ](https://www.nuget.org/packages/WindowsAzure.Storage/): このパッケージを使用すると、ストレージ アカウント内のデータ リソースにプログラムでアクセスできます。
- [.NET 用 Microsoft Azure Configuration Manager ライブラリ](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): このパッケージには、アプリケーションの実行場所に関係なく構成ファイルの接続文字列を解析するためのクラスが用意されています。

NuGet を使って両方のパッケージを取得できます。次の手順に従います。

1. **ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
2. "WindowsAzure.Storage" をオンラインで検索し、**[インストール]** をクリックして ストレージ クライアント ライブラリとその依存関係をインストールします。
3. "ConfigurationManager" をオンラインで検索し、**[インストール]** をクリックして Azure Configuration Manager をインストールします。

>[AZURE.NOTE] ストレージ クライアント ライブラリ パッケージは、[Azure SDK for .NET](https://azure.microsoft.com/downloads/) にも含まれていますが、常に最新バージョンのクライアント ライブラリを使用できるように、NuGet からストレージ クライアント ライブラリもインストールすることをお勧めします。

### ターゲット環境の決定

このガイドの例を実行するための環境オプションとして次の 2 つがあります。

- クラウド内の Azure ストレージ アカウントに対してコードを実行できます。 
- Azure ストレージ エミュレーターに対してコードを実行できます。ストレージ エミュレーターは、クラウド内の Azure ストレージ アカウントをエミュレートするローカル環境です。エミュレーターを使用すると、アプリケーションの開発中にコードのテストとデバッグを無料で実行できます。エミュレーターでは既知のアカウントとキーを使用します。詳細については、「[開発とテストのための Azure のストレージ エミュレーター使用](../articles/storage/storage-use-emulator.md)」を参照してください。

クラウドのストレージ アカウントをターゲットにする場合は、Azure ポータルからストレージ アカウントのプライマリ アクセス キーをコピーします。詳細については、「[ストレージ アクセス キーの表示とコピー](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys)」を参照してください。

> [AZURE.NOTE] ストレージ エミュレーターをターゲットにすると、Azure Storage に関連する利用料金の発生を回避できます。ただし、クラウド内の Azure ストレージ アカウントをターゲットとしても、このチュートリアルを実行するための利用料金はごくわずかです。

### ストレージ接続文字列の構成

Azure .NET 用ストレージ クライアント ライブラリでは、ストレージ接続文字列を使用して、ストレージ サービスにアクセスするためのエンドポイントおよび資格情報を構成できます。ストレージ接続文字列を管理するには、構成ファイルの中に保持することをお勧めします。

接続文字列の詳細については、「[Azure Storage の接続文字列を構成する](../articles/storage/storage-configure-connection-string.md)」を参照してください。

> [AZURE.NOTE] ストレージ アカウント キーは、ストレージ アカウントの root パスワードに似ています。ストレージ アカウント キーは常に慎重に保護してください。このキーを他のユーザーに配布したり、ハードコーディングしたり、他のユーザーがアクセスできるプレーン テキスト ファイルに保存したりしないでください。キーが侵害されたと思われる場合は、Azure ポータルを使用してキーを再生成してください。

接続文字列を構成するには、Visual Studio のソリューション エクスプローラーから `app.config` ファイルを開きます。次に示す `<appSettings>` 要素の内容を追加します。`account-name` をストレージ アカウントの名前に置き換え、`account-key` をアカウントのアクセス キーに置き換えます。

	<configuration>
	    <startup> 
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
	    </startup>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
  		</appSettings>
	</configuration>

たとえば、構成設定は次のようになります。

	<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln9fT7bvY+rxu2iWAEyzPNITGkhM88J8HUoyofpK7C8fHcZc2kIZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />

ストレージ エミュレーターをターゲットとする場合、既知のアカウントの名前とキーにマップされるショートカットを使用できます。この場合、接続文字列の設定は次のようになります。

	<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />

<!---HONumber=AcomDC_0406_2016-->