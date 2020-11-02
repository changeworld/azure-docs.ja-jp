---
author: baanders
description: Azure Digital Twins サンプルで DefaultAzureCredential のローカル認証を設定するためのインクルード ファイル - 概要なし
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8bf0590b867d37a22706f679bfbeee2140935637
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494681"
---
このサンプルは、`DefaultAzureCredential` を使用して、ローカル環境から資格情報を検索します。たとえば、ローカルの [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) や Visual Studio (または Visual Studio Code) から Azure ログインが検索されます。 つまり、サンプルの資格情報を設定するためには、そうしたメカニズムのいずれかを使用して、 **Azure にローカルでログイン** する必要があります。

Visual Studio または Visual Studio Code を使用してコード サンプルを実行する場合は、Azure Digital Twins インスタンスへのアクセスに使用する Azure 資格情報でそのエディターにログインしてください。

または、 [ローカル **Azure CLI** をインストール](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)してマシンのコマンド プロンプトを起動し、`az login` コマンドを使用して Azure アカウントにログインしてもかまいません。 その後、コード サンプルを実行すれば、自動的にログイン処理が行われます。