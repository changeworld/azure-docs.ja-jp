<properties
   pageTitle="Azure AD Connect: プレビュー段階の機能 |Microsoft Azure"
   description="このトピックでは、Azure AD Connect のプレビュー段階の機能について詳しく説明します。"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="12/30/2015"
   ms.author="andkjell;billmath"/>

# プレビュー段階の機能の詳細
このトピックでは、現在プレビュー段階の機能を使用する方法について説明します。

## ディレクトリ拡張機能
ディレクトリ拡張機能を使用すると、オンプレミスの Active Directory から独自の属性を使用して、Azure AD のスキーマを拡張できます。独自に構築した LOB アプリで利用する属性を引き続きオンプレミスで管理することが可能です。これらの属性は、[Azure AD Graph ディレクトリ拡張機能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)または [Microsoft Graph](https://graph.microsoft.io/) を通じて利用できます。それぞれ [Azure AD Graph Explorer](https://graphexplorer.cloudapp.net) と [Microsoft Graph Explorer](https://graphexplorer2.azurewebsites.net/) を使用して、利用可能な属性を表示できます。

現在のところ、これらの属性が使用される Office 365 ワークロードはありません。

これらの属性が利用できるアプリケーションは、Azure AD Connect のインストール中に登録されます。このアプリケーションは、Azure ポータルで確認できます。![Schema Extension App](./media/active-directory-aadconnect-feature-preview/extension3.png)

今後は、これらの属性を Graph を通じて利用できます。![Graph](./media/active-directory-aadconnect-feature-preview/extension4.png)

属性には、extension\_{AppClientId}\_ というプレフィックスが付きます。AppClientId の値は、ご使用の Azure AD ディレクトリに存在するすべての属性で共通となります。

単一値の属性だけがサポートされます。属性の値は、250 文字以下である必要があります。

## グループの書き戻し
オプション機能のグループの書き戻しのためのオプションでは、"Office 365 グループ" を Exchange がインストールされているフォレストに書き戻すことができます。これは、クラウドで常に管理されるグループです。オンプレミスの Exchange をご利用の場合、これらのグループをオンプレミスに書き戻すことができます。オンプレミスの Exchange メールボックスを所有するユーザーは、それらのグループから電子メールを送受信することができます。

Office 365 グループの詳細とその使い方については、[こちら](http://aka.ms/O365g)をご覧ください。

このグループは、オンプレミスの AD DS では配布グループとして表現されます。この新しいグループの種類が表示されるようにするには、オンプレミスの Exchange サーバーを Exchange 2013 累積更新プログラム 8 (2015 年 3 月リリース) または Exchange 2016 で更新する必要があります。

**プレビュー期間中の注意事項**

- 現在、プレビュー版ではアドレス帳の属性は設定されません。この属性がないと、GAL にグループが表示されません。この属性は、Exchange PowerShell コマンドレット `update-recipient` を使って設定するのが最も簡単です。
- Exchange のスキーマを持つフォレストのみを、グループの有効なターゲットとすることができます。Exchange が見つからない場合は、グループの書き戻しを有効にすることはできません。
- 現在サポートされているのは、単一フォレストから成る Exchange 組織のデプロイだけです。複数の Exchange 組織がオンプレミスに存在する場合、他のフォレストにグループを反映するには、オンプレミスの GALSync ソリューションが必要となります。
- 現在、グループの書き戻し機能では、セキュリティ グループや配布グループは扱えません。

>[AZURE.NOTE]グループの書き戻しには、Azure AD Premium のサブスクリプションが必要です。

## ユーザーの書き戻し
> [AZURE.IMPORTANT]ユーザーの書き戻しプレビュー機能は、Azure AD Connect の 2015 年 8 月の更新時に一時的に削除されました。この機能を有効にしていた場合は、無効にする必要があります。

ユーザーの書き戻しは初期プレビュー段階です。この機能を使用できるのは、Azure AD がすべてのユーザー オブジェクトのソースであり、この機能を有効にする前にオンプレミスの Active Directory が空 (初期状態のデプロイ) である場合に限られます。

> [AZURE.WARNING]この機能の評価は必ずテスト環境で行ってください。本番環境で使用される Azure AD ディレクトリでは使用しないでください。

.

>[AZURE.NOTE]ユーザーの書き戻しには、Azure AD Premium のサブスクリプションが必要です。

## 次のステップ
「[Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)」に進んでください。

「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=AcomDC_0107_2016-->