---
title: OMS から Azure にアラートを拡張する - 概要 | Microsoft Docs
description: アラートを OMS から Azure Alerts に拡張するプロセスの概要と、お客様の一般的な懸念に関する詳細について説明します。
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: vinagara
ms.openlocfilehash: 045a7f97d9c4d380e83325c04c209a6afcc761a7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="extend-alerts-from-oms-into-azure"></a>OMS から Azure にアラートを拡張する
新しいアラートのエクスペリエンスにより、Microsoft Azure にさまざまなサービスを超えたアラート エクスペリエンスが統合されました。 この新しいエクスペリエンスは、Portal の Azure Monitor で**アラート**として使用可能で、Log Analytics や Application Insights のアクティビティ ログのアラート、メトリックのアラート、ログのアラートを共通の場所で管理できます。 

ただし、ユーザーによっては Log Analytics やアラートなどの類似の機能を、[Microsoft Operation Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) を通じて使用しています。 今後 Log Analytics を使用しつつ Azure のその他のリソースを簡単に管理できるように、マイクロソフトは体系的に Azure Portal でも OMS の機能を使用できるよう進めています。 その点において、Azure のアラート機能ではすでに ユーザーに Log Analytics のクエリベースのアラートを管理することを許可しています。詳細については、[Azure Alerts のログ アラート](monitor-alerts-unified-log.md)に関するページを参照してください。 Azure Monitor のアラート機能には、OMS で作成したアラートが該当する Log Analytics ワークスペースの下にすでに列挙されています。 ただし、OMS で作成したこれらのアラートを編集または変更するには、Azure を離れて OMS で行う必要があります。その後、その他のサービスを管理する必要がある場合は、Azure に戻ります。 この手間を減らすために、マイクロソフトでは現在、ユーザーによる OMS のアラートの Azure への拡張を有効にするよう進めています。

## <a name="benefits-of-extending-your-alerts"></a>アラートを拡張するメリット
アラートを OMS から Azure に拡張する利点は、Azure Portal から離れる必要がなくなる以外にもあります。

- 作成および表示できるアラートの数が 250 に限定されている OMS とは異なり、Azure のアラート機能にはこの制限がありません。
- Azure のアラート機能では、すべての種類のアラートを管理、列挙、および表示できます。OMS のように Log Analytics のアラートには限定されません。
- Azure のアラート機能では[アクション グループ](monitoring-action-groups.md)を使用して、各アラートにSMS、音声通話、Automation Runbook、Webhook、ITSM Connector など、複数のアクションを設定できます。 OMS のアラート機能では、設定できるアクションの数や種類が限定されています。

## <a name="process-of-extending-your-alerts"></a>アラートを拡張するプロセス
アラートを OMS から Azure に拡張するプロセスには、アラートの定義、クエリ、構成の変更は**伴いません**。 唯一必要な変更は、Azure では電子メールの通知、Webhook の呼び出し、Automation Runbook の実行、ITSM ツールへの接続などのアクションはすべてアクション グループを介して行います。 このため、適切なアクション グループがアラートに関連付けられている場合、それらは Azure に拡張されます。

アラートを拡張するプロセスはアラートの破棄や中断を伴わないため、マイクロソフトでは **2018 年 4 月 23 日**より Operations Management Suite (OMS) で作成されたアラートを自動的に Azure のアラート機能に拡張します。 この日より、マイクロソフトでは Azure にアラートを拡張するスケジュールの設定を開始し、徐々に OMS に存在するすべてのアラートを Azure Portal で管理できるようにします。 

OMS ワークスペース内のアラートが Azure に拡張されるスケジュールが設定されても、それらのアラートは引き続き機能し、監視を邪魔することは**ありません**。 スケジュールが設定されると、アラートを一時的に変更/修正できなくなる場合があります。ただし、新しい Azure のアラートで短期間で作成できます。 この短い時間の間に OMS でアラートの編集や作成が行われた場合、Azure Log Analytics または Azure Alerts で続行するかを選択できます。

 ![スケジュールが設定された期間に Azure にリダイレクトされたアラートのユーザー アクション](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Operations Management Suite (OMS) から Azure へのアラートの拡張にコストは発生しません。また、クエリベースの Log Analytics のアラートでの Azure のアラート機能の使用は、[Azure Monitor の価格ポリシー](https://azure.microsoft.com/en-us/pricing/details/monitor/)に設定された制限や条件内の使用であれば、課金されません。  

ユーザーはアラートを Azure で管理するよう自主的に切り替えることで、この日付の前でもアラートを拡張してそのメリットを享受できます。

### <a name="how-to-voluntarily-extending-your-alerts"></a>アラートを自主的に拡張する方法
OMS のユーザーが Azure のアラート機能に簡単に切り替えられるように、マイクロソフトではアラートの拡張に役立つツールを作成しています。 Microsoft Operations Management Suite (OMS) のお客様は、OMS ポータルのウィザードを使用するか、新しい API を使用してプログラムでアラートを Azure に拡張できます。 詳細については、[OMS ポータルおよび API を使用してアラートを Azure に拡張する方法](monitoring-alerts-extend-tool.md)に関するページを参照してください。


## <a name="usage-after-extending-your-alerts"></a>アラート拡張後の使用について
ここまでで述べたように、Microsoft Operation Management Suite で作成したアラートは Azure Alerts に拡張されます。その後、それらのアラートを Azure で管理できるようになります。 アラートは引き続き OMS ポータルの アラート設定セクションに表示されます。 以下の図に示します。

 ![Azure に拡張された後も OMS ポータルにアラートが表示される](./media/monitor-alerts-extend/PostExtendList.png)

アラートの編集や作成などの操作を OMS ポータルで行うと、ユーザーは透過的に Azure Alerts に誘導されます。 アラートの作成は前と同じように引き続き既存の [Log Analytics API](../log-analytics/log-analytics-api-alerts.md) から行います。ただし、Azure に拡張された後はスケジュールにアクション グループを関連付ける必要があります。

## <a name="next-steps"></a>次の手順

* [OMS から Azure へのアラート拡張の開始する](monitoring-alerts-extend-tool.md)ためのツールについて学習する。
* 新しい[Azure のアラート機能のエクスペリエンス](monitoring-overview-unified-alerts.md)の詳細について学習する。
* [Azure Alerts のログ アラート](monitor-alerts-unified-log.md)について学習する。
