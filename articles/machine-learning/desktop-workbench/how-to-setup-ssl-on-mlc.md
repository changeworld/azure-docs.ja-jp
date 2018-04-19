---
title: Azure Machine Learning コンピューティング (MLC) クラスターでの SSL の有効化 | Microsoft Docs
description: Azure Machine Learning コンピューティング (MLC) クラスターでスコアリング呼び出しの SSL を設定する手順を説明します。
services: machine-learning
author: SerinaKaye
ms.author: serinak
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 444803eeb77e79a8bfe3271ddf27bd428042c875
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="enable-ssl-on-an-azure-machine-learning-compute-mlc-cluster"></a>Azure Machine Learning コンピューティング (MLC) クラスターでの SSL の有効化 

以下の手順に従って、Azure Machine Learning コンピューティング (MLC) クラスターでスコアリング呼び出しの SSL を設定できます。 

## <a name="prerequisites"></a>前提条件 

1. リアルタイムのスコアリング呼び出しを行うときに使用する CNAME (DNS 名) を決定します。

2. その CNAME を使用して "*パスワードのない*" 証明書を作成します。

3. 証明書がまだ PEM 形式でない場合は、*openssl* などのツールを使用して PEM に変換します。

前提条件となる作業が完了すると、次の 2 つのファイルが作成されます。

* 証明書のファイル (例: `cert.pem`)。 ファイルが完全な証明書チェーンを含んでいることを確認します。
* キーのファイル (例: `key.pem`)



## <a name="set-up-an-ssl-certificate-on-a-new-acs-cluster"></a>新しい ACS クラスターで SSL 証明書を設定する

Azure Machine Learning CLI で、`-c` スイッチを指定した次のコマンドを実行して、SSL 証明書がアタッチされた ACS クラスターを作成します。

```
az ml env create -c -g <resource group name> -n <cluster name> --cert-cname <CNAME> --cert-pem <path to cert.pem file> --key-pem <path to key.pem file>
```


## <a name="set-up-an-ssl-certificate-on-an-existing-acs-cluster"></a>既存の ACS クラスターで SSL 証明書を設定する

SSL を使用せずに作成されたクラスターを対象とする場合は、Azure PowerShell コマンドレットを使用して証明書を追加できます。 

```
Set-AzureRmMlOpCluster -ResourceGroupName my-rg -Name my-cluster -SslStatus Enabled -SslCertificate $certValueInPemFormat -SslKey $keyValueInPemFormat -SslCName foo.mycompany.com
```

## <a name="map-the-cname-and-the-ip-address"></a>CNAME と IP アドレスをマップする

前提条件で選択した CNAME とリアルタイム フロントエンド (FE) の IP アドレス間のマッピングを作成します。 FE の IP アドレスを検出するには、次のコマンドを実行します。 リアルタイム クラスター フロントエンドの IP アドレスを含む "publicIpAddress" という名前のフィールドが出力に表示されます。 DNS プロバイダーの手順を参照し、CNAME で使用される FQDN からパブリック IP アドレスへのレコードを設定します。



## <a name="auto-detection-of-a-certificate"></a>証明書の自動検出 

"`az ml service create realtime`" コマンドを使用してリアルタイム Web サービスを作成すると、Azure Machine Learning はクラスターで設定された SSL を自動検出し、指定された証明書を使用してスコアリング URL を自動的に設定します。 

証明書の状態を確認するには、次のコマンドを実行します。 スコアリング URI の "https" プレフィックスとホスト名の CNAME に注意してください。 

``` 
az ml service show realtime -n <service name>
{
                "id" : "<your service id>",
                "name" : "<your service name >",
                "state" : "Succeeded",
                "createdAt" : "<datetime>”,
                "updatedAt" : "< datetime>",
                "scoringUri" : "https://<your CNAME>/api/v1/service/<service name>/score"
}
```
