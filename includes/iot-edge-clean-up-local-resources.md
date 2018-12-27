---
title: インクルード ファイル
description: インクルード ファイル
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: cdb60ffb3ba3c31c336c8b74c46621792c707f74
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "40046326"
---
### <a name="delete-local-resources"></a>ローカル リソースの削除

IoT Edge ランタイムと関連リソースをデバイスから削除するには、お使いのデバイスのオペレーティング システムに合った適切なコマンドを使用します。 

#### <a name="windows"></a>Windows

IoT Edge ランタイムをアンインストールします。

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

IoT Edge ランタイムが削除されると、作成したコンテナーは停止されますが、デバイスには残っています。 すべてのコンテナーを表示します。

   ```powershell
   docker ps -a
   ```

デバイス上に作成されたランタイム コンテナーを削除します。

   ```powershell
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

他にも `docker ps` の出力結果にリストされたコンテナーがあれば、そのコンテナー名を参照して削除します。 

#### <a name="linux"></a>Linux

IoT Edge ランタイムを削除します。

   ```bash
   sudo apt-get remove --purge iotedge
   ```

IoT Edge ランタイムが削除されると、作成したコンテナーは停止されますが、デバイスには残っています。 すべてのコンテナーを表示します。

   ```bash
   sudo docker ps -a
   ```

デバイス上に作成されたランタイム コンテナーを削除します。

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

他にも `docker ps` の出力結果にリストされたコンテナーがあれば、そのコンテナー名を参照して削除します。 

コンテナー ランタイムを削除します。

   ```bash
   sudo apt-get remove --purge moby
   ```