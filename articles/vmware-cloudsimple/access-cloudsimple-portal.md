---
title: CloudSimple による Azure VMware ソリューションへのアクセス - ポータル
description: Azure portal から CloudSimple による Azure VMware ソリューションにアクセスする方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30882899e5be4101ae3d77f9840d8bdef567e53f
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66576679"
---
# <a name="accessing-the-vmware-solution-by-cloudsimple-portal-from-azure-portal"></a>Azure portal から CloudSimple による Azure VMware ソリューションにアクセスする

CloudSimple ポータルへのアクセスにはシングル サインオンがサポートされています。 Azure portal にサインインした後は、もう一度サインインしなくても CloudSimple ポータルにアクセスできます。 初めて CloudSimple ポータルにアクセスすると、[CloudSimple Service Authorization](#consent-to-cloudsimple-service-authorization-application) アプリケーションを承認するように求められます。  承認は、1 回限りの操作です。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="access-the-cloudsimple-portal"></a>CloudSimple ポータルにアクセスする

1. **[すべてのサービス]** を選択します。

2. **CloudSimple Services** を検索します。

3. プライベート クラウドを作成する CloudSimple サービスを選択します。

4. **[概要]** ページで、 **[Go to the CloudSimple portal]\(CloudSimple ポータルに移動\)** をクリックします。  Azure portal から初めて CloudSimple ポータルにアクセスする場合は、[CloudSimple Service Authorization](#consent-to-cloudsimple-service-authorization-application) アプリケーションを承認するように求められます。 

    ![CloudSimple ポータルを起動する](media/launch-cloudsimple-portal.png)

> [!TIP]
> Azure portal から直接プライベート クラウド操作 (プライベート クラウドの作成や拡張など) を選択すると、CloudSimple ポータルの指定のページが表示されます。

CloudSimple ポータルで、サイド メニューの **[ホーム]** を選択してプライベート クラウドに関する概要情報を表示します。 プライベート クラウドのリソースと容量が、注意が必要なアラートとタスクと共に表示されます。 一般的な作業については、ページの上部にある名前付きアイコンをクリックします。

![ホーム ページ](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>CloudSimple Service Authorization アプリケーションに同意する

Azure portal から CloudSimple ポータルを初めて起動するには、CloudSimple Service Authorization アプリケーションに同意する必要があります。  要求されたアクセス許可を付与し、CloudSimple ポータルにアクセスするには、 **[同意する]** を選択します。 

![CloudSimple Service Authorization に同意する - 管理者](media/cloudsimple-azure-consent.png)

全体管理者特権を持っている場合は、組織に代わって同意することができます。  **[組織の代理として同意する]** を選択します。

![CloudSimple Service Authorization に同意する - 全体管理者](media/cloudsimple-azure-consent-global-admin.png)

お持ちのアクセス許可で CloudSimple ポータルへのアクセスが許可されていない場合は、テナントの全体管理者に必要なアクセス許可の付与を依頼します。  全体管理者は、組織に代わって同意することができます。

![CloudSimple Service Authorization に同意する - 管理者が必要](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>次の手順

* [プライベート クラウドを作成する](https://docs.azure.cloudsimple.com/create-private-cloud/)方法を学習する
* [プライベート クラウドの環境を構成する](quickstart-create-private-cloud.md)方法を学習する