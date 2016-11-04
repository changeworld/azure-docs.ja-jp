### 接続文字列の取得
**CloudStorageAccount** 型を使用して、ストレージ アカウント情報を表すことができます。Azure プロジェクト テンプレートを使用している場合や、Microsoft.WindowsAzure.CloudConfigurationManager 名前空間への参照がある場合は、**CloudConfigurationManager** 型を使用して Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

作成しているアプリケーションに Microsoft.WindowsAzure.CloudConfigurationManager への参照が含まれておらず、上記のように接続文字列が `web.config` または `app.config` に格納されている場合は、**ConfigurationManager** を使用して接続文字列を取得できます。System.Configuration.dll への参照をプロジェクトに追加し、対応する名前空間宣言を追加する必要があります。

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.AppSettings["StorageConnectionString"]);

<!---HONumber=AcomDC_0309_2016-->