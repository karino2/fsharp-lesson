---
title: "第一回 まずはライブラリをつついてみよう"
layout: page
---
<style>
   .column{
        padding: 0.5em 1em;
        margin: 2em 0;
        color: #5d627b;
        background: white;
        border-top: solid 5px #5d627b;
        box-shadow: 0 3px 5px rgba(0, 0, 0, 0.22);
    }
    .post-content h1 {
       font-size: 34px;
    }
</style>

F# での基本的な作業の進め方に慣れつつ、あとで使う事になる準備として、
ライブラリをつついてみたいと思います。

## セットアップ

NYI: dotnet coreでVSCodeでIonideのセットアップをしよう、的な事を誰かが書いてくれる。

## Deedleをつついてみよう

Deedleをつついてみましょう。

### fsharp-lesssonでのフォルダとブランチのルール

初回なので、少しfsharp-lesson全般の話をここでします。

まず作業は、fsharp-lessonのレポジトリ、[https://github.com/karino2/fsharp-lesson](https://github.com/karino2/fsharp-lesson)を各自がgithub上でforkして、それをローカルにcloneしたフォルダの上で作業する事にします。

ソースコードはsourcesの下のサブディレクトリに置く事にします。
第一回だったら、`sources/play_library/` というディレクトリがあると思うので、この下にそれぞれディレクトリをほって作業します。

また、少し通常のブランチの使い方と違いますが、添削の都合で各作業はブランチを切る事にします。
第一回のブランチ名は全て `play_library/` を前につけて、あとは数女の通し番号とどこかを表す名前を適当につけます。

例えばDeedleをいじってみるというこのブランチは`play_library/1_playdeedle` としましょう。
次のArguは`play_library/2_helloargu` とします。（2のラベル名はあとで変えるかも）

### PlayDeedleのブランチとプロジェクトの作成

それではまずはブランチとプロジェクトを作成してみます。
Deedleをいじってみるのはfsx上で行うのでプロジェクト無しでも良いのですが、
今後の開発の練習という事でPlayDeedleというプロジェクトを作る事にします。

まずは上記で説明した通り、`play_library/1_playdeedle`というブランチを切り、以後このブランチで作業します。

次にプロジェクトを作成します。場所は`sources/play_library/`の下に`PlayDeedle`というディレクトリが作られるようにプロジェクトを作ります。
以下のように実行しましょう。

```
$ cd sources/play_library
$ dotnet new console -lang "F#" -o PlayDeedle
```

この時にPlayDeedleというディレクトリは勝手に掘られるので、そのディレクトリは掘らないのがコツです。

プロジェクトの作り方の詳細については以下のドキュメントを見ましょう。

[Get Started with F# in Visual Studio Code - Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/fsharp/get-started/get-started-vscode)

### fsxでDeedleをつついてみる

プロジェクトを作ったら、最初にするのは、私の場合はScratch.fsxというファイルを作ってVSCodeでこのプロジェクトを開く事です。
fsharp-lessonでもそうしてみましょう。

```
$ cd PlayDeedle
$ touch Scratch.fsx
$ code .
```

これでVSCodeが開くはずです。

Scratch.fsxはとりあえず実験用の書き捨てのコードを置く場所にしています。

VSCode上でScratch.fsxを開いて、以下のように書いてみましょう。

```
#r "nuget:Deedle"

open Deedle
```

そしてそれぞれの行の終わりで Alt+Enterを押します（その行を実行というショートカットです）

この時、最初の実行ではDeedleという所が破線のままでエラーっぽく表示されたままになっているかもしれません。
これはfsxではちょくちょくある事なので、実行したあとに「Windowの再読み込み」というのをやります。

せっかくなのでもし破線が出てない人も、ここでWindowを再読み込みしてみましょう。

Windowの再読み込みは、「Cmd+Shift+P」を押してから、「Reload」と入力するとそれっぽい候補が出ると思うのでそれを選んでEnterを押して下さい。
なおCmdはWindowsだとCtrlになると思います（以後全部Macのショートカットで書くので適宜読み替えて下さい）

![reload_window](reload_window.png)

こうするとウィンドウごと再読み込みされて、破線が無くなると思います。

なお、再読み込みするとまっさらな状態になるので、先程と同様に、書いたスクリプトの2行のそれぞれの末尾でAlt+Enterして下さい。

### fsxとfs

F# には.fsのファイルと.fsxのファイルがあり、


## Arguをつついてみよう

## FParseをつついてみよう


