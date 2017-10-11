[.NET 用 Microsoft Azure 構成マネージャー ライブラリ](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)構成ファイルから接続文字列を解析するためのクラスを提供します。 [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx)クラスは、クライアント アプリケーションがデスクトップで、モバイル デバイス、または Azure クラウド サービスで Azure の仮想マシンで実行されているかどうかに関係なく構成設定を解析します。

CloudConfigurationManager パッケージを参照するには、次のコードを追加`using`ディレクティブ。

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
```

構成ファイルから接続文字列を取得する方法を示す例を次に示します。

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Azure 構成マネージャーを使用することはオプションです。 .NET Framework のような API を使用することも[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx)クラスです。

