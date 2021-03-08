---
title: スタンドアロンのマイクロ エージェントの概要 (プレビュー)
titleSuffix: Azure Defender for IoT
description: Azure Defender for IoT セキュリティ エージェントを使用すると、新しい IoT デバイスと Azure IoT プロジェクトにセキュリティを直接組み込むことができます。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/19/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 84afa4205b57481efc3ed9df805cfa7a3657a6c8
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809661"
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

## <a name="next-steps"></a>次のステップ

[マイクロ エージェントの認証方法 (プレビュー)](concept-security-agent-authentication.md) を確認します。
