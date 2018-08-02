---
title: Azure Monitor (プレビュー) を使用して Azure Active Directory のログを Splunk と統合する方法 | Microsoft Docs
description: Azure Monitor (プレビュー) を使用して Azure Active Directory のログを Splunk と統合する方法について説明します
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d1dd62d06c7e3ed634795604ce9660694ea073ca
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240447"
---
# <a name="integrate-azure-active-directory-logs-with-splunk-using-azure-monitor-preview"></a>Azure Monitor (プレビュー) を使用して Azure Active Directory のログを Splunk と統合する

この記事では、Azure Monitor を使用して Azure Active Directory のログを Splunk と統合する方法について説明します。 まず、ログを Azure イベント ハブにルーティングしてから Splunk に統合する必要があります。

## <a name="prerequisites"></a>前提条件

1. Azure イベント ハブに Azure AD アクティビティ ログが含まれている。 [アクティビティ ログをイベント ハブにストリーミングする](reporting-azure-monitor-diagnostics-azure-event-hub.md)方法を確認してください。 
2. 以下の[手順](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)を使用して、Splunk 用の Azure モニター アドオンをダウンロードし、Splunk インスタンスを構成します。

## <a name="tutorial"></a>チュートリアル 

1. Splunk インスタンスを開き、**[Data Summary]\(データの概要\)** をクリックします。
    ![[Data Summary]\(データの概要\)](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png "[Data Summary]\(データの概要\)")

2. **[Sourcetypes]\(ソースの種類\)** タブに移動し、**amal: aadal:audit** を選択します。![[Sourcetypes]\(ソースの種類\) タブ](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png "[Sourcetypes]\(ソースの種類\) タブ")

3. 次の図のような Azure AD アクティビティ ログが表示されます。
    ![アクティビティ ログ](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png "アクティビティ ログ")

> [!NOTE]
> アドオンを Splunk インスタンスにインストールできない場合 (たとえば、プロキシを使用している場合、Splunk Cloud で実行している場合など)、[イベント ハブの新しいメッセージによってトリガーされるこの Azure Function](https://github.com/Microsoft/AzureFunctionforSplunkVS) を使用して、Splunk HTTP イベント コレクターにこれらのイベントを転送できます。 
>

## <a name="next-steps"></a>次の手順

* [Azure Monitor で監査ログのスキーマを解釈する](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Azure Monitor でサインイン ログのスキーマを解釈する](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [よく寄せられる質問と既知の問題](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)