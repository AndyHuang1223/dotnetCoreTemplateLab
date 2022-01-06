# .NET Core 建立專案範本

## 必要條件
- .NET 5.0 SDK 或更新版本
- 請參閱參考文章[dotnet new 的自訂範本](https://docs.microsoft.com/zh-tw/dotnet/core/tutorials/cli-templates-create-item-template#create-the-template-config)
## 建立必要的資料夾
將範本存在工作資料夾內，並使用測試資料夾來測試範本。工作資料夾及測試資料夾放在同父資料夾內。
```
.
├── test(測試專案)
└── working
    └── templates（放範本）
```

## 建立項目範本(某幾支程式)

### 目標
- 針對項目範本建立類別
- 建立範本設定資料夾和檔案
- 從檔案路徑安裝範本
- 測試項目範本
- 將項目範本解除安裝

項目範本是特定類型的範本，其會包含一或多個檔案。 這些範本類型很適合在您想要產生設定、程式碼或方案檔之類的項目時使用。在此範例中，您將會建立能將擴充方法加入字串類型的類別。
在working/templates的目錄內建立extensions目錄（擴充方法模板範例）

```
working
└── templates
    └── extensions
```

進入extensions目錄後，建立名為 CommonExtensions.cs 的新檔案，並使用您慣用的文字編輯器開啟它。 此類別將會提供名為 Reverse 的擴充方法，其能反轉字串的內容。 貼上下列程式碼並儲存檔案：
```csharp
using System;

namespace System
{
    public static class StringExtensions
    {
        public static string Reverse(this string value)
        {
            var tempArray = value.ToCharArray();
            Array.Reverse(tempArray);
            return new string(tempArray);
        }
    }
}
```
建立範本內容後，在範本的根目錄（extensions）建立範本設定。

### 建立範本設定
範本的根目錄中有特殊的資料夾和設定檔案可辨識範本。範本資料夾是位於 working\templates\extensions。

當您建立範本時，範本資料夾中的所有檔案和資料夾都會包含為範本的一部分，除了特殊設定資料夾之外。 此設定資料夾名為 .template.config。

首先，建立名為 .template.config 的新子資料夾，然後進入它。 然後，建立名為 template.json 的新檔案。 資料夾結構看起來應該像這樣：
```
working
└── templates
    └── extensions
        ├── .template.config
        │   └── template.json
        └── CommonExtensions.cs
```

```json=
{
  "$schema": "http://json.schemastore.org/template",
  "author": "Me",
  "classifications": [ "Common", "Code" ],
  "identity": "ExampleTemplate.StringExtensions",
  "name": "Example templates: string extensions",
  "shortName": "stringext",
  "tags": {
    "language": "C#",
    "type": "item"
  }
}
```
此設定檔會包含您範本的所有設定。 您可以看見基本設定 (例如 name 和 shortName)，但還有設定為 item 的 tags/type 值。 這會將您的範本分類為項目範本。 您可以建立的範本類型本身並無限制。 item和 project 值是 .net 建議的通用名稱，讓使用者可以輕鬆地篩選其所搜尋的範本類型。

您已經具備有效的 .template.config/template.json 檔案，現在您的範本已經準備好並可供安裝。 在您的終端機中，瀏覽至 extensions 資料夾，並執行下列命令以安裝位於目前資料夾中的範本：

在 Windows： `dotnet new --install .\\`
在 Linux MacOS 上： `dotnet new --install ./`
此命令會輸出已安裝範本的清單，其中應該會包含您的範本。

```
將安裝下列範本套件:
   /Users/huangbaihao/Desktop/parent_folder/working/templates/extensions

成功: /Users/huangbaihao/Desktop/parent_folder/working/templates/extensions 已安裝下列範本:
範本名稱                                  簡短名稱   語言      標記
------------------------------------  ---------  ----  -----------
Example templates: string extensions  stringext  [C#]  Common/Code
```

### 測試項目範本
到test的目錄底下，並使用 `dotnet new console` 建立新的主控台應用程式。
```bash=
dotnet new console
```
```
The template "Console Application" was created successfully.

Processing post-creation actions...
Running 'dotnet restore' on C:\test\test.csproj...
  Restore completed in 54.82 ms for C:\test\test.csproj.

Restore succeeded.
```
使用 `dotnet run` 執行專案。
```bash=
dotnet run
```
輸出顯示
```
Hello World!
```
使用 `dotnet new stringext` 從產出CommonExtensions.cs。
```bash=
dotnet new stringext
```

```
The template "Example templates: string extensions" was created successfully.
```
接著修改program.cs程式碼，由範本提供的擴充方法來反轉"Hello World!"字串。
```csharp=
Console.WriteLine("Hello World!".Reverse());
```
再次執行程式，顯示已反轉後結果。

```
!dlroW olleH
```

### 移除範本

將終端機移動至 extensions（建立範本的地方） 目錄底下並執行下列命令，以卸載位於目前資料夾的範本：

- 在 Windows：`dotnet new --uninstall .\\`
- 在 Linux 或 `MacOS 上： dotnet new --uninstall ./`
此命令會輸出已卸載的範本清單。
```
Success: <root path>\working\templates\extensions was uninstalled.
```
可以使用 `dotnet new --uninstall` 來查看已安裝的範本套件清單，包括每個範本封裝的命令以將其卸載。

## 建立專案範本

將終端機移到至 *working/templates* 資料夾，並建立 consoleasync 的新子資料夾。進入該資料夾後，執行 `dotnet new console` 產出標準主控台應用程式，從此範本開始調整。

```bash=
dotnet new console
```
目前的資料夾結構為：
```
.
└── templates
    ├── consoleasync
    │   ├── Program.cs
    │   └── consoleasync.csproj
    └── extensions
        └── StringExtensions.cs
```
### 修改Program.cs
```csharp=
await Console.Out.WriteAsync("Hello World with C# 10.0!");
```
### 修改consoleasync.csproj
將專案使用的 c # 語言版本更新為10.0 版。 編輯 consoleasync.csproj 檔案並將 \<LangVersion> 設定加入 \<PropertyGroup> 節點。

```xml=
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net6.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
      
     <!--加入此段--> 
    <LangVersion>10.0</LangVersion>    
      
  </PropertyGroup>

</Project>

```
### 建置專案

```bash=
dotnet run
```
輸出為：

```
Hello World with C# 10.0!
```
### 建立範本專案
接著將建置後產生的bin及obj目錄刪除，刪除這些檔案可確保範本只會包含與您的範本相關的檔案。
```
.
└── templates
    ├── consoleasync
    │   ├── .template.config
    │   │   └── template.json
    │   ├── Program.cs
    │   ├── consoleasync.csproj
    └── extensions
        ├── .template.config
        │   └── template.json
        └── StringExtensions.cs
```
consoleasync內的template.json設定檔：
```json=
{
  "$schema": "http://json.schemastore.org/template",
  "author": "Me",
  "classifications": [ "Common", "Console", "C#9" ],
  "identity": "ExampleTemplate.AsyncProject",
  "name": "Example templates: async project",
  "shortName": "consoleasync",
  "tags": {
    "language": "C#",
    "type": "project"
  }
}
```
此設定檔會包含範本的所有設定。 可以看見基本設定 (例如 `name` 和 `shortName`)，但還有設定為 project 的 `tags/type` 值。 這會將您的範本指定為專案範本。 您可以建立的範本類型本身並無限制。 item和 project 值是 .net 建議的通用名稱，讓使用者可以輕鬆地篩選其所搜尋的範本類型。

`classifications` 項目代表您執行 dotnet new 並取得範本清單時所會看見的 [標籤] 欄。 使用者也可以根據分類標籤搜尋。 不要將 JSON 檔案中的 tags 屬性與 classifications 標籤清單混淆在一起。 它們是不同的東西，但不幸地具有類似的名稱。

當已經寫完設定檔之後，即可準備安裝範本，在安裝前先確認將不需要的檔案刪除（/bin /obj），接著將終端機移動至consoleasync資料夾中，並執行指令安裝範本。

- 在 Windows： `dotnet new --install .\\`
- 在 Linux MacOS 上： `dotnet new --install ./`
此命令會輸出已安裝範本的清單，其中應該會包含您的範本。
```bash=
dotnet new --install .\
```
此命令會輸出已安裝的範本清單，其中應包含您的範本。
```
The following template packages will be installed:
   <root path>\working\templates\consoleasync

Success: <root path>\working\templates\consoleasync installed the following templates:
Templates                                         Short Name               Language          Tags
--------------------------------------------      -------------------      ------------      ----------------------
Example templates: async project                  consoleasync             [C#]              Common/Console/C#9
```

### 測試專案範本
接著將終端機移動至test資料夾，使用下列指令新增範本專案
```bash=
dotnet new consoleasync
```
輸出：

```
The template "Example templates: async project" was created successfully.
```
建置專案

```bash=
dotnet run
```
輸出結果：

```
Hello World with C# 10.0!
```
### 解除安裝範本
將終端機中移動至consoleasync資料夾，然後執行下列命令卸載範本：
- 在 Windows： `dotnet new --uninstall .\\`
- 在 Linux MacOS 上： `dotnet new --uninstall ./`

```
Success: <root path>\working\templates\consoleasync was uninstalled.
```

## 建立範本套件（NuGet）
### 目標
- 建立 * .csproj 專案以建立範本套件
- 設定專案檔以用於封裝
- 從 NuGet 套件檔案安裝範本套件
- 依套件識別碼卸載範本套件

範本套件是封裝成 NuGet 套件的一或多個範本。 當您安裝或卸載範本套件時，會分別新增或移除套件中包含的所有範本。 之前皆僅處理個別的範本。 若要共用未封裝的範本，您必須複製範本資料夾並透過該資料夾來安裝。 因為範本套件中可以有一個以上的範本，而且是單一檔案，所以共用比較容易。

範本套件是以 NuGet 套件 (nupkg) 檔表示。 而且，如同任何 NuGet 套件，您可以將範本套件上傳至 NuGet Gallery（公開）。 此 dotnet new --install 命令支援從 NuGet 套件摘要安裝範本套件。 此外，您也可以直接從 nupkg 檔安裝範本套件。

通常，您可以使用 C# 專案檔來編譯程式碼並產生二進位檔。 不過，專案也可以用來產生範本套件。 透過變更 .csproj 的設定，您會防止它編譯任何程式碼，並改成將範本的所有資產包含為資源。 建立此專案時，它會產生 NuGet 套件的範本套件。

您將建立的封裝將包含先前建立的 專案範本 和 套件範本 。 由於我們將這兩個範本分組到 working\templates\ 資料夾，我們可以針對 .csproj 檔案使用 working 資料夾。

在終端機中，瀏覽至 working 資料夾。 建立新專案，並將名稱設定為 `templatepack`，然後將輸出資料夾設定為目前的資料夾。

```bash=
dotnet new console -n templatepack -o .
```
-n（- -name）參數會將 .csproj檔名設定為 *templatepack.csproj* -o（- -output）參數會在目前的目錄中建立檔案。

```
The template "Console Application" was created successfully.

Processing post-creation actions...
Running 'dotnet restore' on .\templatepack.csproj...
  Restore completed in 52.38 ms for C:\working\templatepack.csproj.

Restore succeeded.
```
資料夾內會有一個 *Program.cs* 及 *templatepack.csproj*，將 Program.cs 刪除後，開啟*templatepack.csproj*，並將下列 XML 取代掉原本的內容。

```xml=
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <PackageType>Template</PackageType>
    <PackageVersion>1.0</PackageVersion>
    <PackageId>AdatumCorporation.Utility.Templates</PackageId>
    <Title>AdatumCorporation Templates</Title>
    <Authors>Me</Authors>
    <Description>Templates to use when creating an application for Adatum Corporation.</Description>
    <PackageTags>dotnet-new;templates;contoso</PackageTags>

    <TargetFramework>netstandard2.0</TargetFramework>

    <IncludeContentInPack>true</IncludeContentInPack>
    <IncludeBuildOutput>false</IncludeBuildOutput>
    <ContentTargetFolders>content</ContentTargetFolders>
    <NoWarn>$(NoWarn);NU5128</NoWarn>
  </PropertyGroup>

  <ItemGroup>
    <Content Include="templates\**\*" Exclude="templates\**\bin\**;templates\**\obj\**" />
    <Compile Remove="**\*" />
  </ItemGroup>

</Project>
```

### 建置及安裝
儲存專案檔後，確保資料夾結構如下：
```
working
│   templatepack.csproj
└───templates
    ├───extensions
    │   └───.template.config
    │           template.json
    └───consoleasync
        └───.template.config
                template.json
```
將終端機移動至*working*資料夾，並執行 `dotnet pack` 命令，此命令會建置專案，並在working/bin/Debug資料夾內建立了NuGet套件，如下輸出：
```
\working> dotnet pack

Microsoft (R) Build Engine version 16.8.0+126527ff1 for .NET
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 123.86 ms for C:\working\templatepack.csproj.

  templatepack -> C:\working\bin\Debug\netstandard2.0\templatepack.dll
  Successfully created package 'C:\working\bin\Debug\AdatumCorporation.Utility.Templates.1.0.0.nupkg'.  
```
接著使用CLI來安裝範本套件 `dotnet new --install PATH_TO_NUPKG_FILE` 。

如果您將 NuGet 套件上傳至 NuGet Gallery，可以使用 dotnet new --install PACKAGEID 命令；其中 PACKAGEID 和 .csproj 檔案中的 \<PackageId> 設定相同。 此套件識別碼和 NuGet 套件識別碼相同。
    
### 卸載範本套件
無論是使用 nupkg 檔案直接或 NuGet Gallery 來安裝範本套件，移除範本套件都是一樣的。 使用您想要解除安裝之範本的 \<PackageId>。 您可以透過執行 `dotnet new --uninstall` 命令來取得已安裝範本的清單。
    
```bash=
$ dotnet new --uninstall

Template Instantiation Commands for .NET CLI

Currently installed items:
    AdatumCorporation.Utility.Templates
    Details:
      NuGetPackageId: AdatumCorporation.Utility.Templates
      Version: 1.0.0
      Author: Me
    Templates:
      Example templates: async project (consoleasync) C#
      Example templates: string extensions (stringext) C#
    Uninstall Command:
      dotnet new --uninstall AdatumCorporation.Utility.Templates
```
    
執行 `dotnet new --uninstall AdatumCorporation.Utility.Templates` 以卸載範本套件。 此命令會輸出已卸載哪些範本套件的相關資訊。
    

## 資料來源 
- [教學課程：建立專案範本](https://docs.microsoft.com/zh-tw/dotnet/core/tutorials/cli-templates-create-item-template#create-the-template-config)
- [dotnet 範本化 Wiki（template.json 檔案的詳細資訊）](https://github.com/dotnet/templating/wiki)
- [NuGet 套件和還原為 MSBuild 目標](https://docs.microsoft.com/zh-tw/nuget/reference/msbuild-targets)


