---
title: "Azure Security Center でのパートナー ソリューションの管理 | Microsoft Docs"
description: "このドキュメントでは、Azure サブスクリプションに統合されたパートナー ソリューションの正常性状態を、Azure セキュリティ センターでひと目で監視する方法について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: 3ff38892f198ab5dfb9e08c1c01b942810a40260
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Azure Security Center を使用したパートナー ソリューションの監視
このドキュメントでは、Azure Security Center でパートナー ソリューションの正常性状態を監視する方法について説明します。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。 このドキュメントはステップ バイ ステップ ガイドではありません。
>
>

## <a name="monitoring-partner-solutions"></a>パートナー ソリューションの監視
**[概要]** の **[セキュリティ ソリューション]** タイルでは、Azure サブスクリプションと統合されたパートナー ソリューションの正常性状態をひとめで監視できます。

![Partner solutions tile][1]

パートナー ソリューションの正常性を確認するには、次の手順を実行します。

1. **[セキュリティ ソリューション]** タイルを選択します。 Security Center に接続されているパートナー ソリューションの一覧が表示された **[セキュリティ ソリューション]** が開きます。

   ![パートナー ソリューション][3]

   パートナー ソリューションの状態は、次のいずれかになります。

   * 保護済み (緑) - 正常性の問題はありません。
   * 異常 (赤) - 早急に処置が必要な正常性の問題があります。
   * レポート停止 (オレンジ) - ソリューションが正常性の報告を停止しています。
   * 不明な保護状態 (オレンジ) - 新しいリソースを既存のソリューションに追加するプロセスが失敗したため、この時点のソリューションの正常性が不明です。
   * レポートなし (灰色) - ソリューションがまだ何も報告していません。最近接続されたばかりでデプロイ中の場合、ソリューションの状態は報告されていない可能性があります。

2. パートナー ソリューションを選択します。 パートナー ソリューションの状態およびソリューションの関連付けられたリソースを示すブレードが開きます。 **[ソリューション コンソール]** を選択して、このソリューションのパートナー管理エクスペリエンスを開きます。

   ![パートナー ソリューションの詳細][4]
3. **[Qualys]** ブレードに戻り、**[VM をリンクする]** を選択します。 **[アプリケーションのリンク]** ブレードが開きます。 ここで、パートナー ソリューションにリソースを接続できます。

   ![Link resources to partner solution][5]

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Security Center の **[セキュリティ ソリューション]** タイルについて説明しました。 パートナーと統合してセキュリティを全体的に向上させる方法を学習するには、[パートナーとソリューションの統合](security-center-partner-integration.md)に関するページを参照してください。

Security Center の詳細については、次の記事を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」 -- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」 -- セキュリティの警告の管理と対応の方法について説明しています
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png
