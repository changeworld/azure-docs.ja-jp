<properties linkid="hdinsight-dotnet-avro-serialization" urlDisplayName="HDInsight Avro.NET のシリアル化" pageTitle="Avro.NET を使用したデータのシリアル化 | Azure" metaKeywords="" description="Azure HDInsight が Avro.NET を使用してビッグ データをシリアル化する方法について説明します。" metaCanonical="" services="hdinsight" documentationCenter="" title="Avro.NET を使用したデータのシリアル化 " authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />



# Avro.NET を使用したデータのシリアル化

##概要
このトピックでは、Microsoft Avro Library (Avro.NET) を使用して、オブジェクトとその他のデータ構造体をバイト ストリームにシリアル化し、メモリ、データベース、またはファイルに格納する方法を示します。このライブラリは、言語に依存しないスキーマを定義するために、JSON を使用して、Apache Avro データ シリアル化システムを定義します。このスキーマはコンパクトなバイナリ データ交換形式を定義し、逆シリアル化を使用してオブジェクトを回復する必要があるときは、多くの言語 (現時点で C、C++、C#、Java、PHP、Python、および Ruby) がこの形式を処理できます。
 

Apache Avro のシリアル化形式は、Microsoft HDInsight を含む Hadoop 環境で広く使用されています。この形式の詳細については、Apache Avro Specification を参照してください。

Microsoft HDInsight Avro.NET ライブラリは、オブジェクトをシリアル化する 2 つの方法をサポートしています。
リフレクションを使用する方法
汎用レコードを使用する方法

##前提条件

Microsoft .NET Library for Avro は、NuGet パッケージの形式で配布されています。Visual Studio でインストールを実行する方法: **[プロジェクト]** タブで **[NuGet パッケージの管理]** をクリックし、**[オンライン検索]** ボックスで「Avro」を検索して、**[Microsoft .NET Library for Avro]** の横にある **[インストール]** をクリックします。Newtonsoft.Json (.5.0.5 以降) の依存関係も自動的にダウンロードされます。


###概略
シナリオ: 

 * <a href="#Scenario1">リフレクションを使用したシリアル化

 * <a href="#Scenario2">Avro オブジェクト コンテナーを使用したシリアル化

 * <a href="#Scenario3">オブジェクト コンテナー ファイルを使用したシリアル化とリフレクションを使用したシリアル化


 * <a href="#Scenario4">オブジェクト コンテナー ファイルを使用したシリアル化と汎用レコードを使用したシリアル化


 * <a href="#Scenario5">オブジェクト コンテナー ファイルとカスタム圧縮コーデックの組み合わせを使用したシリアル化


<h2> <a name="Scenario1">リフレクションを使用したシリアル化</a></h2>

次の例では、クラスと構造体のシリアル化、および逆シリアル化を実行し、最後に初期のインスタンスと比較して同一性を確認します。これらの型に対応する JSON スキーマは、アカウント データのコントラクト属性を考慮に入れたうえで、Microsoft Avro Library によって自動的に作成されます。Microsoft Avro ライブラリは[データ コントラクト シリアライザー](http://msdn.microsoft.com/ja-jp/library/ms731072(v=vs.110).aspx)を使用して、シリアル化しようとするフィールドを識別します。

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    
    //Sample Class used in serialization samples
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }
    
    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }
    
    //Sample struct used in serialization samples
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }
    
    [DataMember]
    public int Room { get; set; }
    }
    
    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {
    
    //Serialize and deserialize sample data set represented as an object using Reflection
    //No explicit schema definition is required - schema of serialized objects is automatically built
    public void SerializeDeserializeObjectUsingReflection()
    {
    
    Console.WriteLine("SERIALIZATION USING REFLECTION\n");
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
    //for serializing only properties attributed with DataContract/DateMember
    var avroSerializer = AvroSerializer.Create<SensorData>();
    
    //Create a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    //Create a data set using sample Class and struct 
    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };
    
    //Serialize the data to the specified stream
    avroSerializer.Serialize(buffer, expected);
    
      
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Prepare the stream for deserializing the data
    buffer.Seek(0, SeekOrigin.Begin);
    
    //Deserialize data from the stream and cast it to the same type used for serialization
    var actual = avroSerializer.Deserialize(buffer);
    
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    
    //Finally, verify that deserialized data matches the original one
    bool isEqual = this.Equal(expected, actual);
    
    Console.WriteLine("Result of Data Set Identity Comparison is {0}" , isEqual);
    
    }
    }
    
    //
    //Helper methods
    //
    
    //Comparing two SensorData objects
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }
    
    
      
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Create an instance of AvroSample Class and invoke methods
    //illustrating different serializing approaches
    AvroSample Sample = new AvroSample();
    
    //Serialization to memory using Reflection
    Sample.SerializeDeserializeObjectUsingReflection();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // The example is expected to display the following output: 
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

