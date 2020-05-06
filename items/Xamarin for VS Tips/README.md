1. Releaseビルドでエラーになる場合はPublish Android App...からキーを生成する
2. x86ビルドでエラーになるときはプロジェクトのプロパティからAndroid Options->Advancedでx86にチェックを入れる
3. msbuildでビルドする。例 Rebuild Debug Any CPU: `msbuild `*`solution.sln`*` /t:Rebuild /p:Configuration=Debug /p:Platform="Any CPU" ` ちなみに2秒ほど早い。インストールは[Xamarin Build Process Android](http://developer.xamarin.com/guides/android/under_the_hood/build_process/)を参考に。
4. msbuildのパスを環境変数Pathに加えておくとパッケージマネージャコンソールからビルドできる こちらではソリューションファイル名を指定する必要が（なぜか）必要ない 
5. インストールは`cd`できないので、どうやらソリューションのディレクトリらしいのでそこから相対パスで
`msbuild /t:Install `*`ProjectName\ProjectName.csproj`*` /p:AdbTarget="-s `*`your_device_id`*`"`
参考：http://developer.xamarin.com/guides/android/under_the_hood/build_process/#22-build-targets
AdbTargetの値はadbコマンドと同じだが、なぜか`-e`は効かなかった `-s`の場合は全体を""で括る必要がある

6. APKファイルに署名するには
`msbuild /p:Configuration=Release /t:SignAndroidPackage `*`ProjectName\ProjectName.csproj`*
とすればよい。

7. jarsignerがどうもおかしいので調べてみたら
>警告:
  -tsaまたは-tsacertが指定されていないため、このjarにはタイムスタンプが付加されていません。タイムスタンプがないと、署名者証明書の有効期限(2045-0X-XX)後または将来の失効日後に、ユーザーはこのjarを検証できない可能性があります。

と言われているらしい jarsignerのコマンドを直接変更できないので`doskey`コマンドでaliasしよう、と思ったが標準コマンド以外はどういう書式になるのかわからないので放置、というか証明書は40年も持つので問題ない
8. 国際化(i18n)
どうも標準ではShift_JISとかがサポートされないので、
ビルドオプションに`/p: MandroidI18n=All`を入れれば標準のUTF-8以外が入る。
9．プラットフォーム
Android向けのくせにCPU依存になってしまう。まあ旧Dalvik/ARTのC#版が一緒に入れないといけないからだが、全プラットフォーム対応にするとこれは容量が大きくなるという諸刃の剣になる。それぞれのプラットフォーム向けに複数作るのはいちいち手動でやらないとならない。
設定オプションは`/:AndroidSupportedAbis=armeabi;x86`とかでできる。
実際Mono　VMはAndroidのDalvik/ARTのAPIを無視して最新APIを使えるらしいので(未確認)、メガ単位ぐらいは問題にはならないが。
