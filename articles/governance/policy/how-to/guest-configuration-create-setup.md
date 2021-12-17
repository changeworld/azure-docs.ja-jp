---
title: ゲスト構成の作成モジュールをインストールする方法
description: ゲスト構成ポリシーの定義と割り当てを作成し、テストするための PowerShell モジュールをインストールする方法について説明します。
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: c32c405cffb71527e61b68f7fb532487d695743f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868617"
---
# <a name="how-to-setup-a-guest-configuration-authoring-environment"></a>ゲスト構成の作成環境の設定方法

PowerShell モジュール `GuestConfiguration` では、次のようなカスタム コンテンツの作成プロセスを自動化します。

- ゲスト構成コンテンツ アーティファクト (.zip) を作成する
- パッケージが要件を満たしていることを検証する
- テスト用にゲスト構成エージェントをローカルにインストールする
- パッケージを使用してマシンの設定を監査できることを検証する
- パッケージを使用してマシンの設定を構成できることを検証する
- Azure Storage へのパッケージの公開
- ポリシー定義の作成
- ポリシーの発行

ゲスト構成を使用した構成の適用のサポートは、バージョン `3.4.2` で導入されています。

> [!IMPORTANT]
> 環境の状態を監査するカスタム パッケージは一般公開されていますが、構成を適用するパッケージは **プレビュー段階** にあります。 **次の制限事項が適用されます。**
> 
> Linux で構成の作成と適用をテストする場合、`GuestConfiguration` モジュールは Ubuntu 18 でのみ使用できますが、モジュールによって生成されるパッケージとポリシーの定義は、Azure または Arc でサポートされるすべての Linux ディストリビューションまたはバージョンで使用できます。
>
> MacOS でのテスト パッケージは使用できません。

### <a name="base-requirements"></a>基本要件

モジュールをインストールできるオペレーティング システム:

- Ubuntu 18
- Windows

モジュールは、PowerShell 7 を実行しているマシンにインストールできます。 以下に示す PowerShell のバージョンをインストールします。

| OS | PowerShell バージョン |
|-|-|
|Windows|[PowerShell 7.1.3](https://github.com/PowerShell/PowerShell/releases/tag/v7.1.3)|
|Ubuntu 18|[PowerShell 7.2.0-preview.6](https://github.com/PowerShell/PowerShell/releases/tag/v7.2.0-preview.6)|

`GuestConfiguration` モジュールには、次のソフトウェアが必要です。

- Azure PowerShell 5.9.0 以降。 必要な Az モジュールは、`GuestConfiguration` モジュールと一緒に自動的にインストールされます。または、[次の手順](/powershell/azure/install-az-ps)に従うこともできます。
  - Az モジュール 'Az.Accounts'、'Az.Resources'、'Az.Storage' のみが必要です。
- `PSDesiredStateConfiguration` モジュール。

### <a name="install-the-module-from-the-powershell-gallery"></a>PowerShell ギャラリーからモジュールをインストールする

Windows または Linux に `GuestConfiguration` モジュールをインストールするには、PowerShell 7 で次のコマンドを実行します。

```powershell
# Install the guest configuration DSC resource module from PowerShell Gallery
Install-Module -Name GuestConfiguration
```

モジュールがインポートされていることを確認します。

```powershell
# Get a list of commands for the imported GuestConfiguration module
Get-Command -Module 'GuestConfiguration'
```

## <a name="update-and-import-the-psdesiredstateconfiguration-module-on-linux"></a>Linux で PSDesiredStateConfiguration モジュールを更新してインポートする

PowerShell 7.2 Preview 6 以降、DSC は、PowerShell ギャラリー のモジュールとして PowerShell から独立してリリースされます。 Linux 上の PowerShell 環境に DSC バージョン 3 をインストールするには、次のコマンドを実行します。

```powershell
# Install the DSC module before compiling using the Configuration keyword
Install-Module PSDesiredStateConfiguration -AllowPreRelease -Force
```

## <a name="next-steps"></a>次のステップ

- ゲスト構成用の[パッケージ成果物を作成する](./guest-configuration-create.md)。
- 開発環境から[パッケージ成果物をテストする](./guest-configuration-create-test.md)。
- `GuestConfiguration` モジュールを使用して、環境を大規模に管理するための [Azure Policy の定義を作成する](./guest-configuration-create-definition.md)。
- Azure portal を使用して[カスタム ポリシー定義を割り当てる](../assign-policy-portal.md)。
- [ゲスト構成のポリシー割り当てのコンプライアンスの詳細](./determine-non-compliance.md#compliance-details-for-guest-configuration)を確認する方法を学ぶ。
