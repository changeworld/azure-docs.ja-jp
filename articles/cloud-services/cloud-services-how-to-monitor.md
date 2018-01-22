---
title: "Azure クラウド サービスの監視 | Microsoft Docs"
description: "Azure クラウド サービスの監視に関する情報やいくつかのオプションについて説明します。"
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: adegeo
ms.openlocfilehash: c63a49c65f2d8261caa534308477888c752a89da
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2018
---
# <a name="introduction-to-cloud-service-monitoring"></a>クラウド サービスの監視の概要

どのクラウド サービスでも主要なパフォーマンス メトリックを監視することができます。 各クラウド サービス ロールは最小限のデータ (CPU 使用率、ネットワーク使用状況、ディスク使用率) を収集します。 クラウド サービスにより `Microsoft.Azure.Diagnostics` 拡張機能がロールに適用されている場合、そのロールはその他のデータ ポイントを収集できます。 この記事では、クラウド サービス対応の Azure 診断の概要について説明します。

ベーシックな監視機能では、ロール インスタンスのパフォーマンス カウンター データは、3 分間隔でサンプリングされて収集されます。 このベーシックな監視のデータはストレージ アカウントには格納されないため、追加のコストが伴うことはありません。

高度な監視機能では、5 分、1 時間、12 時間の間隔で追加のメトリックがサンプリングされて収集されます。 集計されたデータはストレージ アカウントにテーブルとして格納され、10 日後に消去されます。 使用されるストレージ アカウントはロールごとに構成されます。ロールによって異なるストレージ アカウントを使用できます。 これは、[.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) ファイルと [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) ファイルで接続文字列を使用して構成されます。


## <a name="basic-monitoring"></a>ベーシックな監視機能

最初のセクションで説明したように、クラウド サービスはベーシックな監視データをホスト仮想マシンから自動的に収集します。 このデータには、CPU 使用率、ネットワーク入出力、ディスクの読み取り/書き込みが含まれています。 収集された監視データは、Azure ポータルで、クラウド サービスの概要ページとメトリック ページに自動的に表示されます。 

ベーシックな監視機能では、ストレージ アカウントは不要です。 

![ベーシックなクラウド サービス監視のタイル](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>高度な監視

高度な監視では、監視対象のロールで Azure 診断拡張機能 (および必要に応じて Application Insights SDK) が使用されます。 診断拡張機能は、(ロールごとに) 構成ファイル **diagnostics.wadcfgx** を使用して、監視する診断メトリックを構成します。 Azure 診断拡張機能が収集するデータは、**.wadcfgx**、[.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef)、[.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) の各ファイルに構成された Azure ストレージ アカウントに格納されます。 つまり、高度な監視機能には追加のコストが伴います。

ロールが作成されるたびに、Visual Studio が Azure 診断拡張機能をロールに追加します。 この拡張機能は、次の種類の情報を収集できます。

* カスタム パフォーマンス カウンター
* アプリケーション ログ
* Windows イベント ログ
* .NET イベント ソース
* IIS ログ
* マニフェスト ベースの ETW
* クラッシュ ダンプ
* カスタム エラー ログ

これらすべてのデータがストレージ アカウントに集計されますが、データのグラフを作成するポータル固有の方法は用意されていません。 Application Insights など他のサービスを使用すると、データを関連付けて表示することができます。

### <a name="use-application-insights"></a>Application Insights を使用する

Visual Studio からクラウド サービスを発行するときに、診断データを Application Insights に送信するオプションが提供されます。 その時点で Application Insights のリソースを作成したり、データを既存のリソースに送信したりすることができます。 クラウド サービスの可用性、パフォーマンス、障害、および使用状況を Application Insights で監視できます。 カスタム グラフを Application Insights に追加して、自分にとって最も重要なデータを確認できます。 ロール インスタンス データは、クラウド サービス プロジェクトで Application Insights SDK を使用して収集できます。 Application Insights を統合する方法について詳しくは、[クラウド サービス向けの Application Insights](../application-insights/app-insights-cloudservices.md) に関する記事をご覧ください。

Application Insights を使用すると、Windows Azure 診断拡張機能で指定したパフォーマンス カウンター (および他の設定) を表示できますが、さらに高度な機能は worker ロールおよび web ロールに Application Insights SDK を統合した場合のみ得られます。


## <a name="add-advanced-monitoring"></a>高度な監視機能を追加する

まず、**クラシック** ストレージ アカウントがない場合は、[作成します](../storage/common/storage-create-storage-account.md#create-a-storage-account)。 ストレージ アカウントが**クラシック デプロイメント モデル**を指定して作成されたことを確認します。

次は、**ストレージ アカウント (クラシック)** リソースに移動します。 **[設定]** > **[アクセス キー]** を選択し、**[プライマリ接続文字列]** の値をコピーします。 クラウド サービスではこの値が必要です。 

高度な診断機能を有効にするには、**ServiceDefinition.csdef** と **ServiceConfiguration.cscfg** の 2 つの構成ファイルを変更する必要があります。 通常、**.cscfg** ファイルは 2 つあります。Azure へのデプロイのための **ServiceConfiguration.cloud.cscfg** と、ローカル デバッグ デプロイに使用される **ServiceConfiguration.local.cscfg** です。 これらの両方を変更します。

**ServiceDefinition.csdef** ファイルでは、高度な診断機能を使用する各ロールに `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` という新しい設定を追加します。 この値は新しいプロジェクトを作成したときに Visual Studio によってファイルに追加されます。 この値がない場合はここで追加することができます。 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

これにより、すべての **ServiceConfiguration.cscfg** ファイルに追加する必要がある新しい設定が定義されます。 各 **.cscfg** ファイルを開いて変更します。 `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` という設定を追加します。 値は、クラシック ストレージ アカウントの**プライマリ接続文字列**または`UseDevelopmentStorage=true` (開発用コンピューターのローカル ストレージを使用する場合) に設定します。

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />

<!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
-->
```

## <a name="next-steps"></a>次の手順

- [クラウド サービスでの Application Insights について学ぶ](../application-insights/app-insights-cloudservices.md)

