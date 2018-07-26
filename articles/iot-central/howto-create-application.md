---
title: Azure IoT Central アプリケーションを作成する | Microsoft Docs
description: 管理者として、Azure IoT Central アプリケーションを作成する方法。
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/09/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 1fbe3ea142e1dd738cd341f57d2b8f48b539ac75
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003768"
---
# <a name="create-your-azure-iot-central-application"></a>Azure IoT Central アプリケーションの作成

Microsoft Azure IoT Central アプリケーションは、[[アプリケーションの作成]](https://apps.microsoftiotcentral.com/create) ページから作成します。 Azure IoT Central アプリケーションを作成するには、このページのすべてのフィールドに入力してから **[作成]** を選択する必要があります。 詳しくは、以下の各フィールドを参照してください。

![[アプリケーションの作成] ページ](media\howto-create-application\image1.png)

## <a name="payment-plan"></a>支払プラン

試用版または有料アプリケーションのどちらかを作成できます。 試用版および有料アプリケーションについては、[Azure IoT Central の価格のページ](https://azure.microsoft.com/pricing/details/iot-central/)を参照してください。

## <a name="application-name"></a>アプリケーション名

アプリケーションの名前は、**[Application Manager] (アプリケーション マネージャー)** ページと、各 Azure IoT Central アプリケーション内に表示されます。 Azure IoT Central アプリケーションとして任意の名前を選択できます。 自分および組織内の他のユーザーにとって意味のある名前を選択してください。

## <a name="application-url"></a>アプリケーションの URL

アプリケーションの URL は、アプリケーションへのリンクです。 そのブックマークをブラウザー内に保存するか、またはそれを他のユーザーと共有できます。

アプリケーションの名前を入力すると、アプリケーションの URL が自動生成されます。 必要に応じて、アプリケーションの別の URL を選択できます。 各 Azure IoT Central URL は、Azure IoT Central 内で一意である必要があります。 選択した URL が既に取得されている場合は、エラー メッセージが表示されます。

## <a name="directory"></a>ディレクトリ

有料アプリケーションでのみ。

Azure IoT Central アプリケーションを作成するための Azure Active Directory テナントを選択します。 Azure Active Directory テナントには、ユーザー ID、資格情報、およびその他の組織情報が含まれています。 1 つの Azure Active Directory テナントに複数の Azure サブスクリプションを関連付けることができます。

Azure Active Directory テナントがない場合は、Azure サブスクリプションを作成したときに自動的に作成されます。

詳細については、[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) に関するページを参照してください。

## <a name="azure-subscription"></a>Azure サブスクリプション

Azure サブスクリプションを使用すると、Azure サービスのインスタンスを作成できます。 Azure IoT Central は、ユーザーがアクセスできるすべての Azure サブスクリプションを自動的に検索し、それを **[アプリケーションの作成]** ページのドロップダウンに表示します。 新しい Azure IoT Central アプリケーションを作成するための Azure サブスクリプションを選択します。

Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で作成できます。 Azure サブスクリプションを作成したら、**[アプリケーションの作成]** ページに戻ります。 新しいサブスクリプションが **[Azure サブスクリプション]** ドロップダウンに表示されます。

詳細については、[Azure サブスクリプション](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing) に関するページを参照してください。

## <a name="region"></a>リージョン

有料アプリケーションでのみ。

Azure IoT Central アプリケーションを作成するリージョンを選択します。 通常、最適なパフォーマンスを得るには、デバイスに物理的に最も近いリージョンを選択する必要があります。

詳細については、[Azure リージョン](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#azure-regions)に関するページを参照してください。

Azure IoT Central を使用できるリージョンは、[[リージョン別の利用可能な製品]](https://azure.microsoft.com/regions/services/) ページで確認できます。

> [!Note]
> いったんリージョンを選択すると、後でアプリケーションを別のリージョンに移動することはできません。

## <a name="application-template"></a>アプリケーション テンプレート

新しい Azure IoT Central アプリケーションには、使用可能なアプリケーション テンプレートのずれかを選択できます。 アプリケーション テンプレートには、使用開始に役立つデバイス テンプレートやダッシュボードなどの事前に定義された項目を含めることができます。

| アプリケーション テンプレート | 説明 |
| -------------------- | ----------- |
| カスタム アプリケーション   | 独自のデバイス テンプレートおよびデバイスにデータを入力するための空のアプリケーションを作成します。 |
| サンプル Contoso       | 単純な接続デバイス用のデバイス テンプレートを含むアプリケーションを作成します。 このテンプレートは、Azure IoT Central の調査を開始するために使用します。 |
| サンプル Devkit       | MXChip または Raspberry Pi デバイスを接続するための準備ができたデバイス テンプレートを含むアプリケーションを作成します。 このテンプレートは、これらのいずれかのデバイスでのコードを経験しているデバイス開発者である場合に使用します。 |

## <a name="next-steps"></a>次の手順

ここでは、Azure IoT Central アプリケーションを作成する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [アプリケーションを管理する](howto-administer.md)