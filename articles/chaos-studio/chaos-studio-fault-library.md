---
title: Chaos Studio の障害およびアクション ライブラリ
description: Chaos Studio で使用可能なアクション (すべての前提条件とパラメーターを含む) について説明します。
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 60e9dd18d8c9357f2696ec79be08925cdd7e97da
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371013"
---
# <a name="chaos-studio-fault-and-action-library"></a>Chaos Studio の障害およびアクション ライブラリ

現時点では、次の障害を使用できます。 サポートされているリソースの種類を確認するには、[[障害プロバイダー]](./chaos-studio-fault-providers.md) ページにアクセスしてください。

## <a name="time-delay"></a>遅延時間

| プロパティ | 値 |
|-|-|
| 障害プロバイダー | 該当なし |
| サポートされている OS の種類 | 該当なし |
| 説明 | 他のアクションの前、間、または後に遅延時間を追加します。 サービスに障害の影響が現れるまで待機したり、その実験の外部にあるアクティビティが完了するまで待機したりする (たとえば、別の障害を挿入する前に自動修復が実行されるまで待機する) のに役立ちます。 |
| 前提条件 | 該当なし |
| Urn | urn:provider:Azure-chaosStudio:Microsoft.Azure.Chaos.Delay.Timed |
| duration | ISO 8601 形式での遅延の期間 (例: PT10M) |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [ 
    {
      "type": "delay",
      "name": "urn:provider:Azure-chaosStudio:Microsoft.Azure.Chaos.Delay.Timed",
      "duration": "PT10M"
    }
  ] 
}
```

## <a name="cpu-pressure"></a>CPU 負荷

| プロパティ | 値 |
|-|-|
| 機能名 | CPUPressure-1.0 |
| 変換後の型 | Microsoft-Agent |
| サポートされている OS の種類 | Windows、Linux |
| 説明 | 障害アクションの期間中に、この障害が挿入されている VM 上で指定された値まで CPU 負荷を追加します。 期間の最後に、または実験が取り消された場合、人工的な CPU 負荷は削除されます。 |
| 前提条件 | **Linux:** Linux VM 上で障害を実行するには、**stress-ng** ユーティリティがインストールされている必要があります。 これは、使用している Linux ディストリビューションのパッケージ マネージャーを使用してインストールできます。 </br> stress-ng をインストールするための APT コマンド: *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> stress-ng をインストールするための YUM コマンド: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng*  |
| | **Windows:** なし。 |
| Urn | urn:csci:microsoft:agent:cpuPressure/1.0 |
| パラメーター (キー、値)  |
| pressureLevel | VM に適用される CPU 負荷 (%) の値を示す 1 から 99 までの整数。 |
| virtualMachineScaleSetInstances | この障害を仮想マシン スケール セットに適用するときのインスタンス ID の配列。 仮想マシン スケール セットの場合に必要です。 |

### <a name="sample-json"></a>サンプル JSON
```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:cpuPressure/1.0",
      "parameters": [
        {
          "key": "pressureLevel",
          "value": "95"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

### <a name="notes"></a>メモ
Linux での既知の問題:
1. AzureChaosAgent が予期せず強制終了された場合は、ストレスの効果が正しく終了されない可能性があります。
2. Linux CPU の障害は、Ubuntu 16.04-LTS と Ubuntu 18.04-LTS でのみテストされています。

## <a name="physical-memory-pressure"></a>物理メモリ負荷

| プロパティ | 値 |
|-|-|
| 機能名 | PhysicalMemoryPressure-1.0 |
| 変換後の型 | Microsoft-Agent |
| サポートされている OS の種類 | Windows、Linux |
| 説明 | 障害アクションの期間中に、この障害が挿入されている VM 上で指定された値まで物理メモリ負荷を追加します。 期間の最後に、または実験が取り消された場合、人工的な物理メモリ負荷は削除されます。 |
| 前提条件 | **Linux:** Linux VM 上で障害を実行するには、**stress-ng** ユーティリティがインストールされている必要があります。 これは、使用している Linux ディストリビューションのパッケージ マネージャーを使用してインストールできます。 </br> stress-ng をインストールするための APT コマンド: *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> stress-ng をインストールするための YUM コマンド: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng*  |
| | **Windows:** なし。 |
| Urn | urn:csci:microsoft:agent:physicalMemoryPressure/1.0 |
| パラメーター (キー、値) |  |
| pressureLevel | VM に適用される物理メモリ負荷 (%) の値を示す 1 から 99 までの整数。 |
| virtualMachineScaleSetInstances | この障害を仮想マシン スケール セットに適用するときのインスタンス ID の配列。 仮想マシン スケール セットの場合に必要です。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:physicalMemoryPressure/1.0",
      "parameters": [
        {
          "key": "pressureLevel",
          "value": "95"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="virtual-memory-pressure"></a>仮想メモリ負荷

| プロパティ | 値 |
|-|-|
| 機能名 | VirtualMemoryPressure-1.0 |
| 変換後の型 | Microsoft-Agent |
| サポートされている OS の種類 | Windows |
| 説明 | 障害アクションの期間中に、この障害が挿入されている VM 上で指定された値まで仮想メモリ負荷を追加します。 期間の最後に、または実験が取り消された場合、人工的な仮想メモリ負荷は削除されます。 |
| 前提条件 | [なし] : |
| Urn | urn:csci:microsoft:agent:virtualMemoryPressure/1.0 |
| パラメーター (キー、値) |  |
| pressureLevel | VM に適用される物理メモリ負荷 (%) の値を示す 1 から 99 までの整数。 |
| virtualMachineScaleSetInstances | この障害を仮想マシン スケール セットに適用するときのインスタンス ID の配列。 仮想マシン スケール セットの場合に必要です。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:virtualMemoryPressure/1.0",
      "parameters": [
        {
          "key": "pressureLevel",
          "value": "95"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="disk-io-pressure-windows"></a>ディスク I/O 負荷 (Windows)

| プロパティ | 値 |
|-|-|
| 機能名 | DiskIOPressure-1.0 |
| 変換後の型 | Microsoft-Agent |
| サポートされている OS の種類 | Windows |
| 説明 | 障害アクションの期間中に、[diskspd ユーティリティ ](https://github.com/Microsoft/diskspd/wiki) を使用して、それが挿入されている VM のプライマリ ストレージにディスク負荷を追加します。 この障害には、5 種類の実行モードがあります。 期間の最後に、または実験が取り消された場合、人工的なディスク負荷は削除されます。 |
| 前提条件 | [なし] : |
| Urn | urn:csci:microsoft:agent:diskIOPressure/1.0 |
| パラメーター (キー、値) |  |
| pressureMode | VM のプライマリ ストレージに追加するディスク負荷のプリセット モード。 次の表にある負荷モードのいずれかである必要があります。 |
| virtualMachineScaleSetInstances | この障害を仮想マシン スケール セットに適用するときのインスタンス ID の配列。 仮想マシン スケール セットの場合に必要です。 |

### <a name="pressure-modes"></a>負荷モード

| PressureMode | 説明 |
| -- | -- |
| PremiumStorageP10IOPS | numberOfThreads = 1<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 25<br/>sizeOfBlocksInKB = 8<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 2<br/>percentOfWriteActions = 50 |
| PremiumStorageP10Throttling |<br/>numberOfThreads = 2<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 25<br/>sizeOfBlocksInKB = 64<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 1<br/>percentOfWriteActions = 50 |
| PremiumStorageP50IOPS | numberOfThreads = 32<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 32<br/>sizeOfBlocksInKB = 8<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 1<br/>percentOfWriteActions = 50 |
| PremiumStorageP50Throttling | numberOfThreads = 2<br/>randomBlockSizeInKB = 1024<br/>randomSeed = 10<br/>numberOfIOperThread = 2<br/>sizeOfBlocksInKB = 1024<br/>sizeOfWriteBufferInKB = 1024<br/>fileSizeInGB = 20<br/>percentOfWriteActions = 50|
| Default | numberOfThreads = 2<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 2<br/>sizeOfBlocksInKB = 64<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 1<br/>percentOfWriteActions = 50 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:diskIOPressure/1.0",
      "parameters": [
        {
          "key": "pressureMode",
          "value": "PremiumStorageP10IOPS"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="disk-io-pressure-linux"></a>ディスク I/O 負荷 (Linux)

| プロパティ | 値 |
|-|-|
| 機能名 | LinuxDiskIOPressure-1.0 |
| 変換後の型 | Microsoft-Agent |
| サポートされている OS の種類 | Linux |
| 説明 | stress-ng を使用して、ディスクに負荷を適用します。 一時ファイルを使用して I/O プロセスを実行する 1 つ以上のワーカー プロセスが生成されます。 適用される負荷の量の詳細については、 https://wiki.ubuntu.com/Kernel/Reference/stress-ng を参照してください。 |
| 前提条件 | Linux VM 上で障害を実行するには、**stress-ng** ユーティリティがインストールされている必要があります。 これは、使用している Linux ディストリビューションのパッケージ マネージャーを使用してインストールできます。 </br> stress-ng をインストールするための APT コマンド: *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> stress-ng をインストールするための YUM コマンド: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng* |
| Urn | urn:csci:microsoft:agent:linuxDiskIOPressure/1.0 |
| パラメーター (キー、値) |  |
| workerCount | 実行されるワーカー プロセスの数。 これを 0 に設定すると、存在するプロセッサと同じ数のワーカー プロセスが生成されます。 |
| fileSizePerWorker | ワーカーが I/O 操作を実行する対象となる一時ファイルのサイズ。 整数とバイト (b)、キロバイト (k)、メガバイト (m)、またはギガバイト (g) での単位 (たとえば、4 メガバイトの場合は 4m、256 ギガバイトの場合は 256g) |
| blockSize | ディスク I/O 操作に使用されるブロック サイズ (上限は 4 メガバイト)。 整数とバイト (b)、キロバイト (k)、またはメガバイト (m) での単位 (たとえば、512 キロバイトの場合は 512k) |
| virtualMachineScaleSetInstances | この障害を仮想マシン スケール セットに適用するときのインスタンス ID の配列。 仮想マシン スケール セットの場合に必要です。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:linuxDiskIOPressure/1.0",
      "parameters": [
        {
          "key": "workerCount",
          "value": "0"
        },
        {
          "key": "fileSizePerWorker",
          "value": "512m"
        },
        {
          "key": "blockSize",
          "value": "256k"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="arbitrary-stress-ng-stress"></a>任意の stress-ng ストレス

| プロパティ | 値 |
|-|-|
| 機能名 | StressNg-1.0 |
| 変換後の型 | Microsoft-Agent |
| サポートされている OS の種類 | Linux |
| 説明 | 引数を直接 stress-ng に渡すことによって、任意の stress-ng コマンドを実行します。 stress-ng に対して事前に定義されたいずれかの障害がニーズを満たさない場合に役立ちます。 |
| 前提条件 | Linux VM 上で障害を実行するには、**stress-ng** ユーティリティがインストールされている必要があります。 これは、使用している Linux ディストリビューションのパッケージ マネージャーを使用してインストールできます。 </br> stress-ng をインストールするための APT コマンド: *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> stress-ng をインストールするための YUM コマンド: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng* |
| Urn | urn:csci:microsoft:agent:stressNg/1.0 |
| パラメーター (キー、値) |  |
| stressNgArguments | stress-ng プロセスに渡す 1 つ以上の引数。 可能性のある stress-ng 引数の詳細については、 https://wiki.ubuntu.com/Kernel/Reference/stress-ng を参照してください。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:stressNg/1.0",
      "parameters": [
        {
          "key": "stressNgArguments",
          "value": "--random 64"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="stop-windows-service"></a>Windows サービスを停止する

| プロパティ | 値 |
|-|-|
| 機能名 | StopService-1.0 |
| 変換後の型 | Microsoft-Agent |
| サポートされている OS の種類 | Windows |
| 説明 | 障害の期間中に、Windows サービス コントローラー API を使用して Windows サービスを停止し、期間の最後に、または実験が取り消された場合はこれを再起動します。 |
| 前提条件 | [なし] : |
| Urn | urn:csci:microsoft:agent:stopService/1.0 |
| パラメーター (キー、値) |  |
| serviceName | 停止する Windows サービスの名前。 コマンド プロンプトで `sc.exe query` を実行して、サービス名を調べることができます。Windows サービスのフレンドリ名はサポートされていません。 |
| virtualMachineScaleSetInstances | この障害を仮想マシン スケール セットに適用するときのインスタンス ID の配列。 仮想マシン スケール セットの場合に必要です。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:stopService/1.0",
      "parameters": [
        {
          "key": "serviceName",
          "value": "nvagent"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="time-change"></a>時刻の変更

| プロパティ | 値 |
|-|-|
| 機能名 | TimeChange-1.0 |
| 変換後の型 | Microsoft-Agent |
| サポートされている OS の種類 | Windows |
| 説明 | それが挿入されている VM のシステム時刻を変更し、期間の最後に、または実験が取り消された場合はこれをリセットします。 |
| 前提条件 | [なし] : |
| Urn | urn:csci:microsoft:agent:timeChange/1.0 |
| パラメーター (キー、値) |  |
| dateTime | [ISO8601 形式](https://www.cryptosys.net/pki/manpki/pki_iso8601datetime.html)での DateTime 文字列。 YYYY-MM-DD 値がない場合は、既定で、実験が実行される現在の日に設定されます。 Thh:mm:ss 値がない場合、既定値は午前 12:00:00 です。 2 桁の年 (YY) が指定されている場合は、現在の世紀に基づいて 4 桁の年 (YYYY) に変換されます。 \<Z\> がない場合は、既定でローカル タイム ゾーンのオフセットに設定されます。 \<Z\> には常に、符号記号 (負または正) が含まれている必要があります。 |
| virtualMachineScaleSetInstances | この障害を仮想マシン スケール セットに適用するときのインスタンス ID の配列。 仮想マシン スケール セットの場合に必要です。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:timeChange/1.0",
      "parameters": [
        {
          "key": "dateTime",
          "value": "2038-01-01T03:14:07"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="kill-process"></a>プロセスを強制終了する

| プロパティ | 値 |
|-|-|
| 機能名 | KillProcess-1.0 |
| 変換後の型 | Microsoft-Agent |
| サポートされている OS の種類 | Windows、Linux |
| 説明 | 障害パラメーターで送信されたプロセス名に一致するプロセスのすべての実行中のインスタンスを強制終了します。 障害アクションに対して設定された期間内は、指定された強制終了の間隔の値に基づいて、プロセスが繰り返し強制終了されます。 この障害は、それに対して自己修復が構成されている場合は、システム管理者がそのプロセスを手動で回復する必要のある破壊的な障害です。 |
| 前提条件 | [なし] : |
| Urn | urn:csci:microsoft:agent:killProcess/1.0 |
| パラメーター (キー、値) |  |
| processName | VM 上で実行されているプロセスの名前 (.exe はなし) |
| killIntervalInMilliseconds | 連続した強制終了の試みの間で障害を待機させる時間 (ミリ秒単位)。 |
| virtualMachineScaleSetInstances | この障害を仮想マシン スケール セットに適用するときのインスタンス ID の配列。 仮想マシン スケール セットの場合に必要です。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:killProcess/1.0",
      "parameters": [
        {
          "key": "processName",
          "value": "myapp"
        },
        {
          "key": "killIntervalInMilliseconds",
          "value": "1000"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="dns-failure"></a>DNS エラー

| プロパティ | 値 |
|-|-|
| 機能名 | DnsFailure-1.0 |
| 変換後の型 | Microsoft-Agent |
| サポートされている OS の種類 | Windows |
| 説明 | DNS 参照要求の応答を指定されたエラー コードに置き換えます。 |
| 前提条件 | [なし] : |
| Urn | urn:csci:microsoft:agent:dnsFailure/1.0 |
| パラメーター (キー、値) |  |
| hosts | DNS 参照要求を失敗させるホスト名の区切られた JSON 配列。 |
| dnsFailureReturnCode | 参照エラーとしてクライアントに返される DNS エラー コード (FormErr、ServFail、NXDomain、NotImp、Refused、XDomain、YXRRSet、NXRRSet、NotAuth、NotZone)。 DNS リターン コードの詳細については、[IANA の Web サイト](https://www.iana.org/assignments/dns-parameters/dns-parameters.xml#dns-parameters-6)を参照してください。 |
| virtualMachineScaleSetInstances | この障害を仮想マシン スケール セットに適用するときのインスタンス ID の配列。 仮想マシン スケール セットの場合に必要です。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:dnsFailure/1.0",
      "parameters": [
        {
          "key": "hosts",
          "value": "[ \"www.bing.com\", \"msdn.microsoft.com\" ]"
        },
        {
          "key": "dnsFailureReturnCode",
          "value": "ServFail"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

### <a name="limitations"></a>制限事項

* DNS エラーの障害には、Windows 2019 RS5 以降が必要です。
* 障害で定義されたホスト名の障害の期間中、DNS キャッシュは無視されます。

## <a name="network-latency"></a>ネットワーク待ち時間

| プロパティ | 値 |
|-|-|
| 機能名 | NetworkLatency-1.0 |
| 変換後の型 | Microsoft-Agent |
| サポートされている OS の種類 | Windows |
| 説明 | 指定されたポート範囲とネットワーク ブロックのネットワーク待ち時間を増やします。 |
| 前提条件 | エージェントは管理者として実行する必要があります。 エージェントが VM 拡張機能としてインストールされている場合は、既定で管理者として実行されます。 |
| Urn | urn:csci:microsoft:agent:networkLatency/1.0 |
| パラメーター (キー、値) |  |
| latencyInMilliseconds | 適用される待ち時間の量 (ミリ秒単位)。 |
| destinationFilters | どの送信パケットを障害挿入の対象とするかを定義するパケット フィルターの区切られた JSON 配列。 最大 3 つです。 |
| address | IP 範囲の開始を示す IP アドレス。 |
| subnetMask | IP アドレス範囲のサブネット マスク。 |
| portLow | (省略可能) ポート範囲の開始のポート番号。 |
| portHigh | (省略可能) ポート範囲の終了のポート番号。 |
| virtualMachineScaleSetInstances | この障害を仮想マシン スケール セットに適用するときのインスタンス ID の配列。 仮想マシン スケール セットの場合に必要です。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:networkLatency/1.0",
      "parameters": [
        {
          "key": "destinationFilters",
          "value": "[ { \"address\": \"23.45.229.97\", \"subnetMask\": \"255.255.255.224\", \"portLow\": \"5000\", \"portHigh\": \"5200\" } ]"
        },
        {
          "key": "latencyInMilliseconds",
          "value": "100",
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="network-disconnect"></a>ネットワーク切断

| プロパティ | 値 |
|-|-|
| 機能名 | NetworkDisconnect-1.0 |
| 変換後の型 | Microsoft-Agent |
| サポートされている OS の種類 | Windows |
| 説明 | 指定されたポート範囲とネットワーク ブロックの送信ネットワーク トラフィックをブロックします。 |
| 前提条件 | エージェントは管理者として実行する必要があります。 エージェントが VM 拡張機能としてインストールされている場合は、既定で管理者として実行されます。 |
| Urn | urn:csci:microsoft:agent:networkDisconnect/1.0 |
| パラメーター (キー、値) |  |
| destinationFilters | どの送信パケットを障害挿入の対象とするかを定義するパケット フィルターの区切られた JSON 配列。 最大 3 つです。 |
| address | IP 範囲の開始を示す IP アドレス。 |
| subnetMask | IP アドレス範囲のサブネット マスク。 |
| portLow | (省略可能) ポート範囲の開始のポート番号。 |
| portHigh | (省略可能) ポート範囲の終了のポート番号。 |
| virtualMachineScaleSetInstances | この障害を仮想マシン スケール セットに適用するときのインスタンス ID の配列。 仮想マシン スケール セットの場合に必要です。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:networkDisconnect/1.0",
      "parameters": [
        {
          "key": "destinationFilters",
          "value": "[ { \"address\": \"23.45.229.97\", \"subnetMask\": \"255.255.255.224\", \"portLow\": \"5000\", \"portHigh\": \"5200\" } ]"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

> [!WARNING]
> ネットワーク切断の障害は、新しい接続にのみ影響を与えます。 既存の **アクティブな** 接続は、引き続き保持されます。 サービスまたはプロセスを再起動して、接続を強制的に切断できます。

## <a name="network-disconnect-with-firewall-rule"></a>ファイアウォール規則を使用したネットワーク切断

| プロパティ | 値 |
|-|-|
| 機能名 | NetworkDisconnectViaFirewall-1.0 |
| 変換後の型 | Microsoft-Agent |
| サポートされている OS の種類 | Windows |
| 説明 | 指定されたポート範囲とネットワーク ブロックの送信トラフィックをブロックする Windows ファイアウォール規則を適用します。 |
| 前提条件 | エージェントは管理者として実行する必要があります。 エージェントが VM 拡張機能としてインストールされている場合は、既定で管理者として実行されます。 |
| Urn | urn:csci:microsoft:agent:networkDisconnectViaFirewall/1.0 |
| パラメーター (キー、値) |  |
| destinationFilters | どの送信パケットを障害挿入の対象とするかを定義するパケット フィルターの区切られた JSON 配列。 最大 3 つです。 |
| address | IP 範囲の開始を示す IP アドレス。 |
| subnetMask | IP アドレス範囲のサブネット マスク。 |
| portLow | (省略可能) ポート範囲の開始のポート番号。 |
| portHigh | (省略可能) ポート範囲の終了のポート番号。 |
| virtualMachineScaleSetInstances | この障害を仮想マシン スケール セットに適用するときのインスタンス ID の配列。 仮想マシン スケール セットの場合に必要です。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:networkDisconnectViaFirewall/1.0",
      "parameters": [
        {
          "key": "destinationFilters",
          "value": "[ { \"Address\": \"23.45.229.97\", \"SubnetMask\": \"255.255.255.224\", \"PortLow\": \"5000\", \"PortHigh\": \"5200\" } ]"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="arm-virtual-machine-shutdown"></a>ARM 仮想マシンのシャットダウン
| プロパティ | 値 |
|-|-|
| 機能名 | Shutdown-1.0 |
| 変換後の型 | Microsoft-VirtualMachine |
| サポートされている OS の種類 | Windows、Linux |
| 説明 | 障害の期間中に VM をシャットダウンし、オプションで、障害期間の最後に、または実験が取り消された場合はその VM を再起動します。 Azure Resource Manager VM のみがサポートされています。 |
| 前提条件 | [なし] : |
| Urn | urn:csci:microsoft:virtualMachine:shutdown/1.0 |
| パラメーター (キー、値) |  |
| abruptShutdown | (省略可能) VM を正常にシャットダウンするか、または突然シャットダウンするか (破壊的) を示すブール値。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:virtualMachine:shutdown/1.0",
      "parameters": [
        {
          "key": "abruptShutdown",
          "value": "false"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="arm-virtual-machine-scale-set-instance-shutdown"></a>ARM 仮想マシン スケール セット インスタンスのシャットダウン

| プロパティ | 値 |
|-|-|
| 機能名 | Shutdown-1.0 |
| 変換後の型 | Microsoft-VirtualMachineScaleSet |
| サポートされている OS の種類 | Windows、Linux |
| 説明 | 障害の期間中に仮想マシン スケール セット インスタンスをシャットダウンまたは強制終了し、オプションで、障害期間の最後に、または実験が取り消された場合はその VM を再起動します。 |
| 前提条件 | [なし] : |
| Urn | urn:csci:microsoft:virtualMachineScaleSet:shutdown/1.0 |
| パラメーター (キー、値) |  |
| abruptShutdown | (省略可能) 仮想マシン スケール セット インスタンスを正常にシャットダウンするか、または突然シャットダウンするか (破壊的) を示すブール値。 |
| instances | 障害が適用される仮想マシン スケール セット インスタンス ID の区切られた配列である文字列。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:virtualMachineScaleSet:shutdown/1.0",
      "parameters": [
        {
          "key": "abruptShutdown",
          "value": "true"
        },
        {
          "key": "instances",
          "value": "[\"1\",\"3\"]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="cosmos-db-failover"></a>Cosmos DB のフェールオーバー

| プロパティ | 値 |
|-|-|
| 機能名 | Failover-1.0 |
| 変換後の型 | Microsoft-CosmosDB |
| 説明 | [書き込みリージョン障害](../cosmos-db/high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage)をシミュレートするために、1 つの書き込みリージョンを持つ Cosmos DB アカウントを指定された読み取りリージョンにフェールオーバーさせます。 |
| 前提条件 | [なし] : |
| Urn | urn:csci:microsoft:cosmosDB:failover/1.0 |
| パラメーター (キー、値) |  |
| readRegion | フェールオーバー中に書き込みリージョンに昇格させる読み取りリージョン ("米国東部 2" など)。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:cosmosDB:failover/1.0",
      "parameters": [
        {
          "key": "readRegion",
          "value": "West US 2"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-network-faults"></a>AKS Chaos Mesh のネットワーク障害

| プロパティ | 値 |
|-|-|
| 機能名 | NetworkChaos-2.1 |
| 変換後の型 | Microsoft-AzureKubernetesServiceChaosMesh |
| 説明 | [Chaos Mesh](https://chaos-mesh.org/docs/simulate-network-chaos-on-kubernetes/) から発生させることができるネットワーク障害を AKS クラスターに対して実行させます。 ネットワークの停止、遅延、重複、損失、破損の結果として得られる AKS インシデントを再作成するのに役立ちます。 |
| 前提条件 | AKS クラスターには [Chaos Mesh がデプロイされている必要があります](chaos-studio-tutorial-aks-portal.md)。 |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:networkChaos/2.1 |
| パラメーター (キー、値) |  |
| jsonSpec | JSON 形式であり、ARM テンプレート、REST API、または Azure CLI を使って作成した場合は、[NetworkChaos の種類](https://chaos-mesh.org/docs/simulate-network-chaos-on-kubernetes/#create-experiments-using-the-yaml-files)を使う JSON でエスケープされた Chaos Mesh 仕様。 [このような YAML から JSON へのコンバーター](https://www.convertjson.com/yaml-to-json.htm)を使用して Chaos Mesh YAML を JSON に変換して縮小し、[このような JSON 文字列エスケープ ツール](https://www.freeformatter.com/json-escape.html)を使用して JSON 仕様をエスケープすることができます。"jsonSpec" プロパティには YAML のみを含めます (メタデータや種類などは含めないでください)。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:networkChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"delay\",\"mode\":\"one\",\"selector\":{\"namespaces\":[\"default\"],\"labelSelectors\":{\"app\":\"web-show\"}},\"delay\":{\"latency\":\"10ms\",\"correlation\":\"100\",\"jitter\":\"0ms\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-pod-faults"></a>AKS Chaos Mesh のポッド障害

| プロパティ | 値 |
|-|-|
| 機能名 | PodChaos-2.1 |
| 変換後の型 | Microsoft-AzureKubernetesServiceChaosMesh |
| 説明 | [Chaos Mesh](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/) から発生させることができるポッド障害を AKS クラスターに対して実行させます。 ポッドのエラーまたはコンテナーの問題の結果である AKS インシデントを再作成するのに役立ちます。 |
| 前提条件 | AKS クラスターには [Chaos Mesh がデプロイされている必要があります](chaos-studio-tutorial-aks-portal.md)。 |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/2.1 |
| パラメーター (キー、値) |  |
| jsonSpec | JSON 形式であり、ARM テンプレート、REST API、または Azure CLI を使って作成した場合は、[PodChaos の種類](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/#create-experiments-using-yaml-configuration-files)を使う JSON でエスケープされた Chaos Mesh 仕様。 [このような YAML から JSON へのコンバーター](https://www.convertjson.com/yaml-to-json.htm)を使用して Chaos Mesh YAML を JSON に変換して縮小し、[このような JSON 文字列エスケープ ツール](https://www.freeformatter.com/json-escape.html)を使用して JSON 仕様をエスケープすることができます。"jsonSpec" プロパティには YAML のみを含めます (メタデータや種類などは含めないでください)。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"pod-failure\",\"mode\":\"one\",\"duration\":\"30s\",\"selector\":{\"labelSelectors\":{\"app.kubernetes.io\/component\":\"tikv\"}}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-stress-faults"></a>AKS Chaos Mesh のストレス障害

| プロパティ | 値 |
|-|-|
| 機能名 | StressChaos-2.1 |
| 変換後の型 | Microsoft-AzureKubernetesServiceChaosMesh |
| 説明 | [Chaos Mesh](https://chaos-mesh.org/docs/simulate-heavy-stress-on-kubernetes/) から発生させることができるストレス障害を AKS クラスターに対して実行させます。 ポッドのコレクションに対するストレス (大量の CPU またはメモリの使用など) を原因とする AKS インシデントを再作成するのに役立ちます。 |
| 前提条件 | AKS クラスターには [Chaos Mesh がデプロイされている必要があります](chaos-studio-tutorial-aks-portal.md)。 |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:stressChaos/2.1 |
| パラメーター (キー、値) |  |
| jsonSpec | JSON 形式であり、ARM テンプレート、REST API、または Azure CLI を使って作成した場合は、[StressChaos の種類](https://chaos-mesh.org/docs/simulate-heavy-stress-on-kubernetes/#create-experiments-using-the-yaml-file)を使う JSON でエスケープされた Chaos Mesh 仕様。 [このような YAML から JSON へのコンバーター](https://www.convertjson.com/yaml-to-json.htm)を使用して Chaos Mesh YAML を JSON に変換して縮小し、[このような JSON 文字列エスケープ ツール](https://www.freeformatter.com/json-escape.html)を使用して JSON 仕様をエスケープすることができます。"jsonSpec" プロパティには YAML のみを含めます (メタデータや種類などは含めないでください)。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:stressChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app\":\"app1\"}},\"stressors\":{\"memory\":{\"workers\":4,\"size\":\"256MB\"}}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-io-faults"></a>AKS Chaos Mesh の IO 障害

| プロパティ | 値 |
|-|-|
| 機能名 | IOChaos-2.1 |
| 変換後の型 | Microsoft-AzureKubernetesServiceChaosMesh |
| 説明 | [Chaos Mesh](https://chaos-mesh.org/docs/simulate-io-chaos-on-kubernetes/) から発生させることができる IO 障害を AKS クラスターに対して実行させます。 IO システム コール (`open`、`read`、`write` など) を使用しているときの IO の遅延や読み取り/書き込みエラーを原因とする AKS インシデントを再作成するのに役立ちます。 |
| 前提条件 | AKS クラスターには [Chaos Mesh がデプロイされている必要があります](chaos-studio-tutorial-aks-portal.md)。 |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:IOChaos/2.1 |
| パラメーター (キー、値) |  |
| jsonSpec | JSON 形式であり、ARM テンプレート、REST API、または Azure CLI を使って作成した場合は、[IOChaos の種類](https://chaos-mesh.org/docs/simulate-io-chaos-on-kubernetes/#create-experiments-using-the-yaml-files)を使う JSON でエスケープされた Chaos Mesh 仕様。 [このような YAML から JSON へのコンバーター](https://www.convertjson.com/yaml-to-json.htm)を使用して Chaos Mesh YAML を JSON に変換して縮小し、[このような JSON 文字列エスケープ ツール](https://www.freeformatter.com/json-escape.html)を使用して JSON 仕様をエスケープすることができます。"jsonSpec" プロパティには YAML のみを含めます (メタデータや種類などは含めないでください)。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:IOChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"latency\",\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app\":\"etcd\"}},\"volumePath\":\"\/var\/run\/etcd\",\"path\":\"\/var\/run\/etcd\/**\/*\",\"delay\":\"100ms\",\"percent\":50,\"duration\":\"400s\"}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-time-faults"></a>AKS Chaos Mesh の時刻の障害

| プロパティ | 値 |
|-|-|
| 機能名 | TimeChaos-2.1 |
| 変換後の型 | Microsoft-AzureKubernetesServiceChaosMesh |
| 説明 | [Chaos Mesh](https://chaos-mesh.org/docs/simulate-time-chaos-on-kubernetes/) を使用して AKS クラスターでシステム クロックの変更を発生させます。 分散システムの同期ずれ、うるう年/うるう秒のロジックの欠落や不正確さなどを原因とする AKS インシデントを再作成するのに役立ちます。 |
| 前提条件 | AKS クラスターには [Chaos Mesh がデプロイされている必要があります](chaos-studio-tutorial-aks-portal.md)。 |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:timeChaos/2.1 |
| パラメーター (キー、値) |  |
| jsonSpec | JSON 形式であり、ARM テンプレート、REST API、または Azure CLI を使って作成した場合は、[TimeChaos の種類](https://chaos-mesh.org/docs/simulate-time-chaos-on-kubernetes/#create-experiments-using-the-yaml-file)を使う JSON でエスケープされた Chaos Mesh 仕様。 [このような YAML から JSON へのコンバーター](https://www.convertjson.com/yaml-to-json.htm)を使用して Chaos Mesh YAML を JSON に変換して縮小し、[このような JSON 文字列エスケープ ツール](https://www.freeformatter.com/json-escape.html)を使用して JSON 仕様をエスケープすることができます。"jsonSpec" プロパティには YAML のみを含めます (メタデータや種類などは含めないでください)。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:timeChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app\":\"app1\"}},\"timeOffset\":\"-10m100ns\"}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-kernel-faults"></a>AKS Chaos Mesh のカーネル障害

| プロパティ | 値 |
|-|-|
| 機能名 | KernelChaos-2.1 |
| 変換後の型 | Microsoft-AzureKubernetesServiceChaosMesh |
| 説明 | [Chaos Mesh](https://chaos-mesh.org/docs/simulate-kernel-chaos-on-kubernetes/) から発生させることができるカーネル障害を AKS クラスターに対して実行させます。 マウント エラーや未割り当てメモリなどの Linux カーネル レベルのエラーを原因とする AKS インシデントを再作成するのに役立ちます。 |
| 前提条件 | AKS クラスターには [Chaos Mesh がデプロイされている必要があります](chaos-studio-tutorial-aks-portal.md)。 |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:kernelChaos/2.1 |
| パラメーター (キー、値) |  |
| jsonSpec | JSON 形式であり、ARM テンプレート、REST API、または Azure CLI を使って作成した場合は、[KernelChaos の種類](https://chaos-mesh.org/docs/simulate-kernel-chaos-on-kubernetes/#configuration-file)を使う JSON でエスケープされた Chaos Mesh 仕様。 [このような YAML から JSON へのコンバーター](https://www.convertjson.com/yaml-to-json.htm)を使用して Chaos Mesh YAML を JSON に変換して縮小し、[このような JSON 文字列エスケープ ツール](https://www.freeformatter.com/json-escape.html)を使用して JSON 仕様をエスケープすることができます。"jsonSpec" プロパティには YAML のみを含めます (メタデータや種類などは含めないでください)。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:kernelChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"namespaces\":[\"chaos-mount\"]},\"failKernRequest\":{\"callchain\":[{\"funcname\":\"__x64_sys_mount\"}],\"failtype\":0}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-http-faults"></a>AKS Chaos Mesh の HTTP 障害

| プロパティ | 値 |
|-|-|
| 機能名 | HTTPChaos-2.1 |
| 変換後の型 | Microsoft-AzureKubernetesServiceChaosMesh |
| 説明 | [Chaos Mesh](https://chaos-mesh.org/docs/simulate-http-chaos-on-kubernetes/) から発生させることができる HTTP 障害を AKS クラスターに対して実行させます。 遅延応答や不正な応答など、HTTP 要求や応答処理の失敗によるインシデントを再現する場合に便利です。 |
| 前提条件 | AKS クラスターには [Chaos Mesh がデプロイされている必要があります](chaos-studio-tutorial-aks-portal.md)。 |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:httpChaos/2.1 |
| パラメーター (キー、値) |  |
| jsonSpec | JSON 形式であり、ARM テンプレート、REST API、または Azure CLI を使って作成した場合は、[HTTPChaos の種類](https://chaos-mesh.org/docs/simulate-http-chaos-on-kubernetes/#create-experiments)を使う JSON でエスケープされた Chaos Mesh 仕様。 [このような YAML から JSON へのコンバーター](https://www.convertjson.com/yaml-to-json.htm)を使用して Chaos Mesh YAML を JSON に変換して縮小し、[このような JSON 文字列エスケープ ツール](https://www.freeformatter.com/json-escape.html)を使用して JSON 仕様をエスケープすることができます。"jsonSpec" プロパティには YAML のみを含めます (メタデータや種類などは含めないでください)。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:httpChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"all\",\"selector\":{\"labelSelectors\":{\"app\":\"nginx\"}},\"target\":\"Request\",\"port\":80,\"method\":\"GET\",\"path\":\"\/api\",\"abort\":true,\"duration\":\"5m\",\"scheduler\":{\"cron\":\"@every 10m\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-dns-faults"></a>AKS Chaos Mesh の DNS 障害

| プロパティ | 値 |
|-|-|
| 機能名 | DNSChaos-2.1 |
| 変換後の型 | Microsoft-AzureKubernetesServiceChaosMesh |
| 説明 | [Chaos Mesh](https://chaos-mesh.org/docs/simulate-dns-chaos-on-kubernetes/) から発生させることができる DNS 障害を AKS クラスターに対して実行させます。 DNS 障害によるインシデントを再現する場合に便利です。 |
| 前提条件 | AKS クラスターには [Chaos Mesh がデプロイされている](chaos-studio-tutorial-aks-portal.md)必要があります。また、[DNS サービスがインストールされている必要があります](https://chaos-mesh.org/docs/simulate-dns-chaos-on-kubernetes/#deploy-chaos-dns-service)。 |
| Urn | urn:csci:microsoft:azureKubernetesServiceChaosMesh:dnsChaos/2.1 |
| パラメーター (キー、値) |  |
| jsonSpec | JSON 形式であり、ARM テンプレート、REST API、または Azure CLI を使って作成した場合は、[DNSChaos の種類](https://chaos-mesh.org/docs/simulate-dns-chaos-on-kubernetes/#create-experiments-using-the-yaml-file)を使う JSON でエスケープされた Chaos Mesh 仕様。 [このような YAML から JSON へのコンバーター](https://www.convertjson.com/yaml-to-json.htm)を使用して Chaos Mesh YAML を JSON に変換して縮小し、[このような JSON 文字列エスケープ ツール](https://www.freeformatter.com/json-escape.html)を使用して JSON 仕様をエスケープすることができます。"jsonSpec" プロパティには YAML のみを含めます (メタデータや種類などは含めないでください)。 |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:dnsChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"random\",\"mode\":\"all\",\"patterns\":[\"google.com\",\"chaos-mesh.*\",\"github.?om\"],\"selector\":{\"namespaces\":[\"busybox\"]}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="network-security-group-set-rules"></a>ネットワーク セキュリティ グループ (規則の設定)

| プロパティ | 値 |
|-|-|
| 機能名 | SecurityRule-1.0 |
| 変換後の型 | Microsoft-NetworkSecurityGroup |
| 説明 | 既存の Azure ネットワーク セキュリティ グループまたは一連の Azure ネットワーク セキュリティ グループでの規則の操作または作成を有効にします (規則の定義がセキュリティ グループにまたがって適用可能であることを前提にしています)。 ダウンストリームの停止またはリージョン間の依存関係/非依存関係のシミュレート、サービスのフェールオーバーを強制的に発生させるロジックをトリガーすることが期待されるイベントのシミュレート、監視または状態管理サービスから、あるいは Chaos Agent をデプロイできない場合にネットワーク トラフィックをブロックまたは許可するための別の方法としてアクションをトリガーすることが期待されるイベントのシミュレートに役立ちます。 |
| 前提条件 | [なし] : |
| Urn | urn:csci:microsoft:networkSecurityGroup:securityRule/1.0 |
| パラメーター (キー、値) |  |
| name | 作成されるセキュリティ規則の一意の名前。 その NSG に同じ名前を持つ別の規則が既に存在する場合、この障害は失敗します。 文字または数字で始まり、文字、数字、またはアンダースコアで終わる必要があり、文字、数字、アンダースコア、ピリオド、またはハイフンのみを含めることができます。 |
| protocol | セキュリティ規則のプロトコル。 [任意]、[TCP]、[UDP]、または [ICMP] である必要があります。 |
| sourceAddresses | CIDR 形式の IP アドレスの JSON 区切り配列を表す文字列。 受信規則のサービス タグ名 ("AppService" など) を指定することもできます。 また、すべてのソース IP に一致させるためにアスタリスク '*' を使用することもできます。 |
| destinationAddresses | CIDR 形式の IP アドレスの JSON 区切り配列を表す文字列。 送信規則のサービス タグ名 ("AppService" など) を指定することもできます。 また、すべての宛先 IP に一致させるためにアスタリスク '*' を使用することもできます。 |
| action | セキュリティ グループのアクセスの種類。 [許可] または [拒否] のどちらかである必要があります。 |
| destinationPortRanges | 1 つのポートまたはポート範囲の JSON 区切り配列を表す文字列 (80 や 1024-65535 など)。 |
| sourcePortRanges | 1 つのポートまたはポート範囲の JSON 区切り配列を表す文字列 (80 や 1024-65535 など)。 |
| priority | ネットワーク セキュリティ グループ内のすべてのセキュリティ規則に対して一意である 100 から 4096 までの値。 その NSG に同じ優先順位を持つ別の規則が既に存在する場合、この障害は失敗します。 |
| 方向 | このセキュリティ規則の影響を受けるトラフィックの方向。 [受信] または [送信] のどちらかである必要があります。 |

### <a name="sample-json"></a>サンプル JSON

```json
{ 
  "name": "branchOne", 
  "actions": [ 
    { 
      "type": "continuous", 
      "name": "urn:csci:microsoft:networkSecurityGroup:securityRule/1.0", 
      "parameters": [ 
          { 
              "key": "name", 
              "value": "Block_SingleHost_to_Networks" 

          }, 
          { 
              "key": "protocol", 
              "value": "Any" 
          }, 
          { 
              "key": "sourceAddresses", 
              "value": "[\"10.1.1.128/32\"]"
          }, 
          { 
              "key": "destinationAddresses", 
              "value": "[\"10.20.0.0/16\",\"10.30.0.0/16\"]"
          }, 
          { 
              "key": "access", 
              "value": "Deny" 
          }, 
          { 
              "key": "destinationPortRanges", 
              "value": "[\"80-8080\"]"
          }, 
          { 
              "key": "sourcePortRanges", 
              "value": "[\"*\"]"
          }, 
          { 
              "key": "priority", 
              "value": "100" 
          }, 
          { 
              "key": "direction", 
              "value": "Outbound" 
          } 
      ], 
      "duration": "PT10M", 
      "selectorid": "myResources" 
    } 
  ] 
} 
```

### <a name="limitations"></a>制限事項

* この障害は、既存のネットワーク セキュリティ グループにのみ適用できます。
* トラフィックを拒否することを目的にした NSG 規則が適用された場合、既存の接続は、4 分間 **アイドル状態** になるまで中断されません。 1 つの回避策として、NSG の障害が適用されたときに既存の接続が切断されるようになる障害を使用する別の分岐を同じ手順で追加します。 たとえば、プロセスを強制終了するか、サービスを一時的に停止するか、または VM を再起動すると、接続がリセットされます。
* 規則は、そのアクションの開始時に適用されます。 そのアクションの期間中に規則に外部からの変更が加えられると、実験は失敗します。
* アプリケーション セキュリティ グループの規則の作成または変更はサポートされていません。
* 優先順位の値は、対象となる各 NSG で一意である必要があります。 別の規則と同じ優先順位の値を持つ新しい規則を作成しようとすると、実験は失敗します。

## <a name="azure-cache-for-redis-reboot"></a>Azure Cache for Redis の再起動

| プロパティ | 値 |
|-|-|
| 機能名 | Reboot-1.0 |
| 変換後の型 | Microsoft-AzureClusteredCacheForRedis |
| 説明 | 短時間の停止をシミュレートするために、ターゲット上で強制的な再起動操作を発生させます。 |
| 前提条件 | [なし] : |
| Urn | urn:csci:microsoft:azureClusteredCacheForRedis:reboot/1.0 |
| 障害の種類 | 離散 |
| パラメーター (キー、値) |  |
| rebootType | PrimaryNode、SecondaryNode、または AllNodes として指定できる、再起動アクションが実行されるノードの種類。  |
| shardId | 再起動されるシャードの ID。  |

### <a name="sample-json"></a>サンプル JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "discrete",
      "name": "urn:csci:microsoft:azureClusteredCacheForRedis:reboot/1.0",
      "parameters": [
        {
          "key": "RebootType",
          "value": "AllNodes"
        },
        {
          "key": "ShardId",
          "value": "0"
        }
      ],
      "selectorid": "myResources"
    }
  ]
}
```

### <a name="limitations"></a>制限事項

* 再起動の障害では、停止イベントをより適切にシミュレートするために強制的な再起動が実行されます。つまり、データ損失が発生する可能性があります。
* 再起動の障害は、**離散的な** 障害の種類です。 連続した障害とは異なり、これは 1 回限りのアクションであるため、期間はありません。
