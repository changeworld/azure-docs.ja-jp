<properties
	pageTitle="Azure Active Directory: テナント サポートの作成に関するトピック | Microsoft Azure"
	description="Azure Active Directory テナントまたは Azure Active Directory B2C テナントの作成 - 問題と解決策"
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
	ms.date="09/24/2015"
	ms.author="swkrish"/>

# Azure AD テナントまたは Azure AD B2C テナントの作成 - 問題と解決策

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Azure AD テナントの作成

Azure AD テナントの初めての作成が失敗した場合は、もう一度やり直してください。問題が解決しない場合は、サポートにお問い合わせください。

## Azure AD B2C テナントの作成 (プレビュー)

[Azure AD B2C テナントの作成](active-directory-b2c-get-started.md)中に発生する可能性のある既知の問題があります。

- Azure AD B2C テナントがテナントの一覧に表示されない場合は、もう一度やり直してください。
- Azure AD B2C テナントがテナントの一覧に表示されても、"B2C テナント 'contosob2c' の作成を完了できませんでした" というエラー メッセージが表示されます。詳細については、この[リンク](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409)を参照してください。" というエラー メッセージが表示される場合は、次のいずれかを行ってください。
    - このリンク [https://portal.azure.com/{tenant}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com) を使用して、Azure プレビュー ポータルの B2C 機能ブレードに移動します。**{tenant}** をテナントの作成時に使用した名前 (例: contosob2c) に置き換え、グローバル管理者としてサインインします。ブレードの上部にある **[B2C の機能を有効にする]** をクリックし、**[OK]** をクリックします。Azure AD B2C テナントを使用できるようになります。
	- 作成したテナントを削除し、もう一度やり直します。
- 上記の解決策で問題が解決しない場合は、サポートに問い合わせてください。Azure AD B2C のサポートを要求する方法については、[こちらの記事](active-directory-b2c-support.md)を参照してください。

<!---HONumber=Oct15_HO1-->