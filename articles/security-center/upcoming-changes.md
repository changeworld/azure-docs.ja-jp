---
title: Azure Security Center に対する今後の重要な変更
description: 知っておく必要があり、計画する必要がある Azure Security Center の今後の変更
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: memildin
ms.openlocfilehash: df863372cbf7abfb6fee145b7d13bb00d8deb074
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380167"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center に対する今後の重要な変更

> [!IMPORTANT]
> このページの情報は、販売を開始する前に大幅に変更される可能性があるプレリリース製品または機能に関連しています。 Microsoft は、ここで提供される情報に対して、明示または黙示を問わず、一切の確約または保証を行わないものとします。

このページでは、Security Center に対して計画されている変更について説明します。 ここでは、セキュリティ スコアやワークフローなどに影響する可能性がある、製品に対して計画されている変更について説明します。

最新のリリース ノートをお探しの場合は、「[Azure Security Center の最新情報](release-notes.md)」をご覧ください。


## <a name="planned-changes"></a>計画されている変更

### <a name="system-updates-should-be-installed-on-your-machines-recommendation-getting-sub-recommendations"></a>"システム更新プログラムをマシンにインストールする必要がある" 推奨事項のサブ推奨事項の作成

#### <a name="summary"></a>まとめ

| 側面 | 詳細 |
|---------|---------|
|Announcement date (発表日) | 2020 月 11 月 9 日  |
|この変更の日付  |  2020 年 11 月中旬 |
|影響     | この推奨事項の現在のバージョンからそれに代わるものへの移行の過程で、セキュリティ スコアが変更される可能性があります。 |
|  |  |

"**システム更新プログラムをマシンにインストールする必要がある**" 推奨事項の拡張バージョンをリリースしています。 新しいバージョンにより、システムの更新プログラムの適用セキュリティ コントロールの現在のバージョンは "*置き換えられ*"、次の機能強化が行われます。

- 不足している各更新プログラムに対するサブ推奨事項
- Azure portal の Azure Security Center ページでの再設計されたエクスペリエンス
- Azure Resource Graph からの推奨事項用に強化されたデータ

#### <a name="transition-period"></a>切り替え期間

切り替え期間は約 36 時間です。 中断の可能性を最小限に抑えるため、週末に更新が行われるようにスケジュールされています。 切り替えの過程で、セキュリティ スコアが影響を受ける可能性があります。

#### <a name="redesigned-portal-experience"></a>再設計されたポータルのエクスペリエンス

"**システム更新プログラムをマシンにインストールする必要がある**" に関する推奨事項の詳細ページには、次に示すような結果の一覧が表示されます。 1 つの結果を選択すると、詳細ウィンドウが開き、修復情報および影響を受けるリソースの一覧へのリンクが表示されます。

:::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="更新された推奨事項のポータル エクスペリエンスで、サブ推奨事項のいずれかを開く":::


#### <a name="richer-data-from-azure-resource-graph"></a>Azure Resource Graph からの豊富なデータ

Azure Resource Graph は、効率的なリソース探索を実現するように設計されている Azure のサービスです。 ARG を使用すると、特定のサブスクリプション セットの大規模なクエリを実行し、環境を効果的に管理できます。 

Azure Security Center では、ARG および [Kusto クエリ言語 (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) を使用して、幅広いセキュリティ態勢データに対してクエリを実行できます。

現在のバージョンの "**システム更新プログラムをマシンにインストールする必要がある**" に対するクエリを実行した場合、ARG から利用できるのは、コンピューター上で推奨事項を修復する必要があるという情報だけです。 更新バージョンがリリースされると、次のクエリによって、足りないシステム更新プログラムがコンピューター別にグループ化されて返されます。

```kusto
securityresources
| where type =~ "microsoft.security/assessments/subassessments"
| where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
| where properties.status.code == "Unhealthy"
```

## <a name="next-steps"></a>次のステップ

製品に対する最近のすべての変更については、「[Azure Security Center の最新情報](release-notes.md)」を参照してください。