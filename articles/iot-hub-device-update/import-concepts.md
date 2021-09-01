---
title: Device Update for IoT Hub へのインポートについて理解する | Microsoft Docs
description: Device Update for IoT Hub への新しい更新プログラムのインポートに関する主な概念。
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/10/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39a1acb596f4d56915aabfa0a64fc69d6e55aec2
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113769109"
---
# <a name="importing-updates-into-device-update-for-iot-hub"></a>Device Update for IoT Hub への更新プログラムのインポート
Device Update for IoT Hub からデバイスに更新プログラムを展開するには、まず、その更新プログラムをデバイス更新サービスに "_インポート_" する必要があります。 ここでは、更新プログラムをインポートするときに理解しておくべき重要な概念の概要を示します。

## <a name="limits-on-importing-updates"></a>更新プログラムのインポートの制限
Device Update for IoT Hub インスタンスごとに、次の制限が適用されます。

* ADU インスタンスあたり 5 プロバイダー
* プロバイダーあたり 5 つの名前
* 名前あたり 25 バージョン

また、累積的に (すべてのプロバイダーと名前全体で) 100 個の更新プログラム バージョンの制限もあります。

更新プログラムの送信における個々のファイルの最大許容サイズは 800 MB です。 1 回の更新プログラムの送信ですべてのファイルの _合計_ が 800 MB を超えないようにする必要もあります。

## <a name="import-manifest"></a>インポート マニフェスト

インポート マニフェストは、インポートする更新プログラムに関する重要な情報を定義する JSON ファイルです。 インポート処理の一環として、インポート マニフェストと関連付けられている更新プログラム ファイル (ファームウェア更新パッケージなど) の両方を送信します。 インポート マニフェストで定義されているメタデータは、更新プログラムの取り込みに使用されます。 一部のメタデータは展開時にも使用されます。たとえば、更新プログラムが正しくインストールされたかどうかを検証するときなどです。

インポート マニフェストには、Device Update for IoT Hub の重要な概念を表すいくつかの項目が含まれています。 これらの概念を以下で説明します。

### <a name="update-identity-update-id"></a>更新プログラム ID

更新プログラム ID は、更新プログラムの一意の識別子を表します。 インポートされる更新プログラムに関する重要なプロパティが定義されます。 更新プログラム ID は、次の 3 つの部分で構成されます。
* プロバイダー: 更新プログラムを作成または直接担当するエンティティです。 多くの場合、会社名になります。
* 名前: 更新プログラムのクラスの識別子です。 クラスには、任意のものを選択できます。 多くの場合、デバイス名またはモデル名になります。
* バージョン: これは、同じプロバイダーと名前を持つ他の更新プログラムとこの更新プログラムを区別するためのバージョン番号です。 このバージョンは、Device Update for IoT Hub サービスで使用されますが、デバイス上の個々のソフトウェア コンポーネントのバージョンと一致しない場合があります。 

### <a name="compatibility"></a>互換性

更新プログラムの展開を簡素化するために、Device Update for IoT Hub では、インポート マニフェストで定義されている、更新プログラムの互換性プロパティと、対応するデバイス プロパティが比較されます。 プロパティが一致する更新プログラムのみが返され、展開に使用することができます。

### <a name="installedcriteria"></a>InstalledCriteria

InstalledCriteria は、デバイス上の更新エージェントによって使用され、更新プログラムが正常にインストールされたかどうかが確認されます。


## <a name="next-steps"></a>次のステップ

準備ができたら、インポート プロセスの詳しい手順が示されている[インポート攻略ガイド](./import-update.md)に進んでください。


