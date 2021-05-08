---
title: Microsoft Azure StorSimple Virtual Array Update 1.3 のリリース ノート | Microsoft Docs
description: Update 1.3 を実行する Azure StorSimple Virtual Array に関する未解決の重大な問題と解決方法について説明します。
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: b2c2af4267140370c2aa9421a67cb3a6f413165f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657554"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>StorSimple Virtual Array Update 1.3 のリリース ノート

このリリース ノートには、Microsoft Azure StorSimple Virtual Array 更新プログラムのリリースに関する未解決の重大な問題と解決済みの問題が示されています。

リリース ノートは、随時更新されます。 回避策を必要とする重大な問題が見つかった場合は、追加されます。 StorSimple Virtual Array をデプロイする前に、リリース ノートに含まれる情報を十分に確認してください。

Update 1.3 はソフトウェア バージョン 10.0.10319.0 に対応しています。

> [!IMPORTANT]
> - Update 1.3 は重要な更新プログラムです。 できるだけ早くインストールすることを強くお勧めします。
> - Update 1.3 は、Update 1.2 を実行しているデバイスにのみインストールできます。
> - 更新プログラムは中断を伴い、デバイスを再起動します。 I/O が進行中である場合は、デバイスにダウンタイムが発生します。 この更新プログラムを適用するために使用されるパッケージに関する詳細な手順については、「[Update 1.3 のダウンロード](#download-update-13)」に進んでください。

## <a name="whats-new-in-update-13"></a>Update 1.3 の新機能

この更新プログラムには、次の機能強化が含まれています。KB4540725

- トランスポート層セキュリティ (TLS) 1.2 は必須の更新プログラムであり、インストールする必要があります。 今回のリリースから、TLS 1.2 はすべての Azure portal 通信の標準プロトコルになります。
  
   次の警告が表示された場合は、続行する前にデバイスでソフトウェアを更新する必要があります。

   1 つ以上の StorSimple デバイスで、古いソフトウェア バージョンが実行されています。 TLS 1.2 の最新の利用可能な更新プログラムは、必須の更新プログラムであり、これらのデバイスに直ちにインストールする必要があります。 TLS 1.2 はすべての Azure portal 通信に使用され、この更新プログラムがないと、デバイスは StorSimple サービスと通信できません。

- ガベージ コレクションのバグ修正によって、デバイスとストレージ アカウントが 2 つの離れたリージョンにあるときに、ガベージ コレクション サイクルのパフォーマンスが向上します。
- BLOB タイムアウトによるバックアップ エラーを修正します。
- 更新された OS/.NET Framework のセキュリティ パッチ。
  - [KB4540725](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86):2020 年 3 月の SSU (サービス スタック更新プログラム)
  - [KB4565541](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01):2020 年 7 月のロールアップ
  - [KB4565622](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00):2020 年 7 月の .NET Framework 更新プログラム

## <a name="download-update-13"></a>Update 1.3 のダウンロード

この更新プログラムをダウンロードするには、[Microsoft Update カタログ](https://www.catalog.update.microsoft.com/Home.aspx)のサーバーへ移動して、KB4575898 パッケージをダウンロードします。 このパッケージには、次のパッケージが含まれています。

- 2012 R2 から 2020 年 3 月までの累積的な Windows Update が含まれる **KB4540725**。 このロールアップに含まれる内容の詳細については、[3 月のマンスリー セキュリティ ロールアップ](https://support.microsoft.com/help/4540725)に関するページをご覧ください。
- 2012 R2 から 2020 年 7 月までの累積的な Windows Update が含まれる **KB4565541**。 このロールアップに含まれる内容の詳細については、[2 月のマンスリー セキュリティ ロールアップ](https://support.microsoft.com/help/4565541)に関するページをご覧ください。
- 2020 年 7 月までの累積的な .NET Framework の更新プログラムが含まれる **KB4565622**。 このロールアップに含まれる内容の詳細については、[2 月のマンスリー セキュリティ ロールアップ](https://support.microsoft.com/help/4565622)に関するページをご覧ください。
- デバイス ソフトウェアの更新プログラムが含まれる **KB3011067**。

KB4575898 をダウンロードして、[ローカル Web UI を使用して更新プログラムを適用する](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)ための手順に従います。

## <a name="known-issues-in-update-13"></a>Update 1.3 の既知の問題
Update 1.3 では、新しくリリース ノートに記載された問題はありませんでした。 リリース ノートに記載されている問題はすべて、以前のリリースからの持ち越しです。 以前のリリースから含まれている既知の問題の概要を確認するには、[Update 1.2 での既知の問題](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12)に関する記事に進んでください。

## <a name="next-steps"></a>次のステップ
KB4575898 をダウンロードして、[ローカル Web UI を使用して更新プログラムを適用](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui)します。

## <a name="references"></a>リファレンス
以前のリリース ノートをお探しですか。 参照先:

- [StorSimple Virtual Array Update 1.2 のリリース ノート](./storsimple-virtual-array-update-12-release-notes.md)
- [StorSimple Virtual Array Update 1.0 のリリース ノート](./storsimple-virtual-array-update-1-release-notes.md)
- [StorSimple Virtual Array Update 0.6 のリリース ノート](./storsimple-virtual-array-update-06-release-notes.md)
- [StorSimple Virtual Array Update 0.5 のリリース ノート](./storsimple-virtual-array-update-05-release-notes.md)
- [StorSimple Virtual Array Update 0.4 のリリース ノート](./storsimple-virtual-array-update-04-release-notes.md)
- [StorSimple Virtual Array Update 0.3 のリリース ノート](./storsimple-ova-update-03-release-notes.md)
- [StorSimple Virtual Array Update 0.1 および 0.2 のリリース ノート](./storsimple-ova-update-01-release-notes.md)
- [StorSimple Virtual Array 一般公開リリース ノート](./storsimple-virtual-array-update-06-release-notes.md)