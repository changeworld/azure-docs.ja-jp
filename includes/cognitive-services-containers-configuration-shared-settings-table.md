---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 57e54c4c3b8ae44d42051c87356f1feeaa5a3f10
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977210"
---
このコンテナーには、次の構成設定があります。

|必須|Setting|目的|
|--|--|--|
|はい|[ApiKey](#apikey-setting)|課金情報の追跡に使用されます。|
|いいえ |[ApplicationInsights](#applicationinsights-setting)|[Azure Application Insights](https://docs.microsoft.com/azure/application-insights) テレメトリ サポートをお客様のコンテナーに追加できます。|
|はい|[Billing](#billing-setting)|Azure 上のサービス リソースのエンドポイント URI を指定します。|
|はい|[Eula](#eula-setting)| コンテナーのライセンスに同意していることを示します。|
|いいえ |[Fluentd](#fluentd-settings)|ログと (必要に応じて) メトリック データを Fluentd サーバーに書き込みます。|
|いいえ |[Logging](#logging-settings)|ASP.NET Core のログ サポートをお客様のコンテナーに提供します。 |
|いいえ |[Mounts](#mount-settings)|ホスト コンピューターからコンテナーに、またコンテナーからホスト コンピューターにデータを読み取ったり書き込んだりします。|
