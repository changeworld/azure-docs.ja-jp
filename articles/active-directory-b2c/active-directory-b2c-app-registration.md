---
title: 'Azure Active Directory B2C: アプリケーションの登録 | Microsoft Docs'
description: Azure Active Directory B2C にアプリケーションを登録する方法
services: active-directory-b2c
documentationcenter: ''
author: swkrish
manager: mbaldwin
editor: bryanla

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/30/2016
ms.author: swkrish

---
# Azure Active Directory B2C: アプリケーションを登録する
## 前提条件
コンシューマーのサインアップおよびサインインを受け付けるアプリケーションを作成するには、最初にアプリケーションを Azure Active Directory B2C テナントに登録する必要があります。「[Azure AD B2C テナントを作成する](active-directory-b2c-get-started.md)」に書かれている手順に従って独自のテナントを取得してください。この記事のすべての手順を実行したら、B2C 機能ブレードがスタート画面にピン留めされているはずです。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## B2C 機能ブレードに移動する
B2C 機能ブレードがスタート画面にピン留めされている場合、B2C テナントの全体管理者として [Azure ポータル](https://portal.azure.com/)にサインインするとすぐに、B2C 機能ブレードが表示されます。

また、**[参照]** をクリックし、[Azure ポータル](https://portal.azure.com/)の左側のナビゲーション ウィンドウで **[Azure AD B2C]** をクリックして、ブレードにアクセスすることもできます。

> [!IMPORTANT]
> B2C 機能ブレードにアクセスするには、B2Cテナントのグローバル管理者である必要があります。他のテナントのグローバル管理者やテナントのユーザーはアクセスできません。Azure ポータルの右上隅にあるテナント スイッチャーを使用して、B2C テナントに切り替えることができます。
> 
> 

## アプリケーションを登録する
1. Azure ポータルの B2C 機能ブレードで、**[アプリケーション]** をクリックします。
2. ブレードの上部にある **[+追加]** をクリックします。
3. アプリケーションの **[名前]** には、コンシューマーがアプリケーションの機能を把握できるような名前を設定します。たとえば、「Contoso B2C app」などと入力します。
4. Web ベースのアプリケーションを作成している場合は、**[Web アプリ/Web API を含める]** スイッチを **[はい]** に切り替えます。**[応答 URL]** は、アプリケーションが要求したトークンを Azure AD B2C が返すエンドポイントです。たとえば、「`https://localhost:44321/`」のように入力します。Web アプリケーションで、Azure AD B2C によってセキュリティ保護された Web API も呼び出す場合は、**[Generate Key (キーの生成)]** ボタンをクリックして**アプリケーション シークレット**を作成することも必要になります。
   
   > [!NOTE]
   > **アプリケーション シークレット**は重要なセキュリティ資格情報であり、適切にセキュリティで保護する必要があります。
   > 
   > 
5. モバイル アプリケーションを作成している場合は、**[ネイティブ クライアントを含める]** スイッチを **[はい]** に切り替えます。自動的に作成される既定の**リダイレクト URI** をメモしておきます。
6. **[作成]** をクリックして、アプリケーションを登録します。
7. 作成したアプリケーションをクリックし、後でコードの作成時に使用するために、グローバルに一意の**アプリケーション クライアント ID** をメモしておきます。

> [!IMPORTANT]
> B2C 機能ブレードで作成したアプリケーションは、同じ場所で管理する必要があります。PowerShell または別のポータルを使用して B2C アプリケーションを編集すると、そのアプリケーションはサポート対象外となり、Azure AD B2C で動作しなくなる可能性があります。
> 
> 

## クイック スタート アプリケーションを構築する
アプリケーションを Azure AD B2C に登録したので、いずれかのクイック スタート チュートリアルを実行して使い始めることができます。以下に、推奨事項をいくつか示します。

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=AcomDC_0831_2016-->