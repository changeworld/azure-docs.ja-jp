---
title: Azure Data Explorer クラスターでの言語拡張機能の管理
description: 言語拡張機能を使用して、Azure Data Explorer の KQL クエリ内で他の言語を統合します。
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 88c3047afebf3d31b50145c6df47776fdc1ddcd4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522675"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>Azure Data Explorer クラスターでの言語拡張機能の管理 (プレビュー)

言語拡張機能により、言語拡張プラグインを使用して、他の言語を Azure Data Explorer の KQL クエリに統合することができます。 関連するスクリプトを使用してユーザー定義関数 (UDF) を実行すると、スクリプトは表形式のデータを入力として取得し、表形式の出力を生成すると想定します。 プラグインのランタイムは、クラスターのノードで実行されている、分離された安全な環境である[サンドボックス](/azure/kusto/concepts/sandboxes)でホストされます。 この記事では、Azure portal 内の Azure Data Explorer クラスターで言語拡張機能プラグインを管理します。

> [!NOTE]
> 現在サポートされている Azure Data Explorer 言語拡張機能は、Python と R です。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)を作成します。

## <a name="enable-language-extensions-on-your-cluster"></a>クラスターで言語拡張機能を有効にする

> [!WARNING]
> 言語拡張機能を有効にする前に[制限事項](#limitations)を確認してください。

クラスターで言語拡張機能を有効にするには、次の手順を実行します。

1. Azure portal で、Azure Data Explorer クラスターに移動します。 
1. **[設定]** で **[構成]** を選択します。 
1. **[構成]** ウィンドウで、 **[オン]** を選択して言語拡張機能を有効にします。
1. **[保存]** を選択します。
 
    ![言語拡張機能がオン](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> 言語拡張機能プロセスの有効化には、数分かかることがあります。 その間、クラスターは中断されます。
 
## <a name="run-language-extension-integrated-queries"></a>言語拡張機能による統合クエリを実行する

* [Python で統合された KQL クエリの実行](/azure/kusto/query/pythonplugin)方法をご覧ください。
* [R で統合された KQL クエリの実行](/azure/kusto/query/rplugin)方法をご覧ください。 

## <a name="disable-language-extensions-on-your-cluster"></a>クラスターで言語拡張機能を無効にする

> [!NOTE]
> 言語拡張機能の無効化には、数分かかることがあります。

クラスターで言語拡張機能を無効にするには、次の手順を実行します。

1. Azure portal で、Azure Data Explorer クラスターに移動します。 
1. **[設定]** で **[構成]** を選択します。 
1. **[構成]** ウィンドウで、 **[オフ]** を選択して言語拡張機能を無効にします。
1. **[保存]** を選択します。

    ![言語拡張機能がオフ](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>制限事項

* 言語拡張機能は[ディスクの暗号化](manage-cluster-security.md)をサポートしていません。 
* 言語拡張機能のランタイム サンドボックスでは、関連する言語のスコープでクエリが実行されていない場合でも、ディスク領域が割り当てられます。

