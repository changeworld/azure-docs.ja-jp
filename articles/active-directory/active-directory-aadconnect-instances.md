<properties
	pageTitle="Azure AD Connect: 同期サービス インスタンス | Microsoft Azure"
	description="このページでは、Azure AD のインスタンスに関する注意事項を説明します。"
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
	ms.date="05/10/2016"
	ms.author="andkjell"/>

# Azure AD Connect: インスタンスに関する特別な考慮事項
Azure AD Connect は、世界中の Azure AD と Office 365 のインスタンスで最もよく使われています。ただし、それ以外のインスタンスも存在し、URL の要件が異なるだけでなく、その他の特別な考慮事項があります。

## Microsoft Cloud Germany
[Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) は、ドイツのデータ トラスティによって運営されている主権のあるクラウドです。

このクラウドは現在プレビューの段階です。通常はユーザーが自ら行うことができるドメインの確認などのシナリオの多くは、事業者によって行われる必要があります。プレビューに参加する方法の詳細については、最寄りの Microsoft の担当者にお問い合わせください。

プロキシ サーバーで開く URL |
--- |
*.microsoftonline.de |
*.windows.net |
\+証明書の失効リスト |

Azure AD ディレクトリにサインインするときは、onmicrosoft.de ドメインのアカウントを使用する必要があります。

Microsoft Cloud Germany に現在ない機能:

- Azure AD Connect Health は使用できません。
- 自動更新は使用できません。
- パスワード ライトバックは使用できません。

## Microsoft Azure Government クラウド
[Microsoft Azure Government クラウド](https://azure.microsoft.com/features/gov/)は、米国政府向けクラウドです。

このクラウドは、DirSync の以前のリリースでサポートされています。Azure AD Connect のビルド 1.1.180 から、次世代のクラウドがサポートされています。この世代は、米国のみをベースにしたエンドポイントを使用し、プロキシ サーバーで開く URL のリストが異なります。

プロキシ サーバーで開く URL |
--- |
*.microsoftonline.com |
*.gov.us.microsoftonline.com |
\+証明書の失効リスト |

Azure AD Connect は、Azure AD ディレクトリが Government クラウドにあることを自動的に検出できません。そのため、Azure AD Connect のインストール時に次の操作を実行する必要があります。

1. Azure AD Connect のインストールを開始します。
2. 使用許諾契約書への同意が求められる最初のページが表示されたら、先に進まずに、インストール ウィザードを実行中のままにしておきます。
3. regedit を起動し、レジストリ キー `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` の値を `2` に変更します。
4. Azure AD Connect のインストール ウィザードに戻り、使用許諾契約書に同意して続行します。インストール中は、**カスタム構成**インストール パス (簡単インストールではなく) を使用してください。以降は通常どおりにインストールを続行します。

Microsoft Azure Government クラウドに現在ない機能:

- Azure AD Connect Health は使用できません。
- 自動更新は使用できません。
- パスワード ライトバックは使用できません。

## 次のステップ
「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=AcomDC_0518_2016-->