---
title: Azure VMware Solutions (AVS) へのアクセス - ポータル
description: Azure portal から Azure VMware Solutions (AVS) にアクセスする方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0b0601b9d2cf505aa5b25e10875338352cab771
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015952"
---
# <a name="access-azure-vmware-solutions-avs-from-the-azure-portal"></a>Azure portal から Azure VMware Solutions (AVS) にアクセスする

AVS ポータルへのアクセスに対してシングル サインオンがサポートされています。 Azure portal にサインインした後は、もう一度サインインしなくても AVS ポータルにアクセスできます。 初めて AVS ポータルにアクセスすると、[AVS Service Authorization](#consent-to-avs-service-authorization-application) アプリケーションを承認するように求められます。 承認は、1 回限りの操作です。

## <a name="before-you-begin"></a>開始する前に

組み込みの **[所有者]** ロールと **[共同作成者]** ロールを持つユーザーは、AVS ポータルにアクセスできます。 ロールは、AVS サービスがデプロイされているリソース グループに対して構成されている必要があります。 AVS サービス オブジェクトに対してロールを構成することもできます。 ご自身のロールの確認について詳しくは、「[ロールの割り当てを表示する](https://docs.microsoft.com/azure/role-based-access-control/check-access)」の記事を参照してください。 組み込みの**所有者**または**共同作成者**ロールが与えられているユーザーだけが AVS ポータルにアクセスできます。 ロールはサブスクリプションで構成する必要があります。 ご自身のロールの確認について詳しくは、「[ロールの割り当てを表示する](https://docs.microsoft.com/azure/role-based-access-control/check-access)」の記事を参照してください。

カスタム ロールを使用している場合、そのロールの ```Actions``` に次のいずれかの操作が必要です。  カスタム ロールの詳細については、「[Azure リソースのカスタム ロール](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)」を参照してください。 いずれかの操作が ```NotActions``` の一部である場合、ユーザーは AVS ポータルにアクセスできません。 

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="access-the-avs-portal"></a>AVS ポータルへのアクセス

1. **[すべてのサービス]** を選択します。

2. **AVS サービス**を検索します。

3. プライベート クラウドを作成する AVS サービスを選択します。

4. **[概要]** ページで、 **[Go to the AVS portal]\(AVS ポータルに移動する\)** をクリックします。 Azure portal から初めて AVS ポータルにアクセスする場合は、[AVS Service Authorization](#consent-to-avs-service-authorization-application) アプリケーションを承認するように求められます。 

    ![AVS ポータルを起動する](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Azure portal から直接プライベート クラウド操作 (プライベート クラウドの作成や拡張など) を選択すると、AVS ポータルの指定のページが表示されます。

AVS ポータルで、サイド メニューの **[ホーム]** を選択して AVS プライベート クラウドに関する概要情報を表示します。 AVS プライベート クラウドのリソースと容量が、注意が必要なアラートとタスクと共に表示されます。 一般的な作業については、ページの上部にある名前付きアイコンをクリックします。

![ホーム ページ](media/cloudsimple-portal-home.png)

## <a name="consent-to-avs-service-authorization-application"></a>AVS Service Authorization アプリケーションに同意する

初めて Azure portal から AVS ポータルを起動するには、AVS Service Authorization アプリケーションに同意する必要があります。 要求されたアクセス許可を付与し、AVS ポータルにアクセスするには、 **[同意する]** を選択します。

![AVS Service Authorization に同意する - 管理者](media/cloudsimple-azure-consent.png)

全体管理者特権を持っている場合は、組織に代わって同意することができます。 **[組織の代理として同意する]** を選択します。

![AVS Service Authorization に同意する - 全体管理者](media/cloudsimple-azure-consent-global-admin.png)

お持ちのアクセス許可が AVS ポータルへのアクセスを許可されていない場合は、テナントの全体管理者に必要なアクセス許可の付与を依頼します。 全体管理者は、組織に代わって同意することができます。

![AVS サービスの承認に同意する - 管理者が必要](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>次のステップ

* [プライベート クラウドを作成する](https://docs.azure.cloudsimple.com/create-private-cloud/)方法を学習する
* [プライベート クラウドの環境を構成する](quickstart-create-private-cloud.md)方法を学習する
