---
title: Azure Storage Explorer の論理的な削除ガイド | Microsoft Docs
description: Azure Storage Explorer での論理的な削除
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: chuye
ms.openlocfilehash: 0d097e769bfea8ff9c65921e8f0d851372187a02
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564906"
---
# <a name="azure-storage-explorer-soft-delete-guide"></a>Azure Storage Explorer の論理的な削除ガイド

論理的な削除を使用すると、重要なデータを誤って削除した場合の影響を軽減することができます。 このガイドは、この機能を Storage Explorer で利用する方法を理解するのに役立ちます。 次に進む前に、[BLOB の論理的な削除](../blobs/soft-delete-blob-overview.md)に関する記事をよく読んでおくことをお勧めします。

## <a name="configuring-delete-retention-policy"></a>削除保持ポリシーの構成

Storage Explorer で各ストレージ アカウントの削除保持ポリシーを構成できます。 ストレージ アカウントの下にある [BLOB コンテナー] ノードのコンテキスト メニューを開き、 **[論理的な削除ポリシーの構成...]** を選択します。

新しいポリシーを設定すると、有効になるまで最大 30 秒かかる場合があります。 新しいポリシーが有効になるのを待たずにデータを削除すると、予期しない動作が発生する可能性があります。 Storage Explorer では、正常に構成されたポリシーをアクティビティ ログに報告する前に 30 秒待機します。

## <a name="soft-delete-with-hierarchical-namespace-enabled"></a>階層型名前空間を有効にした論理的な削除

論理的な削除機能には、階層型名前空間 (HNS) に対応した BLOB コンテナーと対応していない BLOB コンテナーの間で根本的な違いがあります。

HNS に対応した BLOB コンテナーには、実際のディレクトリがあります。 これらのディレクトリも論理的に削除できます。 実際のディレクトリが論理的に削除された場合、その下のすべてのアクティブな BLOB またはディレクトリにアクセスできなくなります。 これらの BLOB とディレクトリは、ディレクトリの削除が取り消されると復元され、ディレクトリの有効期限が切れると破棄されます。 既に論理的に削除されているその下の BLOB またはディレクトリは、そのまま保持されます。

HNS に対応していない BLOB コンテナーでは、論理的に削除された BLOB と、同じ名前のアクティブな BLOB を共存させることができません。 論理的に削除された BLOB と同じ名前の BLOB をアップロードすると、論理的に削除された BLOB が新しい BLOB のスナップショットになります。 HNS に対応した BLOB コンテナーで同じことを行うと、論理的に削除された BLOB が新しい BLOB と共存します。 HNS に対応した BLOB コンテナーでは、同じ名前を持つ論理的に削除された BLOB が複数共存することも可能です。

HNS に対応した BLOB コンテナー内の論理的に削除された各 BLOB またはディレクトリには、`DeletionID` プロパティがあります。 このプロパティによって、同じ名前の BLOB またはディレクトリが区別されます。 HNS に対応していない BLOB コンテナー内の論理的に削除された BLOB には、`DeletionID` プロパティがありません。

HNS に対応していない BLOB コンテナーでは、"BLOB のバージョン管理" もサポートされています。 BLOB のバージョン管理が有効になっている場合、削除などの特定の操作の動作が変更されます。 詳細については、「[Azure Storage Explorer BLOB バージョン管理ガイド](./storage-explorer-blob-versioning.md)」を参照してください。

## <a name="view-soft-deleted-blobs"></a>論理的に削除された BLOB を表示する

BLOB エクスプローラーでは、論理的に削除された BLOB は特定のビュー コンテキストの下に表示されます。

HNS に対応していない BLOB コンテナーの場合、論理的に削除された BLOB を表示するには、"アクティブな BLOB と論理的に削除された BLOB" または "すべての BLOB と現在のバージョンがない BLOB" のビュー コンテキストを選択します。

HNS に対応した BLOB コンテナーの場合、論理的に削除された BLOB を表示するには、"アクティブな BLOB と論理的に削除された BLOB" または "削除済みのみ" のビュー コンテキストを選択します。

## <a name="delete-blobs"></a>BLOB を削除する

BLOB またはディレクトリを削除するときに、Storage Explorer ではアカウントの現在の削除保持ポリシーを確認します。 確認ダイアログでは、削除操作を続行するとどうなるかが通知されます。 論理的な削除が無効になっている場合、 **[論理削除の有効化]** ボタンを選択して、確認ダイアログから有効にすることができます。

> [!WARNING]
> Storage Explorer では、保存されたばかりの新しい削除保持ポリシーが表示されない場合があります。 データを削除する前に、新しいポリシーが有効になるまで少なくとも 30 秒待機することを強くお勧めします。

## <a name="undelete-blobs"></a>BLOB の削除を取り消す

Storage Explorer では、論理的に削除された BLOB の削除を再帰的に、およびバッチで取り消すことができます。

BLOB の削除を取り消す場合、削除を取り消す必要がある論理的に削除された BLOB を選択し、ツール バーまたはコンテキスト メニューから **[削除の取り消し]→[選択した項目の削除取り消し]** を使用します。

ディレクトリの下で BLOB の削除を再帰的に取り消すこともできます。 選択項目にアクティブなディレクトリが含まれている場合、Storage Explorer では、その中の論理的に削除された BLOB またはディレクトリの削除がすべて取り消されます。

HNS に対応した BLOB コンテナーでは、同じ名前のアクティブな BLOB が既に存在する場合、BLOB の削除の取り消しは失敗します。

> [!NOTE]
> 論理的に削除されたスナップショットは、基本の BLOB の削除を取り消すことによってのみ、削除を取り消すことができます。 個々のスナップショットの削除を取り消す方法はありません。

## <a name="undelete-blobs-by-date-range"></a>日付範囲で BLOB の削除を取り消す

Storage Explorer では、BLOB の削除を削除時刻に基づいて取り消すこともできます。

日付範囲で BLOB の削除を取り消す場合、削除を取り消す必要がある論理的に削除された BLOB を選択し、ツール バーまたはコンテキスト メニューから **[削除の取り消し]→[日付別の削除取り消し...]** を使用します。

**[日付別の削除取り消し...]** は、削除時刻が指定した範囲外の BLOB またはディレクトリをフィルターで除外することを除き、 **[選択した項目の削除取り消し]** とまったく同じように機能します。

## <a name="see-also"></a>参照

- [Azure Storage Explorer BLOB バージョン管理ガイド](./storage-explorer-blob-versioning.md)
- [BLOB の論理的な削除](../blobs/soft-delete-blob-overview.md)
