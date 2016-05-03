<properties
   pageTitle="Azure Security Center でのパートナー ソリューションの管理 | Microsoft Azure"
   description="このドキュメントでは、Azure サブスクリプションに統合されたパートナー ソリューションの正常性状態を、Azure セキュリティ センターでひと目で監視する方法について説明します。"
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/22/2016"
   ms.author="terrylan"/>

# Azure Security Center を使用したパートナー ソリューションの監視

このドキュメントでは、Azure Security Center でパートナー ソリューションの正常性状態を監視する方法について説明します。

> [AZURE.NOTE] このドキュメントの情報は、Azure セキュリティ センターのプレビュー リリースに適用されます。このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。ステップ バイ ステップ ガイドではありません。

## セキュリティ センターとは何ですか。
 セキュリティ センターは、Azure リソースのセキュリティを高度に視覚化し、制御することで脅威を回避、検出し、それに対応することに役立ちます。これにより、サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

## パートナー ソリューションの監視

**[セキュリティ センター]** ブレードの **[パートナー ソリューション]** タイルでは、Azure サブスクリプションに統合されたパートナー ソリューションの正常性状態をひと目で監視できます。 ![Partner solutions tile][1]

**[パートナー ソリューション]** タイルには、パートナー ソリューションの数と、それらのソリューションの状態に関する概要が表示されます。

パートナー ソリューションの **[状態]** は次のいずれかになります。

- 保護済み (緑) - 正常性の問題はありません。
- 異常 (赤) - 早急に処置が必要な正常性の問題があります。
- レポート停止 (オレンジ) - ソリューションが正常性の報告を停止しています。
- 不明な保護状態 (オレンジ) - 新しいリソースを既存のソリューションに追加するプロセスが失敗したため、この時点のソリューションの正常性が不明です。
- レポートなし (灰色) - ソリューションがまだ何も報告していません。接続されたばかりでデプロイ中の場合、ソリューションの状態は報告されていない可能性があります。

サブスクリプションに統合されたソリューションがない場合、ソリューションが存在しないことがタイルに表示されます。**[パートナー ソリューション]** タイルを選択すると、**[推奨事項]** ブレードが開き、パートナー セキュリティ ソリューションをデプロイできます。 ![No partner solutions][2]

パートナー ソリューションの正常性を確認するには、次の手順を実行します。

1. **[パートナー ソリューション]** タイルを選択します。セキュリティ センターに接続されているパートナー ソリューションの一覧を示すブレードが開きます。![パートナー ソリューション][3]

2. パートナー ソリューションを選択します。この例では、**[F5-WAF2]** ソリューションを選択します。パートナー ソリューションの状態およびソリューションの関連付けられたリソースを示すブレードが開きます。**[ソリューション コンソール]** を選択すると、このソリューションのパートナー管理エクスペリエンスが開きます。![パートナー ソリューションの詳細][4]

3. **[F5-WAF2]** ブレードに戻り、**[アプリをリンク]** を選択します。**[アプリケーションのリンク]** ブレードが開きます。ここで、パートナー ソリューションにリソースを接続できます。 ![Link resources to partner solution][5]

## 次のステップ
このドキュメントでは、セキュリティ センターの **[パートナー ソリューション]** タイルについて説明しました。セキュリティ センターの詳細については、次を参照してください。

- 「[Azure セキュリティ センターでのセキュリティ ポリシーの設定](security-center-policies.md)」 -- Azure サブスクリプションとリソース グループに対するセキュリティ ポリシーの構成方法について説明しています。
- 「[Azure セキュリティ センターでのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」 -- 推奨事項に従って Azure リソースを保護する方法について説明しています。
- 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。
- 「[Azure セキュリティ センターでのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」 -- セキュリティの警告の管理と対応の方法について説明しています。
- 「[Azure セキュリティ センターのよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
- [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png

<!---HONumber=AcomDC_0427_2016-->