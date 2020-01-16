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
ms.openlocfilehash: 3b43ca5b6bec64d9283a64c9bcc0a3b60c21bca4
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750425"
---
# <a name="azure-app-configuration-best-practices"></a>Azure App Configuration のベスト プラクティス

この記事では、Azure App Configuration を使用する際の一般的なパターンとベスト プラクティスについて説明します。

## <a name="key-groupings"></a>キーのグループ化

App Configuration には、キーを整理するために、次の 2 つの方法が用意されています。

* キー プレフィックス
* ラベル

どちらか一方または両方のオプションを使用して、キーをグループ化できます。

"*キー プレフィックス*" は、キーの先頭部分です。 キー名に同じプレフィックスを使用することによって、一連のキーを論理的にグループ化することができます。 プレフィックスに、URL パスのように区切り記号 (`/` など) で接続した複数の構成要素を含めることで、名前空間を作成できます。 さまざまなアプリケーション、コンポーネント サービス、環境に使用するキーを 1 つの App Configuration ストアに保管する際に、このような階層構造が役立ちます。

キーとは、対応する設定の値を取得するために、アプリケーション コードから参照するものであるということに注意してください。 キーは変更しないでください。変更した場合、その都度、コードを修正する必要があります。

"*ラベル*" はキーの属性です。 同じキーの別形を作成するために使用されます。 たとえばラベルは、1 つのキーの複数のバージョンに割り当てることができます。 バージョンとしては、版、環境、その他のコンテキスト情報が考えられます。 アプリケーションからは、別のラベルを指定して、まったく異なるキーと値のセットを要求することができます。 その結果、すべてのキー参照は、コード内では変更されないままになります。

## <a name="key-value-compositions"></a>キーと値の合成

App Configuration は、そこに格納されているすべてのキーを独立したエンティティとして扱います。 App Configuration では、キーの階層に基づいて、キー間の関係を推測したりキー値を継承したりすることはありません。 ただし、アプリケーション コード内の適切な構成スタックとラベルを併用して、キーの複数のまとまりを集約することができます。

1 つ例を見てみましょう。 開発環境ごとに値が異なる **Asset1** という設定があるとします。 空のラベルを 1 つと "Development" というラベルを 1 つ持つ "Asset1" というキーを作成します。 1 つ目のラベルには **Asset1** の既定の値を設定し、後のラベルには "Development" の特定の値を設定します。

コードでは、最初にラベルなしでキー値を取得し、次に "Development" ラベルを使用して同じキー値セットに対する 2 回目の取得を行います。 2 回目の値の取得を行うと、キーの以前の値は上書きされます。 .NET Core 構成システムを使用すると、構成データの複数のセットをそれぞれの上に "スタック" することができます。 キーが複数のセットに存在する場合は、そのキーを含む最後のセットが使用されます。 .NET Core など、最新のプログラミング フレームワークを使用している場合、ネイティブ構成プロバイダーを使用して App Configuration にアクセスすれば、このスタック機能を無料で利用できます。 次のコード スニペットは、.NET Core アプリケーションにおけるスタック機能の実装方法を示しています。

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>App Configuration の準備

アプリ構成ストアには、対応する接続文字列を Azure portal から入手してアクセスできます。 接続文字列は資格情報を含んでいるため、シークレットと見なされます。 これらのシークレットは Azure Key Vault に格納する必要があり、コードはそれらを取得するために Key Vault に対して認証を行う必要があります。

より良いオプションは、Azure Active Directory のマネージド ID 機能を使用することです。 マネージド ID を使用すると、App Configuration のエンドポイントの URL さえあれば、App Configuration ストアへのアクセスをブートストラップすることができます。 その URL は、アプリケーション コード (*appsettings.json* ファイルなど) に埋め込むことができます。 詳細については、「[Integrate with Azure managed identities (Azure マネージド ID と統合する)](howto-integrate-azure-managed-service-identity.md)」を参照してください。

## <a name="app-or-function-access-to-app-configuration"></a>アプリまたは関数から App Configuration へのアクセス

次のいずれかの方法を使用して、Web アプリまたは関数に App Configuration へのアクセスを提供できます。

* Azure portal を介して、App Service のアプリケーション設定に App Configuration ストアへの接続文字列を入力します。
* 接続文字列を Key Vault の App Configuration ストアに保存し、[App Service からそれを参照](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)します。
* Azure マネージド ID を使用して App Configuration ストアにアクセスします。 詳細については、「[Integrate with Azure managed identities (Azure マネージド ID と統合する)](howto-integrate-azure-managed-service-identity.md)」を参照してください。
* App Configuration から App Service に構成をプッシュします。 App Configuration には、データを直接 App Service に送信するエクスポート機能があります (Azure portal および Azure CLI)。 この方法であれば、アプリケーション コードに一切変更を加える必要がありません。

## <a name="next-steps"></a>次のステップ

* [キーと値](./concept-key-value.md)
