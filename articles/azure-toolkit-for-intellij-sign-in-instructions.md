---
title: "Azure Toolkit for IntelliJ のサインイン手順"
description: "Azure Toolkit for IntelliJ を使用して Microsoft Azure にサインインする方法について説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: routlaw
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/20/2017
ms.author: robmcm
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: def5957b2ed58aee2e03c879ba9fd3d6c714e358
ms.contentlocale: ja-jp
ms.lasthandoff: 09/02/2017

---

# <a name="sign-in-instructions-for-the-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ のサインイン手順

Azure Toolkit for IntelliJ には、Azure アカウントにサインインするための 2 つの方法が用意されています。

  * **自動**: Azure アカウントに自動的にサインインするために使用できる資格情報ファイルを作成します。
  * **対話型**: Azure アカウントにサインインするたびに、Azure 資格情報を入力します。

以下のセクションで、各方法の使用方法について説明します。

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="sign-in-to-your-azure-account-automatically"></a>Azure アカウントに自動的にサインインする

このセクションでは、サービス プリンシパル データを格納する資格情報ファイルを作成します。 このプロセスを完了すると、プロジェクトを開くたびに、Eclipse によって資格情報ファイルが自動的に使用されて、Azure に自動的にサインインします。

1. IntelliJ IDEA でプロジェクトを開きます。

2. **[ツール]** メニューの **[Azure]** をポイントし、**[Azure サインイン]** をクリックします。

   ![IntelliJ Azure サインイン コマンド][A01]

3. **[Azure サインイン]** ダイアログ ボックスで、**[自動]** を選択し、**[新規]** をクリックします。

   ![[自動] が選択されている [Azure サインイン] ウィンドウ][A02]

4. **[Azure ログイン]** ダイアログ ウィンドウで、Azure 資格情報を入力し、**[サインイン]** をクリックします。

   ![[Azure ログイン] ダイアログ ウィンドウ][A03]

5. **[Create authentication files]\(認証ファイルの作成\)** ダイアログ ボックスが表示されたら、使用するサブスクリプションを選び、宛先ディレクトリを選択し、**[開始]** をクリックします。

   ![[Create authentication files]\(認証ファイルの作成\) ウィンドウ][A04]

6. **[Service Principal Creatation Status]\(サービス プリンシパル作成ステータス\)** ダイアログ ボックスで、ファイルが正常に作成されたら **[OK]** をクリックします。

   ![[Service Principal Creatation Status]\(サービス プリンシパル作成ステータス\) ダイアログ ボックス][A05]

7. **[Azure サインイン]** ウィンドウで、**[サインイン]** をクリックします。

   ![[Azure ログイン] ダイアログ ボックス][A06]

8. **[サブスクリプションの選択]** ダイアログ ボックスで、使用するサブスクリプションを選択し、**[OK]** をクリックします。

   ![[サブスクリプションの選択] ダイアログ ボックス][A07]

## <a name="sign-out-of-your-azure-account-after-you-have-signed-in-automatically"></a>自動的にサインインした後で Azure アカウントからサインアウトする

前の手順を使用してアカウントを構成すると、IntelliJ IDEA を再起動するたびに Azure Toolkit によって Azure アカウントに自動的にサインインします。 ただし、Azure アカウントからサインアウトし、Azure Toolkit による自動的なサインインが行われないようにするには、以下の手順を実行します。

1. IntelliJ IDEA で、**[ツール]** メニューの **[Azure]** をポイントし、**[Azure サインアウト]** をクリックします。

   ![IntelliJ Azure サインアウト コマンド][L01]

2. **[Azure サインアウト]** 確認ウィンドウで、**[はい]** をクリックします。

   ![[Azure サインアウト] 確認ウィンドウ][L03]

## <a name="sign-in-to-your-azure-account-automatically-by-using-an-existing-credentials-file"></a>既存の資格情報ファイルを使用して Azure アカウントに自動的にサインインする

