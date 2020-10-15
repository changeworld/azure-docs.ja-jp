---
title: Azure IoT Edge モジュールとしての信頼されたアプリケーション
description: 信頼されたアプリケーションを記述し、コンフィデンシャル コンピューティングのために IoT Edge モジュールとしてそれらを展開するには、Open Enclave SDK と API を使用します
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: kgremban
ms.openlocfilehash: d81cc6c94c04c683362fd12cd6777c304a4b0a84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361978"
---
# <a name="confidential-computing-at-the-edge"></a>エッジでのコンフィデンシャル コンピューティング

Azure IoT Edge では、デバイス上のセキュリティで保護されたエンクレーブ内で実行される信頼されたアプリケーションがサポートされています。 暗号化により転送中または保存時のデータのセキュリティが提供されるのに対し、エンクレーブでは使用中のデータとワークロードのセキュリティが提供されます。 IoT Edge では、信頼されたアプリケーションの開発に対する標準として Open Enclave がサポートされています。

多くの場合、モノのインターネット (IoT) デバイスはセキュリティで保護されたプライベートな施設内ではなく外の世界に存在するため、セキュリティは常に IoT にとっての重要な焦点です。 この露出により、悪意のあるアクターが物理的にアクセスできるため、デバイスは改ざんや偽造のリスクを負うことになります。 IoT Edge デバイスの場合、機密性の高いワークロードのエッジでの実行に対応するため、信頼性と整合性がさらに必要になります。 一般的なセンサーやアクチュエータとは異なり、これらのインテリジェントなエッジ デバイスでは、以前であれば保護されたクラウドやオンプレミス環境でのみ実行されていた機密性の高いワークロードが、公開される可能性があります。

[IoT Edge セキュリティ マネージャー](iot-edge-security-manager.md)により、機密性の高いコンピューティングの課題の 1 つの部分が対処されます。 セキュリティ マネージャーでは、ハードウェア セキュリティ モジュール (HSM) を使用して、IoT Edge デバイスの ID ワークロードと進行中のプロセスが保護されます。

機密性の高いコンピューティングのもう 1 つの部分は、エッジで使用されるデータを保護することです。 "*信頼できる実行環境*" (TEE) は、プロセッサ上のセキュリティ保護されて分離された環境であり、"*エンクレーブ*" と呼ばれることもあります。 "*信頼されたアプリケーション*" は、エンクレーブ内で実行されるアプリケーションです。 エンクレーブの性質により、信頼されたアプリケーションは、メイン プロセッサまたは TEE で実行されている他のアプリから保護されます。

## <a name="trusted-applications-on-iot-edge"></a>IoT Edge 上の信頼されたアプリケーション

信頼されたアプリケーションは、転送中および保存時には暗号化されており、信頼できる実行環境の内部で実行される場合にのみ暗号化を解除されます。 この標準は、IoT Edge モジュールとして展開される信頼されたアプリケーションに対しても当てはまります。

開発者は、信頼されたアプリケーションを作成し、IoT Edge モジュールとしてそれをパッケージ化します。 アプリケーションは、コンテナー レジストリにプッシュされる前に暗号化されます。 アプリケーションは、モジュールが IoT Edge デバイスで開始されるまで、IoT Edge 展開プロセス全体を通して暗号化されたままになります。 信頼されたアプリケーションがデバイスの TEE 内に配置された後、暗号化が解除され、実行を開始できるようになります。

![図 - 信頼されたアプリケーションは、セキュリティで保護されたエンクレーブに展開されるまで、IoT Edge モジュール内で暗号化されている](./media/deploy-trusted-applications/trusted-applications-encrypted.png)

IoT Edge 上の信頼されたアプリケーションは、[Azure コンフィデンシャル コンピューティング](../confidential-computing/overview.md)の論理的な拡張機能です。 クラウドのセキュリティで保護されたエンクレーブ内で実行されるワークロードを、エッジのセキュリティで保護されたエンクレーブ内に展開して実行することもできます。

## <a name="open-enclave"></a>Open Enclave

[Open Enclave SDK](https://openenclave.io/sdk/) は、開発者が複数のプラットフォームと環境を対象とする信頼されたアプリケーションを作成するのに役立つオープンソース プロジェクトです。 Open Enclave SDK がデバイスの信頼できる実行環境内で動作するのに対し、Open Enclave API は、TEE と TEE 以外の処理環境間のインターフェイスとして機能します。

Open Enclave では、複数のハードウェア プラットフォームがサポートされています。 現在、エンクレーブを IoT Edge でサポートするには、Open Portable TEE オペレーティング システム (OP-TEE OS) が必要です。 詳細については、「[OP-TEE OS 用の Open Enclave SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md)」を参照してください。

Open Enclave リポジトリには、開発者が作業を始めるときに役立つサンプルも含まれています。 詳細については、次のいずれかの記事を参照してください。

* [Linux で Open Enclave SDK サンプルを作成する](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [Windows で Open Enclave SDK サンプルを作成する](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>ハードウェア

現時点では、信頼されたアプリケーションを IoT Edge モジュールとして展開するための製造元サービス契約でサポートされているデバイスは、[Scalys による TrustBox](https://scalys.com/trustbox-industrial/) だけです。 TrustBox は TrustBox Edge および TrustBox EdgeXL デバイス上に構築されており、どちらにも Open Enclave SDK と Azure IoT Edge があらかじめ読み込まれています。

詳細については、[Scalys TrustBox 用の Open Enclave の概要](https://aka.ms/scalys-trustbox-edge-get-started)に関するページを参照してください。

## <a name="develop-and-deploy"></a>開発とデプロイ

信頼されたアプリケーションを開発して展開する準備ができたら、Visual Studio Code 用の [Microsoft Open Enclave](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) 拡張機能が役に立つ場合があります。 Linux または Windows を開発用マシンとして使用して、TrustBox 用のモジュールを開発できます。

## <a name="next-steps"></a>次の手順

[Visual Studio Code 用 Open Enclave 拡張機能](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)を使用して IoT Edge モジュールとしての信頼されたアプリケーションの開発を始める方法を学習する
