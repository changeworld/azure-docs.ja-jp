---
title: Azure Security Center に対する今後の重要な変更
description: 知っておく必要があり、計画する必要がある Azure Security Center の今後の変更
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/08/2021
ms.author: memildin
ms.openlocfilehash: f9e1b5d19acbc9bcee86c374a3f843530b8adc61
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103761"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center に対する今後の重要な変更

> [!IMPORTANT]
> このページの情報は、販売を開始する前に大幅に変更される可能性があるプレリリース製品または機能に関連しています。 Microsoft は、ここで提供される情報に対して、明示または黙示を問わず、一切の確約または保証を行わないものとします。

このページでは、Security Center に対して計画されている変更について説明します。 ここでは、セキュリティ スコアやワークフローなどに影響する可能性がある、製品に対して計画されている変更について説明します。

最新のリリース ノートをお探しの場合は、「[Azure Security Center の最新情報](release-notes.md)」をご覧ください。


## <a name="planned-changes"></a>計画されている変更

| 計画されている変更                                                                                                                                                        | 変更予定日 |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [21 個の推奨事項をセキュリティ コントロール間で移動](#21-recommendations-moving-between-security-controls)                                                           | 2021 年 4 月                |
| ["システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | 2021 年 4 月                |
| [AWS からの推奨事項を一般提供 (GA) 用にリリース](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | **2021 年 8 月**             |
| [SQL データ分類の推奨事項を改善](#enhancements-to-sql-data-classification-recommendation)                                                     | 2021 年第 2 四半期                   |
|                                                                                                                                                                       |                           |


### <a name="21-recommendations-moving-between-security-controls"></a>21 個の推奨事項をセキュリティ コントロール間で移動 

**変更予定日:** 2021 年 4 月

次の推奨事項は、別のセキュリティ コントロールに移動されます。 セキュリティ コントロールは、関連するセキュリティ推奨事項の論理グループであり、脆弱な攻撃対象領域を反映しています。 この移動によって、これらの各推奨事項は、その目標を達成するために最も適したコントロールに含められます。 

各セキュリティ コントロールに含まれる推奨事項については、「セキュリティ コントロールとその推奨事項」を参照してください。

|推奨 |変更と影響  |
|---------|---------|
|脆弱性評価を SQL サーバー上で有効にする必要がある<br>脆弱性評価を SQL マネージド インスタンス上で有効にする必要がある<br>SQL データベースの脆弱性を新たに修復する必要がある<br>VM 内の SQL データベースの脆弱性を修復する必要があります     |脆弱性の修復 (6 ポイント分) から<br>セキュリティ構成の修復 (4 ポイント分) に移動されます。<br>お客様の環境によっては、これらの推奨事項がスコアに与える影響が少なくなります。|
|複数の所有者がサブスクリプションに割り当てられている必要がある<br>Automation アカウント変数は、暗号化する必要がある<br> IoT デバイス - Auditd プロセスでイベントの送信が停止された<br> IoT デバイス - オペレーティング システムのベースラインの検証に失敗した<br> IoT デバイス - TLS 暗号スイートのアップグレードが必要<br> IoT デバイス - デバイス上でポートを開く<br> IoT デバイス - チェーンのうちの 1 つに制限の緩すぎるファイアウォール ポリシーが見つかりました<br> IoT デバイス - 入力チェーンに制限の緩すぎるファイアウォール ルールが見つかりました<br> IoT デバイス - 出力チェーンに制限の緩すぎるファイアウォール ルールが見つかりました<br>IoT Hub の診断ログを有効にする必要がある<br> IoT デバイス - エージェントで低使用率のメッセージが送信されている<br>IoT デバイス - 既定の IP フィルター ポリシーを拒否にする必要がある<br>IoT デバイス - IP フィルター ルールの IP 範囲が広い<br>IoT デバイス - エージェントのメッセージ間隔とサイズを調整する必要がある<br>IoT デバイス - 認証の資格情報が同一<br>IoT デバイス - 監査対象プロセスでイベントの送信が停止された<br>IoT デバイス - オペレーティング システム (OS) のベースライン構成を修正する必要がある|**セキュリティのベスト プラクティスの実装** に移動されます。<br>推奨事項が「セキュリティのベスト プラクティスの実装」セキュリティ コントロールに移動される (ポイントは発生しない) と、この推奨事項はセキュリティ スコアに影響を及ぼさなくなります。|
|||


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>"システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化

**変更予定日:** 2021 年 4 月

次の 2 つの推奨事項は非推奨になります。

- **クラウド サービス ロールの OS バージョンを更新する必要がある** - Azure では既定で、ゲスト OS は、サービス構成 (.cscfg) に指定した Windows Server 2016 などの OS ファミリ内でサポートされている最新のイメージに定期的に更新されます。
- **Kubernetes Service を脆弱性のない Kubernetes バージョンにアップグレードする必要がある** - この推奨事項の評価は、目標とする広範囲には及んでいません。 この推奨事項の現在のバージョンは、最終的に、よりお客様のセキュリティ ニーズに合った拡張バージョンに置き換えられます。


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>AWS からの推奨事項を一般提供 (GA) 用にリリース

**変更予定日:** 2021 年 8 月

Azure Security Center は、Azure、アマゾン ウェブ サービス (AWS)、および Google Cloud Platform (GCP) のワークロードを保護します。

クラウド コネクタが導入されて以来、AWS Security Hub からの推奨事項はプレビュー段階にあります。 **プレビュー** としてフラグ付けされた推奨事項は、セキュリティ スコアの計算には含まれませんが、プレビュー期間が終了したときにスコアに貢献できるように、可能な限り修復する必要があります。

この変更により、AWS の推奨事項の 2 つのセットが GA に移行します。

- [Security Hub の PCI DSS コントロール](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Security Hub の CIS AWS Foundations Benchmark コントロール](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

これらが一般提供され、AWS リソースで評価が実行されると、結果はすべてのマルチおよびハイブリッド クラウド リソースの統合されたセキュリティ スコアに影響します。



### <a name="enhancements-to-sql-data-classification-recommendation"></a>SQL データ分類の推奨事項を改善

**変更予定日:** 2021 年第 2 四半期

"**データ分類の適用**" セキュリティ コントロールにおける推奨事項 "**SQL データベースの機密データを分類する必要がある**" は、より Microsoft のデータ分類戦略に沿った新しいバージョンで置き換えられる予定です。 これにより、推奨事項の ID も変更されます (現在は b0df6f56-862d-4730-8597-38c0fd4ebd59)。



## <a name="next-steps"></a>次のステップ

製品に対する最近のすべての変更については、「[Azure Security Center の最新情報](release-notes.md)」を参照してください。