IntelliJ IDEA を使用しているときに Azure アカウントからサインアウトした場合は、既存の資格情報ファイルを使用してアカウントに自動的にサインインしなおす必要があります。 既存の視覚情報ファイルを使用するように Azure Toolkit for Eclipse を構成するには、以下の手順に従います。

1. IntelliJ IDEA でプロジェクトを開きます。

2. **[ツール]** メニューの **[Azure]** をポイントし、**[Azure サインイン]** をクリックします。

   ![IntelliJ Azure サインイン コマンド][A01]

3. **[Azure サインイン]** ウィンドウで、**[自動]** を選択し、**[参照]** をクリックします。

   ![[自動] が選択されている [Azure サインイン] ウィンドウ][A02]

4. **[Select Authentication File]\(認証ファイルの選択\)** ダイアログ ボックスで、作成済みの資格情報ファイルを選択し、**[選択]** をクリックします。

   ![[Select Authentication File]\(認証ファイルの選択\) ダイアログ ボックス][A08]

5. **[Azure サインイン]** ウィンドウで、**[サインイン]** をクリックします。

   ![[自動] が選択されている [Azure サインイン] ウィンドウ][A06]

6. **[サブスクリプションの選択]** ダイアログ ボックスで、使用するサブスクリプションを選択し、**[OK]** をクリックします。

   ![[サブスクリプションの選択] ダイアログ ボックス][A07]

## <a name="sign-in-to-your-azure-account-interactively"></a>Azure アカウントに対話形式でサインインする

Azure 資格情報を手動で入力して Azure にサインインするには、次の手順に従います。

1. IntelliJ IDEA でプロジェクトを開きます。

2. **[ツール]** をクリックし、**[Azure]** をポイントし、**[Azure サインイン]** をクリックします。

   ![IntelliJ Azure サインイン コマンド][I01]

3. **[Azure サインイン]** ウィンドウで、**[対話型]** を選択し、**[サインイン]** をクリックします。

   ![[対話型] が選択されている [Azure サインイン] ウィンドウ][I02]

4. **[Azure ログイン]** ダイアログ ボックスが表示されたら、Azure 資格情報を入力し、**[サインイン]** をクリックします。

   ![[Azure ログイン] ダイアログ ウィンドウ][I03]

5. **[サブスクリプションの選択]** ダイアログ ボックスで、使用するサブスクリプションを選択し、**[OK]** をクリックします。

   ![[サブスクリプションの選択] ダイアログ ボックス][I04]

## <a name="sign-out-of-your-azure-account-after-you-have-signed-in-interactively"></a>対話形式でサインインした後で Azure アカウントからサインアウトする

前の手順を使用してアカウントを構成すると、IntelliJ IDEA を再起動するたびに Azure アカウントから自動的にサインアウトします。 ただし、IntelliJ IDEA を*再起動せずに* Azure アカウントからサインアウトするには、以下の手順を実行します。

1. IntelliJ IDEA で、**[ツール]** メニューの **[Azure]** をポイントし、**[Azure サインアウト]** をクリックします。

   ![IntelliJ Azure サインアウト コマンド][L01]

2. **[Azure サインアウト]** 確認ウィンドウで、**[はい]** をクリックします。

   ![[Azure サインアウト] 確認ウィンドウ][L02]

## <a name="next-steps"></a>次のステップ

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

<!-- URL List -->

[Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Create a Hello World web app for Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Sign-in instructions for the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Sign-in instructions for the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[What's new in the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[What's new in the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/

<!-- IMG List -->

[I01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I01.png
[I02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I02.png
[I03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I03.png
[I04]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I04.png

[A01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A01.png
[A02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A02.png
[A03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A03.png
[A04]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A04.png
[A05]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A05.png
[A06]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A06.png
[A07]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A07.png
[A08]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A08.png

[L01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L01.png
[L02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L02.png
[L03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L03.png

