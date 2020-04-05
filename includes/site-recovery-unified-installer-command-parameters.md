---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9d9790c9b3dbe3b130be999dd76092ae64f7b52c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "67180881"
---
|パラメーター名| 種類 | 説明| 指定できる値|
|-|-|-|-|
| /ServerMode|Mandatory|構成サーバーとプロセス サーバーの両方をインストールするか、プロセス サーバーだけをインストールするかを指定します。|CS<br>PS|
|/InstallLocation|Mandatory|コンポーネントがインストールされているフォルダー。| コンピューター上の任意のフォルダー|
|/MySQLCredsFilePath|Mandatory|MySQL サーバーの資格情報が保存されているファイルのパス。|ファイルは下記の形式である必要があります。|
|/VaultCredsFilePath|Mandatory|コンテナーの資格情報ファイルのパス。|有効なファイル パス|
|/EnvType|Mandatory|保護する環境の種類 |VMware<br>NonVMware|
|/PSIP|Mandatory|レプリケーション データの転送に使用する NIC の IP アドレス。| 任意の有効な IP アドレス|
|/CSIP|Mandatory|構成サーバーがリッスンする NIC の IP アドレス。| 任意の有効な IP アドレス|
|/PassphraseFilePath|Mandatory|パスフレーズ ファイルの場所の完全パス。|有効なファイル パス|
|/BypassProxy|省略可能|構成サーバーがプロキシを介さずに Azure に接続することを指定します。|この値は Venu から取得します。|
|/ProxySettingsFilePath|省略可能|プロキシ設定 (認証を必要とする既定のプロキシ、またはカスタム プロキシ)。|ファイルは下記の形式である必要があります。|
|DataTransferSecurePort|省略可能|レプリケーション データに使用する PSIP のポート番号。| 有効なポート番号 (既定値は 9433)|
|/SkipSpaceCheck|省略可能|キャッシュ ディスクの領域チェックをスキップします。| |
|/AcceptThirdpartyEULA|Mandatory|サード パーティのライセンス条項への同意を意味するフラグ。| |
|/ShowThirdpartyEULA|省略可能|サード パーティのライセンス条項を表示します。 入力として提供された場合、他のすべてのパラメーターが無視されます。| |
