---
title: Azure Managed Applications 用の UI 定義をテストする | Microsoft Docs
description: ポータルを使用してご自身の Azure マネージド アプリケーションを作成するためのユーザー エクスペリエンスをテストする方法について説明します。
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2018
ms.author: tomfitz
ms.openlocfilehash: c88bdce64e88f8639da2c4ebb01f4594fccff8a0
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747090"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>マネージド アプリケーション用の Azure portal のインターフェイスをテストする
ご自身の Azure マネージド アプリケーション用の [createUiDefinition.json ファイルを作成](create-uidefinition-overview.md)したら、ユーザー エクスペリエンスをテストする必要があります。 テストを簡素化するには、ポータルでご自身のファイルを読み込むスクリプトを使用します。 マネージド アプリケーションを実際に展開する必要はありません。

## <a name="prerequisites"></a>前提条件

* **createUiDefinition.json** ファイル。 このファイルがない場合は、[サンプル ファイル](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json)をコピーして、ローカルに保存します。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="download-test-script"></a>テスト スクリプトのダウンロード

ポータルでご自身のインターフェイスをテストするには、お使いのローカル コンピューターに次のスクリプトのいずれかをコピーします。

* [PowerShell side-load スクリプト](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI side-load スクリプト](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>スクリプトの実行

ポータルでご自身のインターフェイス ファイルを確認するには、ダウンロードしたスクリプトを実行します。 スクリプトによりストレージ アカウントがご自身の Azure サブスクリプションに作成され、該当する createUiDefinition.json ファイルがそのストレージ アカウントにアップロードされます。 ストレージ アカウントが作成されるのは、最初にスクリプトを実行したとき、またはストレージ アカウントが削除されている場合です。 ストレージ アカウントが Azure サブスクリプション内に存在している場合は、そのアカウントが再利用されます。 スクリプトによって、ポータルが開き、ストレージ アカウントから該当するファイルが読み込まれます。

ストレージ アカウントに対して場所を入力し、該当する createUiDefinition.json ファイルがあるフォルダーを指定します。

PowerShell では、次を使用します。

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Azure CLI では、次を使用します。

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

createUiDefinition.json ファイルがスクリプトと同じフォルダー内にあり、ストレージ アカウントが既に作成されている場合は、これらのパラメーターを指定する必要はありません。

PowerShell では、次を使用します。

```powershell
.\SideLoad-CreateUIDefinition.ps1
```

Azure CLI では、次を使用します。

```azurecli
./sideload-createuidef.sh
```

## <a name="test-your-interface"></a>インターフェイスのテスト

スクリプトによって、ブラウザーで新しいタブが開きます。 マネージド アプリケーションを作成するためのインターフェイスでポータルが表示されます。

![ポータルの表示](./media/test-createuidefinition/view-portal.png)

フィールドに入力する前に、お使いのブラウザーで Web 開発者ツールを開きます。 **コンソール**にお使いのインターフェイスに関する重要なメッセージが表示されます。

![コンソールの選択](./media/test-createuidefinition/select-console.png)

インターフェイスの定義にエラーがある場合は、コンソールにその説明が表示されます。

![エラーの表示](./media/test-createuidefinition/show-error.png)

フィールドの値を指定します。 完了したら、テンプレートに渡された値が表示されます。

![値の表示](./media/test-createuidefinition/show-json.png)

これらの値をパラメーター ファイルとして使用して、デプロイ テンプレートをテストできます。

## <a name="troubleshooting-the-interface"></a>インターフェイスをトラブルシューティングする

よく発生するエラーを次にいくつか示します。

* ポータルでインターフェイスが読み込まれず、 代わりに、涙の粒が付いたクラウドのアイコンが表示される。 通常、このアイコンが表示されるのは、ファイル内に構文エラーがあるときです。 VS Code (またはスキーマを検証できる他の JSON エディター) で ファイルを開き、構文エラーを探します。

* 概要画面でポータルがハングする。 通常、この状況が発生するのは、出力セクションにバグがある場合です。 たとえば、存在しないコントロールを参照している可能性があります。

* 出力内のパラメーターが空である。 パラメーターが、存在しないプロパティを参照している可能性があります。 たとえば、コントロールの参照は有効であっても、プロパティの参照が無効になっています。

## <a name="test-your-solution-files"></a>ソリューション ファイルのテスト

お使いのポータル インターフェイスが想定どおりに動作していることを確認できたので、次は該当する createUiDefinition ファイルが目的の mainTemplate.json ファイルに正しく統合されていることを検証します。 検証スクリプト テストを実行すると、createUiDefinition ファイルなど、目的のソリューション ファイルのコンテンツをテストできます。 このスクリプトによって JSON 構文が検証され、テキスト フィールドの正規表現式がチェックされます。また、ポータル インターフェイスの出力値が、該当するテンプレートのパラメーターと一致するかどうかが確認されます。 このスクリプトの実行については、[テンプレート用の静的検証チェックの実行](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

ポータル インターフェイスを検証したら、[Marketplace で利用できる Azure マネージド アプリケーション](publish-marketplace-app.md)の作成について確認します。
