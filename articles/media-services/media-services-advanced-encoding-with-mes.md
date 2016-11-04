---
title: Media Encoder Standard を使用した高度なエンコード
description: このトピックでは、Media Encoder Standard のタスク プリセットをカスタマイズして、高度なエンコードを実行する方法を紹介します。また、Media Services .NET SDK を使用してエンコード タスクとジョブを作成する方法も紹介します。エンコード ジョブにカスタム プリセットを与える方法も紹介します。
services: media-services
documentationcenter: ''
author: juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: juliako

---
# Media Encoder Standard を使用した高度なエンコード
## Overview
このトピックでは、Media Encoder Standard を使用して高度なエンコード タスクを実行する方法を紹介します。また、[.NET を使用して、エンコード タスクとそのタスクを実行するジョブを作成する方法](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet)も紹介します。エンコード タスクにカスタム プリセットを与える方法も紹介します。プリセットで使用される要素の説明については、[この文書](https://msdn.microsoft.com/library/mt269962.aspx)を参照してください。

次のエンコード タスクを実行するカスタム プリセットを実演します。

* [サムネイルを生成する](media-services-custom-mes-presets-with-dotnet.md#thumbnails)
* [動画をトリミングする (クリッピング)](media-services-custom-mes-presets-with-dotnet.md#trim_video)
* [オーバーレイを作成する](media-services-custom-mes-presets-with-dotnet.md#overlay)
* [音声が入力されない場合、無音オーディオ トラックを挿入する](media-services-custom-mes-presets-with-dotnet.md#silent_audio)
* [自動インターレース解除を無効にする](media-services-custom-mes-presets-with-dotnet.md#deinterlacing)
* [オーディオのみのプリセット](media-services-custom-mes-presets-with-dotnet.md#audio_only)
* [複数のビデオ ファイルを連結する](media-services-custom-mes-presets-with-dotnet.md#concatenate)
* [Media Encoder Standard を使用してビデオをトリミングする](media-services-custom-mes-presets-with-dotnet.md#crop)
* [入力に映像が含まれていないときにビデオ トラックを挿入する](media-services-custom-mes-presets-with-dotnet.md#no_video)

## <a id="encoding_with_dotnet"></a>Media Services .NET SDK でエンコードする
次のコード サンプルでは、Media Services SDK を使用して次のタスクを実行します。

* エンコード ジョブを作成します。
* Media Encoder Standard エンコーダーの参照を取得します。
* カスタム XML または JSON プリセットを読み込みます。XML または JSON (例: [XML](media-services-custom-mes-presets-with-dotnet.md#xml)、[JSON](media-services-custom-mes-presets-with-dotnet.md#json)) をファイルに保存し、次のコードを使用してファイルを読み込むことができます。
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* エンコード タスクをジョブに追加します。
* エンコードする入力資産を指定します。
* エンコードされた資産が含まれる出力資産を作成します。
* ジョブの進行状況を確認するイベント ハンドラーを追加します。
* ジョブを送信します。
  
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
  
        namespace CustomizeMESPresests
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
  
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
  
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
  
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
  
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
  
                    // Get an uploaded asset.
                    var asset = _context.Assets.FirstOrDefault();
  
                    // Encode and generate the output using custom presets.
                    EncodeToAdaptiveBitrateMP4Set(asset);
  
                    Console.ReadLine();
                }
  
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText("CustomPreset_JSON.json");

                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);

                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);

                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();

                    return job.OutputMediaAssets[0];
                }

                static public IAsset UploadMediaFilesFromFolder(string folderPath)
                {
                    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);

                    foreach (var af in asset.AssetFiles)
                    {
                        // The following code assumes 
                        // you have an input folder with one MP4 and one overlay image file.
                        if (af.Name.Contains(".mp4"))
                            af.IsPrimary = true;
                        else
                            af.IsPrimary = false;

                        af.Update();
                    }

                    return asset;
                }


                static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText(customPresetFileName);

                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);

                    // Specify the input assets to be encoded.
                    // This asset contains a source file and an overlay file.
                    task.InputAssets.Add(assetSource);

                    // Add an output asset to contain the results of the job. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);

                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();

                    return job.OutputMediaAssets[0];
                }


                private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                            break;
                        case JobState.Canceling:
                        case JobState.Queued:
                        case JobState.Scheduled:
                        case JobState.Processing:
                            Console.WriteLine("Please wait...\n");
                            break;
                        case JobState.Canceled:
                        case JobState.Error:

                            // Cast sender as a job.
                            IJob job = (IJob)sender;

                            // Display or log error details as needed.
                            break;
                        default:
                            break;
                    }
                }


                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                    return processor;
                }

            }
        }


