---
title: Azure Application Insights に格納される個人データに関するガイダンス | Microsoft Docs
description: この記事では、Azure Application Insights に格納される個人のデータを管理する方法と、それを識別して削除する方法について説明します。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.reviewer: Evgeny.Ternovsky
ms.author: mbullwin
ms.openlocfilehash: a59b57c546f18a7d91160f2ae7282af82fc42160
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044715"
---
# <a name="guidance-for-personal-data-stored-in-application-insights"></a>Application Insights に格納される個人データに関するガイダンス

Application Insights は、個人データが見つかる可能性が高いデータ ストアです。 この記事では、Application Insights 内でこうしたデータが通常見つかる場所と、このデータの処理に使用できる機能について説明します。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>個人データの処理に関する戦略

プライベート データを処理する際の戦略を策定するとしたら、それを最終的に行うのはお客様とその会社になりますが、考えられるアプローチをいくつか以下に紹介します。 これらは、技術的な観点から優先するべき順序で (上位から下位へ) 記載されています。
* 可能であれば、データの収集をやめるか、収集されるデータの難読化や匿名化を行います。または、その他の方法で収集されるデータを調整して、_プライベート_と見なされないようにデータを除外します。 この方法が望ましいアプローチで、コストがかかり影響が大きなデータ処理戦略を作成する必要がありません。
* 不可能であれば、データの正規化を試みて、データ プラットフォームやパフォーマンスへの影響を軽減します。 たとえば、明示的なユーザー ID を記録するのではなくデータへの参照を作成し、それにより、ユーザー名とユーザーの詳細を、後で別の場所に記録できる内部 ID に関連付けます。 そうすることで、あるユーザーから個人情報の削除を依頼された場合に、そのユーザーに対応するルックアップ テーブルの行を削除するだけで十分な対応となります。 
* 最後は、プライベート データを収集する必要がある場合は、ユーザーに関連付けられるすべてのプライベート データのエクスポートと削除に関連して課される可能性のある義務を満たすため、消去の API パスと既存のクエリ API パスに関するプロセスを構築します。

## <a name="where-to-look-for-private-data-in-application-insights"></a>Application Insights 内でプライベート データを探す場所

Application Insights は非常に柔軟性の高いストアで、データのスキーマを指定するのと同時に、どのフィールドもカスタム値で上書きすることができます。 そのような特徴のため、特定のアプリケーションのどこでプライベート データが見つかるかを正確に言うことができません。 しかし、インベントリ内の以下の場所から始めるのが適切です。

* *IP アドレス*: Application Insights は既定で、すべての IP アドレス フィールドを "0.0.0.0" に難読化しますが、セッション情報を管理するために、この値を実際のユーザー IP で上書きするのがかなり一般的なパターンです。 Analytics の次のクエリを使用すると、過去 24 時間にわたって、IP アドレス以外の列に "0.0.0.0" 以外の値が含まれるすべてのテーブルを検索できます。
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *ユーザー ID*: 既定では、Application Insights は、ユーザーとセッションの追跡のために、ランダムに生成された ID を使用します。 しかし、これらのフィールドは、アプリケーションにとってより関連性の高い ID を格納するために上書きされるのが一般的です。 たとえば、ユーザー名や AAD GUID などです。これらの ID は多くの場合、個人データの範囲にあると見なされます。そのため、適切に処理する必要があります。 これらの ID については、常に難読化または匿名化を試みることをお勧めします。 これらの値がよく見つかるフィールドには、session_Id、user_Id、user_AuthenticatedId、user_AccountId、customDimensions などがあります。
* *カスタム データ*: Application Insights では、どのデータ型にもカスタム ディメンションのセットを追加できます。 これらのディメンションは、*どのような*データでもありえます。 過去 24 時間にわたって収集されたすべてのカスタム ディメンションを識別するには、次のクエリを使用します。
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *メモリ内および転送中のデータ*: Application Insights では、例外、要求、依存関係の呼び出し、およびトレースが追跡されます。 プライベート データは、多くの場合、コードや HTTP 呼び出しのレベルで収集できます。 例外、要求、依存関係、およびトレース テーブルを確認して、このようなデータをすべて識別します。 可能な場所では[テレメトリ初期化子](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling)を使用してこのデータを難読化します。
* *スナップショット デバッガーのキャプチャ*: Application Insights の[スナップショット デバッガー](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)機能では、アプリケーションの実稼働インスタンスで例外がキャッチされるたびにデバッグのスナップショットを収集できます。 スナップショットでは、例外と、スタックに含まれるすべてのステップのローカル変数の値にたどり着く完全なスタック トレースが公開されます。 残念ながら、この機能では、スナップ ポイントを選択的に削除したり、スナップショット内のデータにプログラムからアクセスしたりすることができません。 そのため、既定のスナップショット保有期間のペースではお客様のコンプライアンス要件が満たされない場合は、この機能を無効にすることをお勧めします。

