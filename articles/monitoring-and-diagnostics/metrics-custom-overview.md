---
title: Azure Monitor のカスタム メトリック
description: Azure Monitor のカスタム メトリックと、それらのモデリング方法について説明します。
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: c136772e27dab014c22234f1ef1d2baddd2ffe58
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978082"
---
# <a name="custom-metrics-in-azure-monitor"></a>Azure Monitor のカスタム メトリック

Azure でリソースやアプリケーションをデプロイするとき、それらのパフォーマンスや正常性についての洞察を得るために、テレメトリの収集を開始することができます。 Azure には、リソースのデプロイ後すぐに利用できるいくつかのメトリックがあります。 これらは標準メトリックまたはプラットフォーム メトリックと呼ばれます。 しかし、実際にはこれらのメトリックには制限があります。 より詳細な洞察を得るために、いくつかのカスタム パフォーマンス指標またはビジネス固有のメトリックを収集することができます。
これらの "カスタム" メトリックは、アプリケーション テレメトリ、Azure リソース上で実行されるエージェント、またはアウトサイドイン型の監視システムによって収集し、Azure Monitor に直接送信することができます。 Azure のリソースおよびアプリケーションのカスタム メトリックが Azure Monitor に発行されたら、Azure によって出力された標準メトリックと共に、それらのメトリックを参照、クエリしたり、メトリックに基づいたアラートを設定したりできます。

## <a name="send-custom-metrics"></a>カスタム メトリックの送信
カスタム メトリックはさまざまな方法で Azure Monitor に送信できます。
- Application Insights SDK を使用してアプリケーションをインストルメント化し、カスタム テレメトリを Azure Monitor に送信します 
- [Azure VM](metrics-store-custom-guestos-resource-manager-vm.md)、[Virtual Machine スケール セット](metrics-store-custom-guestos-resource-manager-vmss.md)、[クラシック VM](metrics-store-custom-guestos-classic-vm.md)、または[クラシック クラウド サービス](metrics-store-custom-guestos-classic-cloud-service.md)に Windows 診断拡張機能をインストールして、パフォーマンス カウンターを Azure Monitor に送信します 
- [InfluxDB Telegraf エージェント](metrics-store-custom-linux-telegraf.md)を Azure Linux VM にインストールし、Azure Monitor 出力プラグインを使用してメトリックを送信します
- カスタム メトリックを [Azure Monitor REST API に直接](metrics-store-custom-rest-api.md)送信します (https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics)

Azure Monitor にカスタム メトリックを送信するときは、報告する各データ ポイント (または値) に次の情報が含まれている必要があります。

### <a name="authentication"></a>Authentication
Azure Monitor にカスタム メトリックを送信するために、メトリックを送信するエンティティは、有効な Azure Active Directory トークンを要求の "Bearer" ヘッダーに持っている必要があります。 有効なベアラー トークンを取得するいくつかの方法がサポートされています。
1. [MSI (マネージド サービス ID)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) - (VM などの) Azure リソース自体に ID を付与します。 MSI は、特定の操作を実行するためのアクセス許可をリソースに付与するように設計されており、たとえば、リソース自体についてのメトリックをリソースが出力することを許可します。 別のリソースに対する "Monitoring Metrics Publisher" のアクセス許可をリソース (またはその MSI) に付与すれば、MSI は他のリソースについてのメトリックも出力できるようになります。
2. [AAD サービス プリンシパル](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) - このシナリオでは、Azure リソースについてのメトリックを出力するためのアクセス許可を AAD アプリケーション (サービス) に割り当てることができます。
要求を認証するために、Azure Monitor は AAD 公開キーを使用して Application トークンを検証します。 既存の "Monitoring Metrics Publisher" ロールは既に、Azure portal で利用できるこのアクセス許可を持っています。 サービス プリンシパルには、どのようなリソースについてのカスタム メトリックをそれが出力するのかに応じて、必要なスコープ (サブスクリプション、リソース グループ、または特定のリソース) で "Monitoring Metrics Publisher" ロールを付与することができます。

> [!NOTE]
> カスタム メトリックを出力するために AAD トークンを要求するときは、トークンを要求する対象のオーディエンス/リソースが https://monitoring.azure.com/ であることを確認してください (必ず、末尾の '/' を含めてください)

### <a name="subject"></a>Subject
このプロパティは、どの Azure リソース ID についてのカスタム メトリックが報告されるのかを表します。 この情報は、実行される API 呼び出しの URL にエンコードされます。 各 API は、単一の Azure リソースのメトリック値のみを送信できます。

> [!NOTE]
> リソース グループまたはサブスクリプションのリソース ID に対してカスタム メトリックを出力することはできません。
>
>

