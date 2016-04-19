### 接続文字列を解析する

上述の Microsoft Azure Configuration Manager ライブラリには、構成ファイルの接続文字列を解析するためのクラスが用意されています。[CloudConfigurationManager クラス](https://msdn.microsoft.com/library/azure/mt634650.aspx)を使用すると、クライアント アプリケーションがデスクトップ、モバイル デバイス、Azure 仮想マシン、Azure クラウド サービスのいずれで実行されているかにかかわらず、構成設定を解析できます。

次のコードを `program.cs` の `Main()` メソッドに追加します。

    // Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		CloudConfigurationManager.GetSetting("StorageConnectionString"));

Azure Configuration Manager の使用はオプションです。また、.NET Framework の [ConfigurationManager クラス](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx)などの API を使用することもできます。

<!---HONumber=AcomDC_0413_2016-->