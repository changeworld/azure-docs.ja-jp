---
title: カスタム ゲスト構成パッケージの成果物の作成方法
description: ゲスト構成パッケージ ファイルを作成する方法について説明します。
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: 12767e40ef99cf218666b6dc540a5ae1c2e2bffa
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772539"
---
# <a name="how-to-create-custom-guest-configuration-package-artifacts"></a>カスタム ゲスト構成パッケージの成果物の作成方法

開始する前に、[ゲスト構成](../concepts/guest-configuration.md)の概要ページを参照することをお勧めします。

Windows と Linux の両方の監査または構成時に、ゲスト構成では [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) が使用されます。 DSC 構成では、マシンが満たす必要のある条件を定義します。

> [!IMPORTANT]
> 環境の状態を監査するカスタム パッケージは一般公開されますが、構成を適用するパッケージは **プレビュー段階** にあります。 **次の制限事項が適用されます。**
> 
> 構成を適用するゲスト構成パッケージを使用するには、Azure VM のゲスト構成拡張機能バージョン **1.29.24** 以降、または Arc エージェント **1.10.0** 以降が必要です。
> 
> Linux で構成の作成と適用をテストする場合、`GuestConfiguration` モジュールは Ubuntu 18 でのみ使用できますが、モジュールによって生成されるパッケージとポリシーは、Azure または Arc でサポートされるすべての Linux ディストリビューションまたはバージョンで使用できます。
>
> MacOS でのテスト パッケージは使用できません。
> 
> カスタム コンテンツ パッケージでは、秘密や機密情報を使用しないでください。

Azure または非 Azure マシンの状態を管理するための独自の構成を作成するには、次の手順を使用します。

## <a name="install-powershell-7-and-required-powershell-modules"></a>PowerShell 7 および必要な PowerShell モジュールをインストールする

まず、ページのすべての手順に従って、[ゲスト構成の作成環境を設定する方法](./guest-configuration-create-setup.md)に関するページのすべての手順に従って、自分の OS 用に必要なバージョンの PowerShell と `GuestConfiguration` モジュールをインストールし、必要に応じてモジュール `PSDesiredStateConfiguration` をインストールしていることを確認します。

## <a name="author-a-configuration"></a>構成を作成する

構成パッケージを作成する前に、DSC 構成を作成してコンパイルします。
必要に応じて、Windows および Linux の構成例を使用できます。

> [!IMPORTANT]
> Windows の構成をコンパイルする場合は、`PSDesiredStateConfiguration` バージョン`2.0.5` (安定版リリース) を使用します。 Linux 用の構成をコンパイルする場合は、プレリリース バージョン `3.0.0` をインストールします。

