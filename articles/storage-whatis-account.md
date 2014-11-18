<properties urlDisplayName="What is a Storage Account" pageTitle="ストレージ アカウントとは| Microsoft Azure" metaKeywords="" description="Azure で使用できるさまざまな種類のストレージ アカウントについて説明し、ストレージ関連の主要な用語に関する定義を理解します。" metaCanonical="" services="storage" documentationCenter="" title="ストレージ アカウントとは" authors="tamram" solutions="" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# ストレージ アカウントとは

Azure Storage には、BLOB ストレージ、テーブル ストレージ、キュー ストレージという 3 つのサービスが含まれています。これらのサービスは、すべてのストレージ アカウントに含まれます。ストレージ アカウントでは、BLOB、キュー、およびテーブルを操作するための一意の名前空間が用意されています。

ストレージ アカウントの制限の詳細については、「[Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット][Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット]」を参照してください。

作成日時を含め、ストレージ アカウントに関するすべての情報は、管理ポータルの **[ダッシュボード]** ページのうち **[ストレージ]** で使用できます。

ストレージのコストは、ストレージ容量、レプリケーション スキーム、ストレージ トランザクション、送信データ転送という 4 つの要因に基づいています。ストレージ容量とは、データの保存に使用するためにストレージ アカウントに割り当てられた容量を指します。単純にデータを保存する場合の料金は、保存するデータ量とレプリケーション方法によって決まります。トランザクションには、Azure ストレージに対するすべての読み取り操作と書き込み操作が含まれます。送信データ転送とは、Azure リージョンから転送されたデータを意味します。他のリージョンで動作しているアプリケーションがストレージ アカウント内のデータにアクセスした場合、そのアプリケーションがクラウド サービスか、その他の種類のアプリケーションかに関係なく、送信データ転送として課金されます (Azure サービスでは、同じデータ センター内のデータとサービスをグループ化して、送信データ転送処理とそれに伴う料金を削減または不要にする手順が用意されています)。

ストレージ容量、レプリケーション、トランザクションの料金の詳細については、「[Storage (ストレージ サービス) の料金詳細][Storage (ストレージ サービス) の料金詳細]」のページを参照してください。送信データ転送の料金の詳細については、「[データ転送の料金の詳細][データ転送の料金の詳細]」を参照してください。

## ストレージ アカウントの概念

### ストレージ アカウント レプリケーションのオプション

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

### ストレージ アカウント エンドポイント

ストレージ アカウントの*エンドポイント*とは、BLOB、テーブル、またはキューにアクセスするための最高レベルの名前空間です。ストレージ アカウントに対応する既定のエンドポイントでは、次の書式を使用します。

-   BLOB サービス: http://*mystorageaccount*.blob.core.windows.net

-   テーブル サービス: http://*mystorageaccount*.table.core.windows.net

-   キュー サービス: http://*mystorageaccount*.queue.core.windows.net

-   ファイル サービス: http://*mystorageaccount*.file.core.windows.net

ストレージ アカウント内のオブジェクトにアクセスするための URL は、ストレージ アカウント内のオブジェクトの場所をエンドポイントに追加して作成します。たとえば、BLOB アドレスは次の形式になることがあります。http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*

### ストレージ アカウントのセキュリティ

ストレージ アカウントを作成するときに、Azure によって 2 つの 512 ビット ストレージ アクセス キーが生成されます。これらは、ストレージ アカウントにアクセスするときに認証の目的で使用されます。Azure によって 2 つのストレージ アクセス キーが提供される結果、ストレージ サービスやサービスへのアクセスを中断することなく、これらのキーを再生成できます。

ストレージ アカウント アクセス キーは、他の人と共有しないことをお勧めします。アカウントが侵害されていると感じたときは、ポータル内からアクセス キーを再生成できます。自分のストレージ アカウントを選択し、**[アクセス キーの管理]** を選択してください。

アクセス キーを入力しないでストレージ リソースにアクセスする場合は、*共有アクセス署名*が利用できます。共有アクセス署名を使用すると、指定した期間、指定した権限で、アカウント内のリソースにアクセスできるようになります。詳細については、「[共有アクセス署名、第 1 部: SAS モデルについて][共有アクセス署名、第 1 部: SAS モデルについて]」を参照してください。

### ストレージ アカウントのメトリックとログ

-   **最小限のメトリックと詳細メトリックの比較** ストレージ アカウントの監視設定で、最小限のメトリック、または詳細メトリックを構成できます。*最低限のメトリック*の場合は、Blob サービス、テーブル サービス、キュー サービスに関して集計される、受信/送信、可用性、遅延時間、成功のパーセンテージなどのデータを収集します。*詳細メトリック*の場合は、同じメトリックに関するサービス レベルの集計に加えて、オペレーションレベルの詳細を収集します。詳細メトリックにより、アプリケーションの操作中に発生する問題を詳しく分析できます。使用可能なメトリックの詳細なリストについては、「[Storage Analytics Metrics のテーブル スキーマ][Storage Analytics Metrics のテーブル スキーマ]」を参照してください。ストレージ監視の詳細については、「[Storage Analytics Metrics について][Storage Analytics Metrics について]」を参照してください。

-   **ログ** ログはストレージ アカウントの構成可能な機能であり、BLOB、テーブル、およびキューへの読み取り、書き込み、削除の各要求をログに記録できます。Azure の管理ポータルでログを構成しますが、管理ポータルでログを表示することはできません。ログは、ストレージ アカウント内の $logs コンテナーに格納され、アクセスできます。詳細については、「[Storage Analytics Overview (Storage Analytics の概要)][Storage Analytics Overview (Storage Analytics の概要)]」を参照してください。

### Azure ストレージと他のサービスを併置するためのアフィニティ グループ

*アフィニティ グループ*は、Azure ストレージ アカウント内で Azure サービスと仮想マシンを地理的にまとめたグループです。アフィニティ グループは、コンピューティング ワークロードを同じデータ センター内または対象ユーザーの近くに配置することにより、サービス パフォーマンスを向上させることができます。また、同じアフィニティ グループに属している別のサービスがストレージ アカウント内のデータにアクセスする場合には、課金されません。

  [Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット]: http://msdn.microsoft.com/ja-jp/library/dn249410.aspx
  [Storage (ストレージ サービス) の料金詳細]: http://www.windowsazure.com/ja-jp/pricing/details/#storage
  [データ転送の料金の詳細]: http://www.windowsazure.com/ja-jp/pricing/details/data-transfers/
  [共有アクセス署名、第 1 部: SAS モデルについて]: ../storage-dotnet-shared-access-signature-part-1/
  [Storage Analytics Metrics のテーブル スキーマ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh343264.aspx
  [Storage Analytics Metrics について]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh343258.aspx
  [Storage Analytics Overview (Storage Analytics の概要)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh343268.aspx
