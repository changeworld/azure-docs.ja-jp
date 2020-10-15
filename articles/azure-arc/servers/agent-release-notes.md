---
title: Azure Arc 対応サーバー エージェントの新機能
description: この記事には、Azure Arc 対応サーバー エージェントのリリース ノートが含まれています。 まとめられている問題の多くについては、追加詳細のリンクがあります。
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: 1918d03b5bbfaaa64b7d74c18fad4eb9a86800a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90908151"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Azure Arc 対応サーバー エージェントの新機能

Azure Arc 対応サーバーの Connected Machine エージェントは、継続的に改善が図られています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正

## <a name="september-2020"></a>2020 年 9 月

バージョン:1.0 (一般提供)

### <a name="plan-for-change"></a>変更の計画

- プレビュー エージェント (1.0 より前のすべてのバージョン) のサポートは、将来のサービス更新プログラムで削除される予定です。
- フォールバック エンドポイント `.azure-automation.net` のサポートが削除されました。 プロキシを使用している場合は、エンドポイント `*.his.arc.azure.com` を許可する必要があります。
- Connected Machine エージェントが Azure でホストされている仮想マシンにインストールされている場合は、Arc 対応サーバーのリソースから VM 拡張機能をインストールまたは変更できません。 これは、仮想マシンの **Microsoft.Compute** および **Microsoft.HybridCompute** リソースから競合する拡張機能の操作が実行されることを回避するためです。 マシンの **Microsoft.Compute** リソースをすべての拡張機能の操作に使用します。
- ゲスト構成プロセスの名前が Linux では *gcd* から *gcad* に、Windows では *gcarcservice* から *gcservice* に変更されました。

### <a name="new-feature"></a>新機能

- サポートに関する情報を収集する `azcmagent logs` オプションが追加されました。
- EULA を表示する `azcmagent license` オプションが追加されました。
- エージェントの状態を簡単に解析可能な形式で出力する `azcmagent show --json` オプションが追加されました。
- `azcmagent show` の出力に、Azure でホストされている仮想マシン上にサーバーが存在するかどうかを示すフラグが追加されました。
- Azure サービスに到達できない場合にローカル エージェントの状態のリセットを許可する `azcmagent disconnect --force-local-only` オプションが追加されました。
- 追加のクラウドをサポートする `azcmagent connect --cloud` オプションが追加されました。 このリリースでは、エージェントのリリース時に Azure のみがサービスでサポートされます。
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

複数のハイブリッド マシンにまたがって Arc 対応サーバーを評価するか、または有効にする前に、[Connected Machine エージェントの概要](agent-overview.md)を確認して、要件、エージェントに関する技術的な詳細、デプロイ方法を理解してください。