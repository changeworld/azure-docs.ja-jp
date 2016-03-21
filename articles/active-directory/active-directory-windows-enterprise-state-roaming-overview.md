<properties
	pageTitle="Enterprise State Roaming の概要 | Microsoft Azure"
	description="Windows デバイスの Enterprise State Roaming の設定について取り上げます。Enterprise State Roaming によって複数の Windows デバイスの使用環境が統一され、新しいデバイスを構成するために必要な時間が短縮されます。"
	services="active-directory"
    keywords="Enterprise State Roaming とは, エンタープライズ同期, Windows クラウド"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

# Enterprise State Roaming の概要

Windows 10 では、[Azure Active Directory (Azure AD)](active-directory-whatis.md) のユーザーが、そのユーザー設定とアプリケーション設定のデータをクラウドと安全に同期させることができます。Enterprise State Roaming によって複数の Windows デバイスの使用環境が統一され、新しいデバイスを構成するために必要な時間が短縮されます。Enterprise State Roaming の働きは、Windows 8 で初めて導入された標準的な[コンシューマー設定の同期機能](http://windows.microsoft.com/ja-JP/windows-8/sync-settings-pcs)と似ています。それに加えて、Enterprise State Roaming には次の機能が用意されています。

- **企業データとコンシューマー データの分離** – 組織のデータはその組織の管理下に置かれます。コンシューマー クラウド アカウントに企業データが混入したり、エンタープライズ クラウド アカウントにコンシューマー データが混入したりすることはありません。 
- **セキュリティの強化** – データはユーザーの Windows 10 デバイスから送信される前に、Azure Rights Management (Azure RMS) を使って自動的に暗号化されます。クラウドには、暗号化された状態のままデータが保存されます。名前空間 (設定の名前や Windows アプリの名前) を除き、コンテンツはすべて暗号化された状態でクラウドに保存されます。  
- **管理と監視のサービス** – 組織内のだれがどのデバイスの設定を同期させているかに関して管理性と可視性が向上しています。 
- **クラウド内のデータの地理的位置** – データは、Azure AD ドメインの国に応じた Azure リージョンに保存されます。 



| 記事 | 説明 |
|--------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Azure Active Directory の Enterprise State Roaming を有効にする](active-directory-windows-enterprise-state-roaming-enable.md) | Enterprise State Roaming は、Azure Active Directory (Azure AD) の Premium サブスクリプションを所有しているすべての組織が利用できます。Azure AD サブスクリプションの取得方法について詳しくは、Azure AD の製品ページを参照してください。 |
| [設定とデータのローミングに関する FAQ](active-directory-windows-enterprise-state-roaming-faqs.md) | このトピックでは、設定とアプリ データの同期に関する質問とその答えを IT 管理者向けに紹介しています。 |
| [設定を同期させるためのグループ ポリシーと MDM の設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) | Windows 10 では、グループ ポリシーとモバイル デバイス管理 (MDM) のポリシー設定を通じて、設定の同期を制限することができます。 |
| [Windows 10 ローミング設定リファレンス](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) | Windows 10 でローミングまたはバックアップされる全設定の一覧が掲載されています。 |

<!---HONumber=AcomDC_0204_2016-->