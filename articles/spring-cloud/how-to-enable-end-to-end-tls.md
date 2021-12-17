---
title: エンドツーエンドのトランスポート層セキュリティを有効にする
titleSuffix: Azure Spring Cloud
description: アプリケーションのエンドツーエンドのトランスポート層セキュリティを有効にする方法を説明します。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/24/2021
ms.custom: devx-track-java
ms.openlocfilehash: f4d21c2186706bcdaf92c7ef2b3d3b45c1ccc4f4
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015446"
---
# <a name="enable-end-to-end-tls-for-an-application"></a>アプリケーションのエンド ツー エンド TLS を有効にする

このトピックでは、エンドツーエンドの SSL/TLS を有効にして、受信コントローラーから HTTPS をサポートするアプリケーションへのトラフィックをセキュリティで保護する方法について説明します。 エンドツーエンド TLS を有効にし、keyvault から証明書を読み込むと、Azure Spring Cloud 内のすべての通信は TLS で保護されます。

![TLS で保護された通信のグラフ。](media/enable-end-to-end-tls/secured-tls.png)

## <a name="prerequisites"></a>前提条件

- デプロイされた Azure Spring Cloud インスタンス。 [Azure CLI を使用したデプロイに関するクイックスタート](./quickstart.md)に従って作業を開始してください。
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

## <a name="enable-end-to-end-tls-using-azure-portal"></a>Azure portal を使用してエンドツーエンド TLS を有効にする
[Azure portal](https://portal.azure.com/) でエンドツーエンド TLS を有効にするには、まずアプリを作成し、その後でこの機能を有効にします。

1. ポータルを使って通常の方法でアプリを作成します。 ポータルでアプリに移動します。
2. 左側のナビゲーション ペインで、 **[設定]** グループまで下へスクロールします。
3. **[End-to-end TLS]\(エンドツーエンド TLS\)** を選択します。
4. **[End-to-end TLS]\(エンドツーエンド TLS\)** を *[はい]* に切り替えます。

![ポータルでのエンドツーエンド TLS の有効化](./media/enable-end-to-end-tls/enable-tls.png)

## <a name="verify-end-to-end-tls-status"></a>エンドツーエンドの TLS ステータスを確認する

`az spring-cloud app show` コマンドを使用して、`enableEndToEndTls` の値を確認します。

```azurecli
az spring-cloud app show -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>次のステップ

* [Config Server とサービス レジストリにアクセスする](how-to-access-data-plane-azure-ad-rbac.md)
