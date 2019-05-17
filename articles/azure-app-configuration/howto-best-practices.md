---
title: Azure App Configuration のベスト プラクティス | Microsoft Docs
description: Azure App Configuration の最適な使い方について説明します。
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d1275a48de5cad9321186ba20860d853b8ce55ad
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413618"
---
# <a name="azure-app-configuration-best-practices"></a>Azure App Configuration のベスト プラクティス

この記事では、Azure App Configuration を使用する際の一般的なパターンとプラクティスについて説明します。

## <a name="key-groupings"></a>キーのグループ化

App Configuration には、キーを整理する方法が 2 つ用意されています。キー プレフィックスとラベルです。 どちらか一方を使用することも、両方を使用することもできます。

キー プレフィックスは、キーの先頭部分です。 キー名に同じプレフィックスを使用することによって、一連のキーを論理的にグループ化することができます。 プレフィックスに、区切り記号 (`/` など) で接続した複数の構成要素を含めることで、URL パスのように名前空間を作成することができます。 さまざまなアプリケーション、コンポーネント サービス、環境に使用するキーを 1 つのアプリ構成ストアに保管する際に、このような階層構造が役立ちます。 キーとは、対応する設定の値を取得するために、アプリケーション コードから参照するものであるということに注意してください。 キーに変更を加えるべきではありません。変更を加えた場合、その都度、コードを修正する必要があります。

ラベルはキーの属性です。 同じキーの別形を作成するために使用されます。 たとえばラベルは、1 つのキーの複数のバージョンに割り当てることができます。 バージョンとしては、版や環境など、なんらかのコンテキスト情報が考えられます。 アプリケーションからは、別のラベルを指定するだけで、まったく異なるキーと値のセットを要求することができます。 すべてのキーの参照は、変更しないでおくことができます。

## <a name="key-value-compositions"></a>キーと値の合成

App Configuration は、そこに格納されているすべてのキーを独立したエンティティとして扱います。 キーの階層に基づいて、キー間の関係を推測したりキー値を継承したりすることはありません。 ただし、アプリケーション コード内の適切な構成スタックとラベルを併用して、キーの複数のまとまりを集約することができます。

1 つ例を見てみましょう。 開発環境ごとに値が異なる **Asset1** という設定があります。 空のラベルを 1 つと "Development" というラベルを 1 つ持つ "Asset1" というキーを作成できます。 前者の **Asset1** には既定値を、後者には "Development" に特定の値を格納します。 実際のコードでは、まずラベルなしのキー値を取得し、その後、"Development" ラベルを持つキー値を取得して、先行する同じキーの値を上書きします。 .NET Core など最新のプログラミング フレームワークを使用している場合、ネイティブ構成プロバイダーを使用して App Configuration にアクセスすれば、このスタック機能を無料で入手できます。 次のコード スニペットは、.NET Core アプリケーションにおけるスタック機能の実装方法を示しています。

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Use(KeyFilter.Any, LabelFilter.Null)
           .Use(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>App Configuration の準備

アプリ構成ストアには、対応する接続文字列を Azure portal から入手してアクセスできます。 接続文字列は資格情報を含んでいるため、シークレットと見なされます。 これらはキー コンテナーに格納する必要があります。 もっとよい方法は、Azure マネージド ID を使用することです。 この方法なら、App Configuration のエンドポイントの URL さえあれば、構成ストアへのアクセスを準備することができます。 その URL は、アプリケーション コード (*appsettings.json* ファイルなど) に埋め込むことができます。 詳細については、「[Azure マネージド ID と統合する](howto-integrate-azure-managed-service-identity.md)」を参照してください。

## <a name="web-app-or-function-access-to-app-configuration"></a>Web アプリまたは関数から App Configuration へのアクセス

アプリ構成ストアへの接続文字列は、Azure portal の App Service のアプリケーション設定に入力することができます。 または、Key Vault に格納して、[App Service から参照](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)することもできます。 Azure マネージド ID を使用して構成ストアにアクセスすることもできます。 詳細については、「[Azure マネージド ID と統合する](howto-integrate-azure-managed-service-identity.md)」を参照してください。

さらに別の方法として、App Configuration から App Service に構成をプッシュすることができます。 App Configuration には、データを直接 App Service に送信するエクスポート機能があります (Azure portal および CLI)。 この方法であれば、アプリケーション コードに一切変更を加える必要がありません。

## <a name="next-steps"></a>次の手順

* [キーと値](./concept-key-value.md)
