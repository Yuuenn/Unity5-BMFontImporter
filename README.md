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

## 中文

### 概述

BMFontImporter.cs 是一个用于从 AngelCode BMFont 格式的 .fnt 文件中导入自定义位图字体的 Unity 脚本。该脚本特别适用于制作字符数量庞大的字体（例如 CJK 字体）。在 Unity 5.6.4f1 中，脚本通过解析外部 BMFont 数据，计算每个字符的 UV 坐标、偏移以及基线补正，并将结果填入 CustomFont 的 Character Rects 字段，从而大幅减少手工设置的工作量。

### 使用方法

#### 生成 BMFont

- **使用工具导出**
  请使用 Bitmap Font Generator 等工具导出目标字体（例如 CJK 字体）。
- **参考配置文件**
  请参考仓库中的 Unity5.6_CustomFont.bmfc 文件（Bitmap Font Generator 的配置文件）。
- 注意以下选项
  - **Options/Font Settings/Size (px) 与 Match Char Height**
    根据您的需求调整字体大小，并决定是否勾选 Match Char Height。
  - Options/Export Options 中请确认：
    - **Padding：**
      此选项在制作位图时会在每个字符之间留出一定空隙。推荐开启该选项，因为在我的项目中，将 Padding 设置为 0 会导致字符重叠并产生杂边。
    - **Texture 的尺寸：**
      由于 CJK 字体往往包含成千上万个字符，请确保所有字符**能包含在一张纹理上**。我没有处理过超过一页（包括恰好两页）的情况。
    - **位深度：**
      大多数情况下，建议设置为 32 位深度以确保获得 RGB 三个通道。
    - **预设：**
      对于黑底白字且不需要透明通道的情况，推荐选择 "White text on black (no alpha)"。
    - **文件格式：**
      请导出为 Text 格式，这样会生成 .fnt 文本文件。对于 CJK 字体，不压缩会导致文件体积过大，因此在不需要透明度的情况下建议使用 DXT1 压缩。

#### Unity 项目设置

1. 在 Unity 5.6.4f1 中创建一个新项目。
2. 将生成的 .fnt 文件和对应的纹理文件（例如 "Font.dds"）导入到 Assets 文件夹中。

#### 制作自定义字体

1. 在 Unity 中创建一个新的 CustomFont 资产，并为其命名。
2. 将 BMFontImporter.cs 脚本添加到场景中的任意 GameObject 上。建议新建一个空的 GameObject，然后通过 Add Component 或拖拽的方式将脚本添加到该对象上。
3. 在 Inspector 面板中，将自定义字体、纹理和 .fnt 文件分别赋值给 customFont、fontTexture 和 fntFile。
4. 对于黑底白字的字体，建议创建一个 Material 并绑定到该字体上，以便观察字符是否正确填入。

#### 运行与验证

1. 运行场景（Unity 中可使用 Ctrl+P 快捷键开始播放）。脚本会解析 .fnt 文件，并将字符信息应用到 Font 资产上。
2. 将自定义字体用于 UI 文本或 3D 文本组件，检查显示效果是否正常。

#### 注意事项

1. **基线补正**
   脚本使用 BMFont 文件 common 行中的 base 参数来校正字符的垂直位置。如果字符显示出现上下偏移，请检查 BMFont 生成器的设置和输出值。您可以通过创建一个 UI/Text 组件，应用制作好的字体，并观察文字与组件边框的相对位置，来判断当前的 base 参数是否合理。不合理的 base 参数可能会在游戏反编译后导致 UI 显示时出现上下偏移的问题。
2. **脚本的重新应用**
   重复应用脚本不会生效。因此，如果您修改了 fnt 文件（尤其是字符位置部分），建议删除现有的 CustomFont，然后重新运行脚本来生成新的 CustomFont。
