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
   ms.date="12/02/2015"
   ms.author="andkjell;billmath"/>

# プレビュー段階の機能の詳細
このトピックでは、現在プレビュー段階の機能を使用する方法について説明します。

## ユーザーの書き戻し
> [AZURE.IMPORTANT]ユーザーの書き戻しプレビュー機能は、AAD Connectの 8 月の更新時に一時的に削除されました。この機能を有効にしていた場合は、無効にする必要があります。

ユーザーの書き戻しは初期プレビュー段階です。この機能を使用できるのは、Azure AD がすべてのユーザー オブジェクトのソースであり、この機能を有効にする前にオンプレミスの Active Directory が空である場合に限られます。

> [AZURE.IMPORTANT]この機能はテスト環境でのみテストします。本番環境で使用される Azure AD ディレクトリでは使用しないでください。

## グループの書き戻し
オプション機能のグループの書き戻しのためのオプションでは、"Office 365 グループ" を Exchange がインストールされているフォレストに書き戻すことができます。これは、クラウドで常に管理される新しいグループの種類です。次のように outlook.office365.com や myapps.microsoft.com で確認することができます。


![フィルターの同期](./media/active-directory-aadconnect-feature-preview/office365.png)

![フィルターの同期](./media/active-directory-aadconnect-feature-preview/myapps.png)

このグループは、オンプレミスの AD DS では配布グループとして表現されます。この新しいグループの種類が表示されるようにするには、オンプレミスの Exchange サーバーを Exchange 2013 累積更新プログラム 8 (2015 年 3 月リリース) で更新する必要があります。

**注**

- 現在、プレビュー版ではアドレス帳の属性は設定されません。これを最も簡単に行うには、Exchange PowerShell コマンドレットの update-recipient を使用します。
- Exchange のスキーマを持つフォレストのみを、グループの有効なターゲットとすることができます。Exchange が見つからない場合は、グループの書き戻しを有効にすることはできません。
- 現在、グループの書き戻し機能では、セキュリティ グループや配布グループは扱えません。

Office 365 グループの詳細については、[こちら](http://aka.ms/O365g)をご覧ください。

## ディレクトリ拡張機能
ディレクトリ拡張機能を使用すると、オンプレミスの Active Directory から独自の属性を使用して、Azure AD のスキーマを拡張できます。

単一値の属性だけがサポートされます。属性の値は、250 文字以下である必要があります。選択した属性を使用してメタバースと Azure AD のスキーマが拡張されます。Azure AD では、これらの属性を使用して新しいアプリケーションが追加されます。

![フィルターの同期](./media/active-directory-aadconnect-feature-preview/extension3.png)

これらの属性がグラフで使用可能になります。

![フィルターの同期](./media/active-directory-aadconnect-feature-preview/extension4.png)

## 次のステップ
「[Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)」に進んでください。

「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=AcomDC_1203_2015-->