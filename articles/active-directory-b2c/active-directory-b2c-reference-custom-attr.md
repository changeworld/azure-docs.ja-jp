---
title: 'Azure Active Directory B2C: カスタム属性 | Microsoft Docs'
description: Azure Active Directory B2C でカスタム属性を使用してコンシューマーに関する情報を収集する方法
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: 6f285c10b7d8ff92c8568c42b6a78dc4ea9bcc74
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure Active Directory B2C: カスタム属性を使用してコンシューマーに関する情報を収集する
Azure Active Directory (Azure AD) B2C ディレクトリには、組み込みの情報セット (属性) が用意されています (名、姓、市区町村、郵便番号など)。 ただし、どのようなコンシューマー向けアプリケーションにも、コンシューマーから収集する属性について固有の要件があります。 Azure AD B2C では、各コンシューマー アカウントで保持される属性セットを拡張できます。 以下で示すように、 [Azure Portal](https://portal.azure.com/) でカスタム属性を作成し、サインアップ ポリシーで使用できます。 また、 [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)を使用してこれらの属性を読み書きすることもできます。

> [!NOTE]
> カスタム属性では、 [Azure AD Graph API ディレクトリ スキーマ拡張機能](https://msdn.microsoft.com/library/azure/dn720459.aspx)が使用されています。
> 
> 

## <a name="create-a-custom-attribute"></a>カスタム属性を作成する
1. [この手順に従って、Azure Portal で B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
2. **[ユーザー属性]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. **[名前]** にカスタム属性の名前を入力し (例: "ShoeSize")、必要に応じて **[説明]** を入力します。 **Create** をクリックしてください。
   
   > [!NOTE]
   > 現在使用できる**データ型**は、"String"、"Boolean"、"Int" だけです。
   > 
   > 

**[ユーザー属性]**の一覧およびサインアップ ポリシーで、カスタム属性を使用できるようになります。

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>サインアップ ポリシーでカスタム属性を使用する
1. [この手順に従って、Azure Portal で B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
2. **[サインアップ ポリシー]**をクリックします。
3. クリックしてサインアップ ポリシーを開きます (例: "B2C_1_SiUp")。 ブレードの上部にある **[編集]** をクリックします。
4. **[サインアップ属性]** をクリックして、カスタム属性 (例: "ShoeSize") を選択します。 Click **OK**.
5. **[アプリケーションの要求]** をクリックして、カスタム属性を選択します。 Click **OK**.
6. ブレードの上部にある **[保存]** をクリックします。

ポリシーで [今すぐ実行] 機能を使用して、コンシューマー エクスペリエンスを確認できます。 コンシューマーのサインアップ時に収集される属性の一覧、およびアプリケーションに返送されるトークンに、"ShoeSize" が表示されるようになります。

## <a name="notes"></a>メモ
* サインアップ ポリシーと共に、カスタム属性をサインアップ/サインイン ポリシーおよびプロファイル編集ポリシーとして使用することもできます。
* カスタム属性には既知の制限があります。 カスタム属性が作成されるのは、いずれかのポリシーで初めて使用されるときだけであり、 **[ユーザー属性]**の一覧に追加されるときではありません。

