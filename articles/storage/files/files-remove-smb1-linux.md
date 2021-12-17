---
title: Linux の SMB 1 を解除して Azure とオンプレミス環境のセキュリティを確保する | Microsoft Docs
description: Azure Files は SMB 3.x と SMB 2.1 をサポートします。SMB 1 など、安全でないレガシ バージョンの SMB はサポートされません。 Azure ファイル共有に接続する前に、SMB 1 など以前のバージョンの SMB は無効にすることをお勧めします。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8f0e75ebf45839eb75f22a8fb46e76bdebec4688
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2021
ms.locfileid: "112124785"
---
# <a name="remove-smb-1-on-linux"></a>Linux の SMB 1 を削除する
SMB での通信に使用されるポート (445) は、多くの組織とインターネット サービス プロバイダー (ISP) でブロックされています。 この慣例の由来は、今や非推奨の古いバージョンの SMB プロトコルに関するセキュリティ ガイダンスです。 SMB 3.x はインターネットでも安全に使用できるプロトコルですが、以前のバージョンの SMB、特に SMB 1 はそうではありません。 SMB 1 は CIFS (Common Internet File System) とも呼ばれ、多くの Linux ディストリビューションに備わっています。 

SMB 1 は、旧式で効率が悪く、またセキュリティに不安があるプロトコルです。 さいわいなことに、Azure Files は SMB 1 をサポートしておらず、Linux カーネル バージョン 4.18 以降では、Linux で SMB 1 を無効にすることができます。 運用環境で SMB ファイル共有を使用する前に、Linux クライアント上で SMB 1 を無効にすることを常に[強くお勧めします](https://aka.ms/stopusingsmb1)。

## <a name="linux-distribution-status"></a>Linux ディストリビューション ステータス
Linux カーネル 4.18 以降、レガシの理由から `cifs` と呼ばれる SMB カーネル モジュールでは、`disable_legacy_dialects` と呼ばれる新しいモジュール パラメーター (各種外部ドキュメントでは "*parm*" と呼ばれることが多い) を公開しています。 Linux カーネル 4.18 で導入されましたが、一部のベンダーでは、サポート対象の旧カーネルにこの変更を移植しています。 便宜のため、次の表では、よく知られた Linux ディストリビューションでこのモジュール パラメーターを使用できるかどうかについて詳しく示しています。

| Distribution | SMB 1 を無効にできる |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | No |
| Ubuntu 18.04 | Yes |
| Ubuntu 19.04+ | Yes |
| Debian 8-9 | No |
| Debian 10+ | Yes |
| Fedora 29+ | Yes |
| CentOS 7 | No | 
| CentOS 8+ | Yes |
| Red Hat Enterprise Linux 6.x-7.x | No |
| Red Hat Enterprise Linux 8+ | Yes |
| openSUSE Leap 15.0 | No |
| openSUSE Leap 15.1+ | Yes |
| openSUSE Tumbleweed | Yes |
| SUSE Linux Enterprise 11.x-12.x | No |
| SUSE Linux Enterprise 15 | No |
| SUSE Linux Enterprise 15.1 | No |

次のコマンドを使用して、Linux ディストリビューションで `disable_legacy_dialects` モジュール パラメーターがサポートされているかどうかを確認できます。

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

このコマンドを実行すると、次のメッセージが出力されます。

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

## <a name="remove-smb-1"></a>SMB 1 を削除する
SMB 1 を無効にする前に、SMB モジュールがご利用のシステムに現在読み込まれていないことを確認してください (これは、SMB 共有をマウントしている場合に自動的に発生します)。 これを行うには、次のコマンドを使用します。SMB が読み込まれていない場合は、何も出力されません。

```bash
lsmod | grep cifs
```

モジュールをアンロードするには、最初にすべての SMB 共有のマウントを解除します (前述のように、`umount` コマンドを使用します)。 次のコマンドを使用して、システム上にマウントされているすべての SMB 共有を特定できます。

```bash
mount | grep cifs
```

すべての SMB ファイル共有のマウントを解除したら、モジュールをアンロードしても安全です。 そのためには、 `modprobe` コマンドを使用します。

```bash
sudo modprobe -r cifs
```

`modprobe` コマンドを使用して、SMB 1 をアンロードしてモジュールを手動で読み込むことができます。

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

最後に、`/sys/module/cifs/parameters` の読み込み済みパラメーターを参照すると、パラメーターを使用して SMB モジュールが読み込まれていることを確認できます。

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Ubuntu および Debian ベースのディストリビューションで SMB 1 を永続的に無効にするには、設定を使用して `/etc/modprobe.d/local.conf` という新しいファイルを作成する必要があります (他のモジュールのカスタム オプションがまだない場合)。 これを行うには、次のコマンドを使います。

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

SMB モジュールを読み込むと、これが動作していることを確認できます。

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>次のステップ
Azure Files の詳細については、次のリンクをご覧ください。

- [Azure Files のデプロイの計画](storage-files-planning.md)
- [Linux で Azure Files を使用する](storage-how-to-use-files-linux.md)
- [トラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)