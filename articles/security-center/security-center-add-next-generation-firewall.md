<properties
   pageTitle="Azure Security Center で次世代ファイアウォールを追加する | Microsoft Azure"
   description="このドキュメントでは、**次世代ファイアウォールの追加** という Azure Security Center の推奨事項を実装する方法について説明します。"
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
   ms.date="07/15/2016"
   ms.author="terrylan"/>

# Azure Security Center で次世代ファイアウォールを追加する

Azure Security Center では、セキュリティ保護を強化するために、Microsoft パートナーの次世代ファイアウォール (NGFW) を追加することをお勧めします。このドキュメントでは、追加方法の例を紹介します。

> [AZURE.NOTE] このドキュメントの情報は、Azure Security Center のプレビュー リリースに適用されます。このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。ステップ バイ ステップ ガイドではありません。

## 推奨事項の実装

1. **[推奨事項]** ブレードで、**[次世代のファイアウォールの追加]** を選択します。![次世代ファイアウォールを追加する][1]

2. **[次世代のファイアウォールの追加]** ブレードで、エンドポイントを選択します。![エンドポイントを選択する][2]

3. 2 つ目の **[次世代のファイアウォールの追加]** ブレードが開きます。利用可能な場合は、既存のソリューションを使用することを選択するか、新しいソリューションを作成することができます。この例では、利用可能な既存のソリューションがないため、新しい NGFW を作成します。![新しい次世代ファイアウォールを作成する][3]

4. 新しい NGFW を作成するには、統合されたパートナーの一覧からソリューションを選択します。この例では、**[Check Point]** を選択します。![次世代ファイアウォール ソリューションを選択する][4]

5. パートナー ソリューションに関する情報を提供する **[Check Point]** ブレードが開きます。情報ブレードで **[作成]** をクリックします。![Firewall information blade][5]

6. **[仮想マシンの作成]** ブレードが開きます。ここに、NGFW を実行する仮想マシンの起動に必要な情報を入力できます。手順を実行し、必要な NGFW 情報を指定します。[OK] を選択して適用します。![NGFW を実行する仮想マシンを作成する][6]

## 次のステップ

このドキュメントでは、"次世代ファイアウォールの追加" というセキュリティ センターの推奨事項を実装する方法について説明しました。 NGFW と Check Point パートナー ソリューションの詳細については、次を参照してください。

- [次世代ファイアウォール](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
- [Check Point vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

セキュリティ センターの詳細については、次を参照してください。

- 「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」-- セキュリティ ポリシーの構成方法について説明しています。
- 「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
- 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。
- 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」-- セキュリティの警告の管理と対応の方法について説明しています。
- 「[Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)」 -- パートナー ソリューションの正常性状態を監視する方法について説明しています。
- 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
- [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png

<!---HONumber=AcomDC_0720_2016-->