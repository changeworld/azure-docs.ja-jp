---
title: UI 定義ファイルをテストする
description: ポータルを使用してご自身の Azure マネージド アプリケーションを作成するためのユーザー エクスペリエンスをテストする方法について説明します。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: tomfitz
ms.openlocfilehash: 55a966c17f3bcd51f354198e36e03071efd4834d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111951476"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Azure Managed Applications 用のポータル インターフェイスをテストする

マネージド アプリケーション用の [createUiDefinition.json ファイルを作成](create-uidefinition-overview.md)したら、ユーザー エクスペリエンスをテストする必要があります。 テストを簡素化するには、ポータルにファイルを読み込むサンドボックス環境を使用します。 マネージド アプリケーションを実際に展開する必要はありません。 サンドボックスでは、最新の全画面表示ポータル エクスペリエンスでユーザー インターフェイスが表示されます。 インターフェイスのプレビューには、サンドボックスがお勧めの方法です。

## <a name="prerequisites"></a>前提条件

* **createUiDefinition.json** ファイル。 このファイルがない場合は、[サンプル ファイル](https://github.com/Azure/azure-quickstart-templates/blob/master/demos/100-marketplace-sample/createUiDefinition.json)をコピーします。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="use-sandbox"></a>サンドボックスの使用

1. 「[UI 定義サンドボックスの作成](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)」を開きます。

   ![サンドボックスの表示](./media/test-createuidefinition/show-sandbox.png)

1. 空の定義をご自分の createUiDefinition.json ファイルの内容に置き換えます。 **[Preview]\(プレビュー\)** を選択します。

   ![[プレビュー] の選択](./media/test-createuidefinition/select-preview.png)

1. 作成したフォームが表示されます。 ユーザー エクスペリエンスをステップ実行して、値を入力できます。

   ![フォームの表示](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>トラブルシューティング

**[Preview]\(プレビュー\)** の選択後、フォームが表示されない場合は、構文エラーがある可能性があります。 右側のスクロール バーの赤色のインジケーターを探して、そこに移動します。

![構文エラーの表示](./media/test-createuidefinition/show-syntax-error.png)

フォームが表示されず、代わりにしずくの形が付いたクラウドのアイコンが表示された場合は、プロパティが不足しているなど、フォームにエラーがあります。 ブラウザーで、Web Developer Tools を開きます。 **コンソール** にお使いのインターフェイスに関する重要なメッセージが表示されます。

![エラーの表示](./media/test-createuidefinition/show-error.png)

## <a name="test-your-solution-files"></a>ソリューション ファイルのテスト

お使いのポータル インターフェイスが想定どおりに動作していることを確認できたので、次は該当する createUiDefinition ファイルが目的の mainTemplate.json ファイルに正しく統合されていることを検証します。 検証スクリプト テストを実行すると、createUiDefinition ファイルなど、目的のソリューション ファイルのコンテンツをテストできます。 このスクリプトによって JSON 構文が検証され、テキスト フィールドの正規表現式がチェックされます。また、ポータル インターフェイスの出力値が、該当するテンプレートのパラメーターと一致するかどうかが確認されます。 このスクリプトの実行については、[テンプレート用の静的検証チェックの実行](https://aka.ms/arm-ttk)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

ポータル インターフェイスを検証したら、[Marketplace で利用できる Azure マネージド アプリケーション](../../marketplace/azure-app-offer-setup.md)の作成について確認します。