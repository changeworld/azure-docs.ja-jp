---
title: Device Update for Azure IoT Hub の APT マニフェストについて理解する | Microsoft Docs
description: Device Update for IoT Hub が、APT マニフェストを使用してパッケージ ベースの更新を行う方法について説明します。
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0b68b78499aa3bf0d84d8bd0fa5ab55d1f969113
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678651"
---
# <a name="device-update-apt-manifest"></a>デバイス更新プログラムの APT マニフェスト

APT マニフェストは、APT 更新ハンドラーに必要な更新の詳細を記述した JSON ファイルです。 このファイルは、他の更新プログラムと同様に、Device Update for IoT Hub にインポートすることができます。

更新プログラムをデバイス更新プログラムにインポートする方法の詳細については、[こちら](import-update.md)を参照してください。

## <a name="overview"></a>概要

APT マニフェストが更新プログラムとしてデバイス更新エージェントに配信されると、エージェントはマニフェストを処理し、必要な操作を実行します。 これらの操作には、APT マニフェスト ファイルに指定されたパッケージおよびその依存関係のダウンロードとインストールが含まれます。

デバイス更新プログラムは、APT UpdateType と APT 更新ハンドラーをサポートしています。 このサポートによりデバイス更新エージェントは、インストールされている Debian パッケージを評価し、必要なパッケージを更新することができます。 

## <a name="schema"></a>スキーマ

APT マニフェスト ファイルは、バージョン管理されたスキーマを含む JSON ファイルです。

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

例:

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>name

この APT マニフェストの名前。 これには、シナリオにとって意味のある任意の名前または ID を指定できます。 たとえば、「 `contoso-iot-edge` 」のように入力します。

### <a name="version"></a>version

この APT マニフェストのバージョン番号。 たとえば、「 `1.0.0.0` 」のように入力します。


### <a name="packages"></a>packages

パッケージ固有のプロパティを含むオブジェクトの一覧です。

#### <a name="name"></a>name

パッケージの名前または ID。 たとえば、「 `iotedge` 」のように入力します。

#### <a name="version"></a>version

パッケージの、必要なバージョン基準。 たとえば、「 `1.0.8-2` 」のように入力します。

現在、サポートされているのは、正確なバージョン番号のみです。 バージョン番号は、[epoch:]upstream_version[-debian_revision] という形式の、必要な Debian パッケージバージョンです。

**epoch** は符号なしの int です。

**upstream_version** には、"."、"+"、"-"、"~" などの英数字と文字を含めることができます。 先頭は必ず数字にします。
> [!NOTE]
> '1.0.8' は '1.0.8-0' と同等です

たとえば、 **`"name":"iotedge" and "version":"1.0.8-2"`** は、`apt-get install iotedge=1.0.8-2` コマンドを使用してパッケージをインストールすることと同じです

> [!NOTE]
> バージョン値には等号が含まれません

バージョンを省略した場合は、指定したパッケージの使用可能な最新バージョンがインストールされます。

Debian パッケージのバージョン管理のしくみについては、[こちら](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version)を参照してください。

> [!NOTE]
> インストールする依存パッケージが自動的に解決されると、APT パッケージ マネージャーは、パッケージによって指定されたバージョン管理の要件を無視します。 依存パッケージの明示的なバージョンを指定しない限り、特定のバージョンに対する厳格な要件 (=) がパッケージ自体により指定されている場合でも、最新のバージョンが使用されます。 このように自動で解決されると、条件が満たされない依存関係に関するエラーが発生する可能性があります。 [詳細情報](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

特定のバージョンの Azure IoT Edge セキュリティデーモンを更新している場合は、必要なバージョンの `iotedge` パッケージと、それに依存する `libiothsm-std` パッケージを APT マニフェストに含める必要があります。
[詳細情報](https://docs.microsoft.com/azure/iot-edge/how-to-update-iot-edge#update-the-security-daemon)

> [!NOTE]
> APT マニフェストを使用して、デバイス更新エージェントとその依存関係を更新することができます。 他のパッケージの場合と同様に、APT マニフェストでデバイス更新エージェント名と必要なバージョンを一覧表示します。 その後、この APT マニフェストを、Device Update for IoT Hub のパイプラインを使用してインポートし、デプロイすることができます。 

## <a name="removing-packages"></a>パッケージの削除

APT マニフェストを使用して、インストールされているパッケージをデバイスから削除することもできます。 1 つの APT マニフェストを使用して、複数のパッケージの削除、追加、更新を行うことができます。 パッケージを削除するには、パッケージ名の後にマイナス記号 "-" を追加します。 削除しようとしているパッケージのバージョン番号は含めないでください。 APT マニフェストを使用してパッケージを削除しても、その依存関係と構成は削除されません。

例:

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
この APT マニフェストにより、デプロイ先のデバイスからパッケージ "foo" が削除されます。 

## <a name="recommended-value-for-installed-criteria"></a>インストール条件に推奨される値

APT マニフェストのインストール条件は `<name>-<version>` です。ここで、`<name>` は APT マニフェストの名前、`<version>` は APT マニフェストのバージョンです。 たとえば、「 `contoso-iot-edge-1.0.0.0` 」のように入力します。 

## <a name="guidelines-on-creating-an-apt-manifest"></a>APT マニフェストの作成に関するガイドライン

APT マニフェストの作成時には、注意すべきガイドラインがいくつかあります。

- APT マニフェストは必ず、整形式の json ファイルにします
- 各 APT マニフェストのバージョンは一意であることが必要です。 実際のシナリオで合理的であり、簡単に理解できるように、標準化された方法を使用して、APT マニフェストのバージョンをインクリメントするようにします
- 個々のパッケージの必要な状態に関しては、デバイスにインストールするパッケージの正確な名前とバージョンを指定します。 パッケージのソースとして使用するパッケージリポジトリと照合して、常に値を検証します
- APT マニフェスト内のパッケージが、インストールまたは削除される順序で一覧表示されていることを確認します
- 必要な結果が得られるように、必ずテストデバイスでパッケージのインストールを検証します
- パッケージの特定のバージョン (`iotedge 1.0.9-1` など) をインストールする場合のベスト プラクティスは、インストールする依存パッケージの明示的なバージョン (`libiothsm 1.0.9-1` など) も APT マニフェストに含めることです
- 必須ではありませんが、APT マニフェストは、デバイスが不明な状態にならないように累積的に作成してください。 累積的に更新すると、インストール中にエラーが発生したか、オフラインになったためにデバイスが APT 更新プログラムのデプロイをスキップした場合でも、デバイスには、使用するすべてのパッケージの必要なバージョンが含まれます

次に例を示します。

**基本 APT マニフェスト**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**不適切な更新**

この更新プログラムには、bar パッケージが含まれていますが、foo パッケージは含まれていません。

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**適切な更新**

この更新プログラムには、foo パッケージが含まれており、bar パッケージも含まれています。

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [新しい更新プログラムをインポートする](import-update.md)

