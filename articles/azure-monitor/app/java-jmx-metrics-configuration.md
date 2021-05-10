---
title: JMX メトリックを構成する方法 - Java 向けの Azure Monitor Application Insights
description: Azure Monitor Application Insights Java エージェント用の追加の JMX メトリック コレクションを構成する
ms.topic: conceptual
ms.date: 03/16/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 020278bf6e5a823f6b3caa22d03f4b5dd003c0d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608733"
---
# <a name="configuring-jmx-metrics"></a>JMX メトリックの構成

Application Insights Java 3.0 エージェントによって既定で JMX メトリックの一部が収集されますが、多くの場合、これでは不十分です。 このドキュメントでは、JMX 構成オプションについて詳しく 説明します。

## <a name="how-do-i-collect-additional-jmx-metrics"></a>追加の JMX メトリックを収集する方法

JMX メトリック コレクションを構成するには、applicationinsights.json ファイルに ```"jmxMetrics"``` セクションを追加します。 メトリックの名前は、Azure portal の Application Insights リソースで表示したい様式で指定できます。 収集するメトリックごとに、オブジェクト名と属性を定義する必要があります。

## <a name="how-do-i-know-what-metrics-are-available-to-configure"></a>構成できるメトリックを確認する方法

オブジェクト名と属性は、必ず把握する必要があります。これらのプロパティは、ライブラリ、フレームワーク、アプリケーション サーバーによって異なり、多くの場合、十分に文書化されていません。 オブジェクト名と属性を取得するには、MBean ツリーを表示する必要があります。 MBean はマネージド Java オブジェクトです。これは、デバイス、アプリケーション、またはリソースを表すことができ、一連の属性を持ちます。 

使用可能なメトリックを表示し、使用可能なメトリックを参照するには、[Java Mission Control](https://www.oracle.com/java/technologies/jdk-mission-control.html) を使用することをお勧めします。

### <a name="how-to-navigate-the-java-mission-control-to-get-to-the-right-metrics"></a>Java Mission Control 内を移動して適切なメトリックを取得する方法

Java Mission Control ツールを実行すると、左側で JVM を選択できるようになります。[JVM ブラウザー] タブで該当するプロセスをクリックします。JMC がプロセスのダッシュボードを読み込むまで待機し、下部にある [MBean ブラウザー] タブを選択します (下記参照)。 JMC は JVM と同じフォルダーに配置されている必要があり、ご自分のプロセスまたはアプリは稼働している必要があります。

![JMC MBean ブラウザーのスクリーンショット](media/java-ipa/jmx/jmc-mbean-browser.png)

### <a name="how-to-get-to-the-metrics-i-want-and-the-necessary-attributes"></a>目的のメトリックと必要な属性を取得する方法

MBean ブラウザーで MBean ツリーが開き、展開できるカテゴリの一覧が表示されます。 左側でカテゴリを選択すると、右側で属性の一覧が開きます。 メトリック、そのオブジェクト名、および属性の例を下に示します。 下の例のように、属性は入れ子になっている場合があります。

![JMC MBean ツリーのスクリーンショット](media/java-ipa/jmx/jmc-metric-sample.png)

### <a name="configuration-example"></a>構成の例

上の画像に示されている選択から、いくつかのメトリックを構成してみましょう。 最初のものは、入れ子になったメトリック `LastGcInfo` の例です。これにはいくつかのプロパティがあり、`GcThreadCount` を取得する必要があります。

```json
"jmxMetrics": [
      {
        "name": "Demo - GC Thread Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "LastGcInfo.GcThreadCount"
      },
      {
        "name": "Demo - GC Collection Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "CollectionCount"
      },
      {
        "name": "Demo - Thread Count",
        "objectName": "java.lang:type=Threading",
        "attribute": "ThreadCount"
      }
],
```

### <a name="types-of-collected-metrics-and-available-configuration-options"></a>収集されるメトリックの型と使用可能な構成オプション

数値とブール値の JMX メトリックがサポートされていますが、他の型はサポートされていないため、無視されます。 

現在、ワイルドカードおよび集計の属性はサポートされていません。そのため、すべての属性 'オブジェクト名'/'属性' のペアを別個に構成する必要があります。 


## <a name="where-do-i-find-the-jmx-metrics-in-application-insights"></a>Application Insights で JMX メトリックを確認できる場所

アプリケーションが実行されていて JMX メトリックが収集されている場合に、Azure portal に移動し、Application Insights リソースに移動すると、それらを確認できます。 メトリックを表示するには、[メトリック] タブで、下に示すドロップダウンを選択します。

![ポータル内のメトリックのスクリーンショット](media/java-ipa/jmx/jmx-portal.png)