<h2> <a name="Scenario2"></a>Avro オブジェクト コンテナーを使用したシリアル化</h2>

データを表す .NET クラスを常に使用できるとは限りません。データのスキーマを動的にすることもできます。たとえば、データがコンマ区切り値ファイル (CSV) として表現されており、スキーマが事前に把握できておらず、Avro 形式に変換する必要がある場合を想定します。
このような場合は、明示的に指定した JSON スキーマを使用して特別なクラス (AvroRecord) のインスタンスを作成し、必要なデータを保持するフィールドを設定して、そのインスタンスをシリアル化する必要があります。
次の例では、スキーマを定義し、対応するレコードを作成して、データを設定してから、シリアル化と逆シリアル化を実行する方法を示します。最後に、初期オブジェクトと、逆シリアル化を実行した後のオブジェクトを比較し、同一性を確認します。

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    
    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {
    
    //Serialize and deserialize sample data set using Generic Record.
    //Generic Record is a special class with the schema explicitly defined in JSON.
    //All serialized data should be mapped to the fields of Generic Record,
    //which in turn will be then serialized.
    public void SerializeDeserializeObjectUsingGenericRecords()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
    Console.WriteLine("Defining the Schema and creating Sample Data Set...");
    
    //Define the schema in JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";
    
    //Create a generic serializer based on the schema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;
    
    //Create a Memory Stream buffer
    using (var stream = new MemoryStream())
    {
    //Create a generic record to represent the data
    dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location.Floor = 1;
    location.Room = 243;
    
    dynamic expected = new AvroRecord(serializer.WriterSchema);
    expected.Location = location;
    expected.Value = new byte[] { 1, 2, 3, 4, 5 };
    
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Serialize the data
    serializer.Serialize(stream, expected);
    
    stream.Seek(0, SeekOrigin.Begin);
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Deserialize the data into a generic record
    dynamic actual = serializer.Deserialize(stream);
    
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    
    //Finally, verify the results
    bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
    isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
    isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
    }
    }
    
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Create an instance of AvroSample Class and invoke methods
    //illustrating different serializing approaches
    AvroSample Sample = new AvroSample();
    
    //Serialization to memory using Generic Record
    Sample.SerializeDeserializeObjectUsingGenericRecords();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // The example is expected to display the following output: 
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

<h2> <a name="Scenario3"></a>オブジェクト コンテナー ファイルを使用したシリアル化とリフレクションを使用したシリアル化</h2>

