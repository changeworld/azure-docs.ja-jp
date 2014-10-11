<properties umbracoNaviHide="0" pageTitle="Storage Account Concepts | Azure" metaKeywords="Azure storage, storage service, service, storage account, account, create storage account, create account" description="Learn about storage account concepts." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Storage Account Concepts" services="storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# ストレージ アカウントの概念

## ストレージ アカウント レプリケーションのオプション

[WACOM.INCLUDE [storage-replication-options][]]

## ストレージ アカウント エンドポイント

ストレージ アカウントの*エンドポイント*とは、BLOB、テーブル、またはキューにアクセスするための最高レベルの名前空間です。ストレージ アカウントに対応する既定のエンドポイントでは、次の書式を使用します。

-   BLOB サービス: http://*mystorageaccount\*.blob.core.windows.net
-   
-   テーブル サービス: http://*mystorageaccount\*.table.core.windows.net

-   キュー サービス: http://*mystorageaccount\*.queue.core.windows.net

-   ファイル サービス: http://*mystorageaccount\*.file.core.windows.net

ストレージ アカウント内のオブジェクトにアクセスするための URL は、ストレージ アカウント内のオブジェクトの場所をエンドポイントに追加して作成します。たとえば、BLOB アドレスは次の形式になることがあります。http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob\*

## ストレージ アカウントのセキュリティ

ストレージ アカウントを作成するときに、Azure によって 2 つの 512 ビット ストレージ アクセス キーが生成されます。これらは、ストレージ アカウントにアクセスするときに認証の目的で使用されます。Azure によって 2 つのストレージ アクセス キーが提供される結果、ストレージ サービスやサービスへのアクセスを中断することなく、これらのキーを再生成できます。

ストレージ アカウント アクセス キーは、他の人と共有しないことをお勧めします。アカウントが侵害されていると感じたときは、ポータル内からアクセス キーを再生成できます。自分のストレージ アカウントを選択し、**[アクセス キーの管理]** を選択してください。

アクセス キーを入力しないでストレージ リソースにアクセスする場合は、*共有アクセス署名*が利用できます。共有アクセス署名を使用すると、指定した期間、指定した権限で、アカウント内のリソースにアクセスできるようになります。詳細については、「[共有アクセス署名、第 1 部: SAS モデルについて][]」を参照してください。

## ストレージ アカウントのメトリックとログ

-   **最小限のメトリックと詳細メトリックの比較** ストレージ アカウントの監視設定で、最小限のメトリック、または詳細メトリックを構成できます。*最低限のメトリック*の場合は、Blob サービス、テーブル サービス、キュー サービスに関して集計される、受信/送信、可用性、遅延時間、成功のパーセンテージなどのデータを収集します。*詳細メトリック*の場合は、同じメトリックに関するサービス レベルの集計に加えて、オペレーションレベルの詳細を収集します。詳細メトリックにより、アプリケーションの操作中に発生する問題を詳しく分析できます。使用可能なメトリックの詳細なリストについては、「[Storage Analytics Metrics のテーブル スキーマ][]」を参照してください。ストレージ監視の詳細については、「[Storage Analytics Metrics について][]」を参照してください。

-   **ログ** ログはストレージ アカウントの構成可能な機能であり、BLOB、テーブル、およびキューへの読み取り、書き込み、削除の各要求をログに記録できます。Azure の管理ポータルでログを構成しますが、管理ポータルでログを表示することはできません。ログは、ストレージ アカウント内の $logs コンテナーに格納され、アクセスできます。詳細については、「[Storage Analytics Overview (Storage Analytics の概要)][]」を参照してください。

## Azure ストレージと他のサービスを併置するためのアフィニティ グループ

*アフィニティ グループ*は、Azure ストレージ アカウント内で Azure サービスと仮想マシンを地理的にまとめたグループです。アフィニティ グループは、コンピューティング ワークロードを同じデータ センター内または対象ユーザーの近くに配置することにより、サービス パフォーマンスを向上させることができます。また、同じアフィニティ グループに属している別のサービスがストレージ アカウント内のデータにアクセスする場合には、課金されません。

  [storage-replication-options]: ../includes/storage-replication-options.md
  [共有アクセス署名、第 1 部: SAS モデルについて]: ../storage-dotnet-shared-access-signature-part-1/
  [Storage Analytics Metrics のテーブル スキーマ]: http://msdn.microsoft.com/en-us/library/windowsazure/hh343264.aspx
  [Storage Analytics Metrics について]: http://msdn.microsoft.com/en-us/library/windowsazure/hh343258.aspx
  [Storage Analytics Overview (Storage Analytics の概要)]: http://msdn.microsoft.com/en-us/library/windowsazure/hh343268.aspx
