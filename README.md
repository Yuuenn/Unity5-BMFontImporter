## 日本語

### 概要

BMFontImporter.cs は、AngelCode BMFont 形式の .fnt ファイルからカスタムビットマップフォントをインポートするための Unity スクリプトです。本スクリプトは、特に CJK など膨大な文字数を持つフォントの作成に適しており、Unity 5.6.4f1 環境下で、外部 BMFont データを解析して各文字の UV 座標、オフセット、そして基線補正を行い、その結果を CustomFont の Character Rects フィールドに反映させます。これにより、大量の手作業を大幅に削減できます。

### 使用方法

#### BMFont の生成

- **ツールの利用**
  Bitmap Font Generator などのツールを使用して、対象のフォント（例：CJK フォント）をエクスポートしてください。
- **設定ファイルの参照**
  リポジトリ内の「Unity5.6_CustomFont.bmfc」ファイルを参考にしてください。（Bitmap Font Generator の設定ファイルです）
- 各種オプションの確認
  - **Font Settings/Size (px) と Match Char Height**
    お使いのフォントに合わせてサイズを調整し、Match Char Height のチェックの有無を決定してください。
  - Export Options の確認
    - **Padding**
      各文字間に適度な隙間を設けるためのオプションです。Padding を 0 に設定すると、文字が重なってにじみが発生する場合があるため、設定を推奨します。
    - **Texture のサイズ**
      CJK フォントは通常、数千以上の文字を含むため、すべての文字が**1 枚のテクスチャ内に収まる**ようにしてください。Unity で 2 ページ以上の文字を処理した経験はありません。
    - **ビット深度**
      多くの場合、RGB の 3 つのチャネルを確保するために 32 ビット深度に設定します。
    - **プリセット**
      黒背景に白文字で透明チャンネルが不要な場合は、「White text on black (no alpha)」を推奨します。
    - **ファイル形式**
      Text 形式でエクスポートしてください。これにより .fnt テキストファイルが生成されます。CJK フォントは非圧縮の場合ファイルサイズが大きくなるため、透明度が不要であれば DXT1 圧縮がおすすめです。

#### Unity プロジェクト設定

1. Unity 5.6.4f1 で新規プロジェクトを作成する。
2. 生成された .fnt ファイルと対応するテクスチャファイル（例："Font.dds"）を Assets フォルダにインポートする。

#### カスタムフォントの作成

1. Unity 内で新規に CustomFont アセットを作成し、適切な名前を付ける。
2. シーン内に空の GameObject を作成し、そこに BMFontImporter.cs スクリプトを追加する（もしくは既存の GameObject にドラッグ＆ドロップで追加）。
3. Inspector パネルで、作成した CustomFont アセット、テクスチャ、.fnt ファイルをそれぞれ customFont、fontTexture、fntFile フィールドに割り当てる。
4. 黒背景に白文字のフォントの場合、正しく文字が反映されているか確認するために、Material を作成してフォントにバインドすることを推奨する。

#### 実行と検証

1. シーンを再生する（Unity の再生ショートカットは Ctrl+P です）。スクリプトが .fnt ファイルを解析し、各文字情報を Font アセットに適用します。
2. 作成したカスタムフォントを UI テキストまたは 3D テキストに設定し、表示結果を確認してください。

#### 注意事項

1. **基線補正**
   スクリプトは BMFont ファイルの common 行にある base パラメータを利用して文字の垂直位置を補正します。文字が上下にずれて表示される場合は、BMFont 生成ツールの設定や出力値を確認してください。UI/Text コンポーネントにフォントを適用し、文字とコンポーネントの枠との相対位置を確認することで、base パラメータの妥当性を判断できます。不適切な base 値は、ゲームの逆コンパイル後に UI 表示で上下ずれを引き起こす可能性があります。
2. **スクリプトの再適用**
   同じスクリプトを繰り返し適用しても反映されません。もし fnt ファイル（特に文字の位置）を変更した場合は、既存の CustomFont を削除してから再度スクリプトを実行し、CustomFont を再構築することを推奨します。

------

## English

### Overview

BMFontImporter.cs is a Unity script used to import custom bitmap fonts from AngelCode BMFont-formatted .fnt files. This script is especially suited for creating fonts with an extensive number of characters (such as CJK fonts). In Unity 5.6.4f1, the script parses external BMFont data to compute each character's UV coordinates, offsets, and baseline adjustments, then populates the Character Rects field of a CustomFont asset—thereby saving a significant amount of manual work.

