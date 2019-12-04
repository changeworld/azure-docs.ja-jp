---
title: Azure IoT Central アプリケーションを作成する | Microsoft Docs
description: 新しい Azure IoT Central アプリケーションを作成します。 アプリケーション テンプレートを使用して、試用版や従量課金制のアプリケーションを作成します。
author: lmasieri
ms.author: lmasieri
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 1fcaf694e7a628232ea252585d6d9a507a9850da
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287049"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Azure IoT Central アプリケーションを作成する (プレビュー機能)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

このクイックスタートでは、IoT プラグ アンド プレイなどのプレビュー機能を含む Azure IoT Central アプリケーションを作成する方法について説明します。

> [!WARNING]
> Azure IoT Central の IoT プラグ アンド プレイ機能は、現在パブリック プレビューの段階にあります。 運用環境のワークロードに、IoT プラグ アンド プレイ対応の IoT Central アプリケーションを使用しないでください。 運用環境向けには、現在一般公開されているアプリケーション テンプレートから作成された IoT Central アプリケーションを使用してください。

## <a name="create-an-application"></a>アプリケーションの作成

[Azure IoT Central ビルド](https://aka.ms/iotcentral) Web サイトに移動します。 次に、Microsoft 個人アカウントか、職場または学校アカウントを使用してサインインします。

アプリケーションを作成するには、業界に関連する IoT Central テンプレートの一覧を参照して、すばやく作業を開始するか、**カスタム アプリケーション テンプレート** を使用してゼロから始めることができます。

![Azure IoT Central の [アプリケーションの作成] ページ](media/quick-deploy-iot-central/iotcentralcreate-templates-pnp.png)

新しい Azure IoT Central アプリケーションを作成するには:

1. *業界テンプレート* から新しい Azure IoT Central アプリケーションを作成するには、いずれかの業界の利用可能なテンプレートの一覧からアプリケーション テンプレートを選択します。 *カスタム アプリ*を選択して最初から開始することもできます。
1. Azure IoT Central によって、選択したアプリケーション テンプレートに基づいて **アプリケーション名** が自動的に提案されます。 この名前をそのまま使用することも、独自のわかりやすいアプリケーション名を入力することもできます。
1. また、Azure IoT Central は、アプリケーション名に基づいて、一意の **アプリケーション URL** プレフィックスも生成します。 この URL は、アプリケーションにアクセスするために使用します。 この URL プレフィックスは、もっと覚えやすいものに変更することもできます。

    ![Azure IoT Central の [アプリケーションの作成] ページ](media/quick-deploy-iot-central/iotcentralcreate-industry-pnp.png)

    > [!NOTE]
    > カスタム アプリ テンプレートを使用している場合は、**アプリケーション テンプレート** ドロップダウン フィールドが表示されます。 ここから、プレビューと一般公開されているテンプレートを切り替えることができます。 組織で新たに使用可能になった他のテンプレートが表示される場合もあります。

1. 7 日間の無料試用版を使用してこのアプリケーションを作成するか、従量課金制サブスクリプションを使用するかを選択します。
    - **試用版** アプリケーションは 7 日間無料で、最大 5 台のデバイスをサポートします。 有効期限内で、いつでも従量課金制に変更することができます。 試用版アプリケーションを作成する場合は、連絡先情報を入力し、Microsoft から情報やヒントを受け取るかどうかを選択します。
    - **従量課金制**アプリケーションは、デバイスごとに課金され、最初の 2 個のデバイスは無料です。 [IoT Central の価格](https://aka.ms/iotcentral-pricing) についての詳細をご覧ください。 従量課金制アプリケーションを作成する場合は、"*ディレクトリ*"、"*Azure サブスクリプション*"、および "*場所*" を選択する必要があります。
        - "*ディレクトリ*" は、アプリケーションを作成する Azure Active Directory (AAD) です。 Azure AD には、ユーザー ID、資格情報、およびその他の組織情報が含まれています。 Azure AD を持っていない場合は、Azure サブスクリプションを作成するときに自動的に作成されます。
        - "*Azure サブスクリプション*" を使用すると、Azure サービスのインスタンスを作成できます。 IoT Central では、リソースが自分のサブスクリプション内にプロビジョニングされます。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で作成できます。 Azure サブスクリプションを作成したら、 **[アプリケーションの作成]** ページに戻ります。 新しいサブスクリプションが **[Azure サブスクリプション]** ドロップダウンに表示されます。
        - "*場所*" は、アプリケーションを作成する[地域](https://azure.microsoft.com/global-infrastructure/geographies/)です。 通常、最適なパフォーマンスを得るには、ご利用のデバイスに物理的に最も近い場所を選択する必要があります。 現在、Azure IoT Central パブリック プレビューは、**米国**または**ヨーロッパ**で使用できます。 いったん場所を選択すると、後でアプリケーションを別の場所に移動することはできません。

        > [!NOTE]
        > パブリック プレビュー期間中に**プレビュー アプリケーション**を使用できる場所は**ヨーロッパ**および**米国**のみです。

1. 使用条件を確認し、ページの下部にある **[作成]** を選択します。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、IoT Central アプリケーションを作成しました。 推奨される次の手順は、以下のとおりです。

> [!div class="nextstepaction"]
> [Azure IoT Central アプリケーションにシミュレートされたデバイスを追加する](./quick-create-pnp-device.md)
