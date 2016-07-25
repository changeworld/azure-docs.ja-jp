<properties
   pageTitle="Azure Security Center でセキュリティ連絡先の詳細情報を指定する | Microsoft Azure"
   description="このドキュメントでは、Azure Security Center でセキュリティ連絡先の詳細情報を指定する方法について説明します。"
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
   ms.date="07/11/2016"
   ms.author="terrylan"/>

# Azure Security Center でセキュリティ連絡先の詳細情報を指定する

Azure Security Center では、Azure サブスクリプションに対してセキュリティ連絡先の詳細を指定していない場合、それを指定するよう推奨されます。この情報は、Microsoft セキュリティ レスポンス センター (MSRC) で、不正なユーザーまたは権限のないユーザーによる顧客データへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。MSRC では、Azure のネットワークとインフラストラクチャの選択的なセキュリティ監視を行い、第三者からの脅威インテリジェンスと不正使用の報告を受け取ります。

> [AZURE.NOTE] このドキュメントの情報は、Azure セキュリティ センターのプレビュー リリースに適用されます。このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。ステップ バイ ステップ ガイドではありません。

## 推奨事項の実装

1. **[推奨事項]** ブレードで、**[セキュリティ連絡先の詳細情報の指定]** を選択します。![Provide security contact][1]

2. これにより、**[セキュリティ連絡先の詳細情報の指定]** ブレードが開きます。連絡先情報を指定する Azure サブスクリプションを選択します。![セキュリティの連絡先詳細の提供][2]

3. 2 つ目の **[セキュリティ連絡先の詳細情報の指定]** ブレードが開きます。セキュリティ連絡先の電子メール アドレスを (複数の場合はコンマで区切って) 入力します。入力できる電子メール アドレスの数に制限はありません。セキュリティ連絡先の国際電話番号を 1 件入力します。

4. **[OK]** を選択してセキュリティ連絡先の情報をサブスクリプションに適用します。

## 次のステップ

セキュリティ センターの詳細については、次を参照してください。

- 「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
- 「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
- 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。
- 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」-- セキュリティの警告の管理と対応の方法について説明しています。
- 「[Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。
- 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
- [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]: ./media/security-center-provide-security-contacts/provide-contact-details.png

<!---HONumber=AcomDC_0713_2016-->