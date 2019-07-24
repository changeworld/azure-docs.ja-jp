---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 11626dd95064cf972a845e5c37f930b9e49c57e6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077052"
---
[.NET 用 Microsoft Azure Configuration Manager ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) には、構成ファイルの接続文字列を解析するためのクラスが用意されています。 [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) クラスを使用すると、クライアント アプリケーションがデスクトップ、モバイル デバイス、Azure 仮想マシン、Azure クラウド サービスのいずれで実行されているかにかかわらず、構成設定を解析できます。

CloudConfigurationManager パッケージを参照するには、次の `using` ディレクティブをクラスに追加します。

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

構成ファイルから接続文字列を取得する方法の例を次に示します。

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Azure Configuration Manager の使用はオプションです。 また、.NET Framework の [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) クラスなどの API を使用することもできます。
