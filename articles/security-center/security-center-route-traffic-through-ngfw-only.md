<properties
   pageTitle="Azure Security Center での次世代ファイアウォール経由に限定したトラフィックのルーティング | Microsoft Azure"
   description="このドキュメントでは、";NGFW 経由に限定したトラフィックのルーティング"; という Azure Security Center の推奨事項を実装する方法について説明します。"
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/20/2016"
   ms.author="terrylan"/>

# Azure Security Center での次世代ファイアウォール経由に限定したトラフィックのルーティング

次世代ファイアウォール (NGFW) をデプロイすると、Azure Security Center で検出されます。インターネットに接続するエンドポイントがある場合、Azure Security Center では、仮想マシン (VM) への受信トラフィックを必ず NGFW 経由にするようにネットワーク セキュリティ グループの規則を構成するよう推奨します。

> [AZURE.NOTE] このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。ステップ バイ ステップ ガイドではありません。

## 推奨事項の実装

1. **[推奨事項]** ブレードで、**[Route traffic through NGFW only (NGFW 経由に限定したトラフィックのルーティング)]** を選択します。![NGFW 経由に限定したトラフィックのルーティング][1]

2. **[Route traffic through NGFW only (NGFW 経由に限定したトラフィックのルーティング)]** ブレードが開き、トラフィックをルーティングできる VM の一覧が表示されます。一覧から VM を選択します。![Select a VM][2]

3. 選択した VM のブレードが開き、関連する受信規則が表示されます。考えられる次のステップに関する詳細情報が示されます。![アクセスを制限する規則の構成][3]

## 関連項目

セキュリティ センターの詳細については、次を参照してください。

- 「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
- 「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
- 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。
- 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」-- セキュリティの警告の管理と対応の方法について説明しています。
- 「[Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。
- 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
- [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-route-traffic-through-ngfw/route-traffic-through-ngfw.png
[2]: ./media/security-center-route-traffic-through-ngfw/select-vm.png
[3]: ./media/security-center-route-traffic-through-ngfw/configure-rules-to-limit-access.png

<!---HONumber=AcomDC_0720_2016-->