### Usage

#### Generating BMFont

- **Using the Tool**
  Use tools like Bitmap Font Generator to export your target font (e.g., a CJK font).
- **Reference Configuration**
  Please refer to the Unity5.6_CustomFont.bmfc file in the repository (this is the configuration file for Bitmap Font Generator).
- Options to Note
  - **Options/Font Settings/Size (px) & Match Char Height**
    Adjust the size according to your requirements and decide whether to enable "Match Char Height."
  - Export Options – Confirm the Following:
    - **Padding:**
      This option ensures that a certain amount of space is left between characters when generating the bitmap. I recommend using padding because setting it to 0 has caused character overlapping and artifacts in my project.
    - **Texture Size:**
      Since CJK fonts often contain thousands of characters, ensure that all characters can **fit on a single texture**. I have no experience handling fonts that span more than one page (including exactly two pages) in Unity.
    - **Bit Depth:**
      Typically, a 32-bit depth is required to secure three RGB channels.
    - **Preset:**
      For fonts with white text on a black background where transparency is not needed, "White text on black (no alpha)" is recommended.
    - **File Format:**
      Export as Text, which will generate an .fnt text file. For CJK fonts, uncompressed files can be very large; if transparency is unnecessary, DXT1 compression is recommended.

#### Unity Project Setup

1. Create a new project in Unity 5.6.4f1.
2. Import the generated .fnt file and the corresponding texture file (e.g., "Font.dds") into the Assets folder.

#### Creating a Custom Font

1. Create a new CustomFont asset in Unity and give it an appropriate name.
2. Add the BMFontImporter.cs script to a GameObject in your scene. For example, you can create an empty GameObject and attach the script to it, or drag and drop the script onto an existing GameObject.
3. In the Inspector panel, assign your custom font asset, texture, and .fnt file to the customFont, fontTexture, and fntFile fields respectively.
4. For fonts with white text on a black background, it is recommended to create a Material and assign it to the font so you can verify that the characters have been imported correctly.

#### Running and Verifying

1. Play the scene (the shortcut in Unity is Ctrl+P). The script will parse the .fnt file and apply the character data to the Font asset.
2. Apply the custom font to UI Text or 3D Text components to check that it displays correctly.

#### Notes

1. **Baseline Adjustment**
   The script uses the "base" parameter from the common line of the BMFont file to adjust the vertical positioning of the characters. If the characters appear misaligned vertically, check the BMFont generator settings and output values. You can verify the appropriateness of the base parameter by creating a UI/Text component, applying the generated font, and observing the relative position of the text to the component's bounding box. An incorrect base value may lead to vertical misalignment in the UI after decompiling the game.
2. **Reapplying the Script**
   Reapplying the script will not have any effect. If you modify any parts of the fnt file (especially character positions), it is recommended to remove the existing CustomFont and re-run the script to generate a new CustomFont.

------

## 中文版
### 概述
BMFontImporter.cs 是一个用于从 AngelCode BMFont 格式的 .fnt 文件中导入自定义位图字体的 Unity 脚本。该脚本特别适用于制作字符数量庞大的字体（例如 CJK 字体），并作为对 Unity Default Font 系统的扩展方案，通过预先生成位图字体数据来克服动态字体在处理大量字符时的性能和内存限制。在 Unity 5.6.4f1 中，脚本解析外部 BMFont 数据，计算每个字符的 UV 坐标、偏移及基线补正，并将结果填入 CustomFont 资产的 Character Rects 字段，从而大幅减少手工配置的工作量。

***如果您计划替换已发布游戏中的字体（如进行第三方本地化工作），请先阅读下方“替换已经发布的游戏中的字体”部分。***