### <a name="region"></a>リージョン
このプロパティは、メトリックを出力する対象のリソースがデプロイされている Azure リージョンを表します。 メトリックは、リソースがデプロイされているリージョンと同じリージョンの Azure Monitor エンドポイントに出力される必要があります。 たとえば、米国西部にデプロイされている VM についてのカスタム メトリックは、WestUS リージョンの Azure Monitor エンドポイントに送信される必要があります。 リージョン情報は API 呼び出しの URL にもエンコードされます。

> [!NOTE]
> パブリック プレビュー期間中、カスタム メトリックは一部の Azure リージョンでのみ利用できます。 サポートされているリージョンの一覧は、この記事の後半のセクションに示しています。
>
>

### <a name="timestamp"></a>Timestamp
Azure Monitor に送信されるデータ ポイントには、それぞれタイムスタンプが記録されている必要があります。 このタイムスタンプはメトリック値が測定/収集された日時を表します。 Azure Monitor は、現在から 20 分前までの、および現在から 5 分後までのタイムスタンプが付いたメトリック データを受け付けます。

### <a name="namespace"></a>名前空間
名前空間は類似のメトリックを分類またはグループ化する方法です。 名前空間を使用すると、収集している情報またはパフォーマンス指標に基づいてメトリックのグループを分離できます。 たとえば、*ContosoMemoryMetrics* という名前空間を使用して、アプリをプロファイリングするメモリ使用量メトリックを追跡します。また、*ContosoAppTransaction* という別の名前空間を使用して、アプリケーションでのユーザー トランザクションに関するすべてのメトリックを追跡します。

### <a name="name"></a>Name
報告されているメトリックの名前。 通常は、測定内容を識別するのに十分なほど説明的な名前です。 たとえば、特定の VM で使用中のメモリのバイト数を測定するメトリックは "Memory Bytes In Use" のようなメトリック名になります。

### <a name="dimension-keys"></a>ディメンション キー
ディメンションは、収集しているメトリックに関する追加の特性を説明するのに役立つキーと値のペアです。 この追加の特性に基づいて、より多くの情報をメトリックについて収集し、より深い洞察を得ることが可能になります。 たとえば、VM で各プロセスが消費しているメモリのバイト数を表す "Process" というディメンション キーを "Memory Bytes In Use" メトリックに追加できます。 これによってメトリックをフィルタリングし、メモリ固有のプロセスが使用している容量を確認したり、メモリ使用量で上位 5 つのプロセスを識別したりすることが可能になります。
各カスタム メトリックは最大 10 個のディメンションを持つことができます。

### <a name="dimension-values"></a>ディメンション値
メトリックのデータ ポイントを報告するとき、報告されるメトリックのディメンション キーごとに、対応するディメンション値が存在します。 たとえば、VM で ContosoApp によって使用されているメモリを報告する場合、次のようになります。

* メトリック名は *Memory Bytes in Use*
* ディメンション キーは *Process*
* ディメンション値は *ContosoApp.exe*

メトリック値を発行するとき、ディメンション キーごとに 1 つのディメンション値しか指定できません。 VM 上の複数のプロセスについて同じメモリ使用率を収集する場合、そのタイムスタンプで複数のメトリック値を報告できます。 各メトリック値は、Process ディメンション キーに異なるディメンション値を指定します。

### <a name="metric-values"></a>メトリック値
Azure Monitor では、すべてのメトリックを 1 分刻みの間隔で保存します。 ある 1 分の間に、メトリックを複数回サンプリングする必要がある (例:  CPU 使用率)、あるいは多くの個別イベントについてメトリックを測定する必要がある (例:  サインイン トランザクション待機時間) ことの可能性は理解しています。 出力して Azure Monitor に送信する必要がある未処理値の数を制限するために、ローカルで値を事前集計して出力することができます。

* Min: 1 分間のすべてのサンプル/測定値からの最小観測値
* Max: 1 分間のすべてのサンプル/測定値からの最大観測値
* Sum: 1 分間のすべてのサンプル/測定値からのすべての観測値の合計
* Count: 1 分間に取得されたサンプル/測定値の数

たとえば、ある 1 分の間に、アプリへのサインイン トランザクションが 4 件あり、それぞれについて測定された待機時間の結果が次のとおりであったとします。

|トランザクション 1|トランザクション 2|トランザクション 3|トランザクション 4|
|---|---|---|---|
|7 ミリ秒|4 ミリ秒|13 ミリ秒|16 ミリ秒|
|

この結果として Azure Monitor に送信されるメトリックは次のようになります。
* Min: 4
* Max: 16
* Sum: 40
* Count: 4

