---
title: Azure Arc 対応サーバー (プレビュー) エージェントの新機能
description: この記事には、Azure Arc 対応サーバー (プレビュー) エージェントのリリース ノートが含まれています。 まとめられている問題の多くについては、追加詳細のリンクがあります。
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 0f5060189d6f9cac620b49f414e0e27bed3e356e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236573"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>Azure Arc 対応サーバー (プレビュー) エージェントの新機能

Azure Arc 対応サーバー (プレビュー) の Connected Machine エージェントは、継続的に改善が図られています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正

## <a name="august-2020"></a>2020 年 8 月

バージョン:0.11

- Ubuntu 20.04 のサポート。

- 拡張機能のデプロイの信頼性向上。

### <a name="known-issues"></a>既知の問題

古いバージョンの Linux エージェントを使用しており、プロキシ サーバーを使用するようにエージェントを構成している場合、アップグレード後にプロキシ サーバーの設定を再構成する必要があります。 これを行うには、`sudo azcmagent_proxy add http://proxyserver.local:83` を実行します。

## <a name="next-steps"></a>次のステップ

複数のハイブリッド マシン間で Arc 対応サーバー (プレビュー) を評価または有効化する前に、[Connected Machine エージェントの概要](agent-overview.md)を確認し、要件、エージェントに関する技術的な詳細、デプロイ方法を把握してください。