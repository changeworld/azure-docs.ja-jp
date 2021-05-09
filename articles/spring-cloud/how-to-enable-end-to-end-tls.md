---
title: エンドツーエンドのトランスポート層セキュリティを有効にする
titleSuffix: Azure Spring Cloud
description: アプリケーションのエンドツーエンドのトランスポート層セキュリティを有効にする方法を説明します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/24/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 647cf6f0b1af6a5858bbf1147cc03ecc4637ed25
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227808"
---
# <a name="enable-end-to-end-tls-for-an-application"></a>アプリケーションのエンド ツー エンド TLS を有効にする

このトピックでは、エンドツーエンドの SSL/TLS を有効にして、受信コントローラーから HTTPS をサポートするアプリケーションへのトラフィックをセキュリティで保護する方法について説明します。 エンドツーエンド TLS を有効にし、keyvault から証明書を読み込むと、Azure Spring Cloud 内のすべての通信は TLS で保護されます。

   ![TLS で保護された通信のグラフ。](media/enable-end-to-end-tls/secured-tls.png)

## <a name="prerequisites"></a>前提条件 

- デプロイされた Azure Spring Cloud インスタンス。 [Azure CLI を使用したデプロイに関するクイックスタート](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)に従って作業を開始してください。
- エンドツーエンド TLS の詳細については、[エンドツーエンド TLS のサンプル](https://github.com/Azure-Samples/spring-boot-secure-communications-using-end-to-end-tls-ssl)を参照してください。
- 必要な証明書を Spring Boot アプリに安全に読み込むには、[keyvault spring boot starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-certificates) を使用します。


## <a name="enable-end-to-end-tls-on-an-existing-app"></a>既存のアプリでエンドツーエンド TLS を有効にする

アプリケーションのエンドツーエンド TLS を有効または無効にするには、`az spring-cloud app update --enable-end-to-end-tls` コマンドを使用します。

```azurecli
az spring-cloud app update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app update --enable-end-to-end-tls false -n app_name -s service_name -g resource_group_name
```

## <a name="enable-end-to-end-tls-when-you-bind-custom-domain"></a>カスタム ドメインをバインドする際にエンドツーエンド TLS を有効にする

アプリのエンドツーエンド TLS を有効または無効にするには、`az spring-cloud app custom-domain update --enable-end-to-end-tls` コマンドまたは `az spring-cloud app custom-domain bind --enable-end-to-end-tls` コマンドを使用します。

```azurecli
az spring-cloud app custom-domain update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app custom-domain bind --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
```

## <a name="verify-end-to-end-tls-status"></a>エンドツーエンドの TLS ステータスを確認する

`az spring-cloud app show` コマンドを使用して、`enableEndToEndTls` の値を確認します。
```
az spring-cloud app show -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>次のステップ
* [Config Server とサービス レジストリにアクセスする](how-to-access-data-plane-azure-ad-rbac.md)
