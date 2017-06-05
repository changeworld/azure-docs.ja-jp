---
title: "Azure Functions の従量課金プランと App Service プラン | Microsoft Docs"
description: "ユーザーのイベント ドリブン ワークロードのニーズに合わせて Azure Functions が拡張する方法を説明します。"
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/15/2017
ms.author: donnam, glenga
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 25df99203ccb194d75985fca42d3d05609dc7d7c
ms.contentlocale: ja-jp
ms.lasthandoff: 05/18/2017


---
# <a name="azure-functions-consumption-and-app-service-plans"></a>Azure Functions の従量課金プランと App Service プラン 

## <a name="introduction"></a>はじめに

Azure Functions は、従量課金プランと App Service プランという 2 つの異なるモードで実行できます。 従量課金プランでは、コードの実行時にコンピューティング能力を自動的に割り当て、負荷の処理の必要性に応じてスケールアウトし、コードを実行していないときはスケールダウンします。 そのため、アイドル状態の VM に対して課金されることがなく、事前に容量を予約する必要もありません。 この記事では、従量課金プランを中心に説明します。 App Service プランの仕組みの詳細については、「[Azure App Service プランの詳細な概要](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)」を参照してください。 

まだ Azure Functions に慣れていない場合は、「[Azure Functions の概要](functions-overview.md)」を参照してください。

関数アプリを作成するときに、アプリ内の関数のホスティング プランを構成する必要があります。 使用可能なホスティング プランは、**従量課金プラン**と [**App Service プラン**](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)です。 どちらのモードでも、関数は "*Azure Functions ホスト*" のインスタンスにより実行されます。 プランの種類によって、ホスト インスタンスをスケールアウトする方法と、各ホストで使用できるリソースが変わります。

このようなプランの種類の選択は、現時点では Function App の作成中に行う必要があり、後で変更することはできません。 [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)のレベル間でスケーリングすることはできます。 従量課金プランでは、すべてのリソース割り当てが Azure Functions によって自動的に処理されます。

## <a name="consumption-plan"></a>従量課金プラン

**従量課金プラン**を使用する場合、Azure Functions ホストのインスタンスは、受信イベントの数に基づいて動的に追加および削除されます。 このプランではスケーリングが自動的に行われ、関数の実行中にのみコンピューティング リソースに対して料金が発生します。 従量課金プランでは、関数を最大で 5 分間実行できます。 

課金は実行時間とメモリの使用量に基づいて行われます。Function App 内のすべての関数に対する課金が集計されます。 詳細については、[Azure Functions の価格]に関するページを参照してください。

従量課金プランは既定のプランであり、次の利点があります。
- 関数の実行中にのみ課金されます。
- 負荷が高い期間中であっても、自動的にスケールアウトされます。

## <a name="app-service-plan"></a>App Service プラン

**App Service プラン**では、Function App は Web Apps と同様に、Basic、Standard、Premium SKU の専用 VM 上で実行されます。 専用 VM が App Service アプリに割り当てられるので、Functions ホストは継続的に実行されます。

次のような場合に App Service プランを検討してください。
- 既に他の App Service インスタンスを実行している、使用率の低い既存の VM がある。
- Function App を継続的に、またはほぼ継続的に実行したい。
- 従量課金プランで提供されるよりも多くの CPU またはメモリのオプションが必要である。
- 従量課金プランで許可されている最大実行時間よりも長く実行する必要がある。

VM を使用すると、コストが実行時間とメモリ サイズの両方から切り離されます。 このため、割り当てた VM インスタンスのコストを超えて課金されることはありません。 App Service プランの仕組みの詳細については、「[Azure App Service プランの詳細な概要](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)」を参照してください。 

