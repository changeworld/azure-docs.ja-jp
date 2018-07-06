---
title: Azure Log Analytics に格納された個人データに関するガイダンス | Microsoft Docs
description: この記事では、Azure Log Analytics に格納された個人データを管理する方法と、それを特定して削除する方法について説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 3692c83a4991fc67ec176687bd076ab14e4c640d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129372"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics"></a>Log Analytics に格納された個人データに関するガイダンス

Log Analytics は、個人データが存在する可能性が高いデータ ストアです。 この記事では、そのようなデータがよく存在する Log Analytics の場所と、そのようなデータを処理するために使用できる機能について説明します。

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>個人データの処理に関する戦略

プライベート データを処理する際の戦略を策定するとしたら、それを最終的に行うのはお客様とその会社になりますが、考えられるアプローチをいくつか以下に紹介します。 これらは、技術的な観点に基づいて最も望ましいものから順に記載されています。

* 可能であれば、データの収集をやめるか、収集されるデータの難読化または匿名化を行う。そうでない場合は、"プライベート" と見なされるデータがなくなるよう、収集されるデータを調整する。 "_圧倒的_" に望ましいのはこのアプローチです。このアプローチでは、非常にコストがかかり影響も大きいデータ処理戦略を策定する必要がありません。
* 不可能な場合、データ プラットフォームとパフォーマンスへの影響を抑えるためにデータの正規化を試みる。 たとえば、明示的なユーザー ID を記録するのではなく、ユーザー名と詳細が内部 ID に関連付けられる参照データを作成し、別の場所から記録できるようにします。 そうすることで、あるユーザーから個人情報の削除を依頼された場合に、そのユーザーに対応するルックアップ テーブルの行を削除するだけで済ませることができます。 
* 最後に、プライベート データを収集する必要がある場合は、消去 API パスと既存のクエリ API パスに関するプロセスを構築する。これにより、ユーザーに関連付けられたプライベート データのエクスポートと削除に関して発生し得る義務を満たします。 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>プライベート データは Log Analytics のどこで見つかるか

Log Analytics は柔軟なストアであり、データのスキーマを指定しながら、カスタム値で各フィールドを上書きできます。 さらに、カスタム スキーマが取り込まれる場合があります。 そのため、特定のワークスペースのどこにプライベート データがあるかを正確に断言することはできません。 しかし、インベントリの次の場所から手を着けるのが妥当です。

* "*IP アドレス*": Log Analytics では、多数の異なるテーブルからさまざまな IP 情報が収集されます。 たとえば次のクエリは、過去 24 時間で IPv4 アドレスが収集されたすべてのテーブルを示します。
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* "*ユーザー ID*": ユーザー ID は、さまざまなソリューションとテーブルで見つかります。 検索コマンドを使用して、データセット全体で特定のユーザー名を検索することができます。
    ```
    search "[username goes here]"
    ```
人間が判読できるユーザー名だけでなく、特定のユーザーまで直接追跡できる GUID も忘れずに検索してください。
* "*デバイス ID*": ユーザー ID と同様、デバイス ID も "プライベート" と見なされる場合があります。 上に記載されているユーザー ID の場合と同じ方法を使用して、これが問題となるかもしれないテーブルを特定します。 
* "*カスタム データ*": Log Analytics では、カスタム ログとカスタム フィールド、[HTTP データ コレクター API](log-analytics-data-collector-api.md)、システムのイベント ログの一部として収集されるカスタム データなど、さまざまな方法による収集が可能です。 これらはすべてプライベート データを含んでいる可能性があり、そのようなデータが存在するかどうかを確認するために調べる必要があります。
* "*ソリューションによって収集されたデータ*": ソリューションのメカニズムは変更可能です。そのため、コンプライアンスを確保するために、ソリューションによって生成されたすべてのテーブルを確認することをお勧めします。

## <a name="how-to-export-and-delete-private-data"></a>プライベート データをエクスポートして削除する方法

「[個人データの処理に関する戦略](#strategy-for-personal-data-handling)」セクションで先ほど述べたとおり、可能な場合はデータ収集ポリシーを再構築することが__強く__推奨されます。プライベート データの収集を無効にするか、難読化または匿名化を行ってください。そうでなければ、"プライベート" と見なされるデータがなくなるよう修正してください。 データの処理を行う場合、戦略を定義して自動化するコスト、顧客が問題なくデータを操作できるインターフェイスを作成するコスト、継続的なメンテナンス コストがまず、お客様とそのチームにかかります。 そのうえ、Log Analytics で多額の計算コストがかかります。また、クエリ API または消去 API の同時呼び出しが大量に発生して、Log Analytics 機能に対するすべての他の操作に悪影響が及ぶ可能性があります。 とは言うものの、実際には、プライベート データを収集する必要があるシナリオが有効な場合もあります。 このような場合、このセクションで説明されているとおりデータを処理する必要があります。

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>表示とエクスポート

データ要求の表示とエクスポートの両方に[クエリ API](https://dev.loganalytics.io/) を使用する必要があります。 必要な場合、ユーザーに提供するためにデータの形式を適切なものに変換するロジックを実装できます。 そのようなロジックをホストするには、[Azure Functions](https://azure.microsoft.com/services/functions/) が適しています。

### <a name="delete"></a>削除

> [!WARNING]
> Log Analytics の削除は破壊的であり、元に戻せません。 実行には細心の注意を払ってください。

プライバシー処理の一環として、Microsoft は "*消去*" API パスを提供しています。 実行に関するリスク、パフォーマンスへの潜在的な影響、Log Analytics データの総集計や測定などにおけるスキューの発生の可能性があり、このパスは慎重に使用する必要があります。 プライベート データを処理する別のアプローチについては、「[個人データの処理に関する戦略](#strategy-for-personal-data-handling)」セクションを参照してください。

消去は高度な特権が必要な操作であり、Azure Resource Manager でロールが明示的に付与されない限り、Azure のアプリまたはユーザーは (リソース所有者でさえも) 実行のアクセス許可を得られません。 このロールは "_データ消去者_" であり、データ損失の可能性があるため慎重に委任する必要があります。 

Azure Resource Manager ロールが割り当てられると、2 つの新しい API パスが利用できるようになります。 

* [消去の POST] (https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) - 削除するデータのパラメーターを指定するオブジェクトを受け取り、参照 GUID を返します。 
* GET purge status - POST purge 呼び出しでは、実行した消去 API の状態を確認するために呼び出せる URL が含まれる、'x-ms-status-location' ヘッダーが返されます。 例: 

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

ほとんどの消去操作は Microsoft の SLA よりもはるかに早く完了すると考えられます。しかし、Log Analytics によって使用されるデータ プラットフォームへの重大な影響があるため、消去操作の完了の正式な SLA は 30 日に設定されています。 

## <a name="next-steps"></a>次の手順
データの収集方法、処理方法、保護方法については、「[Log Analytics data security (Log Analytics データのセキュリティ)](log-analytics-data-security.md)」を参照してください。