---
author: baanders
description: Azure Digital Twins サンプルで DefaultAzureCredential のローカル認証を設定するためのインクルード ファイル - 概要なし
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 3cb053f9673532ac19e2098678ec341f2f676486
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011260"
---
このサンプルでは、`DefaultAzureCredential` を使用して、ローカル環境から資格情報が検索されます。たとえば、ローカルの [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) や Visual Studio または Visual Studio Code での Azure サインインなどです。 このため、サンプルの資格情報を設定するために、これらのメカニズムのいずれかを使用して、*Azure にローカルでサインイン* する必要があります。

Visual Studio または Visual Studio Code を使用してコード サンプルを実行する場合は、Azure Digital Twins インスタンスへのアクセスに使用する Azure 資格情報でそのエディターにサインインしてください。

または、[ローカル Azure CLI をインストール](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)してマシンのコマンド プロンプトを起動し、`az login` コマンドを実行して Azure アカウントにサインインしてもかまいません。 サインイン後、コード サンプルを実行すると、自動的にログイン処理が行われます。