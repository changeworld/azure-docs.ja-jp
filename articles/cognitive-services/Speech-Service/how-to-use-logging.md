---
title: Speech SDK のログ - 音声サービス
titleSuffix: Azure Cognitive Services
description: Speech SDK (C++、C#、Python、Objective-C、Java) でログ記録を有効にする方法について説明します。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: ba3f5234c3f11cdd5c2e302679bbb6a79ec0c4f6
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550213"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Speech SDK のログの有効化

ファイルへのログ記録は、Speech SDK のオプションの機能です。 開発時、ログ記録により、Speech SDK のコア コンポーネントから追加の情報や診断が提供されます。 ログを有効にするには、音声構成オブジェクト上のプロパティ `Speech_LogFilename` にログ ファイルの場所と名前を設定します。 ログは、Speech SDK のネイティブ ライブラリの静的クラスによって処理されます。 任意の Speech SDK 認識エンジンまたはシンセサイザー インスタンスのログ記録を有効にすることができます。 同じプロセス内にあるインスタンスはすべて、ログ エントリを同じログ ファイルに書き込みます。

> [!NOTE]
> ログは、Speech SDK バージョン 1.4.0 から、サポートされるすべての Speech SDK プログラミング言語 (JavaScript を除く) で使用できます。

## <a name="sample"></a>サンプル

ログ ファイル名は、構成オブジェクト上で指定されます。 `SpeechConfig` を例にとります。また、`config` というインスタンスが作成されているものと想定します。

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

```go
import ("github.com/Microsoft/cognitive-services-speech-sdk-go/common")

config.SetProperty(common.SpeechLogFilename, "LogfilePathAndName")
```

認識エンジンは、構成オブジェクトから作成できます。 これにより、すべての認識エンジンのログが有効になります。

> [!NOTE]
> 構成オブジェクトから `SpeechSynthesizer` を作成した場合、ログは有効になりません。 ただし、ログを有効にすると、`SpeechSynthesizer` から診断も届きます。

## <a name="create-a-log-file-on-different-platforms"></a>各種プラットフォーム上でのログ ファイルの作成

Windows または Linux の場合、ログ ファイルは、ユーザーが書き込みアクセス許可を持つ任意のパスに配置できます。 他のオペレーティング システムの場合、ファイル システムへの書き込みアクセス許可は、既定で制限されている場合があります。

### <a name="universal-windows-platform-uwp"></a>ユニバーサル Windows プラットフォーム (UWP)

UWP アプリケーションでは、ログ ファイルをアプリケーション データのいずれかの場所 (ローカル、ローミング、または一時) に配置する必要があります。 ログ ファイルは、ローカル アプリケーション フォルダー内に作成できます。

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Unity UWP アプリケーションでは、次のように、アプリケーションの永続データ パス フォルダーを使用してログ ファイルを作成できます。

```csharp
#if ENABLE_WINMD_SUPPORT
    string logFile = Application.persistentDataPath + "/logFile.txt";
    config.SetProperty(PropertyId.Speech_LogFilename, logFile);
#endif
```
UWP アプリケーションのファイル アクセス許可の詳細については、「[ファイル アクセス許可](/windows/uwp/files/file-access-permissions)」を参照してください。

### <a name="android"></a>Android

ログ ファイルは、内部ストレージ、外部ストレージ、またはキャッシュ ディレクトリに保存できます。 内部ストレージまたはキャッシュ ディレクトリ内で作成されたファイルは、アプリケーションに対してプライベートです。 ログ ファイルは、外部ストレージ内で作成することをお勧めします。

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

上記のコードの場合、ログ ファイルは、外部ストレージ上のアプリケーション固有ディレクトリのルート内に保存されます。 ファイルにアクセスするには、ファイル マネージャーを使用します (通常、ファイルは、`Android/data/ApplicationName/logfile.txt` 内にあります)。 ファイルは、アプリケーションがアンインストールされたときに削除されます。

マニフェスト ファイル内で `WRITE_EXTERNAL_STORAGE` アクセス許可を要求する必要もあります。

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Unity Android アプリケーションでは、次のように、アプリケーションの永続データ パス フォルダーを使用してログ ファイルを作成できます。

```csharp
string logFile = Application.persistentDataPath + "/logFile.txt";
config.SetProperty(PropertyId.Speech_LogFilename, logFile);
```
さらに、Android 用の Unity Player 設定の書き込みアクセス許可を "External (SDCard)" に設定する必要もあります。 ログは、AndroidStudio Device File Explorer などのツールを使用して取得できるディレクトリに書き込まれます。 正確なディレクトリ パスは Android デバイスによって異なる場合がありますが、場所は通常、`sdcard/Android/data/your-app-packagename/files` ディレクトリです。

Android アプリケーションのデータおよびファイル ストレージの詳細については、[こちら](https://developer.android.com/guide/topics/data/data-storage.html)を参照してください。

#### <a name="ios"></a>iOS

アプリケーション サンド ボックス内のディレクトリにのアクセスできます。 ファイルは、ドキュメント ディレクトリ、ライブラリ ディレクトリ、および一時ディレクトリ内に作成できます。 ドキュメント ディレクトリ内のファイルは、ユーザーに対して使用可能にすることができます。 

iOS で Objective-C を使用している場合は、次のコード スニペットを使用して、アプリケーション ドキュメント ディレクトリにログ ファイルを作成します。

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

作成されたファイルにアクセスするには、アプリケーションの `Info.plist` プロパティ一覧に次のプロパティを追加します。

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

iOS で Swift を使用している場合は、次のコード スニペットを使用してログを有効にしてください。
```swift
let documentsDirectoryPathString = NSSearchPathForDirectoriesInDomains(.documentDirectory, .userDomainMask, true).first!
let documentsDirectoryPath = NSURL(string: documentsDirectoryPathString)!
let logFilePath = documentsDirectoryPath.appendingPathComponent("swift.log")
self.speechConfig!.setPropertyTo(logFilePath!.absoluteString, by: SPXPropertyId.speechLogFilename)
```

iOS ファイル システムの詳細については、[こちら](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)を参照してください。

## <a name="logging-with-multiple-recognizers"></a>複数の認識エンジンによるログ記録

ログ ファイルの出力パスは、`SpeechRecognizer` または他の SDK オブジェクトに構成プロパティとして指定されていますが、SDK のログ記録はシングルトンであり、個々のインスタンスの概念を持たない *プロセス全体* の機能です。 これは、対応する `SpeechConfig` で使用可能なプロパティ データを使用して静的および内部の "グローバル ログ記録の構成" ルーチンを暗黙的に呼び出す `SpeechRecognizer` コンストラクター (または同様のもの) と考えることができます。

つまり、たとえば 6 つの並列認識エンジンを構成して、6 つの個別のファイルに同時に出力することはできません。 その代わりに、作成された最新の認識エンジンが、構成プロパティで指定されたファイルに出力するようにグローバル ログ インスタンスを構成し、すべての SDK ログがそのファイルに出力されるようになります。

これは、ログを構成したオブジェクトの有効期間が、ログ記録の期間に関連付けられていないことも意味します。 ログは、SDK オブジェクトのリリースに応答して停止することはなく、新しいログ構成が提供されない限り、続行されます。 ログ記録の開始後は、新しいオブジェクトを作成するときに、ログ ファイルのパスを空の文字列に設定することで、プロセス全体のログ記録を停止することができます。

複数のインスタンスのログ記録を構成するときの混乱を軽減するために、実際の作業を行うオブジェクトからのログ記録の制御を抽象化すると便利な場合があります。 ヘルパー ルーチンのペアの例を次に示します。

```cpp
void EnableSpeechSdkLogging(const char* relativePath)
{
    auto configForLogging = SpeechConfig::FromSubscription("unused_key", "unused_region");
    configForLogging->SetProperty(PropertyId::Speech_LogFilename, relativePath);
    auto emptyAudioConfig = AudioConfig::FromStreamInput(AudioInputStream::CreatePushStream());
    auto temporaryRecognizer = SpeechRecognizer::FromConfig(configForLogging, emptyAudioConfig);
}

void DisableSpeechSdkLogging()
{
    EnableSpeechSdkLogging("");
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [GitHub でサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
