---
title: Azure Spring Cloud のアプリケーションで TLS/SSL 証明書を使用する
titleSuffix: Azure Spring Cloud
description: アプリケーションで TLS/SSL 証明書を使用します。
author: karlerickson
ms.author: jieshe
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/08/2021
ms.custom: devx-track-java
ms.openlocfilehash: 9a8671e6a52210bc7f49b6654383e12fd3090b63
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258109"
---
# <a name="use-tlsssl-certificates-in-your-application-in-azure-spring-cloud"></a>Azure Spring Cloud のアプリケーションで TLS/SSL 証明書を使用する

この記事では、Azure Spring Cloud で公開証明書をアプリケーションに使用する方法について説明します。 アプリはクライアントとして、証明書認証を必要とする外部サービスにアクセスすることがあるほか、暗号タスクを実行しなければならない場合もあります。  

Azure Spring Cloud の TLS/SSL 証明書の管理機能を使用すれば、証明書とアプリケーション コードを分離して管理し、機密データを保護できます。 アプリ コードは、Azure Spring Cloud インスタンスに追加した公開証明書にアクセスできます。

> [!NOTE]
> Azure CLI および Terraform のサポートとサンプルは、この記事に近日公開される予定です。

## <a name="prerequisites"></a>前提条件

- Azure Spring Cloud にデプロイされたアプリケーション。 詳しくは「[クイック スタート:初めての Azure Spring Cloud アプリケーションをデプロイする](./quickstart.md)」を確認するか、既存のアプリを使用します。
- 拡張子が *.crt*、*.cer*、*.pem*、または *.der* の証明書ファイル、またはプライベート証明書を持つ Azure Key Vault のデプロイ済みインスタンスのいずれか。

## <a name="import-a-certificate"></a>証明書のインポート

Azure Spring Cloud インスタンスに証明書をインポートするには、Key Vault から行うか、ローカルの証明書ファイルを使用するかを選ぶことができます。

### <a name="import-a-certificate-from-key-vault"></a>Key Vault から証明書をインポートする

証明書をインポートする前に、以下の手順に従って Azure Spring Cloud にキー コンテナーへのアクセス権を付与する必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[キー コンテナー]** を選択してから、証明書のインポート元となるキー コンテナーを選択します。
1. 左側のナビゲーション ウィンドウで **[アクセス ポリシー]** を選択し、**[アクセス ポリシーの追加]** を選択します。
1. **[証明書のアクセス許可]** を選択し、次に **[取得]** と **[一覧表示]** を選択します。
1. **[プリンシパルの選択]** で、**[選択されていません]** を選択してから、セキュリティ プリンシパルを選択します。
1. **[選択]** を選択してから、**[追加]** を選択します。

:::image type="content" source="media/use-tls-certificates/grant-key-vault-permission.png" alt-text="証明書をインポートする前にキー コンテナーへのアクセスを許可する場所を示すスクリーンショット" lightbox="media/use-tls-certificates/grant-key-vault-permission.png":::

キー コンテナーへのアクセス権を付与したら、次の手順を使用して証明書をインポートできます。

1. サービス インスタンスに移動します。
1. インスタンスの左側のナビゲーション ウィンドウで、**[TLS/SSL の設定]** を選択します。
1. **[公開キー証明書]** で **[Key Vault 証明書のインポート]** を選択します。
1. **[キー コンテナー]** でキー コンテナーを、**[証明書]** で証明書を選択したら、**[選択]**、**[適用]** の順に選択します。
1. 証明書が正常にインポートされると、公開キー証明書の一覧に表示されます。

### <a name="import-a-local-certificate-file"></a>ローカルの証明書ファイルをインポートする

次の手順を使用して、ローカルに格納されている証明書ファイルをインポートできます。

1. サービス インスタンスに移動します。
1. インスタンスの左側のナビゲーション ウィンドウで、**[TLS/SSL の設定]** を選択します。
1. **[公開キー証明書]** セクションで **[公開証明書のアップロード]** を選択します。
1. 証明書が正常にインポートされると、公開キー証明書の一覧に表示されます。

## <a name="load-a-certificate"></a>証明書を読み込む

証明書を Azure Spring Cloud のアプリケーションに読み込むには、以下の手順から始めます。

1. アプリケーション インスタンスに移動します。
1. アプリの左側のナビゲーション ウィンドウで、**[証明書管理]** を選択します。
1. **[証明書の追加]** を選択して、アプリでアクセス可能な証明書を選択します。

:::image type="content" source="media/use-tls-certificates/load-certificate.png" alt-text="アプリケーションに証明書を読み込む場所を示すスクリーンショット" lightbox="media/use-tls-certificates/load-certificate.png":::

### <a name="load-a-certificate-from-code"></a>コードから証明書を読み込む

読み込まれた証明書は、*/etc/azure-spring-cloud/certs/public* にあります。 次の Java コードを使用して、Azure Spring Cloud のアプリケーションに公開証明書を読み込みます。

```java
CertificateFactory factory = CertificateFactory.getInstance("X509");
FileInputStream is = new FileInputStream("/etc/azure-spring-cloud/certs/public/<certificate name>");
X509Certificate cert = (X509Certificate) factory.generateCertificate(is);

// use the loaded certificate
```

### <a name="load-a-certificate-into-the-trust-store"></a>証明書を信頼ストアに読み込む

Java アプリケーションの場合は、選択した証明書の **[Load into trust store]\(信頼ストアに読み込む\)** を選択することができます。 証明書は、SSL 認証でサーバーを認証するために、Java の既定の TrustStores に自動的に追加されます。

以下のアプリのログは、証明書が正常に読み込まれたことを示しています。

```
Load certificate from specific path. alias = <certificate alias>, thumbprint = <certificate thumbprint>, file = <certificate name>
```

## <a name="next-steps"></a>次のステップ

* [エンドツーエンドのトランスポート層セキュリティを有効にする](./how-to-enable-end-to-end-tls.md)
* [Config Server とサービス レジストリにアクセスする](./how-to-access-data-plane-azure-ad-rbac.md)