次の例は例 1 に似ています。クラスと構造体をシリアル化し、サンプル アプリケーションの現在のディレクトリにあるオブジェクト コンテナー ファイルに保存します。次にこのファイルを読み取り、データを逆シリアル化し、最後に初期インスタンスと比較して同一性を確認します。
deflate 圧縮コーデックを使用して、オブジェクト コンテナー ファイル内のデータを圧縮します。

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    
    //Sample Class used in serialization samples
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }
    
    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }
    
    //Sample struct used in serialization samples
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }
    
    [DataMember]
    public int Room { get; set; }
    }
    
    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {
    
    //Serializes and deserializes sample data set using Reflection and Avro Object Container Files
    //Serialized data is compressed with Deflate codec
    public void SerializeDeserializeUsingObjectContainersReflection()
    {
    
    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");
    
    //Path for Avro Object Container File
    string path = "AvroSampleReflectionDeflate.avro";
    
    //Create a data set using sample Class and struct
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };
    
    //Serializing and saving data to file
    //Creating a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
    //Data will be compressed using Deflate codec
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serialize the data to stream using the sequential writer
    testData.ForEach(writer.Write);
    }
    }
    
    //Save stream to file
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }
    
    //Reading and deserializing data
    //Creating a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");
    
    //Reading data from Object Container File
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Prepare the stream for deserializing the data
    buffer.Seek(0, SeekOrigin.Begin);
    
    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;
    
    //Finally, verify that deserialized data matches the original one
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    bool isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
    count++;
    }
    }
    }
    
    //Delete the file
    RemoveFile(path);
    }
    
    //
    //Helper methods
    //
    
    //Comparing two SensorData objects
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }
    
    //Saving memory stream to a new file with the given path
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;
    
    }
    }
    
    //Reading a file content using given path to a memory stream
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    
    //Deleting file using given path
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Create an instance of AvroSample Class and invoke methods
    //illustrating different serializing approaches
    AvroSample Sample = new AvroSample();
    
    //Serialization using Reflection to Avro Object Container File
    Sample.SerializeDeserializeUsingObjectContainersReflection();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

<h2> <a name="Scenario4"></a>オブジェクト コンテナー ファイルを使用したシリアル化と汎用レコードを使用したシリアル化</h2>

次の例は例 2 に似ています。AvroRecord と、明示的に定義した JSON スキーマを使用して、サンプル データ セットをシリアル化します。シリアル化されたデータは、サンプル アプリケーションにとっての現在のディレクトリにある新しいオブジェクト コンテナー ファイルに格納されます。次にこのファイルを読み取り、データを逆シリアル化し、最後に初期インスタンスと比較して同一性を確認します。

オブジェクト コンテナー ファイル内のデータは圧縮されません (Null 圧縮コーデックを使用)。

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    
    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {
    
    //Serializes and deserializes sample data set using Generic Record and Avro Object Container Files
    //Serialized data is not compressed
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");
    
    //Path for Avro Object Container File
    string path = "AvroSampleGenericRecordNullCodec.avro";
    
    Console.WriteLine("Defining the Schema and creating Sample Data Set...");
    
    //Define the schema in JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";
    
    //Create a generic serializer based on the schema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;
    
    //Create a generic record to represent the data
    var testData = new List<AvroRecord>();
    
    dynamic expected1 = new AvroRecord(rootSchema);
    dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location1.Floor = 1;
    location1.Room = 243;
    expected1.Location = location1;
    expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
    testData.Add(expected1);
    
    dynamic expected2 = new AvroRecord(rootSchema);
    dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location2.Floor = 1;
    location2.Room = 244;
    expected2.Location = location2;
    expected2.Value = new byte[] { 6, 7, 8, 9 };
    testData.Add(expected2);
    
    //Serializing and saving data to file
    //Create a MemoryStream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
    //Data will not be compressed (Null compression codec)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Serialize the data to stream using the sequential writer
    testData.ForEach(streamWriter.Write);
    }
    }
    
    Console.WriteLine("Saving serialized data to file...");
    
    //Save stream to file
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }
    
    //Reading and deserializng the data
    //Create a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");
    
    //Reading data from Object Container File
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Prepare the stream for deserializing the data
    buffer.Seek(0, SeekOrigin.Begin);
    
    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;
    
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    
    //Finally, verify the results
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
    int count = 1;
    foreach (var pair in pairs)
    {
    bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
    isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
    isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    }
    
    //Delete the file
    RemoveFile(path);
    }
    
    //
    //Helper methods
    //
    
    //Saving memory stream to a new file with the given path
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;
    
    }
    }
    
    //Reading a file content using given path to a memory stream
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    
    //Deleting file using given path
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Create an instance of AvroSample Class and invoke methods
    //illustrating different serializing approaches
    AvroSample Sample = new AvroSample();
    
    //Serialization using Generic Record to Avro Object Container File
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