## 相対サイズのサポート
サムネイルを生成するときに、出力の幅と高さを常にピクセル単位で指定しなければならないというわけではありません。パーセント単位で指定することができます ([1%、…、100%] の範囲)。

### JSON プリセット
    "Width": "100%",
    "Height": "100%"

### XML プリセット
    <Width>100%</Width>
    <Height>100%</Height>

## <a id="thumbnails"></a>サムネイルを生成する
このセクションでは、サムネイルを生成するプリセットをカスタマイズする方法を紹介します。下に定義されているプリセットには、ファイルとサムネイルの生成に必要な情報をエンコードする方法に関する情報が含まれています。[こちら](https://msdn.microsoft.com/library/mt269960.aspx)に記載されている MES プリセットを使用し、サムネイルを生成するコードを追加できます。

> [!NOTE]
> 単一ビットレートのビデオにエンコードする場合、次のプリセットの **SceneChangeDetection** 設定は true にのみ設定できます。マルチビットレートのビデオにエンコードする場合、**SceneChangeDetection** を true に設定すると、エンコーダーからエラーが返されます。
> 
> 

スキーマの詳細については、[こちら](https://msdn.microsoft.com/library/mt269962.aspx)のトピックをご覧ください。

必ず「[考慮事項](media-services-custom-mes-presets-with-dotnet.md#considerations)」セクションを確認してください。

### <a id="json"></a>JSON プリセット
    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"

            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a id="xml"></a>XML プリセット
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

### 考慮事項
次の考慮事項が適用されます。

* 明示的に Start、Step、Range でタイムスタンプを使用する場合、入力ソースは少なくとも 1 分であると仮定しています。
* Jpg/Png/BmpImage 要素には、Start、Step、Range の各文字列属性があります。これらは次のように解釈できます。
  
  * 負ではない整数の場合はフレーム番号 (例: "Start": "120")
  * % サフィックス付きで表現されている場合は、ソース期間に対する相対値 (例: "Start": "15%")
  * HH:MM:SS… 形式で表現されている場合はタイムスタンプ (例: "Start" : "00:01:00")
    
    必要に応じて、表記法を混在させたり、一致させたりすることができます。
    
    また、Start は特殊なマクロの {Best} もサポートしています。このマクロは、コンテンツの最初の "関連する" フレームを決定しようと試みます。注: (Start が {Best} に設定されている場合、Step と Range は無視されます)
  * 既定: Start:{Best}
* 各画像形式の出力形式は明示的に指定する必要があります (Jpg/Png/BmpFormat)。指定されている場合、MES は JpgVideo を JpgFormat などに対応付けます。OutputFormat には新しい画像コーデック固有のマクロである {Index} が導入されました。このマクロは、画像出力形式を指定する場合に (1 度だけ) 指定する必要があります。

## <a id="trim_video"></a>動画をトリミングする (クリッピング)
このセクションでは、エンコーダー プリセットを変更し、入力がいわゆる中間ファイルまたはオンデマンド ファイルの入力動画をクリッピングまたはトリミングする方法について説明します。エンコーダーを使用して、ライブ ストリームからキャプチャまたはアーカイブされた資産をクリッピングまたはトリミングすることもできます。詳細については、[こちらのブログ](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)をご覧ください。

ビデオをトリミングするには、[こちら](https://msdn.microsoft.com/library/mt269960.aspx)に記載されている MES プリセットを使用し、**Sources** 要素を変更します (下記を参照)。StartTime の値は、入力ビデオの絶対タイムスタンプと一致している必要があります。たとえば、入力ビデオの最初のフレームのタイムスタンプが 12:00:10.000 の場合、StartTime は 12:00:10.000 以降でなければなりません。次の例では、入力ビデオの開始タイムスタンプは 0 であると想定しています。**Sources** はプリセットの先頭に配置する必要があります。

### <a id="json"></a>JSON プリセット
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    } 

### XML プリセット
ビデオをトリミングするには、[こちら](https://msdn.microsoft.com/library/mt269960.aspx)に記載されている MES プリセットを使用し、**Sources** 要素を変更します (下記を参照)。

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

## <a id="overlay"></a>オーバーレイを作成する
Media Encoder Standard では、画像を既存の動画に重ね合わせることができます。現在サポートされている形式は png、jpg、gif、bmp です。下に定義されているプリセットはビデオ オーバーレイの基本例です。

プリセット ファイルの定義に加え、資産内のどのファイルがオーバーレイ画像であるか、また画像を重ね合わせるソース動画であるかを Media Services に認識させる必要もあります。ビデオ ファイルは**プライマリ** ファイルである必要があります。

上記の .NET の例では、**UploadMediaFilesFromFolder** と **EncodeWithOverlay** の 2 つの関数を定義しています。UploadMediaFilesFromFolder 関数は、フォルダーからファイルをアップロードし (BigBuckBunny.mp4、Image001.png など)、mp4 ファイルを資産内のプライマリ ファイルとして設定します。**EncodeWithOverlay** 関数は、渡されたカスタム プリセット ファイル (下記のプリセットなど) を使用して、エンコード タスクを作成します。

> [!NOTE]
> 現時点での制限事項:
> 
> オーバーレイの不透明設定には対応していません。
> 
> ソース ビデオ ファイルとオーバーレイ画像ファイルを同じ資産に格納する必要があります。また、ビデオ ファイルをその資産でプライマリ ファイルとして設定する必要があります。
> 
> 

### JSON プリセット
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1,
                  "InputLoop": true
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### XML プリセット
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


## <a id="silent_audio"></a>入力に音声が含まれていないときに無音オーディオ トラックを挿入する
既定では、映像のみで音声の入っていない入力をエンコーダーに送信すると、映像データのみが含まれたファイルが出力資産に含まれます。プレーヤーによっては、このような出力ストリームを処理できないことがあります。そのような場合、この設定を利用すれば、無音のオーディオ トラックを出力に追加するようにエンコーダーに強制できます。

入力に音声が入っていないとき、無音オーディオ トラックが含まれる資産を生成するようにエンコーダーに強制するには、"InsertSilenceIfNoAudio" 値を指定します。

[こちら](https://msdn.microsoft.com/library/mt269960.aspx)に記載されている MES プリセットを使用し、次のように変更します。

### JSON プリセット
    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

### XML プリセット
    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

## <a id="deinterlacing"></a>自動インターレース解除を無効にする
コンテンツのインターレース解除を自動的に行う場合は、何もする必要はありません。自動インターレース解除がオンになっていれば (既定値)、MES がインターレース フレームを自動で検出し、解除処理を行います (インターレースとしてマークされているフレームのみが処理されます)。

自動インターレース解除はオフにすることもできますが、この設定は変更しないことをお勧めします。

### JSON プリセット
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

### XML プリセット
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


## <a id="audio_only"></a>オーディオのみのプリセット
このセクションでは、2 つのオーディオのみの MES プリセット (AAC Audio と AAC Good Quality Audio) を示します。

### AAC Audio
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### AAC Good Quality Audio
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a id="concatenate"></a>複数のビデオ ファイルを連結する
複数のビデオ ファイルを連結するプリセットを生成する方法の例を次に示します。最も一般的なシナリオは、ヘッダーまたはトレーラーをメイン ビデオに追加する場合です。主な用途は、編集対象の複数のビデオ ファイルがプロパティ (ビデオの解像度、フレーム レート、オーディオ トラック数など) を共有している場合です。フレーム レートやオーディオ トラック数が異なるビデオを混在させないように注意してください。

### 要件と考慮事項
* 入力ビデオのオーディオ トラック数の上限は 1 つです。
* 入力ビデオはすべて同じフレーム レートである必要があります。
* ビデオを別の資産にアップロードし、各資産でプライマリ ファイルとしてビデオを設定する必要があります。
* ビデオの再生時間を把握している必要があります。
* 次のプリセット例では、すべての入力ビデオがゼロのタイムスタンプで始まると想定されています。ビデオの開始タイムスタンプが異なる場合、通常のライブ アーカイブの場合と同様に、StartTime 値を変更する必要があります。
* JSON プリセットは、入力資産の AssetID 値の明示的な参照を作成します。
* サンプル コードでは、JSON プリセットがローカル ファイル ("C:\\supportFiles\\preset.json" など) に保存されていると想定されています。また、2 つの資産が 2 つのビデオ ファイルをアップロードして作成されたこと、結果の AssetID 値を知っていることも想定されています。
* このコード スニペットと JSON プリセットは、2 つのビデオ ファイルを連結する例です。次の方法で、複数のビデオに拡張することができます。
  
  1. task.InputAssets.Add() を繰り返し呼び出して複数のビデオを順に追加する。
  2. 同じ順序でエントリを追加して、JSON の対応する "Sources" 要素を編集する。

### .NET コード
    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job. 
    // This output is specified as AssetCreationOptions.None, which 
    // means the output asset is not encrypted. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

### JSON プリセット
連結する資産の ID と、各ビデオの適切な時間セグメントを使用して、カスタム プリセットを更新します。

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a id="crop"></a>Media Encoder Standard を使用してビデオをトリミングする
「[Media Encoder Standard を使用してビデオをトリミングする](media-services-crop-video.md)」をご覧ください。

## <a id="no_video"></a>入力に映像が含まれていないときにビデオ トラックを挿入する
既定では、音声のみで映像の入っていない入力をエンコーダーに送信すると、音声データのみが含まれたファイルが出力資産に含まれます。Azure Media Player ([こちら](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)を参照) など、プレイヤーによっては、このようなストリームを処理できないことがあります。その場合、この設定を使用することで、モノクロのビデオ トラックを出力に追加するようエンコーダーに強制できます。

> [!NOTE]
> 出力ビデオ トラックを挿入するようエンコーダーに強制すると、出力資産のサイズが増えるため、エンコード タスクのコストが発生します。テストを実行して、この増加が月額料金に及ぼす影響がごくわずかであることを確認してください。
> 
> 

### 最も低いビットレートでのみビデオを挿入する
["H264 複数ビットレート 720p"](https://msdn.microsoft.com/library/mt269960.aspx) などの複数ビットレート エンコード プリセットを使用して、ビデオ ファイルと音声のみのファイルが混在する、ストリーミングの入力カタログ全体をエンコードするとします。このシナリオでは、入力に映像が含まれていないときに、すべての出力ビットレートでビデオを挿入するのではなく、最も低いビットレートでのみモノクロのビデオ トラックを挿入するようエンコーダーに強制できます。これを実現には、"InsertBlackIfNoVideoBottomLayerOnly" フラグを指定する必要があります。

[こちら](https://msdn.microsoft.com/library/mt269960.aspx)に記載されている MES プリセットを使用し、次のように変更します。

#### JSON プリセット
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### XML プリセット
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideoBottomLayerOnly</Condition>

### すべての出力ビットレートでビデオを挿入する
["H264 複数ビットレート 720p"](https://msdn.microsoft.com/library/mt269960.aspx) などの複数ビットレート エンコード プリセットを使用して、ビデオ ファイルと音声のみのファイルが混在する、ストリーミングの入力カタログ全体をエンコードするとします。このシナリオでは、入力に映像が含まれていないときに、すべての出力ビットレートでモノクロのビデオ トラックを挿入するようエンコーダーに強制できます。これにより、ビデオ トラックとオーディオ トラックの数に関して、出力資産がすべて均一になります。これを実現には、"InsertBlackIfNoVideo" フラグを指定する必要があります。

[こちら](https://msdn.microsoft.com/library/mt269960.aspx)に記載されている MES プリセットを使用し、次のように変更します。

#### JSON プリセット
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### XML プリセット
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideo</Condition>

## Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## 関連項目
[Media Services Encoding の概要](media-services-encode-asset.md)

<!---HONumber=AcomDC_0831_2016-->