<properties
    pageTitle="Azure Active Directory の Enterprise State Roaming を有効にする | Microsoft Azure"
    description="Windows デバイスの Enterprise State Roaming の設定に関してよく寄せられる質問について取り上げます。Enterprise State Roaming によって複数の Windows デバイスの使用環境が統一され、新しいデバイスを構成するために必要な時間が短縮されます。"
    services="active-directory"
    keywords="Enterprise State Roaming, Windows クラウド, Enterprise State Roaming を有効にする方法"
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

 

# Azure Active Directory の Enterprise State Roaming を有効にする

Enterprise State Roaming は、Azure Active Directory (Azure AD) の Premium サブスクリプションを所有しているすべての組織が利用できます。Azure AD サブスクリプションの取得方法について詳しくは、[Azure AD の製品ページ](https://azure.microsoft.com/services/active-directory)を参照してください。

Enterprise State Roaming を有効にすると、Azure Rights Management の無料サブスクリプションのライセンスが自動的に付与されます。この無料サブスクリプションは、エンタープライズ設定データの暗号化と復号化に限定されます。Azure Rights Management の全機能を使用するためには有料サブスクリプションが必要です。

Azure AD サブスクリプションの取得後、次の手順に従って Enterprise State Roaming を有効にします。
 
1. Azure クラシック ポータルにログインします。 
2. 左側にある **[Active Directory]** を選択し、Enterprise State Roaming を有効にするディレクトリを選択します。 ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)	 
3. 上部にある **[構成]** タブに移動します。 ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4.	ページを下にスクロールし、**[設定とエンタープライズ アプリ データの同期が許可されるユーザー]** を選択して **[保存]** をクリックします。 ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Windows 10 デバイスで Enterprise State Roaming サービスを使って設定をローミングするためには、そのデバイスを Azure AD の ID で認証する必要があります。デバイスが Azure AD に参加している場合、そのユーザーの主要なログインは Azure AD の ID です。それ以外の構成は必要ありません。従来のオンプレミスの Active Directory を使用するデバイスの場合、IT 管理者が [Azure AD Connect](active-directory-aadconnect.md) を使ってオンプレミスの Active Directory を Azure AD に接続したうえで、クライアント デバイスが自動的にユーザー データを Azure と同期するよう、グループ ポリシーの構成で強制する必要があります。

## データ ストレージの同期
Enterprise State Roaming のデータは、Azure AD のインスタンスに設定されている国の値と最も適合する [Azure リージョン](https://azure.microsoft.com/regions/)でホストされます。たとえば、国の値が "France" に設定されている顧客のデータは、ヨーロッパ内の Azure リージョンでホストされます。これに対し、Azure AD で国の値が "US" に設定されている顧客のデータは、米国内の Azure リージョンでホストされます。国の値は Azure AD ドメインの作成プロセスで設定され、後から変更することはできません。

データの保管場所についてさらに詳しい情報が必要な場合は、[Azure サポート](https://azure.microsoft.com/support/options/)にチケットを提出してください。

## Enterprise State Roaming の管理
Enterprise State Roaming は、Azure AD テナント管理者が Azure クラシック ポータルから有効にしたり無効にしたりすることができます。 ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

加えてテナント管理者は、ユーザーごとの同期ステータス レポートを閲覧できるほか、設定の同期を特定のセキュリティ グループに制限することもできます。

##データの保持
Azure との間で Enterprise State Roaming を介して同期されるデータは、手動で削除されるか対象となるデータが古いと判断されるまで無期限に保持されます。
 
- **データの手動削除**: Azure AD 管理者の方は設定データを手動で削除する場合、[Azure サポート](https://azure.microsoft.com/support/options/)にチケットを提出してください。
 
 - **ユーザーの削除**: Azure AD でユーザーを削除した場合、30 日間そのユーザー アカウントは無効状態となります。30 日経過すると、アカウントが削除され、関連付けられていた設定データが削除の対象となります。
 - **テナント (ディレクトリ) の削除**: Azure AD からディレクトリ全体を削除した場合は、その操作が直ちに実行されます。そのディレクトリに関連付けられたすべての設定データが削除の対象となります。 
 - **設定の削除**: Azure AD 管理者の方は、特定のユーザーの設定データを直ちに削除する必要がある場合、Azure サポートにチケットを提出してください。 
- **古いデータ**: 1 年間 ("リテンション期間") にわたってアクセスされていないデータは古いデータと見なされ、Azure から削除される場合があります。Windows またはアプリケーションの特定の設定のまとまりや、ユーザーの全設定が古いデータと見なされることもあります。次に例を示します。 
 - 特定の設定全体にデバイスからのアクセスがない場合 (アプリケーションがデバイスから削除された場合や、特定のユーザーの全デバイスで "テーマ" などの設定グループが無効にされた場合など)、リテンション期間の経過後その設定全体が古いと見なされ、削除される可能性があります。 
 - ユーザーがそのすべてのデバイスで設定の同期をオフにした場合、いずれの設定データもアクセスされなくなり、リテンション期間の経過後にそのユーザーのすべての設定データが古いと見なされ、削除される可能性があります。 
 - Azure AD ディレクトリ管理者がディレクトリ全体の Enterprise State Roaming をオフにした場合、そのディレクトリ内のすべてのユーザーについて設定の同期が中止され、リテンション期間後すべてのユーザーの全設定データが古いデータとなって、削除される可能性があります。 

- **削除されたデータの復元**: データの保持ポリシーを設定で変更することはできません。一度完全に削除されたデータは復元できなくなります。ただし、設定データが削除されるのは Azure からのみであることに注意してください。エンド ユーザーのデバイスからは削除されません。その後いずれかのデバイスから Enterprise State Roaming サービスに再接続すると、設定が再同期されて Azure に保存されます。

## 関連トピック
- [Enterprise State Roaming の概要](active-directory-windows-enterprise-state-roaming-overview.md)
- [設定とデータのローミングに関する FAQ](active-directory-windows-enterprise-state-roaming-faqs.md)
- [設定を同期させるためのグループ ポリシーと MDM の設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Windows 10 ローミング設定リファレンス](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->