---
title: Python と Azure Identity を使用して WebPubSubServiceClient を作成する方法
description: Python と Azure Identity を使用して WebPubSubServiceClient を作成する方法
author: terencefan
ms.author: tefa
ms.date: 11/15/2021
ms.service: azure-web-pubsub
ms.topic: how-to
ms.openlocfilehash: 17dba03200132131fc5e43cd32a3c39317c2321c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725192"
---
# <a name="how-to-create-a-webpubsubserviceclient-with-python-and-azure-identity"></a>Python と Azure Identity を使用して `WebPubSubServiceClient` を作成する方法

この攻略ガイドでは、Python で Azure Active Directory を使用して `WebPubSubServiceClient` を作成する方法について説明します。

## <a name="requirements"></a>要件

- pypi.org から [azure-identity](https://pypi.org/project/azure-identity/) パッケージをインストールします。

  ```bash
  python -m pip install azure-identity
  ```

- pypi.org から [azure-messaging-webpubsubservice](https://pypi.org/project/azure-messaging-webpubsubservice/) パッケージをインストールします。

  ```bash
  python -m pip install azure-messaging-webpubsubservice
  ```

## <a name="sample-codes"></a>サンプル コード

1. Azure Identity SDK を使用して `TokenCredential` を作成します。

    ```python
    from azure.identity import DefaultAzureCredential

    credential = DefaultAzureCredential()
    ```

    `credential` は、`TokenCredential` クラスを継承する任意のクラスにすることができます。

    - EnvironmentCredential
    - ClientSecretCredential
    - ClientCertificateCredential
    - ManagedIdentityCredential
    - VisualStudioCredential
    - VisualStudioCodeCredential
    - AzureCliCredential

    詳細については、[Python 用の Azure Identity クライアント ライブラリ](/python/api/overview/azure/identity-readme)に関するページを参照してください

2. 次に、`endpoint`、`hub`、`credential` を使用して `client` を作成します。 

    ```python
    from azure.identity import DefaultAzureCredential

    credential = DefaultAzureCredential()

    client = WebPubSubServiceClient(hub="<hub>", endpoint="<endpoint>", credential=credential)
    ```

    このクライアントの使用方法については、[Python 用 Azure Web PubSub サービス クライアント ライブラリ](/python/api/overview/azure/messaging-webpubsubservice-readme)に関する記事を参照してください

## <a name="complete-sample"></a>完全なサンプル

- [AAD 認証を使用した簡単なチャットルーム](https://github.com/Azure/azure-webpubsub/tree/main/samples/python/chatapp-aad)