アプリケーションにおいて、ローカルでの事前集計ができず、収集してすぐに個別のサンプルまたはイベントを出力する必要がある場合、未処理の測定値を出力することができます。
たとえば、アプリでサインイン トランザクションが発生するたびに、ただ 1 つの測定値と共にメトリックを Azure Monitor に発行します。 したがって、12 ミリ秒を要したサインイン トランザクションの場合、送信されるメトリックは次のようになります。
* Min: 12
* Max: 12
* Sum: 12
* Count: 1

このプロセスでは、ある 1 分の間に、同じメトリックとディメンションの組み合わせに対して複数の値を出力できます。 次に、Azure Monitor は、ある 1 分の間に出力されたすべての未処理値を集計します。

### <a name="sample-custom-metric-publication"></a>カスタム メトリックの送信例
次の例では、仮想マシン用のメトリック名前空間 "Memory Profile" の下に "Memory Bytes in Use" というカスタム メトリックを作成します。 このメトリックは "Process" という 1 つのディメンションを持ちます。 特定のタイムスタンプに対して、2 つの異なるプロセスについてのメトリック値を出力します。

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]
> Application Insights、Windows Azure 診断拡張機能、および InfluxData Telegraf エージェントは、正しいリージョンのエンドポイントに対するメトリック値を出力し、各出力で上記のプロパティをすべて保持するよう、既に構成されています。
>
>

## <a name="custom-metric-definitions"></a>カスタム メトリック定義
出力される前に Azure Monitor でカスタム メトリックを事前定義する必要はありません。 発行される各メトリックのデータ ポイントには名前空間、名前、およびディメンション情報が含まれているため、最初にカスタム メトリックが Azure Monitor に出力されるときにメトリック定義が自動的に作成されます。 このメトリック定義はその後、そのメトリック定義によってメトリックが出力された対象である任意のリソースで検出可能です。

> [!NOTE]
> Azure Monitor では、カスタム メトリックの "単位" の定義はまだサポートされていません。

## <a name="using-custom-metrics"></a>カスタム メトリックの使用
Azure Monitor に送信されたカスタム メトリックは、Azure portal から参照したり、Azure Monitor REST API を使用してクエリしたりできます。また、それらのメトリックに基づいたアラートを作成し、特定の条件が満たされたときに通知を受け取ることができます。
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Azure portal からカスタム メトリックを参照する
1.  [Azure portal](https://portal.azure.com) に移動します
2.  [モニター] ブレードを選択します
3.  [メトリック] をクリックします
4.  カスタム メトリックを出力した対象のリソースを選択します
5.  カスタム メトリックのメトリック名前空間を選択します
6.  カスタム メトリックを選択します

## <a name="supported-regions"></a>サポートされているリージョン
パブリック プレビュー期間中、カスタム メトリックを発行する機能は一部の Azure リージョンでのみ利用できます。 つまり、サポートされているリージョンのいずれかにあるリソースに対してのみ、メトリックを発行できます。 次の表は、カスタム メトリックがサポートされている Azure リージョンと、それらのリージョン内のリソースに対応しており発行先として適切なエンドポイント メトリックの一覧を示しています。

|Azure リージョン|リージョンのエンドポイントのプレフィックス|
|---|---|
|米国東部|https://eastus.monitoring.azure.com/|
|米国中南部|https://southcentralus.monitoring.azure.com/|
|米国中西部|https://westcentralus.monitoring.azure.com/|
|米国西部 2|https://westus2.monitoring.azure.com/|
|東南アジア|https://southeastasia.monitoring.azure.com/|
|北ヨーロッパ|https://northeurope.monitoring.azure.com/|
|西ヨーロッパ|https://westeurope.monitoring.azure.com/|

## <a name="quotas-and-limits"></a>クォータと制限
Azure Monitor では、カスタム メトリックの使用に次の制限があります。

|Category|制限|
|---|---|
|アクティブ時系列/サブスクリプション/リージョン|50,000|
|メトリックあたりのディメンション キー|10|
|メトリック名前空間、メトリック名、ディメンション キー、およびディメンション値の文字列の長さ|256 文字|
アクティブ時系列は、メトリック、ディメンション キー、およびディメンション値の任意の一意な組み合わせであり、過去 12 時間以内にメトリック値が発行されたものとして定義されます。

## <a name="next-steps"></a>次の手順
さまざまなサービスのカスタム メトリックを使用する 
 - [仮想マシン](metrics-store-custom-guestos-resource-manager-vm.md)
 - [仮想マシン スケール セット](metrics-store-custom-guestos-resource-manager-vmss.md)
 - [仮想マシン (クラシック)](metrics-store-custom-guestos-classic-vm.md)
 - [Telegraf エージェントを使用した Linux 仮想マシン](metrics-store-custom-linux-telegraf.md)
 - [REST API](metrics-store-custom-rest-api.md)
 - [クラウド サービス (クラシック)](metrics-store-custom-guestos-classic-cloud-service.md)
 