---
title: ゲスト構成パッケージの成果物をテストする方法
description: マシンに対する監査または構成の適用を行うパッケージを作成およびテストする経験。
ms.date: 07/20/2021
ms.topic: how-to
ms.openlocfilehash: efa2fbd49509b323cbf0cf442cb0a29bbc51c8b7
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2021
ms.locfileid: "129418729"
---
# <a name="how-to-test-guest-configuration-package-artifacts"></a>ゲスト構成パッケージの成果物をテストする方法

PowerShell モジュール `GuestConfiguration` には、Azure 外部での構成パッケージのテストを自動化するツールが含まれています。 これらのツールは、Azure または Arc に接続された環境でのテストに進む前に、すばやく問題を見つけて反復作業するために使用します。

テストを開始する前に、「[ゲスト構成作成環境を設定する方法](./guest-configuration-create-setup.md)」ページ、次に「[カスタム ゲスト構成パッケージの成果物の作成方法](./guest-configuration-create.md)」のすべての手順に従って、カスタム ゲスト構成パッケージを作成して公開します。

> [!IMPORTANT]
> 環境の状態を監査するカスタム パッケージは一般公開されていますが、構成を適用するパッケージは **プレビュー段階** にあります。 **次の制限事項が適用されます。**
> 
> 構成を適用するゲスト構成パッケージを使用するには、Azure VM のゲスト構成拡張機能バージョン **1.29.24** 以降、または Arc エージェント **1.10.0** 以降が必要です。
> 
> Linux で構成の作成と適用をテストする場合、`GuestConfiguration` モジュールは Ubuntu 18 でのみ使用できますが、モジュールによって生成されるパッケージとポリシーは、Azure または Arc でサポートされるすべての Linux ディストリビューションまたはバージョンで使用できます。
>
> MacOS でパッケージをテストすることはできません。

ワークステーションまたは継続的インテグレーションおよび継続的デプロイ (CI/CD) 環境からパッケージをテストできます。  `GuestConfiguration` モジュールには、Azure や Arc 対応マシン内で使用されているものと同じ開発環境用エージェントが含まれています。 エージェントには、Windows 用の PowerShell 7.1.3 のスタンドアロン インスタンスと、Linux 用の 7.2.0-preview.7 が含まれているため、パッケージのテストが行われるスクリプト環境は、ゲスト構成を使用して管理しているマシンと一貫性があるものとなります。

Azure および Arc 対応マシンのエージェント サービスは、Windows では "LocalSystem" アカウントとして、Linux では "ルート" として実行されています。 下記のコマンドは、最適な結果を得るため、特権付きセキュリティ コンテキストで実行してください。

Windows で PowerShell を "LocalSystem" として実行するには、SysInternals ツールの [PSExec](/sysinternals/downloads/psexec) を使用します。

Linux で PowerShell を "ルート" として実行するには、[Su コマンド](https://manpages.ubuntu.com/manpages/man1/su.1.html)を使用します。

## <a name="validate-the-configuration-package-meets-requirements"></a>構成パッケージが要件を満たしていることを検証する

最初に、`Get-GuestConfigurationPackageComplianceStatus ` を使用して、構成パッケージが基本要件を満たしていることをテストします。 コマンドにより、以下のパッケージ要件が検証されます。

- MOF は存在していて有効であり、正しい場所にある
- 必須のモジュールや依存関係は正しいバージョンのものが存在していて、重複はない
- パッケージが署名されていることを検証する (省略可能)
- `Test` と `Get` で、コンプライアンスの状態に関する情報が返されることをテストする

`Get-GuestConfigurationPackageComplianceStatus ` コマンドレットのパラメーター:

- **Path**: ゲスト構成パッケージのファイル パスまたは URI。
- **Parameter**: ハッシュテーブル形式で提供されるポリシー パラメーター。

このコマンドを初めて実行するときに、テスト マシンにゲスト構成エージェントがインストールされます。場所は、Windows ではパス `c:\programdata\GuestConfig\bin`、Linux ではパス `/var/lib/GuestConfig/bin` です。 ユーザー アカウントからはこのパスにアクセスできないため、コマンドの特権の昇格が必要です。

次のコマンドを実行してパッケージをテストします。

Windows では、管理者特権の PowerShell 7 セッションからです。

```powershell
# Get the current compliance results for the local machine
Get-GuestConfigurationPackageComplianceStatus -Path ./MyConfig.zip
```

Linux では、sudo を使用して PowerShell を実行します。

```bash
# Get the current compliance results for the local machine
sudo pwsh -command 'Get-GuestConfigurationPackageComplianceStatus -Path ./MyConfig.zip'
```

コマンドからは、リソースごとのコンプライアンスの状態と詳細情報を格納しているオブジェクトが出力されます。

```powershell
  complianceStatus  resources
  ----------------  ---------
  True              @{BuiltInAccount=localSystem; ConfigurationName=MyConfig; Credential=; Dependencies=System.Obje…
```

#### <a name="test-the-configuration-package-can-apply-a-configuration"></a>構成パッケージによって構成を適用できることをテストする

最後に、構成パッケージのモードが `AuditandSet` である場合は、`Start-GuestConfigurationPackageRemediation` コマンドを使用して、`Set` メソッドによってローカル マシンに設定を適用可能なことをテストできます。

> [!IMPORTANT]
> このコマンドでは、それが実行されているローカル環境内で変更が試みられます。

`Start-GuestConfigurationPackageRemediation` コマンドレットのパラメーター:

- **パス**: ゲスト構成パッケージの完全なパス。

Windows では、管理者特権の PowerShell 7 セッションからです。

```powershell
# Test applying the configuration to local machine
Start-GuestConfigurationPackageRemediation -Path ./MyConfig.zip
```

Linux では、sudo を使用して PowerShell を実行します。

```bash
# Test applying the configuration to local machine
sudo pwsh -command 'Start-GuestConfigurationPackageRemediation -Path ./MyConfig.zip'
```

このコマンドでは、エラーが発生しない限り出力は返されません。 `Set` の間に発生したイベントの詳細をトラブルシューティングするには、`-verbose` パラメーターを使用します。

`Start-GuestConfigurationPackageRemediation` コマンドの実行後に再度 `Get-GuestConfigurationComplianceStatus` コマンドを実行すると、マシンがその時点で正常な状態になっていることを確認できます。

## <a name="next-steps"></a>次のステップ

- 自分のマシンからアクセスできるように、[パッケージ成果物を公開する](./guest-configuration-create-publish.md)。
- `GuestConfiguration` モジュールを使用して、環境を大規模に管理するための [Azure Policy の定義を作成する](./guest-configuration-create-definition.md)。
- Azure portal を使用して[カスタム ポリシー定義を割り当てる](../assign-policy-portal.md)。
- [ゲスト構成ポリシーの割り当てに関するコンプライアンスの詳細](./determine-non-compliance.md#compliance-details-for-guest-configuration)を見る方法を学習する。
