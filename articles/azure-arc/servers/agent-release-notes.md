---
title: Azure Arc 対応サーバー エージェントの新機能
description: この記事には、Azure Arc 対応サーバー エージェントのリリース ノートが含まれています。 まとめられている問題の多くについては、追加詳細のリンクがあります。
ms.topic: overview
ms.date: 09/01/2021
ms.custom: references_regions
ms.openlocfilehash: d4008a41629ac2e71e1abdb91e30f2d6b9350538
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431606"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Azure Arc 対応サーバー エージェントの新機能

Azure Arc 対応サーバーの Connected Machine エージェントは、継続的に改善が図られています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正

このページは毎月更新されるため、定期的にアクセスしてご確認ください。 6 か月以上前の項目を探す場合は、「[Azure Arc 対応サーバー エージェントの新着情報のアーカイブ](agent-release-notes-archive.md)」を参照してください。

## <a name="august-2021"></a>2021 年 8 月

バージョン 1.10

### <a name="fixed"></a>固定

- ゲスト構成ポリシー エージェントで、システム設定を構成および修復できるようになりました。 既存のポリシーの割り当ては、引き続き監査のみとなります。 Azure Policy の[ゲスト構成の修復オプション](../../governance/policy/concepts/guest-configuration-policy-effects.md)について詳細を確認してください。
- 現在、ゲスト構成ポリシー エージェントは、6 時間ごとではなく、48 時間ごとに再起動されます。

## <a name="july-2021"></a>2021 年 7 月

バージョン 1.9

## <a name="new-features"></a>新機能

インドネシア語のサポートを追加しました

### <a name="fixed"></a>固定

米国西部 3 リージョンで拡張機能の管理ができないバグを修正しました

バージョン 1.8

### <a name="new-features"></a>新機能

- Red Hat および CentOS システムに Azure Monitor Agent 拡張機能をインストールする際の信頼性が向上しました
- リソース名の最大長 (54 文字) をエージェント側で強制する機能を追加しました
- ゲスト構成ポリシーの機能強化:
  - Linux オペレーティング システム上で PowerShell ベースのゲスト構成ポリシーのサポートを追加しました
  - 同一サーバー上で同じゲスト構成ポリシーを複数回割り当てることができるようになりました
  - Windows オペレーティング システム上の PowerShell Core をバージョン 7.1 にアップグレードしました

### <a name="fixed"></a>固定

- Windows アプリケーションのイベント ログにサービスの開始または停止イベントを書き込むことができない場合、エージェントの実行が継続されるようになります。

## <a name="june-2021"></a>2021 年 6 月

バージョン 1.7

### <a name="new-features"></a>新機能

- オンボード時の信頼性が向上しました。
  - HIMDS を使用できない場合の再試行ロジックが向上しました
  - OS 情報を取得できない場合、オンボードを中断せずに継続するようになります
- Red Hat および CentOS システムに OMS エージェント拡張機能をインストールする際の信頼性が向上しました

## <a name="may-2021"></a>2021 年 5 月

バージョン 1.6

### <a name="new-features"></a>新機能

- SUSE Enterprise Linux 12 のサポートを追加しました
- ゲスト構成エージェントがバージョン 1.26.12.0 に更新され、次のものが含まれるようになりました。

   - ポリシーは個別のプロセスで実行されます。
   - 拡張機能検証用の V2 署名サポートが追加されました。
   - データ ログのマイナーな更新。

## <a name="april-2021"></a>2021 年 4 月

バージョン 1.5

### <a name="new-features"></a>新機能

- Red Hat Enterprise Linux 8 および CentOS Linux 8 のサポートが追加されました。
- エラーおよび詳細出力を stderr に送る新しい `-useStderr` パラメーター。
- 出力結果を JSON 形式 (-useStderr と共に使用するとき) で送る新しい `-json` パラメーター。
- 他のインスタンス メタデータとして製造元、モデル、クラスター リソース ID (Azure Stack HCI ノードの場合) を収集します。
 
## <a name="next-steps"></a>次のステップ

- 複数のハイブリッド マシンにまたがって Arc 対応サーバーを評価するか、または有効にする前に、[Connected Machine エージェントの概要](agent-overview.md)を確認して、要件、エージェントに関する技術的な詳細、デプロイ方法を理解してください。

- [計画と展開ガイド](plan-at-scale-deployment.md)を参照して、任意の規模で Azure Arc 対応サーバーをデプロイし、一元的な管理と監視を実装する計画を立ててください。
