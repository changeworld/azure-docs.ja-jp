<properties
	pageTitle="条件付きアクセス ポリシーを使用したリスクの管理"
	description="ポリシーに準拠している既知のデバイスであれば、どこからでも企業ネットワーク内の特定のリソースにアクセスできるようなり、紛失したデバイス、盗まれたデバイス、非対応のデバイスからのアクセスが禁止されます。"
	services="active-directory, virtual-network"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="08/19/2015"
	ms.author="femila"/>


# 条件付きアクセス ポリシーを使用したリスクの管理

従業員の勤務方法、生産性、作業の手段に関する最新の傾向はめまぐるしく変化しています。従業員は個人のデバイスを職場に持ち込んでいます。このような個人のデバイスには、個人のデジタル ライフで使用しているアプリケーションがインストールされています。また、従業員はこれらのアプリケーションが提供する自由性と機能に慣れています。そして、職場でも同じアプリケーションを使い、職場でも個人のデジタル生活と同様の柔軟性があることを希望します。最近の従業員は、任意の場所で勤務し、好みのデバイスを使ってビジネス アプリケーションにシームレスに接続してアクセスすることを望んでいます。

このようにユーザーの望む場所、時間、方法で勤務できるように柔軟性を高めると、リスクも高まります。デバイスの盗難、置き忘れ、紛失が起きていることは多くのデータが示しています。それらスマートフォンやタブレットの多くには、驚くほど大量の顧客や企業の重要な機密コンテンツが保持されています。これが、IT アーキテクト、セキュリティ スペシャリスト、および IT 管理者が維持しようとしている微妙なバランスです。つまり、ユーザーが好むデバイスで生産性を向上することと、ユーザーが好むすべてのデバイスで、デバイスに格納されている会社のコンテンツに対して適切なレベルのセキュリティと保護を実現することのバランスです。

Azure Active Directory、Office 365、Microsoft Intune が提供する複数の条件付きアクセス機能により、IT 管理者は以下のようなソリューションを提供できます。

- インターネット上で他者からのアクセスは制限しつつ、従業員はどこからでもアクセスできるようにする。
- ポリシーに準拠している既知のデバイスであれば、どこからでも特定のリソースにアクセスできるようにする。
- 紛失したデバイス、盗まれたデバイス、非対応のデバイスからのアクセスを禁止する。

![][1]

## 参照トピック

以下のトピックでは、組織で条件付きのアクセス ポリシーを設定するときに利用できる各メカニズムについて説明しています。

- [Azure Active Directory Device Registration Overview](active-directory-conditional-access-device-registration-overview.md)
- [Setting up On-premises Conditional Access using Azure Active Directory Device Registration](active-directory-conditional-access-on-premises-setup.md)
- [Conditional Access Device Policies for Office 365 services](active-directory-conditional-access-device-policies.md)
- [Azure Conditional Access Preview for SaaS Apps](active-directory-conditional-access-azuread-connected-apps.md)


<!--Image references-->
[1]: ./media/active-directory-conditional-access/condaccoverviewvsdx1.png

<!---HONumber=Oct15_HO3-->