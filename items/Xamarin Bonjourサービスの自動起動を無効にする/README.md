Xamarinをインストールすると、Bonjour Web serviceが自動起動してしまいます。
これはXamarin.iOS向けですが、Xamarin.Androidしか使わない人には不要なサービスで、起動を遅くするだけです。
これを停止するには、Windows+Rからservices.mscを起動して、「Xamarin Bonjour Service」を右クリックし、プロパティから
スタートアップの種類を「手動」に変えます。
![キャプチャ2.PNG](https://qiita-image-store.s3.amazonaws.com/0/44129/fc4e3701-6180-7944-b247-480f0e4efdea.png)

参考画像