<h2> <a name="Scenario5"></a>オブジェクト コンテナー ファイルとカスタム圧縮コーデックの組み合わせを使用したシリアル化</h2>

[Avro Specification](http://avro.apache.org/docs/current/spec.html#Required+Codecs) では、(Null と Deflate 以外に) オプションの圧縮コーデックを使用することもできます。次の例では、オブジェクト コンテナー ファイルに対してカスタム圧縮コーデックを使用する方法を使用します。この例では、"実際に" 異なっているコーデック (サポートされているオプションのコーデックとして Avro Specification に記載されている Snappy など) を実装せず、.NET Framework 4.5 での [Deflate](http://msdn.microsoft.com/ja-jp/library/system.io.compression.deflatestream(v=vs.110).aspx) の実装 (zlib に基づく、より良い圧縮アルゴリズムを提供) を使用します。多くの開発者にとって、この方法が役に立つ可能性があります。

    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of Deflate compression algorithm is used
    // Ensure your C# Project is set up accordingly
    //
    
    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.IO;
    using System.IO.Compression;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    
    #region Defining objects for serialization
    //Sample Class used in serialization samples
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }
    
    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }
    
    //Sample struct used in serialization samples
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }
    
    [DataMember]
    public int Room { get; set; }
    }
    #endregion
    
    #region Defining custom codec based on .NET Framework V.4.5 Deflate
    //Avro.NET Codec class contains two methods 
    //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed)
    //which are the key ones for data compression.
    //To enable a custom codec one needs to implement these methods for the required codec
    
    #region Defining Compression and Decompression Streams
    //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
    //can not be directly used for Avro because it does not support vital operations like Seek.
    //Thus one needs to implement two classes inherited from Stream
    //(one for compressed and one for decompressed stream)
    //that use Deflate compression and implement all required features 
    internal sealed class CompressionStreamDeflate45 : Stream
    {
    private readonly Stream buffer;
    private DeflateStream compressionStream;
    
    public CompressionStreamDeflate45(Stream buffer)
    {
    Debug.Assert(buffer != null, "Buffer is not allowed to be null.");
    
    this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
    this.buffer = buffer;
    }
    
    public override bool CanRead
    {
    get { return this.buffer.CanRead; }
    }
    
    public override bool CanSeek
    {
    get { return true; }
    }
    
    public override bool CanWrite
    {
    get { return this.buffer.CanWrite; }
    }
    
    public override void Flush()
    {
    this.compressionStream.Close();
    }
    
    public override long Length
    {
    get { return this.buffer.Length; }
    }
    
    public override long Position
    {
    get
    {
    return this.buffer.Position;
    }
    
    set
    {
    this.buffer.Position = value;
    }
    }
    
    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.buffer.Read(buffer, offset, count);
    }
    
    public override long Seek(long offset, SeekOrigin origin)
    {
    return this.buffer.Seek(offset, origin);
    }
    
    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }
    
    public override void Write(byte[] buffer, int offset, int count)
    {
    this.compressionStream.Write(buffer, offset, count);
    }
    
    protected override void Dispose(bool disposed)
    {
    base.Dispose(disposed);
    
    if (disposed)
    {
    this.compressionStream.Dispose();
    this.compressionStream = null;
    }
    }
    }
    
    internal sealed class DecompressionStreamDeflate45 : Stream
    {
    private readonly DeflateStream decompressed;
    
    public DecompressionStreamDeflate45(Stream compressed)
    {
    this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
    }
    
    public override bool CanRead
    {
    get { return true; }
    }
    
    public override bool CanSeek
    {
    get { return true; }
    }
    
    public override bool CanWrite
    {
    get { return false; }
    }
    
    public override void Flush()
    {
    this.decompressed.Close();
    }
    
    public override long Length
    {
    get { return this.decompressed.Length; }
    }
    
    public override long Position
    {
    get
    {
    return this.decompressed.Position;
    }
    
    set
    {
    throw new NotSupportedException();
    }
    }
    
    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.decompressed.Read(buffer, offset, count);
    }
    
    public override long Seek(long offset, SeekOrigin origin)
    {
    throw new NotSupportedException();
    }
    
    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }
    
    public override void Write(byte[] buffer, int offset, int count)
    {
    throw new NotSupportedException();
    }
    
    protected override void Dispose(bool disposing)
    {
    base.Dispose(disposing);
    
    if (disposing)
    {
    this.decompressed.Dispose();
    }
    }
    }
    #endregion
    
    #region Define Codec
    //Define the actual codec class containing the required methods for manipulating streams:
    //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed)
    //Codec class uses classes for comressed and decompressed streams defined above
    internal sealed class DeflateCodec45 : Codec
    {
    
    //We merely use different IMPLEMENTATION of Deflate, so the CodecName remains "deflate"
    public static readonly string CodecName = "deflate";
    
    public DeflateCodec45()
    : base(CodecName)
    {
    }
    
    public override Stream GetCompressedStreamOver(Stream decompressed)
    {
    if (decompressed == null)
    {
    throw new ArgumentNullException("decompressed");
    }
    
    return new CompressionStreamDeflate45(decompressed);
    }
    
    public override Stream GetDecompressedStreamOver(Stream compressed)
    {
    if (compressed == null)
    {
    throw new ArgumentNullException("compressed");
    }
    
    return new DecompressionStreamDeflate45(compressed);
    }
    }
    #endregion
    
    #region Define modified Codec Factory
    //Define modified Codec Factory to be used in Reader
    //It will catch the attempt to use "deflate" and provide Custom Codec 
    //For all other cases it will rely on the base class (CodecFactory)
    internal sealed class CodecFactoryDeflate45 : CodecFactory
    {
    
    public override Codec Create(string codecName)
    {
    if (codecName == DeflateCodec45.CodecName)
    return new DeflateCodec45();
    else
    return base.Create(codecName);
    }
    }
    #endregion
    
    #endregion
    
    #region Sample Class with demonstration methods
    //This class contains methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {
    
    //Serializes and deserializes sample data set using Reflection and Avro Object Container Files
    //Serialized data is compressed with the Custom compression codec (Deflate of .NET Framework 4.5)
    //
    //This sample uses Memory Stream for all operations related to serialization, deserialization and
    //Object Container manipulation, though File Stream could be easily used.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {
    
    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");
    
    //Path for Avro Object Container File
    string path = "AvroSampleReflectionDeflate45.avro";
    
    //Create a data set using sample Class and struct
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };
    
    //Serializing and saving data to file
    //Creating a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
    //Here the custom Codec is introduced. For convenience the next commented code line shows how to use built-in Deflate.
    //Note, that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
    //in read-write operations
    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serialize the data to stream using the sequential writer
    testData.ForEach(writer.Write);
    }
    }
    
    //Save stream to file
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }
    
    //Reading and deserializing data
    //Creating a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");
    
    //Reading data from Object Container File
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Prepare the stream for deserializing the data
    buffer.Seek(0, SeekOrigin.Begin);
    
    //Because of SequentialReader<T> constructor signature an AvroSerializerSettings instance is required
    //when Codec Factory is explicitly specified
    //You may comment the line below if you want to use built-in Deflate (see next comment)
    AvroSerializerSettings settings = new AvroSerializerSettings();
    
    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
    //Here the custom Codec Factory is introduced.
    //For convenience the next commented code line shows how to use built-in Deflate
    //(no explicit Codec Factory parameter is required in this case).
    //Note, that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
    //in read-write operations
    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;
    
    //Finally, verify that deserialized data matches the original one
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    bool isEqual;
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    
    //Delete the file
    RemoveFile(path);
    }
    #endregion
    
    #region Helper Methods
    
    //Comparing two SensorData objects
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }
    
    //Saving memory stream to a new file with the given path
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;
    
    }
    }
    
    //Reading a file content using given path to a memory stream
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    
    //Deleting file using given path
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    #endregion
    
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Create an instance of AvroSample Class and invoke methods
    //illustrating different serializing approaches
    AvroSample Sample = new AvroSample();
    
    //Serialization using Reflection to Avro Object Container File using Custom Codec
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

	



