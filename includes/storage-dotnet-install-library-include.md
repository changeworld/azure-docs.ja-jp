### コンソール アプリケーションの作成とアセンブリの取得

Visual Studio で新しいコンソール アプリケーションを作成して Azure Storage Client Library を含む NuGet パッケージをインストールするには、次の手順を実行します。

1. Visual Studio で、**[ファイル]、[新しいプロジェクト]** の順にクリックした後、**[Windows] をクリックし、Visual C# テンプレートの一覧から [コンソール アプリケーション]**をクリックします。
2. コンソール アプリケーションの名前を入力して、**[OK]** をクリックします。
3. プロジェクトが作成されたら、ソリューション エクスプローラーでプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。"WindowsAzure.Storage" をオンラインで検索し、**[インストール]** をクリックして Azure Storage Client Library for .NET のパッケージと依存関係をインストールします。

また、この記事のコード例では、[Microsoft Azure Configuration Manager Library](https://msdn.microsoft.com/library/azure/mt634646.aspx) も使用して、コンソール アプリケーションの app.config ファイルからストレージ接続文字列を取得します。Azure Configuration Manager を使用すると、アプリケーションが Microsoft Azure で実行されているか、デスクトップ、モバイル、Web アプリケーションから実行されているかに関係なく、実行時に接続文字列を取得できます。

Azure Configuration Manager パッケージをインストールするには、ソリューション エクスプローラーでプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。"ConfigurationManager" をオンラインで検索し、**[インストール]** をクリックしてパッケージをインストールします。

Azure Configuration Manager の使用はオプションです。また、.NET Framework の [ConfigurationManager クラス](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx)などの API を使用することもできます。

<!---HONumber=AcomDC_0309_2016-->