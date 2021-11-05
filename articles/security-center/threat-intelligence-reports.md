---
title: Microsoft Defender for Cloud の脅威インテリジェンス レポート|Microsoft Docs
description: このページは、調査中に Microsoft Defender for Cloud の脅威インテリジェンス レポートを使用して、セキュリティ アラートの詳細を見つけるのに役立ちます。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2021
ms.author: memildin
ms.openlocfilehash: 190cc14274b8e96434ab82fe7ff1df486d1b608b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472088"
---
# <a name="microsoft-defender-for-cloud-threat-intelligence-report"></a>Microsoft Defender for Cloud の脅威インテリジェンス レポート。

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このページでは、Microsoft Defender for Cloudの脅威インテリジェンス レポートを使用して、セキュリティ アラートをトリガーした脅威の詳細を確認する方法について説明します。

## <a name="what-is-a-threat-intelligence-report"></a>脅威インテリジェンス レポートとは

Defender for Cloud の脅威保護は、Azure リソース、ネットワーク、接続されているパートナー ソリューションからのセキュリティ情報を監視することで機能します。 この情報を分析し、ときには複数の情報源から得た情報との関連性を探りながら、脅威を特定します。 詳細については [Microsoft Defender for Cloud が脅威を](alerts-overview.md#detect-threats)検出して対応する方法を参照してください。

Defender for Cloud によって脅威が識別されると、[セキュリティ アラート](managing-and-responding-alerts.md) がトリガーされます。このアラートには、修復の提案など、イベントに関する詳細情報が含されます。 インシデント対応チームが脅威を調査して修復するために、Defender for Cloud は、検出された脅威に関する情報を含む脅威インテリジェンス レポートを提供します。 レポートには次のような情報が含まれます。

* 攻撃者の ID または所属団体 (この情報が利用可能な場合)
* 攻撃者の目的
* 現在および過去の攻撃キャンペーン (この情報が利用可能な場合)
* 攻撃者の戦術、ツール、手順
* URL やファイル ハッシュなど、関連する侵害の兆候 (IoC)
* 被害者情報 (攻撃が発生している業界や地域に関する情報で、Azure リソースにリスクが存在するかどうかの判断に役立ちます)
* 軽減策と修復方法に関する情報

> [!NOTE]
> レポートに記載される情報の量はさまざまであり、どの程度まで詳細な情報が表示されるかは、マルウェアの活動と拡散度に応じて決まります。

Defender for Cloud には、攻撃によって異なる可能性がある 3 種類の脅威レポートがあります。 具体的には、以下のとおりです。

* **活動グループ レポート**: 攻撃者、その目的、戦術に関する詳しい情報を記載したレポートです。
* **キャンペーン レポート**: 主に特定の攻撃キャンペーンの詳細を取り扱うレポートです。
* **脅威概要レポート**: 前の 2 つのレポートの全項目を確認できるレポートです。

この種の情報は、インシデント対応プロセスにおいて継続的に調査を実施し、攻撃元、攻撃者の動機、この問題の影響を将来軽減するための方策を把握するために利用できます。

## <a name="how-to-access-the-threat-intelligence-report"></a>脅威インテリジェンス レポートにアクセスする方法

1. Defender for Cloud のメニューから、**セキュリティ アラート** ページを開きます。
1. アラートを選択します。 

    アラートの詳細ページが開き、アラートの詳細が表示されます。 次に示すのは、 **[Ransomware indicators detected]\(検出されたランサムウェア インジケーター\)** アラート詳細ページです。

    [![[Ransomware indicators detected]\(検出されたランサムウェア インジケーター\) アラート詳細ページ。](media/threat-intelligence-reports/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/threat-intelligence-reports/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. レポートへのリンクを選択すると、既定のブラウザーで PDF が開きます。

    [![[Potentially Unsafe Action]\(安全でない可能性があるアクション\) アラート詳細ページ。](media/threat-intelligence-reports/threat-intelligence-report.png)](media/threat-intelligence-reports/threat-intelligence-report.png#lightbox)

    PDF レポートをダウンロードすることもできます。 

    >[!TIP]
    > 各セキュリティ アラートに関して表示される情報の量は、アラートの種類に応じて変わります。

## <a name="next-steps"></a>次のステップ

このページでは、セキュリティ アラートの調査時に脅威インテリジェンス レポートを開く方法について説明しました。 関連情報については、次のページを参照してください。

* [Microsoft Defender for Cloudでのセキュリティ アラートの管理と対応](managing-and-responding-alerts.md) セキュリティの警告の管理と対応の方法について説明しています。
* [Microsoft Defender for Cloud でのセキュリティ インシデントの処理](incidents.md)
