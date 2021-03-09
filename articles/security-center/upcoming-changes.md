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
ms.date: 01/25/2021
ms.author: memildin
ms.openlocfilehash: a2c29049decc056f0d3c8083d21574456610c124
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555132"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center に対する今後の重要な変更

> [!IMPORTANT]
> このページの情報は、販売を開始する前に大幅に変更される可能性があるプレリリース製品または機能に関連しています。 Microsoft は、ここで提供される情報に対して、明示または黙示を問わず、一切の確約または保証を行わないものとします。

このページでは、Security Center に対して計画されている変更について説明します。 ここでは、セキュリティ スコアやワークフローなどに影響する可能性がある、製品に対して計画されている変更について説明します。

最新のリリース ノートをお探しの場合は、「[Azure Security Center の最新情報](release-notes.md)」をご覧ください。


## <a name="planned-changes"></a>計画されている変更

- ["システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [SQL データ分類の推奨事項を改善](#enhancements-to-sql-data-classification-recommendation)
- [11 個の Azure Defender アラートの非推奨化](#deprecation-of-11-azure-defender-alerts)

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>"システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化 

**変更予定日:** 2021 年 2 月

次の 2 つの推奨事項が 2021 年 2 月に非推奨化される予定です。

- **システムの更新プログラムを適用するには、マシンを再起動する必要があります**。 これが原因でセキュア スコアに若干の影響が生じる可能性があります。
- **お使いのマシンに監視エージェントをインストールする必要があります**。 この推奨事項は、オンプレミスのマシンにのみ関係します。また、そのロジックのいくつかは、別の推奨事項 (**お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある**) に移される予定です。 これが原因でセキュア スコアに若干の影響が生じる可能性があります。

連続エクスポートとワークフロー自動化の構成をチェックして、該当する推奨事項が含まれているかどうかを確認することをお勧めします。 また、ダッシュボードまたはそれらを使用する他の監視ツールを適宜更新する必要があります。

これらの推奨事項の詳細については、[セキュリティに関する推奨事項のリファレンス ページ](recommendations-reference.md)を参照してください。


### <a name="enhancements-to-sql-data-classification-recommendation"></a>SQL データ分類の推奨事項を改善

**変更予定日:** 2021 年第 2 四半期

"**データ分類の適用**" セキュリティ コントロールにおける推奨事項 "**SQL データベースの機密データを分類する必要がある**" は、より Microsoft のデータ分類戦略に沿った新しいバージョンで置き換えられる予定です。 そのため、推奨事項の ID も変更されます (現在は b0df6f56-862d-4730-8597-38c0fd4ebd59)。


### <a name="deprecation-of-11-azure-defender-alerts"></a>11 個の Azure Defender アラートの非推奨化

**変更予定日:** 2021 年 3 月

来月、以下に示す 11 個の Azure Defender アラートが非推奨となります。

- 次の 2 つのアラートは、新しいアラートで置き換えられ、より適切なカバレッジが提供されます。

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | PREVIEW - MicroBurst toolkit "Get-AzureDomainInfo" function run detected (プレビュー - MicroBurst ツールキット "Get-AzureDomainInfo" 関数の実行が検出されました) |
    | ARM_MicroBurstRunbook    | PREVIEW - MicroBurst toolkit "Get-AzurePasswords" function run detected (プレビュー - MicroBurst ツールキット "Get-AzurePasswords" 関数の実行が検出されました)  |
    |                          |                                                                          |

- 次の 9 つのアラートは、既に非推奨になっている Azure Active Directory Identity Protection コネクタに関連したものです。

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | 通常とは異なるサインイン プロパティ |
    | AnonymousLogin      | 匿名 IP アドレス          |
    | InfectedDeviceLogin | マルウェアにリンクした IP アドレス     |
    | ImpossibleTravel    | 特殊な移動               |
    | MaliciousIP         | 悪意のある IP アドレス          |
    | LeakedCredentials   | 漏洩した資格情報            |
    | PasswordSpray       | パスワード スプレー                |
    | LeakedCredentials   | Azure AD 脅威インテリジェンス  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 



## <a name="next-steps"></a>次のステップ

製品に対する最近のすべての変更については、「[Azure Security Center の最新情報](release-notes.md)」を参照してください。
