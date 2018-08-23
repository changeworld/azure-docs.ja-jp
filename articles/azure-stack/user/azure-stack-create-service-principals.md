---
title: Azure Stack のサービス プリンシパルを作成する | Microsoft Docs
description: Azure Resource Manager でロール ベースのアクセス制御と共に使用してリソースへのアクセスを管理できる、サービス プリンシパルを作成する方法について説明します。
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: 77940a52c0817b9eaf49cdf7d1a2d284c5e662e3
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2018
ms.locfileid: "42366102"
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>サービス プリンシパルを作成してアプリケーションに Azure Stack リソースへのアクセスを付与する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Resource Manager を使用するサービス プリンシパルを作成して、アプリケーションに Azure Stack リソースへのアクセスを付与できます。 サービス プリンシパルによって、[ロールベースのアクセス コントロール](azure-stack-manage-permissions.md)を使用して特定のアクセス許可を委任できます。

ベスト プラクティスとして、お使いのアプリケーションのサービス プリンシパルを使用してください。 サービス プリンシパルは、ご自身の資格情報を使ってアプリを実行するよりも推奨されますが、その理由は次のとおりです。

* お客様自身のアカウントのアクセス許可とは異なるアクセス許可を、サービス プリンシパルに割り当てることができます。 通常、サービス プリンシパルのアクセス許可は、アプリが行う必要がある機能に制限されます。
* お客様の役割や責任が変わっても、アプリの資格情報を変更する必要はありません。
* 無人インストール用スクリプトを実行するときに、証明書を使用して認証を自動化できます。

## <a name="example-scenario"></a>サンプル シナリオ

Azure Resource Manager を使用して Azure リソースのインベントリを管理する必要がある構成管理アプリがあります。 サービス プリンシパルを作成して、リーダー ロールに割り当てることができます。 このロールは、Azure リソースにアプリの読み取り専用アクセスを付与します。

## <a name="getting-started"></a>使用の開始

この記事の手順を使用して、次の作業を行います。

* アプリのサービス プリンシパルを作成する。
* アプリを登録して、認証キーを作成する。
* アプリをロールに割り当てる。

Azure Stack に Active Directory を構成した方法によって、サービス プリンシパルの作成方法が決まります。 次のいずれかの方法を選択します。

* [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) のサービス プリンシパルを作成する。
* [Active Directory フェデレーション サービス (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs) のサービス プリンシパルを作成する。

サービス プリンシパルをロールに割り当てる手順は、Azure AD および AD FS で同じです。 サービス プリンシパルを作成した後は、ロールに割り当てることで[アクセス許可を委任](azure-stack-create-service-principals.md#assign-role-to-service-principal)できます。

## <a name="create-a-service-principal-for-azure-ad"></a>Azure AD のサービス プリンシパルを作成する

Azure Stack で Azure AD を ID ストアとして使用する場合、Azure Portal を使って、Azure と同じ手順を利用してサービス プリンシパルを作成できます。

>[!NOTE]
サービス プリンシパルの作成を開始する前に、[必要な Azure AD のアクセス許可](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions)があることを確認してください。

### <a name="create-service-principal"></a>サービス プリンシパルの作成

アプリケーションのサービス プリンシパルを作成するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) で Azure アカウントにサインインします。
2. **[Azure Active Directory]** > **[アプリの登録]** > **[追加]** の順に選択します。
3. アプリケーションの名前と URL を指定します。 作成するアプリケーションの種類として、**[Web アプリ/API]** または **[ネイティブ]** を選択します。 値を設定したら、**[作成]** をクリックします。

### <a name="get-credentials"></a>資格情報の取得

プログラムによってログインするときは、アプリケーションの ID と認証キーを使用します。 これらの値を取得するには、次の手順を実行します。

1. Active Directory の **[アプリの登録]** で、アプリケーションを選択します。

2. **アプリケーション ID** をコピーし、アプリケーション コードに保存します。 [サンプル アプリケーション](#sample-applications)内にあるアプリケーションでは、**アプリケーション ID** を参照するときに**クライアント ID** を使用します。

     ![アプリケーションのアプリケーション ID](./media/azure-stack-create-service-principal/image12.png)
3. 認証キーを生成するには、**[キー]** を選択します。

4. キーの説明を入力し、キーの期間を指定します。 操作が完了したら、**[保存]** をクリックします。

>[!IMPORTANT]
キーを保存した後、キーの**値**が表示されます。 後からキーを取得することはできないので、この値をメモしてください。 アプリケーションが取得できる場所にキー値を保存します。

![保存されているキーに対するキー値の警告です。](./media/azure-stack-create-service-principal/image15.png)

最後の手順として、[アプリケーションへのロールの割り当て](azure-stack-create-service-principals.md#assign-role-to-service-principal)を行います。

## <a name="create-service-principal-for-ad-fs"></a>AD FS のサービス プリンシパルを作成する

AD FS を ID ストアとして使用して Azure Stack をデプロイした場合、次のタスクに PowerShell を使用できます。

* サービス プリンシパルを作成する。
* サービス プリンシパルをロールに割り当てる。
* サービス プリンシパルの ID を使ってサインインする。

サービス プリンシパルを作成する方法の詳細については、「[AD FS のサービス プリンシパルを作成する](../azure-stack-create-service-principals.md#create-service-principal-for-ad-fs)」を参照してください。

## <a name="assign-the-service-principal-to-a-role"></a>サービス プリンシパルをロールに割り当てる

サブスクリプション内のリソースにアクセスするには、アプリケーションをロールに割り当てる必要があります。 アプリケーションにとって適切なアクセス許可を表すのはどのロールであるかを判断します。 利用可能なロールについては、「[RBAC: 組み込みのロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

>[!NOTE]
ロールのスコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。 アクセス許可は、スコープの下位レベルに継承されます。 たとえば、アプリがリソース グループに対するリーダー ロールを備えている場合、そのアプリはリソース グループ内の任意のリソースを読み取ることができます。

次の手順を使用して、サービス プリンシパルにロールを割り当てます。

1. Azure Stack ポータルで、アプリケーションを割り当てるスコープのレベルに移動します。 たとえば、サブスクリプション スコープでロールを割り当てるには、**[サブスクリプション]** を選択します。

2. アプリケーションを割り当てるサブスクリプションを選択します。 この例では、サブスクリプションは、Visual Studio Enterprise です。

     ![割り当てる Visual Studio Enterprise サブスクリプションを選択する](./media/azure-stack-create-service-principal/image16.png)

3. サブスクリプションの **[アクセス制御 (IAM)]** を選択します。

     ![アクセス制御を選択する](./media/azure-stack-create-service-principal/image17.png)

4. **[追加]** を選択します。

5. アプリケーションに割り当てるロールを選択します。

6. アプリケーションを検索して選択します。

7. **[OK]** をクリックして、ロールの割り当てを完了します。 該当のスコープのロールに割り当てられたユーザーの一覧にアプリケーションが表示されます。

サービス プリンシパルを作成してロールを割り当てたので、アプリケーションから Azure Stack リソースにアクセスできるようになりました。

## <a name="next-steps"></a>次の手順

[ユーザー アクセス許可の管理](azure-stack-manage-permissions.md)
