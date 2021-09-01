---
title: Azure Arc 対応サーバー エージェントの新機能
description: この記事には、Azure Arc 対応サーバー エージェントのリリース ノートが含まれています。 まとめられている問題の多くについては、追加詳細のリンクがあります。
ms.topic: conceptual
ms.date: 07/16/2021
ms.openlocfilehash: d53ebd32c870ce8ec26bca7bcb811fbdd45c58b2
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114392376"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Azure Arc 対応サーバー エージェントの新機能

Azure Arc 対応サーバーの Connected Machine エージェントは、継続的に改善が図られています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正

## <a name="july-2021"></a>2021 年 7 月

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
 
## <a name="march-2021"></a>2021 年 3 月

バージョン 1.4

### <a name="new-features"></a>新機能

- 現在限定プレビュー段階にあるプライベート エンドポイントのサポートを追加しました。
- azcmagent 用の終了コードの詳細な一覧。
- エージェントの構成パラメーターを、`--config` パラメーターを使用してファイルから読み取れるようになりました。
- Microsoft SQL Server がサーバーにインストールされているかどうかを判断できる新しいインスタンスのメタデータを収集します

### <a name="fixed"></a>固定

ネットワーク エンドポイントのチェックが高速になりました。

## <a name="december-2020"></a>2020 年 12 月

バージョン:1.3

### <a name="new-features"></a>新機能

Windows Server 2008 R2 SP1 のサポートを追加。

### <a name="fixed"></a>固定

Linux にカスタム スクリプト拡張機能を正常にインストールできない問題を解決しました。

## <a name="november-2020"></a>2020 年 11 月

バージョン:1.2

### <a name="fixed"></a>固定

RPM ベースのディストリビューションでアップグレード後にプロキシ構成が失われる可能性がある問題が解決されました。

## <a name="october-2020"></a>2020 年 10 月

Version: 1.1

### <a name="fixed"></a>固定

- 代替 GC デーモンのユニット ファイルの場所を処理するようにプロキシ スクリプトが修正されました。
- GuestConfig エージェントの信頼性が変更されています。
- GuestConfig エージェントで US Gov バージニア リージョンがサポートされます。
- GuestConfig エージェント拡張機能によって、エラー発生時により詳細なメッセージが報告されます。

## <a name="september-2020"></a>2020 年 9 月

バージョン:1.0 (一般提供)

### <a name="plan-for-change"></a>変更の計画

- プレビュー エージェント (1.0 より前のすべてのバージョン) のサポートは、将来のサービス更新プログラムで削除される予定です。
- フォールバック エンドポイント `.azure-automation.net` のサポートが削除されました。 プロキシを使用している場合は、エンドポイント `*.his.arc.azure.com` を許可する必要があります。
- Connected Machine エージェントが Azure でホストされている仮想マシンにインストールされている場合は、Arc 対応サーバーのリソースから VM 拡張機能をインストールまたは変更できません。 これは、仮想マシンの **Microsoft.Compute** および **Microsoft.HybridCompute** リソースから競合する拡張機能の操作が実行されることを回避するためです。 マシンの **Microsoft.Compute** リソースをすべての拡張機能の操作に使用します。
- ゲスト構成プロセスの名前が Linux では *gcd* から *gcad* に、Windows では *gcarcservice* から *gcservice* に変更されました。

### <a name="new-features"></a>新機能

- サポートに関する情報を収集する `azcmagent logs` オプションが追加されました。
- EULA を表示する `azcmagent license` オプションが追加されました。
- エージェントの状態を簡単に解析可能な形式で出力する `azcmagent show --json` オプションが追加されました。
- `azcmagent show` の出力に、Azure でホストされている仮想マシン上にサーバーが存在するかどうかを示すフラグが追加されました。
- Azure サービスに到達できない場合にローカル エージェントの状態のリセットを許可する `azcmagent disconnect --force-local-only` オプションが追加されました。
- 他のクラウドをサポートするために `azcmagent connect --cloud` オプションが追加されました。 このリリースでは、エージェントのリリース時に Azure のみがサービスでサポートされます。
- エージェントは、Azure でサポートされている言語にローカライズされています。

### <a name="fixed"></a>固定

- 接続チェックの機能強化。
- Linux でエージェントをアップグレードしたときにプロキシ サーバーの設定が失われる問題が修正されました。
- Windows Server 2012 R2 を実行しているサーバーにエージェントをインストールしようとするときの問題が解決されました。
- 拡張機能のインストールの信頼性の向上

## <a name="august-2020"></a>2020 年 8 月

バージョン:0.11

- このリリースでは、以前に Ubuntu 20.04 のサポートが発表されました。 一部の Azure VM 拡張機能では Ubuntu 20.04 がサポートされないため、このバージョンでの Ubuntu のサポートは削除されます。

- 拡張機能のデプロイの信頼性向上。

### <a name="known-issues"></a>既知の問題

古いバージョンの Linux エージェントを使用しており、プロキシ サーバーを使用するようにエージェントを構成している場合、アップグレード後にプロキシ サーバーの設定を再構成する必要があります。 これを行うには、`sudo azcmagent_proxy add http://proxyserver.local:83` を実行します。

## <a name="next-steps"></a>次のステップ

- 複数のハイブリッド マシンにまたがって Arc 対応サーバーを評価するか、または有効にする前に、[Connected Machine エージェントの概要](agent-overview.md)を確認して、要件、エージェントに関する技術的な詳細、デプロイ方法を理解してください。

- [計画と展開ガイド](plan-at-scale-deployment.md)を参照して、任意の規模で Azure Arc 対応サーバーをデプロイし、一元的な管理と監視を実装する計画を立ててください。
