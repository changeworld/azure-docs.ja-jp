---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: 1a3de57446c5c92afbc5dd5901e284bc3580f9db
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479013"
---
このコンテナーには、次の構成設定があります。

|必須|Setting|目的|
|--|--|--|
|はい|[ApiKey](#apikey-setting)|課金情報の追跡に使用されます。|
|いいえ |[ApplicationInsights](#applicationinsights-setting)|[Azure Application Insights](https://docs.microsoft.com/azure/application-insights) テレメトリ サポートをお客様のコンテナーに追加できます。|
|はい|[課金](#billing-setting)|Azure 上のサービス リソースのエンドポイント URI を指定します。|
|はい|[Eula](#eula-setting)| コンテナーのライセンスに同意していることを示します。|
|いいえ |[Fluentd](#fluentd-settings)|ログと (必要に応じて) メトリック データを Fluentd サーバーに書き込みます。|
|いいえ |[Http Proxy](#http-proxy-credentials-settings)|送信要求を行うために、HTTP プロキシを構成します。|
|いいえ |[Logging](#logging-settings)|ASP.NET Core のログ サポートをお客様のコンテナーに提供します。 |
|いいえ |[Mounts](#mount-settings)|ホスト コンピューターからコンテナーに、またコンテナーからホスト コンピューターにデータを読み取ったり書き込んだりします。|
