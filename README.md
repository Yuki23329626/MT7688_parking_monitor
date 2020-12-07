# MT7688 Duo parking monitor

## 一、讓 7688 同時開啟 wifi station mode 與 wifi ap mode

1. 將 firmware 刷成 0.93 的版本  
- 0.94 版本可能出現各種未知錯誤
2. 複製 wireless/ralink.sh 取代 /lib/netifd/wireless/ralink.sh  
- 請根據各自需求更改 \<SSID> 跟 \<KEY>  
例如：'uci set wireless.sta.ssid="\<SSID>"'，  
可以更改成 'uci set wireless.sta.ssid="HSNG-A2.4"'  
也就是你想讓你的 7688 連線過去的基地台的 SSID  
另外 wireless.ap.ssid  則是你想讓你的 7688 作為 AP 時想要叫的 SSID  
其他依此類推

```bash
cp wireless/ralink.sh /lib/netifd/wireless/
chmod +x /lib/netifd/wireless/ralink.sh
uci set wireless.sta.ssid="HSNG-A2.4"
uci set wireless.sta.key="hsng@root"
uci set wireless.ap.ssid="nxshen_7688"
uci set wireless.ap.key="123123123"
uci set wireless.sta.encryption="psk"
uci set wireless.sta.disabled="0"
uci commit wireless
wifi

```

## 二、擴充 7688 容量(將系統 Mount 在 SD card 上)

一些好處，例如下載大容量的 library 不會炸裂  

1. 登入 linkit smart 7688  
2. 輸入下方指令  

```bash
opkg update
opkg install block-mount kmod-fs-ext4 kmod-usb-storage-extras e2fsprogs fdisk

```

3. 記得插上 sd card，接下來會對 sd card 進行格式化為 ext4 檔案系統的動作  

```bash
mkfs.ext4 /dev/mmcblk0p1

```

4. 將 root FS 移至 SD 中

```bash
mount /dev/mmcblk0p1 /mnt
tar -C /overlay -cvf - . | tar -C /mnt -xf -
umount /mnt

```

5. 建立 fstab 樣板

```bash
block detect > /etc/config/fstab

```

6. 修改 fstab 設定檔

```bash
vi /etc/config/fstab

```

7. 修改相關設定

- 輸入【 i 】進入編輯模式
- 修改相關設定
- - 將 mount 區域中的 target 修改成【 /overlay 】
- - 將 mount 區域中的 enabled 修改成【 1 】
- 按 【 ESC 】再輸入【 :wq! 】存檔離開
- 重新啟動 LinkIt Smart 7688

8. 察看結果

```bash
df -h

```

## 三、個人偏好的套件

```bash
opkg install git
opkg install vim

```
