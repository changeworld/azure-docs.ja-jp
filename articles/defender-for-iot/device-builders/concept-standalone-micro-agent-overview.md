---
title: スタンドアロンのマイクロ エージェントの概要 (プレビュー)
description: Azure Defender for IoT セキュリティ エージェントを使用すると、新しい IoT デバイスと Azure IoT プロジェクトにセキュリティを直接組み込むことができます。
ms.date: 11/04/2021
ms.topic: article
ms.openlocfilehash: 2dfdc4b1d3696a3887e251d6ccd3213f5bc2bc0c
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577074"
---
# <a name="standalone-micro-agent-overview-preview"></a>スタンドアロンのマイクロ エージェントの概要 (プレビュー)

セキュリティは、IoT 実装者にとってほぼ普遍的な問題です。 IoT デバイスには、エンドポイント監視、セキュリティ体制管理、および脅威検出 (これらすべてに非常に独特なパフォーマンス要件があります) に対する固有のニーズがあります。

Azure Defender for IoT セキュリティ エージェントを使用すると、新しい IoT デバイスと Azure IoT プロジェクトにセキュリティを直接組み込むことができます。 マイクロ エージェントには、バイナリ パッケージとしてデプロイする機能やソース コードを変更する機能など、柔軟なデプロイ オプションが用意されています。 また、マイクロ エージェントは、Linux や Azure RTOS などの標準的な IoT オペレーティング システムで利用できます。

Azure Defender for IoT マイクロ エージェントは、統合セキュリティ管理のため、セキュリティ体制管理に対するエンドポイントの可視性、脅威検出、および Microsoft の他のセキュリティ ツールへの統合を提供します。

## <a name="security-posture-management"></a>セキュリティ体制管理

IoT デバイスのセキュリティ体制を事前に監視します。 Azure Defender for IoT では、CIS ベンチマークに基づくセキュリティ体制の推奨事項と、デバイス固有の推奨事項が提供されます。 OS 構成、ファイアウォール構成、アクセス許可など、オペレーティング システムのセキュリティを可視化できます。

## <a name="endpoint-iot-and-ot-threat-detection"></a>エンドポイント IoT と OT の脅威検出

ボットネット、ブルート フォース試行、クリプト マイナー、疑わしいネットワーク アクティビティなどの脅威を検出します。 独自の組織で最も重要な脅威を対象とするカスタム アラートを作成します。

## <a name="flexible-distribution-and-deployment-models"></a>柔軟な分散モデルとデプロイ モデル

Azure Defender for IoT マイクロ エージェントにはソース コードが含まれているため、マイクロ エージェントをファームウェアに組み込んだり、必要なものだけを含めるようにカスタマイズしたりすることができます。 マイクロ エージェントは、バイナリ パッケージとして使用することも、他の Azure IoT ソリューションに直接統合することもできます。

## <a name="meets-the-needs-of-your-iot-devices-with-minimal-impact"></a>影響を最小限に抑え、IoT デバイスのニーズに対応

Azure Defender for IoT マイクロ エージェントは簡単にデプロイでき、エンドポイントに対するパフォーマンスへの影響は最小限に抑えられています。 Defender for IoT マイクロ エージェントを使用すると、以下のことが可能になります。

- **最適化してパフォーマンスを向上させる**:Azure Defender for IoT マイクロ エージェントは、占有領域が小さく、CPU 使用率が低くなっています。  

- **プラグ アンド プレイ**:特定の OS カーネルへの依存や、すべての主要な IoT オペレーティング システムに必要なサポートはありません。 Azure Defender for IoT マイクロ エージェントは、デバイスがある場所でデバイスを満たします。

- **柔軟なデプロイ**:スタンドアロンのエージェントとして、Azure Defender for IoT のマイクロ エージェントは、さまざまな分散モデルと柔軟なデプロイをサポートしています。

## <a name="data-processing-and-residency"></a>データの処理と保存場所

> [!Note]
> Azure Defender for IoT のデータ処理と保存は、IoT Hub リージョンとは異なるリージョンで発生する場合があります。 Azure Defender for IoT は、セキュリティ検出ロジックの一部として、デバイス ツイン、マスクされていない IP、追加の構成データを使用しています。

Azure Defender for IoT のデータ処理と保存は、IoT Hub のリージョンのとは異なるリージョンで発生する場合があります。 IoT Hub と Azure Defender for IoT リージョンの間のマッピングは、次のとおりです。

- ヨーロッパにあるハブの場合、データは *西ヨーロッパ* リージョンに保存されます。

- ヨーロッパ以外にあるハブの場合、データは *米国東部* リージョンに保存されます。

Azure Defender for IoT は、セキュリティ検出ロジックの一部として、デバイス ツイン、マスクされていない IP、追加の構成データを既定で使用します。 デバイス ツインを無効にし、IP アドレス コレクションをマスク解除するには、データ コレクションの設定ページに移動します。

:::image type="content" source="media/data-collection-settings.png" alt-text="データ コレクションの設定ページのスクリーンショット。":::

詳細については、[Defender for IoT ソリューションをカスタマイズする](concept-micro-agent-configuration.md)方法に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[マイクロ エージェントの認証方法 (プレビュー)](concept-security-agent-authentication.md) を確認します。
