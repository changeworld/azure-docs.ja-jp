---
title: Azure Security Center での接続されたパートナー ソリューションの管理 | Microsoft Docs
description: このドキュメントでは、Azure サブスクリプションに統合されたパートナー ソリューションの正常性状態を、Azure セキュリティ センターでひと目で監視する方法について説明します。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/20/2018
ms.author: terrylan
ms.openlocfilehash: 3d4c9c79ff90ee816719f0179fbb5096b0035854
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "42143016"
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>Azure Security Center での接続されたパートナー ソリューションの管理
この記事では、Azure Security Center で接続されたセキュリティ ソリューションを管理および監視する方法について説明します。

## <a name="monitoring-partner-solutions"></a>パートナー ソリューションの監視
接続されたセキュリティ ソリューションの正常性状態を監視し、基本的な管理を実行するには。

1. **[Security Center - 概要]** で、**[セキュリティ ソリューション]** を選択します。

  ![セキュリティ ソリューションを選択する][1]

  **[接続済みのソリューション]** セクションには、Security Center に接続されたセキュリティ ソリューションと、各ソリューションの正常性状態に関する情報が含まれます。

  ![パートナー ソリューション][2]

   パートナー ソリューションの状態は、次のいずれかになります。

   * 正常 (緑) - 正常性の問題はありません。
   * 異常 (赤) - 早急に処置が必要な正常性の問題があります。
   * 正常性の問題 (オレンジ) - ソリューションが正常性の報告を停止しています。
   * レポートなし (灰色) - ソリューションがまだ何も報告していません。最近接続されたばかりでデプロイ中の場合、または使用できる正常性データがない場合は、ソリューションの状態が報告されていない可能性があります。

   > [!NOTE]
   > 正常性状態データを使用できない場合、Security Center には、最後に受信したイベントの日時が表示され、ソリューションが報告を行っているかどうかを示します。 使用できる正常性データがなく、過去 14 日以内にアラートを受信していない場合、Security Center は、ソリューションで異常が発生しているか、報告が行われていないことを示します。
   >
   >

2. **[表示]** を選択すると、次の追加情報とオプションを確認できます。

  - **ソリューション コンソール**。 このソリューションの管理エクスペリエンスが開きます。
  - **VM をリンクする**。 [アプリケーションのリンク] ブレードが開きます。 ここで、パートナー ソリューションにリソースを接続できます。
  - **ソリューションの削除**。
  - **構成**。

   ![パートナー ソリューションの詳細][3]

## <a name="next-steps"></a>次の手順
この記事では、Security Center で接続されたセキュリティ ソリューションを管理および監視する方法について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [セキュリティ ソリューションの概要](security-center-partner-integration.md) - セキュリティ ソリューションの接続および管理方法について説明します。
* [Microsoft Advanced Threat Analytics (ATA) の接続](security-center-ata-integration.md) - ATA からのアラートを接続する方法について説明します。
* [Azure Active Directory (AD) Identity Protection の接続](security-center-aadip-integration.md) - Azure AD Identity Protection からのアラートを接続する方法について説明します。
* [パートナーとソリューションの統合](security-center-partner-integration.md) - 他のセキュリティ ソリューションとの統合について概説します。
* [セキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) -- セキュリティの警告の管理と対応の方法について説明します。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png
