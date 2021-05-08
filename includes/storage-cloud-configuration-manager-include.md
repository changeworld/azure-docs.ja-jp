---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: cedfd719a5f0aeed6fc2e932d3aa5189b83c9796
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026645"
---
[.NET 用 Microsoft Azure Configuration Manager ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) には、構成ファイルの接続文字列を解析するためのクラスが用意されています。 [CloudConfigurationManager](/previous-versions/azure/reference/mt634650(v=azure.100)) クラスで構成の設定を解析します。 デスクトップ、モバイル デバイス、Azure 仮想マシン、または Azure クラウド サービスで実行されるクライアント アプリケーションの設定を解析します。

`CloudConfigurationManager` パッケージを参照するには、次の `using` ディレクティブを追加します。

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

Azure Configuration Manager の使用はオプションです。 また、.NET Framework の [ConfigurationManager クラス](/dotnet/api/system.configuration.configurationmanager)などの API を使用することもできます。