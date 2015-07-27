<properties 
	pageTitle="Azure AD でのパスワードの管理" 
	description="Azure AD でパスワードを管理する方法について説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Azure AD でのパスワードの管理

管理者は、Azure クラシック ポータルを使用して Azure でのユーザーのパスワードをリセットできます。ディレクトリの名前をクリックし、[ユーザー] ページでユーザーの名前をクリックして、ポータルの下部にある **[パスワードのリセット]** をクリックします。

このトピックの以降では、Azure Active Directory がサポートする次のようなパスワード管理機能について詳しく説明します。

- **セルフ サービスによるパスワード**の変更では、エンド ユーザーまたは管理者は、管理者またはヘルプ デスクにサポートを依頼することなく、有効期限が切れた、または有効期限が切れていないパスワードを変更できます。
- **セルフ サービスによるパスワード**のリセットでは、エンド ユーザーまたは管理者は、管理者またはヘルプ デスクにサポートを依頼することなく、パスワードを自動的にリセットできます。セルフ サービスのパスワード リセットには、Azure AD Premium または Basic が必要です。詳細については、「[Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。
- **管理者によるパスワードのリセット**では、管理者は、Azure 管理ポータル内からエンド ユーザーまたは別の管理者のパスワードをリセットできます。
- **パスワード管理アクティビティ レポート**では、組織内で発生したパスワードのリセットおよび登録アクティビティの詳細が管理者に提供されます。 
- **パスワード ライトバック**では、クラウドからオンプレミスのパスワードを管理できるので、フェデレーション ユーザーまたはパスワード同期済みユーザーは、またはこれらのユーザーに代わって、上記のシナリオをすべて実行できます。パスワード ライトバックには Azure AD Premium が必要です。詳細については、「[Azure Active Directory Premium の概要](active-directory-get-started-premium.md)」を参照してください。

> [AZURE.NOTE]Azure AD のワールドワイド インスタンスを使用している中国のお客様は、Azure AD Premium を利用できます。中国の 21Vianet が運営する Microsoft Azure サービスでは、Azure AD Premium は現在サポートされていません。詳細については、[Azure Active Directory フォーラム](http://feedback.azure.com/forums/169401-azure-active-directory)からお問い合わせください。

次のリンクを使用して、最も関心のあるドキュメントに移動してください。

- [概要: Azure AD でのパスワード管理](https://msdn.microsoft.com/library/azure/dn683880.aspx)
- [Azure AD のセルフ サービス パスワードのリセット: セルフ サービスのパスワード リセットを有効化、構成、テストする方法](https://msdn.microsoft.com/library/azure/dn683881.aspx)
- [Azure AD のセルフ サービス パスワードのリセット: ニーズに合わせてパスワード リセットをカスタマイズする方法](https://msdn.microsoft.com/library/azure/dn688249.aspx)
- [Azure AD のセルフ サービス パスワードのリセット: 展開と管理のベスト プラクティス](https://msdn.microsoft.com/library/azure/dn903643.aspx)
- [Azure AD でのパスワード管理のレポート: テナントでパスワード管理アクティビティを表示する方法](https://msdn.microsoft.com/library/azure/dn903641.aspx)
- [パスワード ライトバック: オンプレミスのパスワードの管理用に Azure AD を構成する方法](https://msdn.microsoft.com/library/azure/dn903642.aspx)
- [Azure AD のパスワード管理に関する FAQ/トラブルシューティング](https://msdn.microsoft.com/library/azure/dn683878.aspx)

## 参照トピック

- [Administer your Azure AD directory (Azure AD ディレクトリの管理)](active-directory-administer.md)
- [Azure AD でのユーザーの作成または編集](active-directory-create-users.md)
- [Azure AD でのグループの管理](active-directory-manage-groups.md)
 

<!---HONumber=July15_HO3-->