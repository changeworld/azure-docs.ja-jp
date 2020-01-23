---
title: Azure でサポートされているメインフレーム ワークロード | Microsoft Docs
description: メインフレーム エミュレーターや Microsoft パートナーの他のサービスを使用して、Microsoft Azure を使用する IBM Z ベースのシステムなどのメインフレーム ワークロードをリホストします。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: cf02ff7078fe5d33f220c0abc2d941d483e5c125
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293506"
---
# <a name="mainframe-workloads-supported-on-azure"></a>Azure でサポートされているメインフレーム ワークロード

ここで紹介するソリューションは、メインフレーム ワークロードを Microsoft Azure に移行する際に役立ちます。 一部のワークロードは、比較的簡単に移行できます。 レガシ システム ソフトウェアに依存する他のワークロードはリホストできます。 

メインフレームのエミュレーションとサービスの詳細については、[Azure メインフレーム移行センター](https://azure.microsoft.com/migration/mainframe/)を参照してください。

## <a name="migrate-mainframe-closer-to-azure"></a>Azure の近くへのメインフレームの移行

- [Micro Focus Visual COBOL](https://www.microfocus.com/products/visual-cobol/) 開発および統合ツール。
- メインフレームの PL/I 構文、データ型、動作をサポートする、.NET プラットフォーム向け [Micro Focus PL/I](https://www.microfocus.com/campaign/download/pli-modernization/) レガシ コンパイラ。
- [Micro Focus Enterprise Server](https://www.microfocus.com/products/enterprise-suite/enterprise-server/) メインフレーム統合プラットフォーム。
- [Fujitsu NetCOBOL](https://www.fujitsu.com/global/products/software/developer-tool/netcobol/) 開発および統合ツール。
- [NTT Data Enterprise COBOL](https://us.nttdata.com/en/digital/application-development-and-modernization) 開発および統合ツール。
- メインフレームの PL/I 構文、データ型、動作をサポートする、.NET プラットフォーム向け [NTT Open PL/I](https://us.nttdata.com/en/digital/application-development-and-modernization) レガシ コンパイラ。
- [Raincode COBOL コンパイラ](https://www.raincode.com/products/cobol/)開発および統合ツール。
- メインフレームの PL/I 構文、データ型、動作をサポートする、.NET プラットフォーム向け [Raincode PL/I コンパイラ](https://www.raincode.com/products/pli/)。
- メインフレームのアセンブラー 370 および HLASM 構文に対応する [Raincode ASM370 コンパイラ](https://www.raincode.com/technical-landscape/asm370/)。
- .NET Framework 向け [ASNA Visual RPG for .NET](https://asna.com/us/products/visual-rpg) コンパイラ (Visual Studio プラグインを使用)。
- [Modern Systems CTU (COBOL-To-Universal)](https://modernsystems.com/automatic-cobol-to-java-conversion/) 開発および統合ツール。

## <a name="deploy-an-emulation-environment-for-online-and-batch-processing"></a>オンラインおよびバッチ処理用のエミュレーション環境のデプロイ

- [Micro Focus Enterprise Server](https://www.microfocus.com/products/enterprise-suite/enterprise-server/) メインフレーム統合プラットフォーム。
- [NTT DATA メインフレーム リホスティング開発環境](https://us.nttdata.com/en/-/media/assets/white-paper/apps-mainframe-re-hosting-development-environment-whitepaper.pdf) (ネイティブ トランザクション処理環境)。
- JCL トランザクション機能を含む、[NTT DATA バッチ処理環境](https://us.nttdata.com/en/-/media/assets/white-paper/apps-mainframe-re-hosting-development-environment-whitepaper.pdf) (BPE)。
- .NET および Azure プラットフォーム向け [Raincode CICS](https://www.raincode.com/technical-landscape/cics/) エミュレーター。
- [Raincode JCL](https://www.raincode.com/products/jcl/) プラグ互換 JCL インタープリター。

## <a name="code-conversion"></a>コード変換

- [Asysco](https://www.asysco.com/azure-cloud/) ソース コード変換。
- [LzLabs Software Defined Mainframe](https://www.lzlabs.com/) マネージド ソフトウェア コンテナー。メインフレーム アプリケーションを Linux コンピューターまたはプライベート/パブリック/ハイブリッド クラウド環境に移行します。
- [Blu Age](https://www.bluage.com/) ツール。レガシ ビジネス アプリケーションおよびデータベースをデジタル化します。
- [Heirloom Computing](https://www.heirloomcomputing.com/tag/convert-cobol-to-java/) サービス。メインフレームの COBOL、CICS、VSAM を Java に変換します。

## <a name="modernization-services"></a>最新化サービス

Microsoft は、大規模な組織によるソリューションの設計、構築、管理を支援するグローバル システム インテグレーター (GSI) と提携しています。 

- [Azure メインフレーム移行センター](https://azure.microsoft.com/migration/mainframe/)
