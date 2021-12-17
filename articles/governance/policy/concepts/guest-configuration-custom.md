---
title: ゲスト構成用の PowerShell Desired State Configuration の動作に対する変更
description: この記事では、Azure Policy を介してマシンに構成の変更を配信するために使用されるプラットフォームの概要について説明します。
ms.date: 05/31/2021
ms.topic: how-to
ms.openlocfilehash: 6118ec0ce0bb8b0296153d32dbad559a6b53ebb8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261864"
---
# <a name="changes-to-behavior-in-powershell-desired-state-configuration-for-guest-configuration"></a>ゲスト構成用の PowerShell Desired State Configuration の動作に対する変更

開始する前に、[ゲスト構成](./guest-configuration.md)の概要を参照することをお勧めします。

[このドキュメントのビデオ チュートリアルを利用できます](https://youtu.be/nYd55FiKpgs)。

ゲスト構成では、[Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) バージョン 3 を使用してマシンを監査および構成します。 DSC 構成では、マシンで満たされている必要がある状態を定義します。 ゲスト構成での DSC の実装方法には、多くの重要な違いがあります。

## <a name="guest-configuration-uses-powershell-7-cross-platform"></a>ゲスト構成でプラットフォームをまたいで PowerShell 7 を使用する

ゲスト構成は、Windows と Linux の管理エクスペリエンスが一貫性を持つように設計されています。 どちらのオペレーティング システム環境でも、PowerShell DSC の知識を持つユーザーは、スクリプトのスキルを使用して構成を作成して発行できます。

ゲスト構成では、PowerShell DSC バージョン 3 のみが使用され、[Linux 用 DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux) の以前の実装や、そのリポジトリに含まれる "nx" プロバイダーには依存していません。

ゲスト構成は、Windows 用の PowerShell 7.1.3 と Linux 用の PowerShell 7.2 プレビュー 6 で動作します。 バージョン 7.2 以降では、`PSDesiredStateConfiguration` モジュールは PowerShell のインストールの一部ではなくなり、代わりに [PowerShell ギャラリーからモジュール](https://www.powershellgallery.com/packages/PSDesiredStateConfiguration)としてインストールされます。

## <a name="multiple-configurations"></a>複数の構成

ゲスト構成では、同じマシンへの複数の構成の割り当てがサポートされます。 ゲスト構成拡張機能のオペレーティング システム内で必要となる特別な手順はありません。 [部分構成](/powershell/scripting/dsc/pull-server/partialConfigs)を構成する必要はありません。

## <a name="dependencies-are-managed-per-configuration"></a>依存関係は構成ごとに管理される

[利用できるツールを使用して構成をパッケージ化する](../how-to/guest-configuration-create.md)と、構成に必要な依存関係が .zip ファイルに組み込まれます。
マシンによって、構成ごとに内容が一意のフォルダーに抽出されます。
ゲスト構成拡張機能によって提供されるエージェントにより、パッケージが抽出されたパスにのみモジュールの自動読み込みを制限する `$Env:PSModulePath` を使用して、構成ごとに専用の PowerShell セッションが作成されます。

この変更には複数の利点があります。

- 同じマシンで、構成ごとに異なるモジュール バージョンを使用できます。
- マシンで構成が削除されなくなった場合、構成間で共有される依存関係を管理する必要なしに、抽出されたフォルダー全体がエージェントによって安全に削除されます。
- 中央のサービスでモジュールの複数のバージョンを管理する必要はありません。
  
## <a name="artifacts-are-managed-as-packages"></a>成果物はパッケージとして管理される

Azure Automation State Configuration 機能には、モジュールと構成スクリプトの成果物管理が含まれます。 両方がサービスに発行されると、スクリプトを MOF 形式にコンパイルできます。 同様に、Windowsプル サーバーも、Web サービス インスタンスで構成とモジュールを管理する必要がありました。 これに対し、DSC 拡張機能ではモデルが簡略化されており、すべての成果物がまとめてパッケージ化され、HTTPS 要求を使用してターゲット マシンからアクセスできる場所に格納されます (Azure Blob Storage が一般的なオプションです)。

ゲスト構成では、すべての成果物がまとめてパッケージ化され、HTTPS 経由でターゲット マシンからアクセスされる、簡略化されたモデルのみが使用されます。
サービスでモジュール、スクリプト、またはコンパイルを発行する必要はありません。 1 つの変更点は、コンパイル済みの MOF が常にパッケージに含まれる必要があることです。 スクリプト ファイルをパッケージに組み込み、ターゲット マシンでコンパイルすることはできません。

## <a name="maximum-size-of-custom-configuration-package"></a>カスタム構成パッケージの最大サイズ

Azure Automation の状態の構成では、DSC 構成で[サイズの制限](../../../automation/automation-dsc-compile.md#compile-your-dsc-configuration-in-windows-powershell)がありました。
ゲスト構成では、合計 100 MB (圧縮前) までのパッケージ サイズがサポートされます。 パッケージ内の MOF ファイルのサイズに特定の制限はありません。

## <a name="configuration-mode-is-set-in-the-package-artifact"></a>構成モードはパッケージ成果物で設定する

構成パッケージの作成時に、次のオプションを使用してモードが設定されます。

- _Audit_: マシンのコンプライアンスを検証します。 変更は行われません。
- _AuditandSet_: マシンのコンプライアンス状態を検証して修復します。
  マシンが準拠していない場合は、変更が行われます。

モードは、複数の構成が割り当てられている場合、構成ごとに異なる可能性があるため、[ローカルの構成マネージャー](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings) サービスではなくパッケージで設定されます。

## <a name="parameter-support-through-azure-resource-manager"></a>Azure Resource Manager を介したパラメーターのサポート

構成 MOF ファイルがマシンに格納されている場合、[ゲスト構成の割り当て](guest-configuration-assignments.md)で `configurationParameter` プロパティ配列によって設定されたパラメーターによって、そのファイル内の静的テキストが上書きされます。 パラメーターを使用すると、マシン内でコマンドを実行することなく、オペレーターがサービス API からカスタマイズと変更を制御できます。

ゲスト構成の割り当てに値を渡す Azure Policy のパラメーターは、"_文字列_" 型である必要があります。 DSC リソースで配列がサポートされている場合でも、パラメーターを使用して配列を渡すことはできません。

## <a name="trigger-set-from-outside-machine"></a>外部マシンから設定されたトリガー

以前のバージョンの DSC の課題は、多くのカスタム コードを使用せずに、WinRM リモート接続に依存することなく、大規模なドリフトを修正することでした。 ゲスト構成によってこの問題が解決されます。 ゲスト構成のユーザーは、[オンデマンド修復](./guest-configuration-policy-effects.md#remediation-on-demand-applyandmonitor)を使用してドリフトの修正を制御できます。

## <a name="sequence-includes-get-method"></a>シーケンスには Get メソッドが含まれる

ゲスト構成でマシンを監査または構成する場合、同じイベントの順序が Windows と Linux の両方で使用されます。 動作の主な変更点は、マシンの状態に関する詳細を返す `Get` メソッドがサービスによって呼び出されることです。

1. 最初にエージェントで `Test` を実行し、構成が正しい状態であるかどうかを判断します。
1. パッケージが `Audit` に設定されている場合、この関数から返されるブール値によって、ゲスト割り当ての Azure Resource Manager の状態が準拠であるかどうかが判別されます。
1. パッケージが `AuditandSet` に設定されている場合、このブール値によって、`Set` メソッドを使用して構成を適用してマシンを修復するかどうかが判別されます。
   `Test` メソッドによって False が返される場合、`Set` が実行されます。 `Test` によって True が返される場合、`Set` は実行されません。
1. 最後に、プロバイダーによって `Get` が実行され、各設定の現在の状態が返されます。これにより、マシンが準拠していない理由と、現在の状態が準拠していることの確認に関する両方の詳細情報が得られます。

## <a name="special-requirements-for-get"></a>Get の特別な要件

関数の `Get` メソッドには、Windows PowerShell の Desired State Configuration では不要だった Azure Policy ゲスト構成の特殊な要件があります。

- 返されるハッシュテーブルには **Reasons** という名前のプロパティが含まれている必要があります。
- Reason プロパティは配列である必要があります。
- 配列内の各項目は、**コード** および **フレーズ** という名前のキーを持つハッシュテーブルである必要があります。
- ハッシュテーブル以外の値を返さないでください。

Reasons プロパティは、コンプライアンス情報をどのように表すかをサービスで標準化するために使用されます。 Reasons の各項目は、そのリソースが準拠していない「理由」と考えることができます。 リソースが複数の理由で準拠していない可能性があるため、プロパティは配列となっています。

サービスでは、プロパティ **コード** および **フレーズ** を指定する必要があります。 カスタムリソースを作成する場合、リソースが準拠していない理由として表示するテキスト (通常は stdout) を **フレーズ** の値に設定します。
**コード** には特定の書式設定要件があるため、監査を行うために使用されたリソースに関する情報をレポートではっきり表示できます。 このソリューションにより、ゲスト構成を拡張できます。 出力を、**Phrase** プロパティの文字列値として返すことができる限り、任意のコマンドを実行できます。

- **コード** (文字列):リソースの名前、繰り返し、およびスペースを含まない短い名前を理由の識別子として指定します。 これら 3 つの値は、スペースを含まず、コロンで区切られている必要があります。
  - たとえば、`registry:registry:keynotpresent` などです
- **フレーズ** (文字列):設定が準拠していない理由を説明するための、人間が判読できるテキスト。
  - たとえば、`The registry key $key isn't present on the machine.` などです

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

コマンドライン ツールを使用して Get で返される情報を取得すると、予期しない出力が返されることがあります。 PowerShell で出力をキャプチャする場合でも、出力が標準エラーに書き込まれている可能性があります。 この問題を回避するには、出力を null にリダイレクトすることを検討してください。

### <a name="the-reasons-property-embedded-class"></a>Reasons プロパティが埋め込まれたクラス

スクリプトベースのリソース (Windows のみ) では、スキーマ MOF ファイルに Reasons クラスを次のように含めます。

```mof
[ClassVersion("1.0.0.0")]
class Reason
{
  [Read] String Phrase;
  [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
  [Key, Description("Example description")] String Example;
  [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

クラスベースのリソース (Windows と Linux) では、PowerShell モジュールに `Reason` クラスを次のように含めます。 Linux では、大文字と小文字が区別されるため、Code の "C" と Phrase の "P" は大文字にする必要があります。

```powershell
enum ensure {
  Absent
  Present
}

class Reason {
  [DscProperty()]
  [string] $Code

  [DscProperty()]
  [string] $Phrase
}

[DscResource()]
class Example {

  [DscProperty(Key)]
  [ensure] $ensure

  [DscProperty()]
  [Reason[]] $Reasons

  [Example] Get() {
    # return current current state
  }

  [void] Set() {
    # set the state
  }

  [bool] Test() {
    # check whether state is correct
  }
}

```

リソースに必須のプロパティがある場合は、`Get` によって `Reason` クラスと同時にそれらのプロパティも返される必要があります。 `Reason` が含まれていない場合、サービスには、`Get` に入力された値と `Get` によって返された値を比較し、詳細な比較を `Reason` として提供する "キャッチオール" の動作が含まれます。

## <a name="configuration-names"></a>構成名

カスタム構成の名前は、すべての場所で一貫している必要があります。 コンテンツ パッケージの `.zip` ファイルの名前、MOF ファイル内の構成名、Resource Manager テンプレート内のゲスト割り当て名は、同じである必要があります。

## <a name="common-dsc-features-not-available-during-guest-configuration-public-preview"></a>ゲスト構成のパブリック プレビュー期間に使用できない DSC の一般的な機能

パブリック プレビュー期間に、ゲスト構成では、"WaitFor*" リソースを使用した[マシン間の依存関係の指定](/powershell/scripting/dsc/configurations/crossnodedependencies)はサポートされません。 あるマシンで別のマシンを監視し、別のマシンがある状態に達するのを待ってから先に進むことはできません。

[再起動処理](/powershell/scripting/dsc/configurations/reboot-a-node)は、ゲスト構成のパブリック プレビュー リリースでは使用できません (`$global:DSCMachineStatus` を使用できないことを含む)。 構成では、構成中または構成の最後にノードを再起動することはできません。

## <a name="known-compatibility-issues-with-supported-modules"></a>サポートされているモジュールに関する既知の互換性の問題

PowerShell ギャラリーの `PsDscResources` モジュールと Windows に含まれている `PSDesiredStateConfiguration` モジュールは、Microsoft によってサポートされ、DSC で一般的に使用される一連のリソースでした。 DSCv3 用に `PSDscResources` モジュールが更新されるまでは、次の既知の互換性の問題に注意してください。

- Windows に含まれている `PSDesiredStateConfiguration` モジュールのリソースは使用しないでください。 代わりに、`PSDscResources` に切り替えます。
- `WindowsFeature` リソースと `WindowsFeatureSet` リソースを `PsDscResources` で使用しないでください。 代わりに、`WindowsOptionalFeature` および `WindowsOptionalFeatureSet` リソースに切り替えます。
  
[Linux 用 DSC](https://github.com/microsoft/PowerShell-DSC-for-Linux/tree/master/Providers) リポジトリに含まれていた Linux 用の "nx" リソースは、C 言語と Python 言語を組み合わせて記述されていました。 Linux での DSC に進むパスでは PowerShell を使用するため、既存の "nx" リソースは DSCv3 と互換性がありません。 Linux でサポートされているリソースを含む新しいモジュールが利用可能になるまで、カスタム リソースを作成する必要があります。

## <a name="coexistence-with-dsc-version-3-and-previous-versions"></a>DSC バージョン 3 と以前のバージョンの共存

ゲスト構成の DSC バージョン 3 は、[Windows](/powershell/scripting/dsc/getting-started/wingettingstarted) および [Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted) にインストールされている以前のバージョンと共存させることができます。
実装は別々です。 ただし、DSC バージョン間の競合検出は行われないため、同じ設定を管理しようとしないでください。

## <a name="next-steps"></a>次の手順

- [ゲスト構成の概要](./guest-configuration.md)に関するページを確認する。
- カスタム ゲスト構成パッケージの[開発環境](../how-to/guest-configuration-create-setup.md)を設定する。
- ゲスト構成用の[パッケージ成果物を作成する](../how-to/guest-configuration-create.md)。
- 開発環境から[パッケージ成果物をテストする](../how-to/guest-configuration-create-test.md)。
- `GuestConfiguration` モジュールを使用して、環境を大規模に管理するための [Azure Policy の定義を作成する](../how-to/guest-configuration-create-definition.md)。
- Azure portal を使用して[カスタム ポリシー定義を割り当てる](../assign-policy-portal.md)。
- [ゲスト構成ポリシーの割り当てに関するコンプライアンスの詳細](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)を見る方法を学習する。
