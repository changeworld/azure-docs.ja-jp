---
title: Azure Security Center での Azure SQL サービスとデータの保護 | Microsoft Docs
description: このドキュメントでは、Azure Security Center での推奨事項に従ってデータと Azure SQL サービスを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.openlocfilehash: 45f5dc840f015793912e314ab3d47e54a409708e
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126668"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Azure Security Center での Azure SQL サービスとデータの保護
Azure セキュリティ センターは、Azure リソースのセキュリティの状態を分析します。 潜在的なセキュリティの脆弱性を識別すると、Security Center は、必要な管理を構成するプロセスを説明する推奨事項を作成します。  推奨事項は、仮想マシン (VM)、ネットワーク、SQL、データ、アプリケーションといった、Azure のリソースの種類に適用されます。

この記事では、Azure SQL サービスとデータに適用される推奨事項について説明します。 推奨事項は、Azure SQL サーバーおよびデータベースの監査と SQL データベースの暗号化を有効にすることと、Azure Storage アカウントの暗号化を有効にすることが中心です。  次の表を参考にすると、SQL サービスとデータに関する利用可能な推奨事項と、それぞれを適用した場合の結果を理解しやすくなります。
### <a name="monitor-data-security"></a>データのセキュリティの監視

**[防止]** セクションの **[データのセキュリティ]** をクリックすると、**[データ リソース]** が開き、SQL とストレージの推奨事項が表示されます。 また、データベースの全般的な正常性状態に関する [推奨事項](security-center-sql-service-recommendations.md) も示されます。 ストレージの暗号化の詳細については、「[Enable encryption for Azure storage account in Azure Security Center (Azure Security Center における Azure ストレージ アカウントの暗号化の有効化)](security-center-enable-encryption-for-storage-account.md)」を参照してください。

![データ リソース](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

**[SQL Recommendations (SQL の推奨事項)]** で任意の推奨事項をクリックすると、問題解決のためのアクションの詳細を確認できます。 次の例では、**[Database Auditing & Threat detection on SQL databases (データベースの監査と SQL Database の脅威の検出)]** という推奨事項を展開した状態を示しています。

![SQL の推奨事項の詳細](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

**[SQL データベースの監査と脅威検出を有効にする]** には、次の情報が表示されます。

* SQL データベースの一覧
* SQL データベースが配置されているサーバー
* この設定がサーバーから継承されたものか、このデータベースに固有の設定かに関する情報
* 現在の状態
* 問題の重大度

この推奨事項に対処するためにデータベースをクリックすると、以下に示す **[監査と脅威検出]** が開きます。

![監査と脅威検出](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

監査を有効にするには、**[監査]** オプションの **[オン]** を選択します。

## <a name="available-sql-service-and-data-recommendations"></a>SQL サービスとデータに関する利用可能な推奨事項
| 推奨 | 説明 |
| --- | --- |
| [SQL サーバーの監査と脅威検出を有効にする](security-center-enable-auditing-on-sql-servers.md) |Azure SQL サーバーの監査と脅威検出を有効にすることをお勧めします (Azure SQL サービスのみ。仮想マシンで実行されている SQL を除く)。 |
| [SQL データベースの監査と脅威検出を有効にする](security-center-enable-auditing-on-sql-databases.md) |Azure SQL データベースの監査と脅威検出を有効にすることをお勧めします (Azure SQL サービスのみ。仮想マシンで実行されている SQL を除く)。 |
| [SQL データベースでの透過的なデータ暗号化の有効化](security-center-enable-transparent-data-encryption.md) |SQL データベース (Azure SQL のサービスのみ) に対して暗号化を有効にすることをお勧めします。 |

## <a name="see-also"></a>関連項目
その他の Azure リソースの種類に適用される推奨事項の詳細については、次をご覧ください。

* [Azure Security Center での仮想マシンの保護](security-center-virtual-machine-recommendations.md)
* [Azure Security Center でのアプリケーションの保護](security-center-application-recommendations.md)
* [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)

セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。
