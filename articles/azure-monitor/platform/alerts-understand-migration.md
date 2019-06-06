---
title: Azure Monitor アラートに対する自主的移行ツールのしくみを理解する
description: アラート移行ツールのしくみと問題のトラブルシューティングの方法について説明します。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: b5a13254fc9dfd58db83a1bc8b9dd071cfbbdab2
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015584"
---
# <a name="understand-how-the-migration-tool-works"></a>移行ツールの動作の理解

[以前発表された](monitoring-classic-retirement.md)ように、Azure Monitor のクラシック アラートは 2019 年 9 月に廃止される予定です (当初は 2019 年 7 月に廃止予定でした)。 Azure portal には、クラシック アラート ルールを使用しているお客様と移行を自分でトリガーしたいと思っているお客様を対象に、移行ツールが用意されています。

この記事では、自主的移行ツールのしくみについて説明します。 また、一般的な問題をいくつか取り上げて、その解決方法を紹介します。

> [!NOTE]
> 移行ツールの展開が遅れたことで、クラシック アラート移行の提供終了日が、最初に発表された 2019 年 6 月 30 日から [2019 年 8 月 31日に延長](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)されました。

## <a name="which-classic-alert-rules-can-be-migrated"></a>移行可能なクラシック アラート ルール

ほぼすべてのクラシック アラート ルールをツールで移行できますが、例外がいくつかあります。 次のアラート ルールは、ツールを使用して (または 2019 年 9 月 の自動移行時に) 移行されません。

- 仮想マシンのゲスト メトリックに対するクラシック アラート ルール (Windows と Linux の両方)。 この記事の後半の[新しいメトリック アラートでこれらのアラート ルールを再作成するためのガイダンス](#guest-metrics-on-virtual-machines)を参照してください。
- クラシック ストレージ メトリックに対するクラシック アラート ルール。 [お使いのクラシック ストレージ アカウントの監視に関するガイダンス](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)を参照してください。
- 一部のストレージ アカウント メトリックに対するクラシック アラート ルール。 この記事の後半で[詳細](#storage-account-metrics)をご確認ください。

お使いのサブスクリプションにこれらのクラシック ルールが含まれている場合、そのルールは手動で移行する必要があります。 自動移行を提供することができないため、これらの種類の既存のクラシック メトリック アラートは 2020年 6 月まで動作し続ける予定です。 この拡張機能により、新しいアラートに移行する時間が確保されます。 ただし、2019 年 8 月を過ぎると、新しいクラシック アラートを作成することはできません。

### <a name="guest-metrics-on-virtual-machines"></a>仮想マシンのゲスト メトリック

ゲスト メトリックに新しいメトリック アラートを作成するには、その前にゲスト メトリックを Azure Monitor カスタム メトリック ストアに送信する必要があります。 次の手順に従って、診断設定で Azure Monitor シンクを有効にします。

- [Windows VM のゲスト メトリックの有効化](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Linux VM のゲスト メトリックの有効化](collect-custom-metrics-linux-telegraf.md)

これらの手順が完了したら、ゲスト メトリックで新しいメトリック アラートを作成できます。 新しいメトリック アラートを再作成したら、クラシック アラートを削除できます。

### <a name="storage-account-metrics"></a>ストレージ アカウントのメトリック

これらのメトリックに対するアラートを除いて、ストレージ アカウントに対するすべてのクラシック アラートを移行できます。

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

パーセント メトリックに対するクラシック アラート ルールは、[従来のストレージ メトリックと新しいストレージ メトリック間のマッピング](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)に基づいて移行する必要があります。 使用可能な新しいメトリックは絶対値であるため、しきい値を適切に変更する必要があります。

AnonymousThrottlingError および SASThrottlingError に関するクラシック アラート ルールについては、同様の機能を提供する結合されたメトリックがないため、2 つの新しいアラートに分割する必要があります。 しきい値を適切に調整する必要があります。

## <a name="rollout-phases"></a>展開の段階

移行ツールは、クラシック アラート ルールを使用しているすべてのお客様に対して段階的に展開中です。 ツールを使用してサブスクリプションを移行する準備ができたら、サブスクリプションの所有者に電子メールが届きます。

> [!NOTE]
> ツールは段階的に展開されているため、初期段階では、ほとんどのサブスクリプションがまだ移行できる状態ではない可能性があります。

現時点では、サブスクリプションのサブセットが、移行準備完了としてマークされます。 サブセットに含まれるのは、これらのサブスクリプションのうち、次のリソースの種類に関するクラシック アラート ルールを持つものだけです。 他のリソースの種類に対するサポートは、今後の段階で追加されます。

- Microsoft.apimanagement/service
- Microsoft.batch/batchaccounts
- Microsoft.cache/redis
- Microsoft.datafactory/datafactories
- Microsoft.dbformysql/servers
- Microsoft.dbforpostgresql/servers
- Microsoft.eventhub/namespaces
- Microsoft.logic/workflows
- Microsoft.network/applicationgateways
- Microsoft.network/dnszones
- Microsoft.network/expressroutecircuits
- Microsoft.network/loadbalancers
- Microsoft.network/networkwatchers/connectionmonitors
- Microsoft.network/publicipaddresses
- Microsoft.network/trafficmanagerprofiles
- Microsoft.network/virtualnetworkgateways
- Microsoft.search/searchservices
- Microsoft.servicebus/namespaces
- Microsoft.streamanalytics/streamingjobs
- Microsoft.timeseriesinsights/environments
- Microsoft.web/hostingenvironments/workerpools
- Microsoft.web/serverfarms
- Microsoft.web/sites

## <a name="who-can-trigger-the-migration"></a>移行をトリガーできるユーザー

サブスクリプション レベルで、組み込みロールの監視共同作業者を割り当てられたすべてのユーザーが移行をトリガーできます。 また、次の権限を持つカスタム ロールが割り当てられたユーザーも移行をトリガーできます。

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-problems-and-remedies"></a>一般的な問題と解決方法

[移行をトリガー](alerts-using-migration-tool.md)したら、移行が完了したこと、または何らかのアクションが必要なことを通知するメールが、指定したアドレスに届きます。 このセクションでは、一般的な問題とその対処方法について説明します。

### <a name="validation-failed"></a>検証に失敗しました

サブスクリプション内のクラシック アラートに対する最近のいくつかの変更が原因で、サブスクリプションを移行できません。 これは一時的な問題です。 移行の状態が **[Ready for migration]\(移行準備完了\)** に戻ったら、移行を再開できます。

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>ポリシーまたはスコープ ロックによりルールを移行できません

移行の一環として、新しいメトリック アラートと新しいアクション グループが作成され、クラシック アラート ルールが削除されますが、 ポリシーまたはスコープ ロックがあるため、リソースを作成できません。 ポリシーまたはスコープ ロックによって、一部またはすべてのルールを移行できませんでした。 この問題を解決するには、スコープ ロックまたはポリシーを一時的に削除して、移行を再度トリガーします。

## <a name="next-steps"></a>次の手順

- [移行ツールの使用方法](alerts-using-migration-tool.md)
- [移行を準備する](alerts-prepare-migration.md)