Windows 用の例は DSC の[使用の開始に関するドキュメント](/powershell/scripting/dsc/getting-started/wingettingstarted#define-a-configuration-and-generate-the-configuration-document)で提供されています。

Linux の場合は、[PowerShell クラス](/powershell/scripting/dsc/resources/authoringResourceClass)を使用してカスタム DSC リソース モジュールを作成する必要があります。
カスタム リソースと構成の完全な例については、PowerShell のドキュメント ページ「[PowerShell クラスを使用したカスタム DSC リソースの記述](/powershell/scripting/dsc/resources/authoringResourceClass)」から参照でき、例はゲスト構成でテストされています。

## <a name="create-a-configuration-package-artifact"></a>構成パッケージの成果物を作成する

MOF をコンパイルしたら、サポート ファイルをまとめてパッケージ化する必要があります。
完成したパッケージは、Azure Policy の定義を作成するためにゲスト構成によって使われます。

`New-GuestConfigurationPackage` コマンドレットでパッケージを作成します。 構成で必要とされるモジュールは、モジュール内のコマンドでパッケージにそれらを追加できるように、開発環境用に `$Env:PSModulePath` で使用可能である必要があります。

Windows コンテンツを作成するときの `New-GuestConfigurationPackage` コマンドレットのパラメーター:

- **名前**: ゲスト構成のパッケージ名。
- **構成**:コンパイル済み DSC 構成ドキュメントの完全なパス。
- **パス**:出力フォルダーのパス。 このパラメーターは省略可能です。 指定しないと、パッケージは現在のディレクトリに作成されます。
- **種類**: (Audit、AuditandSet) 構成で監査のみを行う必要があるかどうか、または構成を適用してコンピューターの状態を変更する必要があるかどうかを判断します。 既定値は "Audit" です。

この手順では昇格を必要としません。 コマンドを複数回実行した場合、Force コマンドレットは既存のパッケージを上書きするために使用されます。

次のコマンドを実行すると、パッケージの成果物が作成されます。

```powershell
# Create a package that will only audit compliance
New-GuestConfigurationPackage `
  -Name 'MyConfig' `
  -Configuration './Config/MyConfig.mof' `
  -Type Audit `
  -Force
```

```powershell
# Create a package that will audit and apply the configuration (Set)
New-GuestConfigurationPackage `
  -Name 'MyConfig' `
  -Configuration './Config/MyConfig.mof' `
  -Type AuditAndSet `
  -Force
```

オブジェクトは、作成されたパッケージの名前とパスと共に返されます。

```
Name      Path                                                    
----      ----                                                    
MyConfig  /Users/.../MyConfig/MyConfig.zip
```

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>ゲスト構成アーティファクトの予想されるコンテンツ

完成したパッケージは、Azure Policy の定義を作成するためにゲスト構成によって使われます。 パッケージは次のもので構成されます。

- MOF としてのコンパイル済み DSC 構成
- モジュール フォルダー
  - GuestConfiguration モジュール
  - DscNativeResources モジュール
  - MOF に必要な DSC リソース モジュール
- パッケージ `type` と `version` を格納する metaconfig ファイル

PowerShell コマンドレットにより、パッケージ .zip ファイルが作成されます。 ルート レベル フォルダーまたはバージョン フォルダーは必要ありません。 パッケージの形式は、.zip ファイルである必要があります。圧縮を解除されたときの合計サイズが 100 MB を超えることはできません。

## <a name="extending-guest-configuration-with-third-party-tools"></a>サードパーティ製ツールを使用したゲスト構成の拡張

ゲスト構成のアーティファクト パッケージを拡張して、サードパーティ製のツールを含めることができます。 ゲスト構成を拡張するには、2 つのコンポーネントの開発が必要です。

- サードパーティ製ツールの管理に関連するすべてのアクティビティを処理する Desired State Configuration リソース
  - インストール
  - Invoke
  - 出力の変換
- ツールがネイティブで使用するための正しい形式のコンテンツ

コミュニティ ソリューションがまだ存在しない場合、DSC リソースにはカスタム開発が必要です。 コミュニティ ソリューションを見つけるには、PowerShell ギャラリーで [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22) タグを検索します。

> [!NOTE]
> ゲスト構成の拡張性は、"ライセンス持ち込み" シナリオです。 サードパーティ製ツールを使用する前に、その使用条件が満たされていることを確認してください。

開発環境に DSC リソースをインストールした後、`New-GuestConfigurationPackage` の **FilesToInclude** パラメーターを使用して、コンテンツ アーティファクトにサードパーティ製のプラットフォームのコンテンツを含めます。

## <a name="next-steps"></a>次の手順

- 開発環境から[パッケージ成果物をテストする](./guest-configuration-create-test.md)。
- マシンからアクセス可能になるように、[パッケージ成果物を発行](./guest-configuration-create-publish.md)する。
- `GuestConfiguration` モジュールを使用して、環境を大規模に管理するための [Azure Policy の定義を作成する](./guest-configuration-create-definition.md)。
- Azure portal を使用して[カスタム ポリシー定義を割り当てる](../assign-policy-portal.md)。
- [ゲスト構成ポリシーの割り当てに関するコンプライアンスの詳細](./determine-non-compliance.md#compliance-details-for-guest-configuration)を見る方法を学習する。
