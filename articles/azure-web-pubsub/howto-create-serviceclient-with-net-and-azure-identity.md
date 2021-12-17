---
title: .NET と Azure Identity を使用して WebPubSubServiceClient を作成する方法
description: .NET と Azure Identity を使用して WebPubSubServiceClient を作成する方法
author: terencefan
ms.author: tefa
ms.date: 11/15/2021
ms.service: azure-web-pubsub
ms.topic: how-to
ms.openlocfilehash: 95143bce385245eb1130d2f9e7317b46f7e31e62
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725206"
---
# <a name="how-to-create-a-webpubsubserviceclient-with-net-and-azure-identity"></a>.NET と Azure Identity を使用して `WebPubSubServiceClient` を作成する方法

この攻略ガイドでは、.NET で Azure Active Directory を使用して `WebPubSubServiceClient` を作成する方法について説明します。

## <a name="requirements"></a>要件

- nuget.org から [Azure.Identity](https://www.nuget.org/packages/Azure.Identity) をインストールします。

  ```bash
  Install-Package Azure.Identity
  ```

- nuget.org から [Azure.Messaging.WebPubSub](https://www.nuget.org/packages/Azure.Messaging.WebPubSub) をインストールします

  ```bash
  Install-Package Azure.Messaging.WebPubSub 
  ```

## <a name="sample-codes"></a>サンプル コード

1. Azure Identity SDK を使用して `TokenCredential` を作成します。

    ```C#
    using Azure.Identity

    namespace chatapp 
    {
        public class Program
        {
            public static void Main(string[] args)
            {
                var credential = new DefaultAzureCredential();
            }
        }
    }
    ```

    `credential` は、`TokenCredential` クラスを継承する任意のクラスにすることができます。

    - EnvironmentCredential
    - ClientSecretCredential
    - ClientCertificateCredential
    - ManagedIdentityCredential
    - VisualStudioCredential
    - VisualStudioCodeCredential
    - AzureCliCredential

    詳細については、[.NET 用の Azure Identity クライアント ライブラリ](/dotnet/api/overview/azure/identity-readme)に関するページを参照してください

2. 次に、`endpoint`、`hub`、`credential` を使用して `client` を作成します。 

    ```C#
    using Azure.Identity

    public class Program
    {
        public static void Main(string[] args)
        {
            var credential = new DefaultAzureCredential();
            var client = new WebPubSubServiceClient(new Uri("<endpoint>"), "<hub>", credential);
        }
    }
    ```

    または、`BuilderExtensions` を使用して `IServiceCollections` に挿入します。

    ```C#
    using System;

    using Azure.Identity;

    using Microsoft.Extensions.Azure;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;

    namespace chatapp
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }

            public IConfiguration Configuration { get; }

            public void ConfigureServices(IServiceCollection services)
            {
                services.AddAzureClients(builder =>
                {
                    var credential = new DefaultAzureCredential();
                    builder.AddWebPubSubServiceClient(new Uri("<endpoint>"), "<hub>", credential);
                });
            }
        }
    }
    ```

    このクライアントの使用方法については、[.NET 用 Azure Web PubSub サービス クライアント ライブラリ](/dotnet/api/overview/azure/messaging.webpubsub-readme-pre)に関する記事を参照してください

## <a name="complete-sample"></a>完全なサンプル

- [AAD 認証を使用した簡単なチャットルーム](https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp/chatapp-aad)