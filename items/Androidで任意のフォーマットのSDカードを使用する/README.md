#SDカードはFAT32
通常、Androidで使用されるのはFAT32ですが、AndroidはLinuxなので、その他のフォーマットも使用できます。
利用できるフォーマットは/proc/filesystemsに記述されています。
<div><b>

```bash:/proc/filesystems
nodev	sysfs
nodev	rootfs
nodev	bdev
nodev	proc
nodev	cgroup
nodev	tmpfs
nodev	debugfs
nodev	sockfs
nodev	pipefs
nodev	anon_inodefs
nodev	devpts
	    ext3
	    ext2
	    ext4
nodev	ramfs
	    vfat
	    exfat
nodev	ecryptfs
nodev	sdcardfs
	    fuseblk
nodev	fuse
nodev	fusectl
nodev	selinuxfs
nodev	oprofilefs
```
</font></b></div>
その他にも、FUSEを使用するNTFSやLUKSなどは利用できるはずです。(Linux kernelのバージョン・オプションによる。)
ただしFUSEについてはプログラムがインストールされていないのでそのままでは利用できません。
#コマンド
mountコマンドを使ってマウントできます。root権限が必要です。

```bash
#ext4の例
mount -w -t ext4 /dev/block/vold/179\:65 /mnt/media_rw/extSdCard
mount -o bind /mnt/media_rw/extSdCard /storage/extSdCard
mount -o bind /mnt/media_rw/extSdCard /mnt/secure/asec
```

自動マウントする場合、/etc/fstabではなく、/etc/vold.fstabに記述しましょう。

#問題点
どうもrootに変身せずにSDカードに書き込めるようにする方法が分かりません。いろいろ試していますが、chmodやchownでもうまく変更できないようです。
uid=1023,gid=1023でマウントできないからです。(ext2だといけるかも?)

```bash
#FAT32の場合
mount -w -t vfat -o uid=1023,gid=1023 /dev/block/vold/179\:65 /mnt/media_rw/extSdCard
#ext4だとuid,gidが設定できないのでrootでしかアクセスできない
mount -w -t ext4 -o uid=1023,gid=1023 /dev/block/vold/179\:65 /mnt/media_rw/extSdCard
#mount: Invalid argument & returns 255
```
ローカルにSambaを立てればいいのではないか、とか思いましたが、試してません。
#おまけ
ecryptfsが使えるみたいなので、rootアプリでフォルダ暗号化とかできそうです。
注釈:ecryptfsとはUbuntuのhomeフォルダの暗号化などで使われるフォルダ暗号化ファイルシステム(スタックファイルシステムに対する暗号化)です。
