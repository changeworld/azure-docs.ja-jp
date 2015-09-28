<properties
	pageTitle="Azure Active Directory B2C プレビュー: カスタム属性 | Microsoft Azure"
	description="Azure Active Directory B2C でカスタム属性を使用してコンシューマーに関する情報を収集する方法"
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
	ms.date="09/15/2015"
	ms.author="swkrish"/>

#  Azure Active Directory B2C プレビュー: カスタム属性を使用してコンシューマーに関する情報を収集する

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Azure Active Directory (AD) B2C ディレクトリには、組み込みの属性セットが用意されています (名、姓、市区町村、郵便番号など)。ただし、どのようなコンシューマー向けアプリケーションにも、コンシューマーから収集する情報 (属性) について固有の要件があります。Azure AD B2C では、ディレクトリを拡張できます (具体的には、各コンシューマー アカウントで保持される属性セットの拡張)。以下で示すように、[Azure プレビュー ポータル](https://portal.azure.com/)でカスタム属性を作成し、サインアップ ポリシーで使用できます。また、Azure AD Graph API を使用して属性を読み書きすることもできます ([こちら](active-directory-b2c-devquickstarts-graph-dotnet.md)を参照)。

> [AZURE.NOTE]カスタム属性の内部では、[Azure AD Graph API ディレクトリ スキーマ拡張機能](https://msdn.microsoft.com/library/azure/dn720459.aspx)が使用されています。

## カスタム属性を作成する方法

1. [Azure プレビュー ポータルで B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. **[ユーザー属性]** をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. **[名前]** にカスタム属性の名前を入力し (例: "ShoeSize")、必要に応じて **[説明]** を入力します。**[作成]** をクリックします。以上で終わりです。

    > [AZURE.NOTE]現時点では、"String" **データ型**のみを使用できます。将来的には、他のデータ型 (DateTime、Integer など) も追加される予定です。

**[ユーザー属性]** の一覧およびサインアップ ポリシーで、カスタム属性を使用できるようになります。

## サインアップ ポリシーでカスタム属性を使用する方法

1. [Azure プレビュー ポータル](htts://portal.azure.com/)で B2C 機能ブレードに移動します。方法については、[こちら](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)をご覧ください。
2. **[サインアップ ポリシー]** をクリックします。
3. クリックしてサインアップ ポリシーを開きます (例: "B2C\_1\_SiUp")。ブレードの上部にある **[編集]** をクリックします。
4. **[サインアップ属性]** をクリックして、カスタム属性 (例: "ShoeSize") を選択します。**[OK]** をクリックします。
5. **[アプリケーションの要求]** をクリックして、カスタム属性を選択します。**[OK]** をクリックします。 
6. ブレードの上部にある **[保存]** をクリックします。以上で終わりです。

ポリシーで [今すぐ実行] 機能を使用して、コンシューマー エクスペリエンスを確認できます。コンシューマーのサインアップ時に収集される属性の一覧、およびアプリケーションに返送されるトークンに、"ShoeSize" が表示されるようになります。

<!---HONumber=Sept15_HO3-->