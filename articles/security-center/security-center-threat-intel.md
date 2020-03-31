---
title: 脅威インテリジェンスとセキュリティ アラート マップ - Azure Security Center
description: Azure Security Center のセキュリティ アラート マップと脅威インテリジェンス機能を使用して、VM とコンピューターの潜在的な脅威を特定する方法について説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 39835d9d73adcbe474d3b70dfced313e18d1a3b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603420"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>セキュリティ アラート マップと脅威インテリジェンス
この記事は、Azure Security Center のセキュリティ アラート マップとセキュリティ イベント ベースの脅威インテリジェンス マップを使用して、セキュリティ関連の問題を解決するのに役立ちます。

> [!NOTE]
> セキュリティ "*イベント*" マップ ボタンは 2019 年 7 月 31 日に廃止されました。 詳細および代替サービスについては、「[Security Center の機能の廃止 (2019 年 7 月)](security-center-features-retirement-july2019.md#menu_securityeventsmap)」を参照してください。


## <a name="how-the-security-alerts-map-works"></a>セキュリティ アラート マップのしくみ
Security Center では、その環境に対するセキュリティ上の脅威を特定するのに役立つマップを提供します。 たとえば、ある特定のコンピューターがボットネットの一部であるかどうか、および脅威の発生元を特定することができます。 ボットネットを管理するコマンド アンド コントロールでひそかにやりとりするマルウェアが攻撃者によって不正にインストールされた場合、コンピューターはボットネットのノードになる可能性があります。 

このマップを構築するために、Security Center では Microsoft 内部の複数のソースから取得したデータが使用されます。 Security Center では、このデータを使用して、ご利用の環境に対する潜在的な脅威をマップします。 

[セキュリティ インシデント対応プロセス](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response)の手順の 1 つは、侵害されたシステムの重大度を特定することです。 このフェーズでは、次のタスクを実行する必要があります。

- 攻撃の性質を特定する。
- 攻撃の発生ポイントを特定する。
- 攻撃の目的を特定する。 その攻撃が、特定の情報を取得するために自分の組織に向けられたものか、それともランダムなものかを特定します。
- 侵害されたシステムを特定する。
- アクセスされたファイルを特定して、そのファイルの機密度を判定する。

Security Center のセキュリティ アラート マップを使用して、これらのタスクに役立てることができます。

## <a name="access-the-security-alerts-map"></a>セキュリティ アラート マップにアクセスする
ご利用の環境で現在の脅威を視覚化するには、セキュリティ アラート マップを開きます。

1. **[Security Center]** ダッシュボードを開きます。
2. 左側のウィンドウの **[脅威の防止]** で、 **[セキュリティ アラート マップ]** を選択します。 マップが開きます。
3. アラートに関する詳細情報を取得して修正手順を受け取るには、マップ上のアラート ドットをクリックして、手順に従います。 
 
セキュリティ アラート マップはアラートに基づいています。 これらのアラートは、IP アドレスが既知の危険な IP アドレス (例: 既知の Cryptominer) か、以前には危険として認識されていない IP アドレスかどうかにかかわらず、ネットワーク通信が正常に解決された IP アドレスに関連付けられているアクティビティに基づいています。 このマップによって、Azure で以前に選択したサブスクリプション全体でアラートが提供されます。 

マップ上のアラートは、発生元として検出された場所の地理的な位置に基づいて表示され、重大度によって色分けされます。 
    ![脅威インテリジェンス情報](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>参照
この記事では、Security Center の脅威インテリジェンスを使用して、疑わしいアクティビティの特定を支援する方法について説明しました。 Security Center の詳細については、次の記事を参照してください。

* [Azure Security Center でのセキュリティ アラートの管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 Security Center でアラートを管理し、セキュリティ インシデントに対応する方法を説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 さまざまな種類のセキュリティ アラートについて説明します。
* [Azure Security Center トラブルシューティング ガイド](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 Security Center における一般的な問題のトラブルシューティング方法について説明します。