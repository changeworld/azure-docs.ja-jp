---
title: Azure Service Fabric CLI - sfctl settings telemetry
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 sfctl テレメトリを構成するためのコマンドの一覧が含まれています。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6af5fa944ef399756f9e890ddd77a7f5f32e2bfb
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903015"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
sfctl のこのインスタンスに対してローカルなテレメトリ設定を構成します。

Sfctl テレメトリでは、パラメーターやパラメーター値が指定されていないコマンドの名前、sfctl バージョン、OS の種類、python のバージョン、コマンドの成否、返されたエラー メッセージが収集されます。

## <a name="commands"></a>コマンド

|command|説明|
| --- | --- |
| set-telemetry | テレメトリを有効または無効にします。 |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl settings telemetry set-telemetry
テレメトリを有効または無効にします。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --off | テレメトリを無効にします。 |
| --on | テレメトリを有効にします。 これが既定値です。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

### <a name="examples"></a>例

テレメトリを無効にします。

```
sfctl settings telemetry set_telemetry --off
```

テレメトリを有効にします。

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。