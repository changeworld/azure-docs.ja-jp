---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 1c2a4f1e463fff278981de2297662a94cca8944e
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850816"
---
## <a name="prepare-your-repository"></a>リポジトリを準備する

App Service Kudu ビルド サーバーから自動ビルドを取得するには、リポジトリのルートに、プロジェクトの適切なファイルがあることを確認してください。

| ランタイム | ルート ディレクトリのファイル |
|-|-|
| ASP.NET (Windows のみ) | _*.sln_、_*.csproj_、または _default.aspx_ |
| ASP.NET Core | _*.sln_ または _*.csproj_ |
| PHP | _index.php_ |
| Ruby (Linux のみ) | _Gemfile_ |
| Node.js | _server.js_、_app.js_、またはスタート スクリプトを含む _package.json_ |
| Python (Windows のみ) | _\*.py_、_requirements.txt_、または _runtime.txt_ |
| HTML | _default.htm_、_default.html_、_default.asp_、_index.htm_、_index.html_、または _iisstart.htm_ |
| WebJobs | _App\_Data/jobs/continuous_ (継続的 WebJobs の場合) または _App\_Data/jobs/triggered_ (トリガーされた WebJobs の場合) 配下の _\<job_name>/run.\<extension>_。 詳細については、[Kudu WebJobs のドキュメント](https://github.com/projectkudu/kudu/wiki/WebJobs)をご覧ください |
| Functions | [Azure Functions の継続的なデプロイ](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements)に関するページをご覧ください。 |

デプロイをカスタマイズするには、_.deployment_ ファイルをリポジトリのルートに含めます。 詳細については、「[Custom Deployment (カスタム デプロイ)](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)」および「[Custom deployment script (カスタム デプロイ スクリプト)](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)」を参照してください。

> [!NOTE]
> Visual Studio で開発する場合は、[Visual Studio にリポジトリを自動作成](/vsts/git/tutorial/creatingrepo?view=vsts&tabs=visual-studio)させてください。 プロジェクトは Git を使用してすぐにデプロイできます。
>
>

