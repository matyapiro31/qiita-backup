持ち駒を横に置きたければhspaceやraiseboxを使う。
とりあえず自分の環境ではこれで良い。chessboardパッケージには結構色々なパッケージやフォントなどが必要だが、全部説明書に書いてあるので省略する。


```latex:chess.tex
\usepackage{chessboard}
\usepackage{chessfss}
\usepackage[inline, shortlabels]{enumitem}%改行なしでリスト化するためのパッケージ
\def\myfen{rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR}%FENの設定
\chessboard[setfen=\myfen]%ボードを表示
\raisebox{30ex}{\huge{\BlackQueenOnWhite\BlackQueenOnWhite}}%黒の駒を横に
\hspace{-8.5ex}{\huge{\WhiteBishopOnWhite}}%白のコマの位置を揃える。上の駒の数に合わせる。
\normalsize{}%フォントを元に戻す
\begin{enumerate*}
\item \figsymbol{K}f1\item \figsymbol{K}f8
\end{enumerate*}
```
これでこんな感じになる。
<img width="319" alt="chessboard" src="https://qiita-image-store.s3.amazonaws.com/0/44129/686e076d-4495-1e26-fef5-aacde04c9e1a.png">

棋譜の書き方は、enumitemパッケージを利用して横書きでナンバリングできるようにすると楽。棋譜中だと白黒を区別せずどちらも白を用いている。インクの節約だろうか。