## <a name="how-to-export-and-delete-private-data"></a>プライベート データをエクスポートして削除する方法

可能であれば、プライベート データの収集を無効にするか、難読化または匿名化を行うようにデータ収集ポリシーを再構築することを__強く__お勧めします。またはその他の方法でデータに変更を加え、それが "プライベート" と見なされないように取り除いてください。 データが収集された後にその処理を行うと、お客様とそのチームに、戦略を定義して自動化するコスト、顧客がデータ全体を操作するためのインターフェイスを構築するコスト、継続的なメンテナンス コストが生じることになります。 さらに、それは Application Insights の計算コストを増やすことになり、クエリや消去の API 同時呼び出しが大量に発生して、Application Insights の機能との他の操作すべてに悪影響が及ぶ可能性があります。 とは言え、プライベート データを収集する必要があるシナリオが有効な場合が確かにあります。 このような場合、このセクションで説明されているとおりデータを処理する必要があります。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>表示とエクスポート

データ要求の表示とエクスポートの両方に[クエリ API](https://dev.applicationinsights.io/quickstart) を使用する必要があります。 必要に応じて、ユーザーに提供するためにデータの形式を適切なものに変換するロジックを実装できます。 [Azure Functions](https://azure.microsoft.com/services/functions/) は、そのようなロジックをホストするのに適した場所です。

### <a name="delete"></a>Delete

> [!WARNING]
> Application Insights での削除は破壊的であり、元に戻せません。 その実行には細心の注意を払ってください。

Microsoft はプライバシーの取り扱いに関する構想の一環として、"消去" の API パスを提供してきました。 このパスは、控え目に使用する必要があります。実施に伴うリスクやパフォーマンスへの潜在的影響があり、Application Insights データの全体の集計、測定、およびその他の側面をゆがめる可能性があるためです。 プライベート データを処理する別の方法については、前述の「[個人データの処理に関する戦略](#strategy-for-personal-data-handling)」を参照してください。

消去は高い特権が必要な操作で、Azure Resource Manager でロールが明示的に付与されない限り、Azure のアプリやユーザーは (リソースの所有者であっても)、実行のアクセス許可を得られません。 このロールは_データ消去者_で、データ損失の可能性があるため委任は慎重に行う必要があります。

Azure Resource Manager ロールが割り当てられると、2 つの新しい API パスが使用可能になります。完全な開発者向けドキュメントと呼び出し形式がリンクされています。

* [POST purge](https://docs.microsoft.com/rest/api/application-insights/components/purge) - 削除するデータのパラメーターを指定するオブジェクトを受け取り、参照 GUID を返します
* GET purge status - POST purge 呼び出しでは、実行した消去 API の状態を確認するために呼び出せる URL が含まれる、'x-ms-status-location' ヘッダーが返されます。 例: 
   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  消去操作の大部分は、Application Insights で使用されるデータ プラットフォームへの影響が大きいため、SLA よりもずっと短期間に完了する場合があります。**消去操作の完了についての SLA は、30 日に設定されています**。

## <a name="next-steps"></a>次の手順
データがどのように収集、処理、セキュリティで保護されるかの詳細については、[Application Insights データのセキュリティ](app-insights-data-retention-privacy.md)に関する記事を参照してください。