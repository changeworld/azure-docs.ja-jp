---
title: Apache Kafka REST プロキシ - Azure HDInsight
description: Azure HDInsight で Kafka REST プロキシを使用して Apache Kafka 操作を実行する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d99a3b803b80dc41990a63e647d3ba928deb31af
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198907"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>REST プロキシを使用して Azure HDInsight で Apache Kafka クラスターを操作する

Kafka REST プロキシを使用すると、HTTP 経由の REST API を使用して Kafka クラスターを操作することができます。 これは、Kafka クライアントが仮想ネットワークの外部にある可能性があることを意味します。 さらに、クライアントは、Kafka ライブラリを利用するのではなく、単純な HTTP 呼び出しを使用して Kafka クラスターにメッセージを送受信することができます。 このチュートリアルでは、REST プロキシ対応の Kafka クラスターを作成する方法について説明し、REST プロキシを呼び出す方法を示すサンプル コードを紹介します。

## <a name="rest-api-reference"></a>REST API リファレンス

Kafka REST API によってサポートされる操作の詳細については、[HDInsight Kafka REST Proxy の API リファレンス](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)を参照してください。

## <a name="background"></a>バックグラウンド

![Kafka REST プロキシのアーキテクチャ](./media/rest-proxy/rest-proxy-architecture.png)

API でサポートされている操作の詳細については、[Apache Kafka REST プロキシ API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy) を参照してください。

### <a name="rest-proxy-endpoint"></a>REST プロキシ エンドポイント

REST プロキシを使用して HDInsight Kafka クラスターを作成すると、クラスターの新しいパブリック エンドポイントが作成されます。これは、Azure portal の HDInsight クラスターの [プロパティ] にあります。

### <a name="security"></a>Security

Kafka REST プロキシへのアクセスは Azure Active Directory セキュリティ グループで管理されます。 REST プロキシが有効な Kafka クラスターを作成する場合は、REST エンドポイントにアクセスする必要がある Azure Active Directory セキュリティ グループを指定します。 REST プロキシにアクセスする必要がある Kafka クライアント (アプリケーション) は、グループの所有者によってこのグループに登録される必要があります。 グループの所有者は、ポータルまたは Powershell を使用してこれを行うことができます。

REST プロキシ エンドポイントへの要求を行う前に、クライアント アプリケーションは、適切なセキュリティ グループのメンバーシップを確認するために OAuth トークンを取得する必要があります。 OAuth トークンを取得する方法については、後述する「[クライアント アプリケーションのサンプル](#client-application-sample)」を参照してください。 クライアント アプリケーションが OAuth トークンを受け取ると、REST プロキシに対する HTTP 要求でそのトークンを渡す必要があります。

> [!NOTE]  
> AAD セキュリティ グループの詳細については、「[Azure Active Directory グループを使用したアプリとリソース アクセスの管理](../../active-directory/fundamentals/active-directory-manage-groups.md)」を参照してください。 OAuth トークンのしくみについては、「[OAuth 2.0 コード付与フローを使用して Azure Active Directory Web アプリケーションへアクセスを承認する](../../active-directory/develop/v1-protocols-oauth-code.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

1. アプリケーションを Azure AD に登録する。 Kafka REST プロキシを操作するために記述するクライアント アプリケーションは、このアプリケーションの ID とシークレットを使用して Azure に対する認証を行います。
1. Azure AD セキュリティ グループを作成し、Azure AD に登録したアプリケーションをセキュリティ グループに追加します。 このセキュリティ グループは、REST プロキシの操作を許可するアプリケーションを制御するために使用されます。 Azure AD グループの作成方法の詳細については、「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>REST プロキシが有効な Kafka クラスターを作成する

1. Kafka クラスター作成ワークフローの実行中に、[セキュリティとネットワーク] タブで、[Enable Kafka REST proxy]\(Kafka REST プロキシを有効にする\) オプションをオンにします。

     ![Kafka REST プロキシを有効にし、セキュリティ グループを選択する](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. **[セキュリティ グループの選択]** をクリックします。 セキュリティ グループの一覧から、REST プロキシにアクセスするセキュリティ グループを選択します。 検索ボックスを使用して、適切なセキュリティ グループを見つけることができます。 下部にある **[選択]** ボタンをクリックします。

     ![Kafka REST プロキシを有効にし、セキュリティ グループを選択する](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. 「[Azure portal を使用して Azure HDInsight 内に Apache Kafka クラスターを作成する](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)」の説明に従って、残りのステップを完了してクラスターを作成します。

1. クラスターが作成されたら、クラスターのプロパティにアクセスして Kafka REST プロキシ URL を記録します。

     ![REST プロキシ URL の表示](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>クライアント アプリケーションのサンプル

次の python コードを使用して、Kafka クラスターで REST プロキシを操作できます。 コード サンプルを使用するには、次のステップを実行します。

1. Python がインストールされているマシンにサンプルコードを保存します。
1. `pip3 install adal` と `pip install msrestazure` を実行して、必要な python 依存関係をインストールします。
1. 実際の環境に合わせて *Configure these properties* のコード セクションを変更し、次のプロパティを変更します。
    1.  *テナント ID* – 該当するサブスクリプションがある Azure テナント。
    1.  *クライアント ID* – セキュリティ グループに登録したアプリケーションの ID。
    1.  *クライアント シークレット* – セキュリティ グループに登録したアプリケーションのシークレット
    1.  *Kafkarest_endpoint* – この値は、[デプロイ セクション](#create-a-kafka-cluster-with-rest-proxy-enabled)の説明に従って、クラスターの概要の [プロパティ] タブから取得します。 `https://<clustername>-kafkarest.azurehdinsight.net` という形式にする必要があります。
3. コマンド ラインから、`python <filename.py>` を実行して python ファイルを実行します

このコードは、次の処理を実行します。

1. Azure AD から OAuth トークンを取得する
1. Kafka REST プロキシに対して要求を行う方法を示します。

Python での OAuth トークンの取得の詳細については、[Python の AuthenticationContext クラス](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)に関する記事を参照してください。 Kafka REST プロキシで作成または削除されていないトピックがそこに反映される間、遅延が発生する可能性があります。 この遅延は、キャッシュの更新によるものです。

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

## <a name="next-steps"></a>次のステップ

* [Kafka REST プロキシ API リファレンス ドキュメント](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
