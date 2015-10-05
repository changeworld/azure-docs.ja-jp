<properties
	pageTitle="Azure Active Directory: ディレクトリ サポートの作成トピック | Microsoft Azure"
	description="Azure Active Directory ディレクトリまたは Azure Active Directory B2C ディレクトリの作成 - 問題と解決"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Azure AD ディレクトリまたは Azure AD B2C ディレクトリの作成 - 問題と解決

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Azure AD ディレクトリの作成

Azure AD ディレクトリの初めての作成が失敗する場合は、もう一度やり直してください。問題が解決しない場合は、サポートにお問い合わせください。

## Azure AD B2C ディレクトリの作成 (プレビュー)

[Azure AD B2C ディレクトリの作成](active-directory-b2c-get-started)の間に発生する可能性のある既知の問題があります。

- Azure AD B2C ディレクトリがディレクトリの一覧に表示されない場合は、もう一度やり直してください。
- Azure AD B2C ディレクトリがディレクトリの一覧に表示されても、"B2C ディレクトリ 'contosob2c' の作成を完了できませんでした。詳細については、この[リンク](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409)を参照してください。" というエラー メッセージが表示される場合は、次のいずれかを行ってください。
    - リンク [https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com) を使用して、Azure プレビュー ポータルの B2C 機能ブレードに移動します。**{directory}** は、ディレクトリの作成時に使用した名前に置き換えます (例: contosob2c)。その後、グローバル管理者としてサインインします。ブレードの上部にある **[B2C の機能を有効にする]** をクリックし、**[OK]** をクリックします。Azure AD B2C ディレクトリが使用できるようになります。
	- 作成したディレクトリを削除し、もう一度やり直します。
- 上記の解決策で問題が解決しない場合は、サポートに問い合わせてください。Azure AD B2C のサポートを要求する方法については、[こちらの記事](active-directory-b2c-support.md)を参照してください。

<!---HONumber=Sept15_HO4-->