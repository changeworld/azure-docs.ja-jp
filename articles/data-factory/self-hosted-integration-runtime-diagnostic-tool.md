---
title: セルフホステッド統合ランタイム診断ツール
description: セルフホステッド統合ランタイムの診断ツール
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
- devx-track-azurepowershell
ms.date: 07/28/2021
ms.openlocfilehash: b3c98d4e85b1a7d04b017eea2da00f36a479eeac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598577"
---
# <a name="diagnostic-tool-for-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムの診断ツール
セルフホステッド統合ランタイムは、異なるネットワーク環境間でデータ統合機能を提供するために Azure Data Factory によって使用されるコンピューティング インフラストラクチャです。 セルフホステッド統合ランタイムは、オンプレミス コンピューター、またはプライベート ネットワーク内の仮想マシンにインストールする必要があります。 場合によっては、ネットワーク、ファイアウォール、依存関係、OS 関連の問題など、オンプレミスのマシンの問題の調査は困難です。 この記事では、オンプレミス環境での問題をトラブルシューティングするための新しい診断ツールについて説明します。

このツールは、セルフホステッド統合ランタイム マシンで一連のテスト シナリオを実行します。 各シナリオには、一般的な問題に対する典型的な正常性チェックのケースがあります。 問題が発生した場合、ユーザーは "問題のトラブルシューティング" 機能をトリガーできます。 このツールは、ユーザーの環境情報を収集し、正常性チェックのケースを実行します。 

## <a name="get-started"></a>はじめに 
診断ツールを実行して、考えられる問題を検出するには、次の 2 つの方法があります。

- オンプレミスのマシンにセルフホステッド統合ランタイムをインストールする場合は、Configuration Manager からトラブルシューティング機能にアクセスできます。 問題が発生した場合は、 **[問題のトラブルシューティング]** を選択して診断ツールを実行します。 インストール後に **[診断]** タブでも同じオプションを選択できます。

   :::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/troubleshoot-ui.png" alt-text="Runtime Configuration Manager を使用して問題をトラブルシューティングする方法を示すスクリーンショット。":::
   
- コマンド ラインから診断ツールを起動することもできます。

   ```console
   dmgcmd.exe -ts [AUTH_KEY]
   ```

## <a name="diagnostic-result"></a>診断結果
実行結果と詳細ログ メッセージは、HTML レポートとして生成されます。 エラーを確認し、推奨される軽減策またはパブリック ドキュメントの URL をレポートから取得できます。

:::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/diagnostic-process.png" alt-text="診断プロセスを示すスクリーンショット。":::

:::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/diagnostic-report.png" alt-text="診断結果レポートを示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

- [Azure Data Factory の統合ランタイムの概念](./concepts-integration-runtime.md)を確認します。

- [Azure portal 上でセルフホステッド統合ランタイムを作成する](./create-self-hosted-integration-runtime.md)方法を確認します。
