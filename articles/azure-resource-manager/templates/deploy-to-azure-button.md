---
title: '[Azure にデプロイ] ボタン'
description: Azure Resource Manager テンプレートを GitHub リポジトリからデプロイするためのボタンを使用する。
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 88436eac970b252d7b0bc7bccee4131e06e9e0cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77109051"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>デプロイ ボタンを使用して GitHub リポジトリからテンプレートをデプロイする

この記事では、 **[Azure に配置する]** ボタンを使用して、GitHub リポジトリからテンプレートをデプロイする方法について説明します。 このボタンは、GitHub リポジトリ内の README.md ファイル、またはリポジトリを参照する Web ページに直接追加できます。

## <a name="use-common-image"></a>一般的なイメージを使用する

Web ページまたはリポジトリにボタンを追加するには、次の画像を使用します。

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

画像は次のように表示されます。

![[Azure にデプロイ] ボタン](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>テンプレートをデプロイするための URL を作成する

テンプレートの URL を作成するには、リポジトリ内のテンプレートの未加工の URL から開始します。

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

次に、これを URL エンコードします。 オンライン エンコーダーを使用するか、コマンドを実行できます。 次の PowerShell の例は、値を URL エンコードする方法を示しています。

```powershell
[uri]::EscapeDataString($url)
```

サンプル URL は、URL エンコードされると次のようになります。

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

各リンクは、同じベース URL で始まります。

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

URL エンコードされたテンプレート リンクをベース URL の末尾に追加します。

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

リンクの完全な URL が表示されます。

## <a name="create-deploy-to-azure-button"></a>[Deploy to Azure] (Azure にデプロイ) ボタンを作成する

最後に、リンクと画像を一緒に配置します。

GitHub リポジトリまたは Web ページの README.md ファイルに Markdown を使用してボタンを追加するには、次のように使用します。

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

HTML の場合は、次を使用します。

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>テンプレートのデプロイ

完全なソリューションをテストするには、次のボタンを選択します。

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

ポータルには、パラメーター値を簡単に指定できるペインが表示されます。 パラメーターには、テンプレートの既定値があらかじめ入力されています。

![デプロイのためのポータルを使用する](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>次のステップ

- テンプレートの詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](template-syntax.md)」を参照してください。
