# 日本語版

## 概要
BMFontImporter.cs は、AngelCode BMFont 形式の .fnt ファイルからカスタムビットマップフォントを Unity にインポートするためのスクリプトです。本スクリプトは、特に CJK など多数の文字を含むフォントの作成に適しており、Unity Default Font システムの拡張として、動的フォントが大量文字処理で抱える性能・メモリの問題を、あらかじめ生成したビットマップフォントデータで解決します。Unity 5.6.4f1 環境下では、外部 BMFont データを解析し、各文字の UV 座標、オフセット、基線補正を計算、そしてその結果を CustomFont アセットの Character Rects フィールドに設定することで、手動設定の手間を大幅に削減します。

> **※リリース済みのゲームのフォント置換（例：サードパーティローカライズ）の場合は、下記「既にリリース済みゲームのフォント置換」もご参照ください。**

## 必要な準備物
1. ビットマップテクスチャファイル（通常は .dds または .png）と、AngelCode BMFont のフォントインデックスファイル  
   （[Bitmap Font Generator](https://www.angelcode.com/products/bmfont/) などで生成）
2. インストール済みの Unity Editor
3. リポジトリから BMFontImporter.cs ファイルをダウンロード

## 成果物
Unity Editor 上で利用可能な、カスタム Unity Default Font のビットマップフォント

## 使用手順

### 1. プロジェクトの作成とアセットのインポート
1. Unity で新規プロジェクトを作成し、起動する。
2. BMFontImporter.cs、生成済みの .fnt ファイル、及び対応するテクスチャファイルを Assets フォルダにインポートする。
3. Assets フォルダで右クリックし、**Create → Custom Font** を選択して新規 CustomFont アセットを作成し、適切な名前を付ける。
4. 同じく、Assets フォルダで右クリックし、**Create → Material** を選択して新しい Material を作成する。
5. Hierarchy パネルで **Create → Create Empty** を選び、空の GameObject（例：FontManager）を作成。Inspector で BMFontImporter.cs を **Add Component** またはドラッグ＆ドロップで追加する。
6. Hierarchy パネルで **Create → 3D Object → 3D Text** を選び、Text オブジェクトを作成する。

### 2. アセットの設定調整
1. **テクスチャ設定**  
   - Assets フォルダでテクスチャファイルを選択し、Inspector で以下を確認：  
     - **Read/Write Enable**：チェック（必須）  
     - **Wrap Mode**：Clamp（または元の設定）  
     - **Filter Mode**：Bilinear（または元の設定）  
   - 設定後、**Apply** をクリック。
2. **マテリアル設定**  
   - 作成した Material を選択し、Inspector で：  
     - **Shader** を Unlit/Transparent に設定  
     - テクスチャファイルを Material の None (Texture) フィールドにドラッグしてバインドする。
3. **フォント設定**  
   - 作成した CustomFont アセットを選択し、Inspector で：  
     - **Line Spacing** を、使用するフォントサイズ（または元の設定）に合わせる  
     - 先に作成した Material をフォントにバインドする。
4. **Text オブジェクト設定**  
   - Hierarchy で Text オブジェクトを選び、Inspector で：  
     - Rect Transform の Height を、制作するフォントサイズに設定（文字位置確認用）  
     - Text コンポーネントにテスト文字列（CJK を含む）を入力する  
     - CustomFont アセットを Text コンポーネントの Font フィールドに割り当てる。
5. **BMFontImporter スクリプト設定**  
   - Hierarchy で FontManager を選択し、Inspector で BMFontImporter.cs の各フィールドに対して：  
     - **Custom Font** に CustomFont アセットを割り当てる  
     - **Font Texture** にテクスチャファイルを割り当てる  
     - **Fnt File** に .fnt ファイルを割り当てる。

### 3. スクリプトの適用
1. Ctrl+P または再生ボタンをクリックしてシーンを実行する。  
   スクリプトが .fnt ファイルを解析し、文字データを CustomFont の Character Rects に設定する。
2. Scene ビューにテスト文字列が新フォントで表示されれば、インポート成功です。

**※すでにリリース済みのゲームのフォントを置き換える予定がある場合（第三者によるローカライズ作業など）、以下の「リリース済みゲームのフォントの置き換え／テストフォント、暫定ゲームの構築とフォントのエクスポート」セクションを続けてお読みください。**

## 既にリリース済みゲームのフォント置換

> この部分は以下の場合に該当する方へ：  
> 1. 置換対象のフォントが、Unity Default Font のビットマップフォーマットである。  
> 2. TrueType フォントの差し替えが無効、または存在しない、もしくは AssetStudio で確認した際に TextMeshPro の特徴（MonoBehaviour など）が見られない場合。  
> 3. その場合、本プロジェクトの手法で必要なフォントを生成し、対応ファイルを作成してリリース済みゲームの資源を置換します。  
> 4. 基本的な流れは、元フォントの情報を抽出し、類似の機能・属性を持つフォントを特定、Unity Editor を用いた一時テストプロジェクトで各資源を適切に処理後、元ゲームに導入する方法です。

### 必要な準備物
1. ゲームに導入する TrueType フォントファイル。
2. [AssetStudio](https://github.com/Perfare/AssetStudio/)（または [zhangjiequan/AssetStudio](https://github.com/zhangjiequan/AssetStudio/)）を使用し、元フォントの Texture2D（テクスチャ）の以下の属性を確認：
   - Format（フォーマット）
   - Filter Mode（フィルターモード）
   - Wrap Mode（ラップモード）
   - Channels（チャンネル）
3. 元フォントテクスチャの配色タイプを確認：
   - 白字＋不透明な黒背景
   - 黒字＋不透明な白背景
   - 白字＋透明背景
   - 黒字＋透明背景
4. [UABEAvalonia](https://github.com/nesrak1/UABEA) 等のツールで、元フォントの Font と Texture のテキストファイルを Export Dump 方式で出力（複数フォントの場合は、フォント名ごとにフォルダ分け推奨）。
5. [Bitmap Font Generator](https://www.angelcode.com/products/bmfont/) をダウンロード。
6. リポジトリ内の CustomFontConfig.bmfc 設定ファイルをダウンロード。
7. （任意）生成するフォントを TrueType フォントの一部の文字（例：頻出漢字）のみにする場合、対象文字を含むテキストファイル（UTF-8 BOM または UTF-16 BOM 形式）を用意。

### 手順
1. **フォントファイルのインストール**  
   対象の TrueType フォントをシステムにインストールする。複数のウェイトが存在する場合、必要なウェイトのみ残し、他は一時的にアンインストールして Bitmap Font Generator が正しく認識できるようにする。
2. **設定の読み込み**  
   Bitmap Font Generator を起動し、**Options → Load Configuration** を選択して CustomFontConfig.bmfc を読み込む。
3. **フォントパラメータの設定**  
   **Options → Font Settings** をクリックし、以下を確認・調整：
   - **Font**：対象フォントに変更
   - **Size (px)**：対象フォントサイズ（ピクセル単位）に変更
   - **Match char height**：チェック推奨
   - **Super sampling**：チェックのまま、Level を 4 に設定
   - その他はデフォルトまたは必要に応じて調整。完了後、**OK** をクリックして保存。
4. **エクスポートオプションの設定**  
   **Options → Export Options** をクリックし、以下を確認：
   - Padding は 2 に設定（文字周辺にノイズが発生する場合は、適宜増加）
   - **Width** と **Height** は必要に応じて調整。Unity 5 などの旧バージョンでは最大 8192×8192 ピクセルまで対応
   - **Bit depth** は通常 32 に設定
   - Presets の通道設定を、元ファイルに合わせて調整：
     - 白字＋透明背景 → White text with alpha  
     - 黒字＋透明背景 → Black text with alpha  
     - 白字＋不透明黒背景 → White text on black (no alpha)  
     - 黒字＋不透明白背景 → Black text on white (no alpha)
   - **File/Font** は Text 形式を選択
   - 元ファイルの Format に合わせて、Textures と Compression（例：DXT1 の場合は dds と DXT1）を設定。完了後、**OK** をクリック。
5. **（任意）部分文字のインポート**  
   部分文字のみ生成する場合は、**Edit → Select Chars From File** で対象文字を含むテキストファイル（UTF-8 BOM または UTF-16 BOM）を読み込む。
6. **フォントのプレビュー**  
   Bitmap Font Generator 内の左側 Unicode ビューおよび右側の選択パネルでフォント状態を確認し、問題なければ **Options → Visualize** をクリックしてフォントテクスチャをプレビューする。  
   プレビューウィンドウのタイトルが “Preview: 1/1” となっている場合、単一テクスチャに全文字が収まっていることを意味する。  
   複数テクスチャが生成された場合は、テクスチャサイズの調整または文字数の削減が必要です。
7. **フォントの保存**  
   プレビューを閉じ、**Options → Save bitmap font as...** をクリックして、生成されたテクスチャファイルと .fnt フォントインデックスファイルを保存する。
8. **基線補正**  
   正しい表示を実現するため、生成された .fnt ファイルをテキストエディタで開き、`base=` の値（整数）を修正して保存する。  
   > 私の経験では、適切な base 値は lineHeight の約 0.2 倍（四捨五入した値）が目安です。
9. 上記の手順で、必要なフォントテクスチャと .fnt 設定ファイルが生成されます。次の工程に進む前に、本プロジェクトの上記「必要な準備物」の内容から確認してください。

## テストフォント、暫定ゲームの構築とフォントのエクスポート
### 成果物
Export Dump 方法でエクスポートされた新しいフォントの Font および Texture 2D のテキストファイル。

### 手順

#### 1. Text 内の文字位置の確認
1. Hierarchy パネルで作成済みの Text オブジェクトを選択し、Inspector の Text コンポーネントで表示される文字が灰色の境界（Rect Transform）内に収まり、垂直方向に中央揃えになっているか確認する。
2. もし文字全体が高すぎる場合は、.fnt ファイル内の `base=` の値を小さく、低すぎる場合は大きく調整する。
3. より正確に調整するため、Scene パネルを拡大して必要なオフセット量を測定・記録し、適宜修正する。

#### 2. プロジェクトの保存と暫定ゲームのビルド
1. Ctrl+S またはメニューバーの **File → Save Project** でプロジェクトを保存し、すべての変更が反映されていることを確認する。
2. メニューバーの **File → Build Settings** を開き、ターゲットプラットフォーム（例：PC、Mac & Linux Standalone）を選択する。
3. 「Add Open Scenes」をクリックして、現在のシーンをビルドリストに追加する。
4. 「Build」ボタンをクリックし、保存先を指定して暫定ゲームをビルドする。

#### 3. 作成したフォントのエクスポート
1. ビルドされた暫定ゲームの `GameName_Data` フォルダを開く。
2. UABEAvalonia などのツールを使用して、暫定ゲーム内の `resources.assets` ファイルを開く。
3. Export Dump 方式を用いて、新しく作成されたフォントの Font と Texture 2D のテキストファイルをエクスポートする（元のフォントと混同しないよう注意する）。

---

# English Version

## Overview
BMFontImporter.cs is a Unity script designed to import custom bitmap fonts from AngelCode BMFont-formatted .fnt files. This script is especially useful for creating fonts with a large number of characters (such as CJK fonts) and serves as an extension to the Unity Default Font system. By pre-generating bitmap font data, it overcomes the performance and memory limitations of dynamic fonts when handling extensive character sets. In Unity 5.6.4f1, the script parses external BMFont data to calculate each character's UV coordinates, offsets, and baseline adjustments, then populates the Character Rects field of a CustomFont asset—greatly reducing manual configuration work.

> **If you plan to replace fonts in a released game (for third-party localization, for example), please refer to the “Replacing Fonts in Released Games” section below.**

## Requirements
1. A bitmap texture file (typically .dds or .png) and an AngelCode BMFont index file, which can be generated using [Bitmap Font Generator](https://www.angelcode.com/products/bmfont/).
2. A working installation of the Unity Editor.
3. Download the BMFontImporter.cs file from the repository.

## Final Product
A custom Unity Default Font bitmap font that can be used in the Unity Editor.

## Steps to Use

### 1. Create a Project and Import Assets
1. Create and open a new Unity project.
2. Import BMFontImporter.cs, the generated .fnt file, and the corresponding texture file into the Assets folder.
3. In the Assets folder, right-click and select **Create → Custom Font** to create a new CustomFont asset, and name it appropriately.
4. In the Assets folder, right-click and select **Create → Material** to create a new Material.
5. In the Hierarchy panel, select **Create → Create Empty** to create an empty GameObject (recommended name: FontManager), then add the BMFontImporter.cs script to this GameObject via **Add Component** or by dragging and dropping.
6. In the Hierarchy panel, select **Create → 3D Object → 3D Text** to create a Text object.

### 2. Adjust Asset Properties
1. **Texture Settings:**  
   - Select the texture file in the Assets folder and verify in the Inspector that:  
     - **Read/Write Enabled:** is checked (essential for font replacement)  
     - **Wrap Mode:** is set to Clamp (or matches the original file)  
     - **Filter Mode:** is set to Bilinear (or matches the original file)  
   - Click **Apply** after making changes.
2. **Material Settings:**  
   - Select the created Material, and in the Inspector:  
     - Set the **Shader** to Unlit/Transparent  
     - Drag the texture file into the Material’s None (Texture) field to bind it.
3. **Font Settings:**  
   - Select the CustomFont asset in the Assets folder and in the Inspector, set:  
     - **Line Spacing:** to the desired font size (or match the original file)  
     - Bind the Material created earlier to the font.
4. **Text Object Settings:**  
   - In the Hierarchy, select the Text object, and in the Inspector:  
     - Set the Height in the Rect Transform to match the desired font size (for checking alignment)  
     - Enter a test string (recommended to include CJK characters) in the Text component  
     - Assign the CustomFont asset to the Font field of the Text component.
5. **BMFontImporter Script Settings:**  
   - In the Hierarchy, select the FontManager GameObject, and in the Inspector assign:  
     - The CustomFont asset to the **Custom Font** field  
     - The texture file to the **Font Texture** field  
     - The .fnt file to the **Fnt File** field

### 3. Apply the Script
1. Run the scene by pressing Ctrl+P or clicking the Play button.  
   The script will parse the .fnt file and populate the CustomFont’s Character Rects with the character data.
2. If the test string appears in the Scene view with the new font, the import is successful.

*If you plan to replace the font in a published game (such as for third-party localization work), please continue reading the "Replacing Fonts in Released Games / Testing the Font, Building a Temporary Game, and Exporting the Font" section below.*

## Replacing Fonts in Released Games

> This section is intended for situations where:
> 1. The target font to be replaced is a bitmap version of the Unity Default Font.
> 2. Replacing the TrueType font file is ineffective or the TrueType file does not exist; or if AssetStudio shows that the font has an associated texture but does not exhibit TextMeshPro characteristics (e.g., lacking the corresponding MonoBehaviour). In such cases, this solution may be more appropriate.
> 3. In this scenario, we use the methods described in this project to create the required font and generate the corresponding files for replacing the assets in the released game.
> 4. The general approach is: first extract the relevant information from the existing font, then identify a font with similar functions and properties, and finally import it into the original game’s assets via a temporary test project in the Unity Editor for proper asset processing.

### Required Materials
1. The TrueType font file you wish to import into the game.
2. Use [AssetStudio](https://github.com/Perfare/AssetStudio/) (or an updated fork like [zhangjiequan/AssetStudio](https://github.com/zhangjiequan/AssetStudio/)) to examine the original font’s Texture2D and record the following properties:
   - Format
   - Filter Mode
   - Wrap Mode
   - Channels
3. Determine the color scheme of the original font texture:
   - White text with opaque black background
   - Black text with opaque white background
   - White text with transparent background
   - Black text with transparent background
4. Use tools like [UABEAvalonia](https://github.com/nesrak1/UABEA) to export the original font’s Font and Texture text files via Export Dump (if exporting multiple fonts, store them in separate folders named by font to avoid confusion).
5. Download the Bitmap Font Generator from [here](https://www.angelcode.com/products/bmfont/).
6. Download the CustomFontConfig.bmfc configuration file from the repository.
7. (Optional) If you wish to generate a font that includes only a subset of characters from the TrueType file (e.g., common Chinese characters), prepare a text file containing those characters (encoded in UTF-8 BOM or UTF-16 BOM).

### Steps
1. **Install the Font File**  
   Install the target TrueType font on your computer. If multiple weights exist, temporarily uninstall those not needed so that Bitmap Font Generator can correctly identify the target weight.
2. **Load Configuration**  
   Open Bitmap Font Generator, then go to **Options → Load Configuration** and select CustomFontConfig.bmfc to import the configuration.
3. **Set Font Parameters**  
   Click **Options → Font Settings** and adjust the following:
   - **Font:** Change to the target font.
   - **Size (px):** Set to the desired size (in pixels) of the target font.
   - **Match char height:** Recommended to keep checked.
   - **Super sampling:** Keep checked with level set to 4.
   - Leave other options at default or adjust as needed, then click **OK** to save.
4. **Set Export Options**  
   Click **Options → Export Options** and adjust:
   - Padding is set to 2 (increase if edge artifacts appear).
   - Adjust **Width** and **Height** as needed; note that older versions of Unity (e.g., Unity 5) support a maximum texture size of 8192×8192 pixels.
   - **Bit depth:** Keep at 32 unless special requirements exist.
   - Adjust the Presets for channel settings based on the original file:
     - White text with alpha → White text with alpha  
     - Black text with alpha → Black text with alpha  
     - White text on black (no alpha) → White text on black (no alpha)  
     - Black text on white (no alpha) → Black text on white (no alpha)
   - Choose **File/Font** as Text format.
   - Set Textures and Compression based on the original Format (e.g., for DXT1, choose dds and DXT1). Click **OK** to save.
5. **(Optional) Import Partial Characters**  
   If generating a font with only a subset of characters, use **Edit → Select Chars From File** to import the text file (UTF-8 BOM or UTF-16 BOM).
6. **Preview the Font**  
   Use the left Unicode view and right-side selection panel in Bitmap Font Generator to adjust and inspect the font. When satisfied, click **Options → Visualize** to preview the font texture.  
   - Ensure only one texture is generated (the preview window title should read “Preview: 1/1”).  
   - If multiple textures are generated, a single texture is insufficient; adjust texture size in **Options → Font Settings** or reduce the number of characters.
7. **Save the Generated Font**  
   Close the preview window, then click **Options → Save bitmap font as...** to save the texture file and the .fnt index file.
8. **Baseline Adjustment**  
   To ensure correct display, manually adjust the baseline. Open the .fnt file in a text editor and change the value following `base=` to an integer, then save.  
   > In my experience, an appropriate base value is roughly the lineHeight multiplied by 0.2 (rounded).
9. After these steps, you will have the necessary font texture and .fnt configuration files. Please continue reading the "Requirements" section above and proceed with the subsequent steps.

## Testing the Font, Building a Temporary Game, and Exporting the Font
### Final Product
Font and Texture 2D text files of the new font exported via the Export Dump method.

### Steps
#### 1. Check the Text Position in the Text Object
1. In the Hierarchy panel, select the created Text object and verify in the Inspector that the text displayed by the Text component is contained within the gray boundary (Rect Transform) and is vertically centered.
2. If the characters appear too high, try reducing the `base=` value in the .fnt file; if they appear too low, increase it accordingly.
3. For precise adjustment, zoom in on the Scene view to measure and record the required offset, then adjust as needed.

#### 2. Save the Project and Build a Temporary Game
1. First, save the project by pressing Ctrl+S or selecting **File → Save Project** from the menu to ensure all changes are saved.
2. Open **File → Build Settings** from the menu and select your target platform (e.g., PC, Mac & Linux Standalone).
3. Click **Add Open Scenes** to add the current scene to the build list.
4. Finally, click the **Build** button, choose a save location, and build the temporary game.

#### 3. Export the Created Font
1. Open the `GameName_Data` folder of the built temporary game.
2. Using tools such as UABEAvalonia, open the `resources.assets` file of the temporary game.
3. Export the Font and Texture 2D text files of the newly created font using the Export Dump method (be careful not to confuse them with the original font).

---

# 中文版

## 概述
BMFontImporter.cs 是一个用于从 AngelCode BMFont 格式的 .fnt 文件中导入自定义位图字体的 Unity 脚本。该脚本特别适用于制作字符数量庞大的字体（例如 CJK 字体），并作为对 Unity Default Font 系统的扩展方案，通过预先生成位图字体数据来克服动态字体在处理大量字符时的性能和内存限制。在 Unity 5.6.4f1 中，脚本解析外部 BMFont 数据，计算每个字符的 UV 坐标、偏移及基线补正，并将结果填入 CustomFont 资产的 Character Rects 字段，从而大幅减少手工配置的工作量。

> **如果您计划替换已发布游戏中的字体（例如进行第三方本地化工作），请先阅读下方“替换已经发布的游戏中的字体”部分。**

## 需要准备的内容
1. 位图纹理文件（通常为 .dds 或 .png）和 AngelCode BMFont 字体索引文件（可通过 [Bitmap Font Generator](https://www.angelcode.com/products/bmfont/) 生成）。
2. 已安装的 Unity Editor。
3. 下载仓库中的 BMFontImporter.cs 文件至本地。

## 获得成品
在 Unity Editor 中可使用的自定义 Unity Default Font 位图字体。

## 使用步骤

### 1. 创建项目并导入资产
1. 创建并打开一个 Unity 项目。
2. 将 BMFontImporter.cs、生成好的 .fnt 文件以及对应的纹理文件导入到 Assets 文件夹中。
3. 在 Assets 文件夹中，右键选择 **Create → Custom Font** 创建一个新的 CustomFont 资产，并命名。
4. 在 Assets 文件夹中，右键选择 **Create → Material** 创建一个新的 Material。
5. 在 Hierarchy 面板中，选择 **Create → Create Empty** 创建一个空 GameObject（推荐命名为 FontManager），然后在 Inspector 面板中通过 **Add Component** 或拖拽的方式将 BMFontImporter.cs 脚本添加到该对象上。
6. 在 Hierarchy 面板中，点击 **Create → 3D Object → 3D Text** 新建一个 Text 对象。

### 2. 调整资产属性
1. **纹理设置**  
   - 在 Assets 文件夹中选中纹理文件，在 Inspector 中确认：  
     - **Read/Write Enable** → 勾选（对于替换字体来说非常重要）  
     - **Wrap Mode** → 设置为 Clamp（或与原始文件相同）  
     - **Filter Mode** → 设置为 Bilinear（或与原始文件相同）  
   - 设置完成后，点击 **Apply**。
2. **材质设置**  
   - 选中创建的 Material，在 Inspector 中设置：  
     - **Shader** → 选择 Unlit/Transparent  
     - 将纹理文件拖拽至 Material 的 None (Texture) 区域进行绑定。
3. **字体设置**  
   - 选中创建的 CustomFont 资产，在 Inspector 中设置：  
     - **Line Spacing** → 设置为与所需字体大小相同（或与原始文件一致）  
     - 将创建的 Material 绑定到字体上。
4. **Text 对象设置**  
   - 在 Hierarchy 中选中创建的 Text 对象，在 Inspector 中：  
     - 将 Rect Transform 的 Height 设定为所需字体大小（便于检查文字位置）  
     - 在 Text 组件的 Text 字段中输入测试字符串（建议包含 CJK 字符）  
     - 将 CustomFont 资产分配到 Text 组件的 Font 字段。
5. **BMFontImporter 脚本设置**  
   - 在 Hierarchy 中选中 FontManager GameObject，在 Inspector 中将：  
     - CustomFont 资产分配到 **Custom Font** 字段  
     - 纹理文件分配到 **Font Texture** 字段  
     - .fnt 文件分配到 **Fnt File** 字段。

### 3. 应用脚本
1. 使用 Ctrl+P 快捷键或点击播放按钮运行场景。脚本将解析 .fnt 文件，并将字符数据填入 CustomFont 的 Character Rects 字段。
2. 若 Scene 面板中以新字体显示测试字符串，则说明导入成功。

***如果您计划替换已发布游戏中的字体（如进行第三方本地化工作），请继续阅读下方“替换已经发布的游戏中的字体/测试字体、构建临时游戏并导出字体”部分。***

## 替换已经发布的游戏中的字体

> 本部分面向以下情况：  
> 1. 您确定需要替换的字体为位图格式的 Unity Default Font。  
> 2. 或者您发现替换 TrueType 字体文件后无效，或者根本不存在 TrueType 文件；又或者通过 AssetStudio 预览时发现该字体带有纹理图片，但并不符合 TextMeshPro 字体的特征（如无相应 MonoBehaviour）。此时，本方案可能更适合您。  
> 3. 在这种情况下，我们将采用本项目介绍的方法生成所需字体，并制作相应文件，以便替换已发布游戏中的资源。  
> 4. 整体思路为：先提取原有字体相关信息，再选择功能和属性相似的字体，通过导入到原游戏资源中实现替换。通常需要借助 Unity Editor 制作临时测试项目，对各资源进行预处理。

### 需要准备的内容
1. 您希望导入到游戏中的 TrueType 字体文件。
2. 通过 [AssetStudio](https://github.com/Perfare/AssetStudio/)（或其他更新分支如 [zhangjiequan/AssetStudio](https://github.com/zhangjiequan/AssetStudio/)）查看原字体对应的 Texture2D（纹理图片），并记录以下属性：
   - Format（格式）
   - Filter Mode（过滤模式）
   - Wrap Mode（拉伸模式）
   - Channels（通道）
3. 观察确认原字体纹理的配色类型：
   - 白字＋不透明黑色背景  
   - 黑字＋不透明白色背景  
   - 白字＋透明背景  
   - 黑字＋透明背景
4. 通过 [UABEAvalonia](https://github.com/nesrak1/UABEA) 等工具，以 Export Dump 方式导出原字体的 Font 与 Texture 文本文件（若导出多个字体，建议按字体名称分别存放于不同文件夹中）。
5. 下载位图字体制作工具 [Bitmap Font Generator](https://www.angelcode.com/products/bmfont/) 至本地。
6. 下载仓库中的 CustomFontConfig.bmfc 配置文件至本地。
7. （可选）若您希望生成的字体仅包含 TrueType 字体文件中的部分字符（例如常用汉字），请准备包含这些字符的文本文件（要求以 UTF-8 BOM 或 UTF-16 BOM 格式编码）。

### 步骤
1. **安装字体文件**  
   将目标 TrueType 字体文件安装到您的计算机上。若系统中存在多个字重，请暂时卸载不需要的版本，仅保留目标字重，以确保 Bitmap Font Generator 能正确识别目标字体。
2. **加载配置**  
   打开 Bitmap Font Generator，依次点击 **Options → Load Configuration**，选择 CustomFontConfig.bmfc 导入配置文件。
3. **设置字体参数**  
   点击 **Options → Font Settings**，确认并调整以下设置：
   - **Font** → 更换为目标字体  
   - **Size (px)** → 更换为目标字体大小（单位：像素）  
   - **Match char height** → 建议保持勾选  
   - **Super sampling** → 保持勾选，level 设为 4  
   - 其他选项保持默认或根据需要调整。完成后点击 **OK** 保存。
4. **设置导出选项**  
   点击 **Options → Export Options**，确认以下设置：
   - Padding 已设为 2（若生成后边缘出现杂色，可适当增大 Padding 值）  
   - **Width** 与 **Height** 根据实际需求调整；注意 Unity 5 等旧版本中纹理最大支持 8192×8192 像素  
   - **Bit depth** → 保持为 32，除非有特殊需求  
   - 根据原有文件情况，调整 Presets 中的通道设置：  
     - 白字＋透明背景 → White text with alpha  
     - 黑字＋透明背景 → Black text with alpha  
     - 白字＋不透明黑色背景 → White text on black (no alpha)  
     - 黑字＋不透明白色背景 → Black text on white (no alpha)
   - **File/Font** 选择 Text 格式  
   - 根据记录的原文件 Format 参数，设置 Textures 与 Compression（例如，若为 DXT1，则选择 dds 格式与 DXT1 压缩）。完成后点击 **OK** 保存。
5. **（可选）导入部分字符**  
   若仅生成部分字符的字体，可通过 **Edit → Select Chars From File** 导入包含目标字符的文本文件（要求编码为 UTF-8 BOM 或 UTF-16 BOM）。
6. **预览字体效果**  
   在 Bitmap Font Generator 中，通过左侧的 Unicode 查看面板和右侧字符选择面板，调整或检查字体情况。确认无误后，点击 **Options → Visualize** 预览字体纹理。  
   > 预览窗口标题应为 “Preview: 1/1”，表示仅生成了一张纹理。若生成多张纹理，则说明单一纹理不足以容纳所有字符，此时需返回 **Options → Font Settings** 调整纹理尺寸，或减少字符数量。
7. **保存生成的字体**  
   关闭预览窗口后，点击 **Options → Save bitmap font as...**，保存生成的纹理文件和 .fnt 字体索引文件。
8. **基线补正**  
   为确保正确显示效果，请手动校正基线。使用文本编辑器打开生成的 .fnt 文件，将 `base=` 后的值（整数）修改后保存。  
   > 我的经验表明，适当的 base 值大约为 lineHeight 的 0.2 倍（或四舍五入后的值）。
9. 完成上述步骤后，您将获得所需的字体纹理文件和 .fnt 字体配置文件，请继续阅读上方的“需要准备的内容”部分，进行后续操作。

## 测试字体、构建临时游戏并导出字体
### 获得成品
通过 Export Dump 方式导出的新字体的Font 和 Texture 2D 文本文件。

### 步骤
#### 1. 检查文字在 Text 中的位置
1. 在 Hierarchy 面板中选中已创建的 Text 对象，检查 Inspector 中 Text 组件显示的文本是否位于灰色边界框（Rect Transform）内，并且垂直居中。
2. 如果发现字符整体位置偏高，则尝试在 .fnt 文件中减小 `base=` 值；如果偏低，则需要增大该值。
3. 为了更精确地调整，您可以放大 Scene 面板的视野，仔细测量并记录所需偏移量，然后进行相应调整。

#### 2. 保存项目并构建临时游戏
1. 首先，通过 Ctrl+S 或菜单栏 **File → Save Project** 保存当前项目，确保所有修改均已存盘。
2. 然后，打开菜单栏中的 **File → Build Settings**，选择目标平台（例如 PC、Mac & Linux Standalone）。
3. 点击 “Add Open Scenes” 将当前场景添加到 Build 列表中。
4. 最后，点击 “Build” 按钮，选择一个保存路径，构建临时游戏。

#### 3. 导出创建的字体
1. 打开构建好的临时游戏的 `GameName_Data` 文件夹。
2. 使用 UABEAvalonia 等工具打开临时游戏中的 `resources.assets` 文件。
3. 以 Export Dump 方式导出新创建的字体的 Font 和 Texture 2D 文本文件（注意不要与原有字体混淆）。
