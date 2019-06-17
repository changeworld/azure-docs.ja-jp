---
title: CloudSimple サービスによる Azure VMware ソリューションの有効化
description: Azure サブスクリプションの CloudSimple サービスを有効にし、その後 CLoudSimple リソース プロバイダーを登録する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cacd5510147ce997efec922f4b4656956a098d88
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66576673"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Azure サブスクリプションで Microsoft.VMwareCloudSimple リソース プロバイダーを登録する

CloudSimple サービスにより、CloudSimple によって Azure VMware ソリューションを使用できます。 CloudSimple サービスを使用するには、最初にこれを Azure サブスクリプションで有効にする必要があります。 次に、Microsoft.VMwareCloudSimple サービスをリソース プロバイダーとして登録できます。

## <a name="enable-the-cloudsimple-service"></a>CloudSimple サービスを有効にする

Azure サブスクリプションで CloudSimple サービスを有効にするには、[Microsoft サポート](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)でサポート要求を開きます。 要求を送信するときは、次のオプションを選択してください。

* [問題の種類]: **テクニカル**
* サブスクリプション:**ご使用のサブスクリプション ID**
* サービスの種類:**VMware Solution by CloudSimple**
* 問題の種類:**Dedicated Nodes quota (専用ノードのクォータ)**
* 問題のサブタイプ:**Increase quota of dedicated nodes (専用ノードのクォータを増やす)**
* 件名:**Enable CloudSimple service (CloudSimple サービスを有効にする)**

また、お客様の Microsoft アカウント担当者に [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) から問い合わせることもできます。 お客様の Azure サブスクリプション ID をメールに記載してください。  

サブスクリプションの CloudSimple サービスを有効にした後は、サブスクリプションでリソース プロバイダーを有効にすることができます。

## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[すべてのサービス]** を選択します。

3. **サブスクリプション**を検索して選択します。

    ![[サブスクリプション] を選択する](media/cloudsimple-service-select-subscriptions.png)

4. CloudSimple サービスを有効にするサブスクリプションを選択します。

5. サブスクリプションの **[リソース プロバイダー]** をクリックします。

6. **Microsoft.VMwareCloudSimple** を使用してリソース プロバイダーをフィルタ処理します。

7. リソース プロバイダーを選択し、 **[登録]** をクリックします。

    ![リソース プロバイダーの登録](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>次の手順

* [CloudSimple サービスを作成する](create-cloudsimple-service.md)方法を学習する
* [プライベート クラウドの環境を構成する](quickstart-create-private-cloud.md)方法を学習する