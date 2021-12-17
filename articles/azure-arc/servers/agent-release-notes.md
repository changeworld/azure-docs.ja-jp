---
title: Azure Arc 対応サーバー エージェントの新機能
description: この記事には、Azure Arc 対応サーバー エージェントのリリース ノートが含まれています。 まとめられている問題の多くについては、追加詳細のリンクがあります。
ms.topic: overview
ms.date: 09/01/2021
ms.custom: references_regions
ms.openlocfilehash: 027d682a6f9727edb7ce39ac1eeea9947b2e4957
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128628536"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Azure Arc 対応サーバー エージェントの新機能

Azure Arc 対応サーバーの Connected Machine エージェントは、継続的に改善が図られています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正

このページは毎月更新されるため、定期的にアクセスしてご確認ください。 6 か月以上前の項目を探す場合は、「[Azure Arc 対応サーバー エージェントの新着情報のアーカイブ](agent-release-notes-archive.md)」を参照してください。

## <a name="september-2021"></a>2021 年 9 月

バージョン 1.11

### <a name="fixed"></a>固定

- エージェントは、[System objects: Require case insensitivity for non-Windows subsystems](/windows/security/threat-protection/security-policy-settings/system-objects-require-case-insensitivity-for-non-windows-subsystems) (システム オブジェクト: Windows 以外のサブシステムに対して大文字小文字を区別しないことを要求する) ポリシーを [無効] に設定して、Windows システムにインストールできるようになりました。
- サービスの開始または再起動イベント中にエラーが発生した場合、ゲスト構成ポリシー エージェントで自動的に再試行が行われるようになりました。
- Linux マシンでゲスト構成の監査ポリシーを正常に実行できない問題を修正しました。

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

## <a name="next-steps"></a>次のステップ

- 複数のハイブリッド マシンにまたがって Azure Arc 対応サーバーを評価または有効にする前に、[Connected Machine エージェントの概要](agent-overview.md)に関するページを確認して、要件、エージェントに関する技術的な詳細、デプロイ方法を理解してください。

- [計画と展開ガイド](plan-at-scale-deployment.md)を参照して、任意の規模で Azure Arc 対応サーバーをデプロイし、一元的な管理と監視を実装する計画を立ててください。
