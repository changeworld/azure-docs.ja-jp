---
title: Azure Stack でサービス プリンシパルの資格情報を Key Vault に格納する | Microsoft Docs
description: Azure Stack でサービス プリンシパルの資格情報が Key Vault にどのように格納されるかについて説明します
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2019
ms.author: sethm
ms.openlocfilehash: 570c1adc2f4615e78cbe5656c13b0e22b863baf7
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192697"
---
# <a name="store-service-principal-credentials-in-key-vault"></a>サービス プリンシパルの資格情報を Key Vault に格納する

通常、Azure Stack におけるアプリケーションの開発では、デプロイ前にサービス プリンシパルを作成し、その資格情報を使用して認証を行う必要があります。 しかし、サービス プリンシパルに関して保存された資格情報が間違った場所に置かれることも少なくありません。 この記事では、サービス プリンシパルを作成し、その値を後で取り出せるよう Azure Key Vault に格納する方法について説明します。

Key Vault の詳細については、[こちらの記事](azure-stack-key-vault-intro.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure Key Vault サービスを含むオファーをサブスクライブします。
- Azure Stack で使うように PowerShell を構成します。

## <a name="key-vault-in-azure-stack"></a>Azure Stack の Key Vault

Azure Stack の Key Vault には、クラウド アプリケーションやクラウド サービスで使用される暗号化キーとシークレットを保護する機能があります。 Key Vault を使用することにより、キーとシークレットを暗号化することができます。

キー コンテナーを作成するには、次の手順に従います。

1. Azure Stack ポータルにサインインします。

2. ダッシュボードから、**[+ リソースの作成]**、**[セキュリティ + ID]**、**[Key Vault]** の順に選択します。

   ![キー コンテナーの作成](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. **[Key Vault の作成]** ウィンドウで、Vault に**名前**を割り当てます。 Vault の名前には、英数字とハイフン (-) 文字のみを含めることができます。 名前を数字で始めることはできません。

4. 使用可能なサブスクリプションの一覧から選択します。

5. 既存のリソース グループを選択するか、新しいリソース グループを作成します。

6. [価格レベル] を選択します。

7. 既存のアクセス ポリシーのいずれかを選択するか、新しいアクセス ポリシーを作成します。 この Vault に対して操作を実行するための権限を、アクセス ポリシーを通じて、ユーザー、アプリケーション、またはセキュリティ グループに付与できます。

8. 必要に応じて、[高度なアクセス ポリシー] を選んで機能へのアクセスを有効にします。

9. 設定を構成したら、**[OK]** を選択し、**[作成]** を選択します。 キー コンテナーのデプロイが開始されます。

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

1. Azure portal で Azure アカウントにサインインします。

2. **[Azure Active Directory]**、**[アプリの登録]**、**[追加]** の順に選択します。

3. アプリケーションの名前と URL を指定します。 作成するアプリケーションの種類として、**[Web アプリ/API]** または **[ネイティブ]** を選択します。 値を設定したら、**[作成]** をクリックします。

4. **[Active Directory]**、**[アプリの登録]** を順に選択し、目的のアプリケーションを選択します。

5. **アプリケーション ID** をコピーし、アプリケーション コードに保存します。 サンプル アプリケーション内にあるアプリケーションでは、**アプリケーション ID** を参照するときに**クライアント ID** を使用します。

6. 認証キーを生成するには、**[キー]** を選択します。

7. キーの説明を入力し、期間を指定します。

8. **[保存]** を選択します。

9. **[保存]** をクリックした後、利用可能になった**キー**をコピーします。

## <a name="store-the-service-principal-inside-key-vault"></a>Key Vault 内にサービス プリンシパルを格納する

1. Azure Stack のユーザー ポータルにサインインした後、先ほど作成したキー コンテナーを選択し、**[シークレット]** タイルを選択します。

2. **[シークレット]** ウィンドウで **[生成/インポート]** を選択します。

3. **[シークレットの作成]** ウィンドウで、オプションの一覧から **[手動]** を選択します。

4. キーの名前として、サービス プリンシパルからコピーした**アプリケーション ID** を入力します。 キーの名前には、英数字とハイフン (-) 文字のみを含めることができます。

5. サービス プリンシパルから **[値]** タブにキーの値を貼り付けます。

6. **[コンテンツの種類]** に **[サービス プリンシパル]** を選択します。

7. キーの **[アクティブ化した日]** と **[有効期限]** の値を設定します。

8. **[作成]** を選択してデプロイを開始します。

シークレットが正常に作成された後、そこにサービス プリンシパルの情報が格納されます。 いつでも **[シークレット]** からそれを選択し、そのプロパティを表示したり変更を加えたりすることができます。 プロパティ セクションにはシークレット識別子が含まれます。これは、外部アプリケーションがこのシークレットにアクセスするために使う Uniform Resource Identifier (URI) です。

## <a name="next-steps"></a>次の手順

- [サービス プリンシパルの使用](azure-stack-create-service-principals.md)
- [ポータルを使って Azure Stack の Key Vault を管理する](azure-stack-key-vault-manage-portal.md)  
- [PowerShell を使用した Azure Stack での Key Vault の管理](azure-stack-key-vault-manage-powershell.md)