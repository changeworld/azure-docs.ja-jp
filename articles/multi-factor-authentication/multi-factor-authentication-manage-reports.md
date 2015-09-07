<properties 
	pageTitle="Azure Multi-Factor Authentication レポート"
	description="ここでは、Azure Multi-Factor Authentication 機能のレポートを使用する方法について説明します。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Azure Multi-Factor Authentication のレポート

Azure Multi-Factor Authentication は、個人や組織が使用できるいくつかのレポートを提供します。これらのレポートは、Multi-Factor Authentication 管理ポータルを通じてアクセスできます。次に、利用可能なレポートの一覧を示します。

レポートは、Azure 管理ポータルを介してアクセスできます。

名前| 説明
:------------- | :------------- | 
使用法 | 使用法レポートは、全体的な使用状況、ユーザーの概要およびユーザーの詳細に関する情報を示します。
サーバーの状態|このレポートは、アカウントに関連付けられている Multi-Factor Authentication Server の状態を示します。
ユーザーのブロックの履歴|これらのレポートは、ユーザーのブロックまたはブロック解除の要求の履歴を示します。
ユーザーの認証バイパスの履歴|ユーザーの電話番号について、Multi-Factor Authentication をバイパスする要求の履歴を示します。
不正アクセスのアラート|指定した日付範囲で送信された不正アクセスのアラートの履歴を示します。
キューに登録済み|処理するためにキューに追加されたリストとその状態を一覧表示します。レポートが完成すると、そのレポートのダウンロードまたは表示を行うためのリンクが提供されます。

## レポートを表示するには

1. [http://azure.microsoft.com](http://azure.microsoft.com) にログオンします。
2. 左側で、[Active Directory] を選択します。
3. 上部にある [多要素認証プロバイダー] を選択します。多要素認証プロバイダーの一覧が表示されます。
4. 複数の多要素認証プロバイダーがある場合は、不正アクセスのアラート レポートを表示するプロバイダーを選択し、ページの下部にある [管理] をクリックします。プロバイダーが 1 つだけある場合は、単に [管理] をクリックします。これにより、Azure Multi-Factor Authentication 管理ポータルが開きます。
5. Azure Multi-Factor Authentication 管理ポータルの左側に、[レポートの表示] が表示されます。ここから、上記で説明したレポートを選択できます。


 
<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**その他のリソース**

* [ユーザー向け](multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication (MSDN)](https://msdn.microsoft.com/library/azure/dn249471.aspx)
 

<!---HONumber=August15_HO9-->