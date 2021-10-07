---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 023f3413fbd7b642300de19ad479029c94e7c4d5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699160"
---
## <a name="setting-up"></a>設定

### <a name="creating-the-visual-studio-project"></a>Visual Studio プロジェクトの作成

Visual Studio 2019 で、新しい `Blank App (Universal Windows)` プロジェクトを作成します。 プロジェクト名を入力した後、`10.0.17134` より大きい任意の Windows SDK を選択します。 

### <a name="install-the-package-and-dependencies-with-nuget-package-manager"></a>NuGet パッケージ マネージャーを使用してパッケージと依存関係をインストールする

Calling SDK の API とライブラリは、NuGet パッケージにより一般公開されています。
次の手順では、Calling SDK NuGet パッケージを検索、ダウンロード、インストールする方法を示します。

1. NuGet パッケージ マネージャーを開きます (`Tools` -> `NuGet Package Manager` -> `Manage NuGet Packages for Solution`)
2. [`Browse`] をクリックし、検索ボックスに「`Azure.Communication.Calling`」と入力します。
3. `Include prerelease` チェック ボックスがオンになっていることを確認します。
4. `Azure.Communication.Calling` パッケージをクリックします。
5. 右側のタブで、CS プロジェクトに対応するチェックボックスをオンにします。
6. [`Install`] ボタンをクリックします。