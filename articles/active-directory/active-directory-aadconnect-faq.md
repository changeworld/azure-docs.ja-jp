<properties
	pageTitle="Azure AD Connect の FAQ | Microsoft Azure"
	description="このページでは、Azure AD Connect についてよく寄せられる質問を紹介します。"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="billmath"/>

# Azure Active Directory Connect の FAQ

## 一般的なインストール
**Q: Azure AD のグローバル管理者が 2FA を有効化している場合、インストールを実行できますか。**

この場合、インストールは実行できません。Azure AD Connect をインストールするグローバル管理者は、MFA を有効化しないでください。この制限については、将来的にサポートされます。

**Q: Azure AD Connect を無人インストールする方法はありますか。**

インストール ウィザードを使用して Azure AD Connect をインストールする場合にのみサポートされます。サイレント モードでの無人インストールはサポートされていません。

## 高速インストール

## カスタム インストール

## ネットワーク
**Q: ファイアウォールやネットワーク デバイスなど、ネットワーク上で接続を開ける最大時間を制限するものがあります。Azure AD Connect を使用する場合、クライアント側のタイムアウトしきい値はどのくらいにすればいいでしょうか。**

すべてのネットワーク ソフトウェアや物理デバイスなど、接続を開ける最大時間を制限するものは、Azure AD Connect クライアントがインストールされているサーバーと Azure Active Directory 間の接続に対して少なくとも 5 分 (300 秒) のしきい値を使用する必要があります。これは、以前リリースされたすべての Microsoft Identity 同期ツールにも適用されます。

**Q: Office 365 の証明書を更新するように求める電子メールを受信した場合はどうすればいいですか。**

[こちら](active-directory-aadconnect-o365-certs.md)の記事に記載されているガイダンスに従って、証明書を更新して解決してください。

## トラブルシューティング

**Q: Azure AD Connect に関するヘルプを参照する方法を教えてください。**

[Microsoft サポート技術情報 (KB) の検索](https://www.microsoft.com/ja-JP/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Azure AD Connect のサポートに関する一般的な障害対応の技術的な解決策について、Microsoft サポート技術情報 (KB) を検索してください。

[Microsoft Azure Active Directory フォーラム](https://social.msdn.microsoft.com/Forums/azure/ja-JP/home?forum=WindowsAzureAD)

- このコミュニティで技術的な質問と回答を検索して参照したり、[こちら](https://social.msdn.microsoft.com/Forums/azure/ja-JP/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)をクリックして独自の質問を行ったりできます。


[Azure AD Connect のカスタマー サポート](https://manage.windowsazure.com/?getsupport=true)

- このリンクを使用して、Azure ポータルからサポートを受けることができます。

<!---HONumber=Nov15_HO3-->