App Service プランでは、VM インスタンスを追加して手動でスケールアウトするか、自動スケールを有効にすることができます。 詳細については、「[手動または自動によるインスタンス数のスケール変更](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)」を参照してください。 別の App Service プランを選択してスケールアップすることもできます。 詳細については、 [Azure でのアプリのスケールアップ](../app-service-web/web-sites-scale.md) に関するページを参照してください。 App Service プランで JavaScript 関数を実行する予定がある場合は、コアの少ないプランを選択してください。 詳細については、「[JavaScript 関数リファレンス](functions-reference-node.md#choose-single-core-app-service-plans)」を参照してください。  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
### 常時接続

App Service プランを実行する場合、Function App が正常に実行されるように、**常時接続**設定を有効にする必要があります。 App Service プランでは、関数のランタイムは非アクティブな状態が数分続くとアイドル状態となるため、関数を実際に "起こす" ことができるのは HTTP トリガーのみとなります。 これは、WebJobs で常時接続を有効にしておく必要がある理由と似ています。 

常時接続は App Service プランでのみ使用可能です。 従量課金プランでは、Function App はプラットフォームにより自動的にアクティブ化されます。

## <a name="storage-account-requirements"></a>ストレージ アカウントの要件

Function App を使用するには、従量課金プランと App Service プランのどちらでも、Blob、Queue、Table Storage をサポートする Azure Storage アカウントが必要です。 内部的には、Azure Functions はトリガーの管理や関数実行のログなどの操作に Azure Storage を使用します。 BLOB 専用のストレージ アカウント (Premium Storage を含む) や ZRS レプリケーションを使用する汎用ストレージ アカウントなどの一部のストレージ アカウントでは、キューとテーブルがサポートされません。 これらのアカウントは、Function App の作成時に [ストレージ アカウント] ブレードから除外されます。

ストレージ アカウントの種類の詳細については、[Azure Storage サービスの概要に関する記事] (../storage/storage-introduction.md#introducing-the-azure-storage-services) をご覧ください。

## <a name="how-the-consumption-plan-works"></a>従量課金プランの仕組み

従量課金プランでは、関数がトリガーされるイベントの数に基づいて Functions ホストのインスタンスを追加することで、CPU とメモリのリソースが自動的にスケーリングされます。 Functions ホストの各インスタンスのメモリは、最大で 1.5 GB までに制限されています。

従量課金ホスティング プランを使用する場合、関数コード ファイルはメイン ストレージ アカウントの Azure Files 共有に保存されます。 メイン ストレージ アカウントを削除すると、このコンテンツは削除され、復元できません。

> [!NOTE]
> 従量課金プランで BLOB トリガーを使用しているときに Function App がアイドル状態になると、新しい BLOB の処理が最大で 10 分遅延する場合があります。 Function App が実行されると、BLOB は直ちに処理されます。 この初期段階の遅延を避けるために、次のオプションのいずれかを検討してください。
> - 常時接続が有効な App Service プランを使用する。
> - 別のメカニズムを使用して、BLOB 名を含むキュー メッセージなどの BLOB 処理をトリガーする。 例については、[BLOB 入力バインドでのキュー トリガー](functions-bindings-storage-blob.md#input-sample)に関する記事を参照してください。

### <a name="runtime-scaling"></a>実行時のスケーリング

Azure Functions は "*スケール コントローラー*" と呼ばれるコンポーネントを使用して、イベント レートを監視し、スケールアウトとスケールダウンのどちらを実行するかを決定します。 スケール コントローラーは、トリガーの種類ごとにヒューリスティックを使用します。 たとえば、Azure Queue Storage トリガーを使用した場合、スケーリングはキューの長さや最も古いキュー メッセージの経過時間に基づいて実施されます。

スケーリングは Function App 単位で行われます。 スケールアウトするときは、Azure Functions ホストの複数のインスタンスを実行するためのリソースが追加で割り当てられます。 反対に、コンピューティングの需要が減ると、Functions ホストのインスタンスは削除されます。 Function App 内で関数が何も実行されていない場合、インスタンスの数は最終的に 0 にスケールダウンされます。

![](./media/functions-scale/central-listener.png)

### <a name="billing-model"></a>課金モデル

従量課金プランの課金の詳細については、[Azure Functions の価格]に関するページをご覧ください。 使用量は Function App レベルで集計され、関数コードが実行されている期間のみカウントされます。 課金の単位は、次のとおりです。 
* **GB/秒 (ギガバイト/秒) 単位でのリソース使用量**。 メモリ サイズと、Function App 内の全関数の実行時間の組み合わせとして計算されます。 
* **実行回数**。 バインドによってトリガーされる関数がイベントに応じて実行されるたびにカウントされます。

[Azure Functions の価格]: https://azure.microsoft.com/pricing/details/functions