### 需要准备的内容
1. 位图纹理文件（通常是.dds或.png）和 AngelCode BMFont 字体索引文件，可使用 [Bitmap Font Generator](https://www.angelcode.com/products/bmfont/) 生成。
2. 已安装的 Unity Editor。
3. 下载仓库中的 BMFontImporter.cs 文件至本地。
### 获得成品
1. 在  Unity Editor 中可用的自定义 Unity Default Font 位图字体。
### 使用步骤
#### 创建项目并导入资产
1. 创建并打开一个 Unity 项目。
2. 将 BMFontImporter.cs、生成好的 .fnt 文件和对应的纹理文件导入到 Unity Editor 下方 Assets 文件夹中。
3. 在 Assets 文件夹中，右键选择 Create→Custom Font 创建一个新的 CustomFont 资产，并命名。
4. 在 Assets 文件夹中，右键选择 Create→Material 创建一个新的 Material。
5. 在左侧 Hierarchy 面板，选择 Create→Create Empty 创建一个空 GameObject（推荐将其命名为FontManager），然后在右侧 Inspector 面板通过 Add Component 或拖拽的方式将 BMFontImporter.cs 脚本添加到该对象上。
6. 在 Hierarchy 面板，点击 Create→3D Object→3D Text 新建一个 Text。
#### 调整资产属性
1. 在 Assets 文件夹中，单击纹理文件。在 Inspector 面板中确认纹理文件应用如下配置。
> Read/Write Enable → 勾选 （对于替换字体来说很重要！）
> Wrap Mode → Clamp（或与要替换的原始文件相同）
> Filter Mode → Bilinera（或与要替换的原始文件相同）
> 设置好之后，点击 Apply
2. 在 Assets 文件夹中，单击你创建的 Material。在 Inspector 面板中确认 Material 应用如下配置。
> Shader → Unlit/Transparent
> 将 Assets 文件夹中的纹理文件与  Material 绑定——可以将纹理文件拖拽至  Inspector 中的 None (Texture)  区域
3. 在 Assets 文件夹中，单击你创建的 Font。在 Inspector 面板中确认Font 应用如下配置。
> Line Spacing → 行高，等于或大于需要制作的字体大小（或与要替换的原始文件相同）
> 将 Assets 文件夹中的 Material 与字体绑定
4. 点击 Hierarchy 面板中你创建的 Text。在 Inspector 面板中确认Font 应用如下配置。
> Rect Transform 中的 Height 设定为您需要制作字体大小（为了方便检查文字的位置）
> Text Script/Text 中输入测试字符串（建议包含CJK字符）
> 将 Assets 文件夹中的 Font 分配至 Text Script/Character/Font ——可以将 Font 拖拽至  Inspector 中的 Text Script/Character/Font 字段
5. 点击 Hierarchy 面板中你创建的 GameObject（FontManager）。在 Inspector 面板中确认Font 应用如下配置。
> 将 Assets 文件夹中的 Font 分配至 Custom Font 字段。
> 将纹理文件分配至 Font Texture 字段。
> 将 .fnt 字体索引文件分配至 Fnt File 字段。
#### 应用脚本
1. 使用 Ctrl+P 快捷键（或点击播放按钮）运行场景。脚本会解析 .fnt 文件，并将字符数据填入 CustomFont 的 Character Rects 字段。
2. 若 Scene 面板以新字体显示出您的测试字符串，说明导入成功。

*如果您计划替换已发布游戏中的字体（如进行第三方本地化工作），请继续阅读下方“替换已经发布的游戏中的字体/测试字体并构建临时游戏”部分。* 

### 替换已经发布的游戏中的字体
> 本部分面向以下情况：
> 1. 您确定需要替换的字体为位图格式的 Unity Default Font。
> 2. 或者您发现替换 TrueType 字体文件后无效，或者根本不存在 TrueType 文件；又或者通过 AssetStudio 预览时发现该字体带有纹理图片，但并不符合 TextMeshPro 字体的特征（例如没有相应的 MonoBehaviour）。此时，本方案可能更适合您。
> 3. 在这种情况下，我们将采用本项目介绍的方法创建所需字体，并生成相应文件，以便替换已发布游戏中的资源。
> 4. 整体思路是：先提取原有字体的相关信息，然后找到功能和属性相似的字体，通过导入到原游戏资源中实现替换。需要通过 Unity Editor 制作临时测试项目，以便对各种资源进行预处理。

#### 需要准备的内容
1. 您希望导入到游戏中的 TrueType 字体文件。
2. 通过 [AssetStudio](github.com/Perfare/AssetStudio/)（或其他更新分支如 [zhangjiequan/AssetStudio](github.com/zhangjiequan/AssetStudio/)）查看原字体对应的 Texture2D（纹理图片），并记录如下属性：
  - Format（格式）
  - Filter Mode（过滤模式）
  - Wrap Mode（拉伸模式）
  - Channels（通道）
3. 观察确认原字体纹理的配色类型：
   - 白字+不透明黑色背景
   - 黑字+不透明白色背景
   - 白字+透明背景
   - 黑字+透明背景
4. 通过 [UABEAvalonia](https://github.com/nesrak1/UABEA) 等工具，以 Export Dump 方式导出原字体的 Font 和 Texture 文本文件（若导出多个字体，建议按字体名称分别存放于不同文件夹中，以免混淆）。
5. 下载位图字体制作工具 [Bitmap Font Generator](https://www.angelcode.com/products/bmfont/) 至本地。
6. 下载仓库中的 Unity5.6_CustomFont.bmfc 配置文件至本地。
7. （可选）如果您希望生成的字体仅包含 TrueType 字体文件中的部分字符（例如常用汉字），请准备包含这些字符的文本文件（要求以 UTF-8 BOM 或 UTF-16 BOM 格式编码）。
#### 步骤
1. **安装字体文件**
    将目标 TrueType 字体文件安装到您的计算机上。若系统中存在多个字重，请暂时卸载不需要的版本，仅保留目标字重，以确保 Bitmap Font Generator 能正确识别目标字体。

2. **加载配置**
    打开 Bitmap Font Generator，依次点击 Options → Load Configuration，选择 Unity5.6_CustomFont.bmfc 导入配置文件。

3. **设置字体参数**
    点击 Options → Font Settings，确认并调整以下设置：
	> Font → 更换为目标字体
	> Size (px) → 更换为目标字体大小（单位：像素）
	> Match char height → 建议保持勾选
	> Super sampling → 保持勾选，level → 4
	> 其他选项保持默认或根据需要调整。 完成后点击 **OK** 保存

4. **设置导出选项**
	点击 Options → Export Options，确认以下设置：
	> Padding 已设为 2（如生成后边缘出现杂色，可适当增大 Padding 值）
	> Width 和 Height 根据实际需要调整；注意 Unity 5 等旧版本中纹理最大支持 8192×8192 像素
	> Bit depth → 保持为 32，除非您有特殊需求
	> 根据原有文件情况，调整 Presets 中的通道设置
	>
	> > 白字+透明背景 → White text with alpha
	> > 黑字+透明背景 → Black text with alpha
	> > 白字+不透明黑色背景 → White text on black (no alpha)
	> > 黑字+不透明白色背景 → Black text on white (no alpha)
	>
	> **File/Font** 选择 Text 格式
	> 根据记录的原文件 Format 参数，设置 Textures 与 Compression（例如，若为 DXT1，则选择 dds 格式与 DXT1 压缩）。 完成后点击** OK** 保存。
5. **（可选）导入部分字符**
	若仅生成部分字符的字体，可通过 Edit → Select Chars From File 导入包含目标字符的文本文件（要求编码为 UTF-8 BOM 或 UTF-16 BOM）。
6. **预览字体效果**
	通在 Bitmap Font Generator 中，通过左侧的 Unicode 查看面板和和右侧 Unicode 区块选择面板，调整或检查字体情况。确认无误后，点击 Options → Visualize 预览字体纹理。在预览窗口中，请确认生成的纹理数量：
	> **仅生成了一张纹理**：预览窗口的标题**应为** Preview: 1/1
	> 若生成多张纹理，则说明单一纹理不足以容纳所有字符，此时需返回 Options → Font Settings 调整纹理尺寸；极端情况下，需要减少字符数量。
7. **保存生成的字体**
	关闭预览窗口后，点击 Options → Save bitmap font as...，保存生成的纹理文件和 .fnt 字体索引文件。
8. **基线补正** 
	为确保正确显示效果，请手动校正基线。用文本编辑器打开生成的 .fnt 文件，将 <code>base=</code> 后的值修改为**整数**后保存。
> 我的项目经验表明，适当的 base 值大约为 lineHeight 乘以 0.2 后的整数（或四舍五入后的结果）
9. 完成上述步骤后，您将获得所需的字体纹理文件和 .fnt 字体配置文件。**请继续阅读上方的“需要准备的内容”部分，继续后续操作**
