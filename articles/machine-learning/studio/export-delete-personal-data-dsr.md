---
title: Machine Learning Studio からのデータのエクスポートおよび削除 - Azure | Microsoft ドキュメント
description: Azure Machine Learning Studio によって格納された製品内データは、Azure Portal を使用して、および認証された REST API を介してエクスポートおよび削除することができます。 テレメトリ データには、Azure Privacy Portal を介してアクセスすることができます。 この記事では、その方法について説明します。
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: cgronlun
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.date: 05/25/2018
ms.openlocfilehash: 2ebd777a9723732de6ebbdf07020802190cb4b61
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969284"
---
# <a name="export-and-delete-in-product-user-data-from-machine-learning-studio"></a>Machine Learning Studio から製品内ユーザー データをエクスポートおよび削除する

Azure Machine Learning Studio によって格納された製品内データは、Azure Portal、Studio インターフェイス、PowerShell、および認証された REST API を使用して削除またはエクスポートすることができます。 この記事では、その方法について説明します。 

テレメトリ データには、Azure Privacy Portal を介してアクセスすることができます。 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-collect"></a>Studio ではどのような種類のユーザー データが収集されますか?

このサービスの対象となるユーザー データは、ワークスペースへのアクセス権限およびユーザーとサービスとの対話のテレメトリ レコードへのアクセス権限を持つユーザーに関する情報で構成されます。

Machine Learning Studio には、次に示す 2 種類のユーザー データがあります。
- **個人用アカウントのデータ:** アカウントに関連付けられたアカウント ID と電子メール アドレス。
- **顧客データ:** 分析のためにアップロードしたデータ。

## <a name="studio-account-types-and-how-data-is-stored"></a>Studio アカウントの種類とデータの格納方法

Machine Learning Studio には 3 種類のアカウントがあります。 所有しているアカウントの種類によって、データの格納方法と、データの削除またはエクスポートの方法が決まります。

- **ゲスト ワークスペース**は、無料の匿名アカウントです。 電子メール アドレスやパスワードなどの資格情報を指定することなく、サインアップします。
    -  ゲスト ワークスペースの有効期限が切れると、データは削除されます。
    - ゲスト ユーザーは、UI、REST API、または PowerShell パッケージを介して顧客データをエクスポートできます。
- **無料のワークスペース**は、Microsoft アカウントの資格情報 (電子メール アドレスとパスワード) を使用してサインインする無料アカウントです。
    - データ主体の権利 (DSR) 要求の影響を受ける、個人データおよび顧客データをエクスポートおよび削除できます。
    - UI、REST API、または PowerShell パッケージを介して顧客データをエクスポートすることができます。
    - Azure AD アカウントを使用していない無料のワークスペースの場合は、Privacy Portal を使用してテレメトリをエクスポートすることができます。
    - ワークスペースを削除する場合は、個人の顧客データもすべて削除します。
- **標準ワークスペース**は、サインイン資格情報を使用してアクセスする有料アカウントです。
    - DSR 要求の影響を受ける、個人データおよび顧客データをエクスポートおよび削除できます。
    - データには Azure Privacy Portal を介してアクセスできます。
    - UI、REST API、または PowerShell パッケージを介して個人データおよび顧客データをエクスポートすることができます。
    - Azure Portal でデータを削除できます。

## <a name="delete-workspace-data-in-studio"></a>Studio でワークスペース データを削除する 

### <a name="delete-individual-assets"></a>個々の資産を削除する

ユーザーはワークスペース内の資産を削除できます。それには該当する資産を選択し、[削除] ボタンを選択します。

![Machine Learning Studio 内の資産を削除する](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>ワークスペース全体を削除する

ユーザーはまた、ワークスペース全体を削除することもできます。
- 有料ワークスペース: Azure Portal を介して削除します。
- 無料のワークスペース: **[設定]** ウィンドウの [削除] ボタンを使用します。

![Machine Learning Studio で無料のワークスペースを削除する](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-data-with-powershell"></a>PowerShell を使用して Studio データをエクスポートする
PowerShell では、コマンドを使用して Azure Machine Learning Studio から、すべての情報をポータブル形式にエクスポートできます。 詳細については、「[Azure Machine Learning 用 PowerShell モジュール](powershell-module.md)」を参照してください。

## <a name="next-steps"></a>次の手順

Web サービスおよび契約プランの課金を説明するドキュメントについては、[Azure Machine Learning REST API リファレンス](https://docs.microsoft.com/rest/api/machinelearning/)に関するページを参照してください。 
