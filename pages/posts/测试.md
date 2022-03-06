

## (一)   测试工具（服务器）

/etc/systemd/system/natapp.service

|**编号**|**名称**|**版本**|**用途**|**工具状态**|
|:----|:----|:----|:----|:----|
|1            |Unixbench|5.1.3|测试操作系统综合处理能力。|开源工具|
|2            |Spec2006|2006|测试 CPU 性能。|商用软件|
|3            |iozone|4.3.0|测试磁盘读写性能（Mb/s），包括随机、顺序读写、最大最小和平均读写速度等。|开源工具|
|4            |Netperf|2.7.0|测试网络传输速率、网络吞吐率、网络响应时间等，包括 TCP、UDP 流吞吐速率、TCP 请求/响应、丢包率、误包率等。|开源工具|
|5            |SPECjvm2008|2008|测试 JAVA 虚拟机的性能。|开源工具|
|6            |LMbench|3.0|测试操作系统性能|开源工具|
|7            |LTP|LTP 20160510|测试系统稳定性|开源工具|
|  8|Stream|5.09|测试内存带宽性能|开源工具|
|  9| FIO  |2.1.10|测试磁盘读写性能|开源工具|

## (2)   uos

1. deb http://mirrors.163.com/debian stable main
2. 内核更新：
3. apt install linux-image* linux-header*
4. 卸载多余内核：
```plain
apt remove linux-image-5.6.0-2-arm64
```
5. deb https://uos.deepin.cn/uos eagle main contrib non-free
6. deb https://enterprise-packages.chinauos.com/server-enterprise fou/sp2 main contrib non-free
7. apt-key adv --keyserver keyserver.ubuntu.com --recv-keys
8. deb https://professional-store-packages.chinauos.com/appstore eagle appstore
9. 麒麟v10源：
```plain
deb http://archive.kylinos.cn/kylin/KYLIN-ALL 10.0 main restricted universe multiverse
deb http://archive.kylinos.cn/kylin/partner juniper main
```
10. 定制版uos：
```dockerfile
mkdir /tmp/livecd
mount -o loop uos*.iso /tmp/livecd
mkdir -p livecd/cd
rsync --exclude=/live/filesystem.squashfs -a /tmp/livecd/ livecd/cd
mkdir livecd/squashfs&&mkdir livecd/custom
modprobe squashfs
mount -t squashfs -o loop /tmp/livecd/live/filesystem.squashfs livecd/squashfs
cp -a livecd/squashfs/* livecd/custom
cp /etc/resolv.conf livecd/custom/etc/
cp /etc/apt/sources.list livecd/custom/etc/apt/sources.list
chroot livecd/custom apt install xrdp htop gnome-disk*
rm -rf livecd/custom/recovery*
mksquashfs livecd/custom ./filesystem.squashfs && chmod -R 777 ./*.squashfs
```
11. uos无需激活即可root：
```plain
nano /etc/pam.d/su
#auth    requisite           deepin_security_verify.so
```
12. 命令行激活试用：uos
```plain
uos-activator-cmd -t
```
13. 1
## (3) unixbench

1. wget http://github.itzmx.com/1265578519/unixbench/master/5.1.3/unixbench.sh
2. chmod 777 *.sh && ./unixbench.sh
3. 多核的话改一下run文件，第 109,110,111,112 行修改数字为当前系统 CPU 核数，然后./Run -c 核数
4. nano Makefile ，47行
```plain
1GL_LIBS = -lGL -lXext -lX11 lm
```
5. nano Run，111行
```plain
# '3d'  => { 'name' => "3D Graphics Benchmarks",'maxCopies' => 8 },
```
6. make all && ./Run graphics
7. 1
## (4) iozone

1.  wget http://www.iozone.org/src/current/iozone3_487.tar 
2.  tar -xvf iozone3_487.tar 
3.  cd iozone3_487/src/current/
4. make linux 
5. ./iozone -a -i 0 -i 1 -i 2 -f /mnt/testfile -r 16m -s 8G | tee -a iozone.log
6. ./iozone -a -i 0 -i 1 -i 2 -f /mnt/testfile -r 16m -s 16G | tee -a iozone.log
7. ./iozone -a -i 0 -i 1 -i 2 -f /mnt/testfile -r 16m -s 32G | tee -a iozone.log
8. wget [https://gitee.com/haolixu/test/raw/master/iozone.sh](https://gitee.com/haolixu/test/raw/master/fio.sh) && bash iozone.sh
## (5) 远程桌面

1. apt install  xrdp
2. apt install  gnome*
3. echo "gnome-session --session=gnome-classic" > ~/.xsession
4. /etc/init.d/xrdp start
```
19.12.23更新！
apt install xdm* 
不用选xdm即可远程
20.1.15更新！
apt install dde*
可恢复deepin桌面！！
```
## (6) fio

1. apt install fio*
2. dd if=/dev/zero  of=/root/test bs=4k count=524288
3. fio --filename=/root/test -iodepth=64 -ioengine=libaio --direct=1 --rw=read --bs=1m --size=2g --numjobs=4 --runtime=10 --group_reporting --name=test-read
4. fio -filename=/root/test -iodepth=64 -ioengine=libaio -direct=1 -rw=write -bs=1m -size=2g -numjobs=4 -runtime=20 -group_reporting -name=test-write
5. fio -filename=/root/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randread -bs=4k -size=2G -numjobs=64 -runtime=20 -group_reporting -name=test-rand-read
6. fio -filename=/root/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=2G -numjobs=64 -runtime=20 -group_reporting -name=test-rand-write
7. wget [https://gitee.com/haolixu/test/raw/master/fio.sh](https://gitee.com/haolixu/test/raw/master/fio.sh) && bash fio.sh


## (7) SPECjvm2008

1. wget http://spec.cs.miami.edu/downloads/osg/java/SPECjvm2008_1_01_setup.jar
2. apt install openjdk-8-jre-headless
3. update-alternatives --config java
4. nano   /etc/profile
```
JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-arm64/
CLASSPATH=$JAVA_HOME/lib/tools.jar:/lib.dt.jar
PATH=$JAVA_HOME/bin:$PATH
export JAVA_HOME CLASSPATH PATH
```
5. source /etc/profile
6. java -jar   SPECjvm2008_1_01_setup.jar   -i   console
7. nano   /etc/java-8-openjdk/accessibility.properties
```
#assistive_technologies=org.GNOME.Accessibility.AtkWrapper
```
8. java -jar SPECjvm2008.jar --base
9. java -jar SPECjvm2008.jar --peak
## (8) LTP

1. 麒麟v10 1231版本修改：
```plain
nano ltp/testscripts/ltpstress.sh #250行
 TOTALRAM=$(LC_ALL=C free -m | grep Mem: | awk {'print $2'})
  TOTALSWAP=$(LC_ALL=C free -m | grep Swap: | awk {'print $2'})
  TESTSWAP=$(($TOTALSWAP / 2))
  if [ $TESTSWAP -eq 0 ]; then
       #if there is no swap in the system, use only the free RAM
       TESTMEM=$(LC_ALL=C free -m | grep Mem: | awk {'print $4'})
```
2. wget    https://github.com/linux-test-project/ltp/releases/download/20190930/ltp-full-20190930.tar.bz2
3. cd   ltp-full-20190930
4. apt install gcc g++ gfortran
5. ./configure &&  make && make install
6. ./ltpstress.sh –n –t 12 –l /root/ltpstress.log
```plain
The NFS related tests should fail because network stress was disabled,去掉这个 bug：
./ltpstress.sh –n –t 12 
```

## (9) web性能

1. http://html5test.com
2. http://CSS3test.com
3. https://www.browserbench.org/JetStream/
4. https://webkit.org/perf/sunspider-1.0.2/sunspider-1.0.2/driver.html
## (10) stream

1. mkdir stream && cd stream
2. wget https://www.nersc.gov/assets/Trinity--NERSC-8-RFP/Benchmarks/Jan9/stream.tar
3. tar -xvf stream.tar
4. gcc -O2 -DSTREAM_ARRAY_SIZE=1000000000 stream.c -o stream
5. ./stream
## (11) Lmbench

1. wget   http://106.54.231.176/tar/lmbench.tar
2. tar -xvf lmbench.tar && cd lmbench
3.  cd lmbench3/src && make
4. cd  ../scripts && ./config-run
5. ./results
6. make see | tee -a lmbench.log
## (12) Netperf，iperf

1. wget   http://106.54.231.176/tar/netperf-2.7.0.tar
2. tar -xvf  netperf-2.7.0.tar  &&  cd  netperf-netperf-2.7.0
3. ./configure && make  && make install
4. netserver
5. netperf -H 192.168.4.11 -l 60 -t TCP_STREAM | tee -a netperf.log
6. 测延迟：
7. ./cepreiperf-ARM  192.168.124.13 tcp_lat
8. ./cepreiperf-ARM  192.168.124.13 udp_lat
## (13) 图形稳定性测试

1. apt install mesa-utils
2. ## (14) 宝德OS！！！

1. 安装文件管理器，图片查看器：
```plain
apt-get install nautilus gpicview
```
2. 网络连接图标异常：
```plain
nano  /etc/NetworkManager/NetworkManager.conf
managed=true
```
3. 更改grub壁纸和开机logo：
```plain
apt install plymouth plymouth-themes
cd /usr/share/desktop-base/futureprototype-theme/
cd /usr/share/plymouth/themes/futureprototype
plymouth-set-default-theme -R futureprototype
```
4. 更改桌面壁纸，对应用户执行：
```plain
gsettings set org.gnome.desktop.background picture-uri 'file:///baode/Flowerbed.png'
```
5. 安装桌面：
```css
apt-get install gnome-shell gnome-panel gnome-menus gnome-session gdm3
apt-get install kde-plasma-desktop
apt install x-window-system-core gnome-core  
apt install lightdm
apt install sddm
apt install cinnamon
apt install lxde
```
6. 更改登陆界面logo：
```plain
nano /etc/gdm3/greeter.dconf-defaults
[org/gnome/login-screen]
logo='/baode/logo.png'
dpkg-reconfigure gdm3
```
7. 添加自动登陆（只有gdm）：
```plain
nano /etc/gdm3/daemon.conf
AutomaticLogin = guest
```
8. 安装其他终端：
```plain
apt install xfce4-terminal
```
9. 安装程序：
```plain
apt install calamares-settings-debian
```
10. 添加桌面图标：
```plain
apt  install nemo
nano ~/.config/autostart/nemo.desktop
[Desktop Entry]
Type=Application
Name=Desktop Icons
Exec=nemo-desktop
OnlyShowIn=GNOME;
NoDisplay=true
X-GNOME-Autostart-Phase=Desktop
X-GNOME-Autostart-Notify=true
X-GNOME-AutoRestart=true
X-GNOME-Provides=filemanager

gsettings set org.nemo.desktop show-desktop-icons true
如果执行不了，root下执行su -重新运行。
```
11. live cd：
```python
apt-get install -y     \
    debootstrap             \
    squashfs-tools          \
    xorriso                 \
    grub-efi-arm64-bin      \
    mtools

debootstrap            \
     --arch=arm64           \
     --variant=minbase      \
     stable                \
     /home/guest/LIVE_BOOT/chroot \
     http://mirrors.163.com/debian

chroot /home/guest/LIVE_BOOT/chroot
echo "baodeOS" >/etc/hostname
apt-get install live-boot systemd-sysv sudo vim iputils-ping nano x-window-system-core gnome-core plymouth plymouth-themes linux-image-arm64 linux-headers-arm64
adduser user
 nano /etc/sudoers
 user ALL=(ALL) NOPASSWD:ALL

nano /etc/lightdm/lightdm.conf
autologin-user=user

exit chroot
mksquashfs LIVE_BOOT/chroot/ ./filesystem.squashfs && chmod -R 777 ./*.squashfs
mkdir -vp /home/guest/LIVE_BOOT/{scratch,image/live}
     mksquashfs                            \
    /home/guest/LIVE_BOOT/chroot                 \
    /home/guest/LIVE_BOOT/image/live/os.squashfs \
    -e boot

cp -v /home/guest/LIVE_BOOT/chroot/boot/vmlinuz-*     /home/guest/LIVE_BOOT/image/vmlinuz
cp -v /home/guest/LIVE_BOOT/chroot/boot/initrd.img-*  /home/guest/LIVE_BOOT/image/initrd

nano  /home/guest/LIVE_BOOT/image/DEBIAN_CUSTOM

nano /home/guest/LIVE_BOOT/scratch/grub.cfg
insmod all_video
set default="0"
set timeout="30"
menuentry "baodeOS-installer"" {
    search --set=root --no-floppy --file /DEBIAN_CUSTOM
    linzux  /vmlinuz boot=live quiet nomodeset
    initrd /initrd
}

grub-mkstandalone                                \
    --format=arm64-efi                          \
    --output=/home/guest/LIVE_BOOT/scratch/grubaa64.efi \
    --locales=""                                 \
    --fonts=""                                   \
    "boot/grub/grub.cfg=/home/guest/LIVE_BOOT/scratch/grub.cfg"

cd /home/guest/LIVE_BOOT/scratch
dd if=/dev/zero of=efiboot.img bs=1M count=10
mkfs.vfat efiboot.img
mmd -i efiboot.img efi efi/boot
mcopy -i efiboot.img ./grubaa64.efi ::efi/boot/

xorriso                                                            \
  -as mkisofs                                                    \
  -iso-level 3                                                   \
  -full-iso9660-filenames                                        \
  -volid "baodeOS_CUSTOM"              \
  -eltorito-alt-boot                                             \
  -e EFI/efiboot.img                                         \
  -no-emul-boot                                              \
  -append_partition 2 0xef /home/guest/LIVE_BOOT/scratch/efiboot.img \
  -output "/home/guest/baodeOS-installer.iso"                  \
  -graft-points                                                  \
  "/home/guest/LIVE_BOOT/image"                                  \
  /EFI/efiboot.img=/home/guest/LIVE_BOOT/scratch/efiboot.img

dd if=./baodeOS-installer.iso of=/dev/sdb bs=4M 
```
12. 一些有用的：
```plain
https://bitbucket.org/chandrakantsingh/
https://www.linux.com/topic/distributions/aryalinux-distribution-and-platform/
https://www.linuxprobe.com/aryalinux-base-lfs.html
https://blog.csdn.net/weixin_41217917/article/details/80757652?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-14&spm=1001.2101.3001.4242
http://wiki.linuxfromscratch.org/lfs
https://github.com/marco-buratto/lamponepi.qemubox
```
13. live-build：
```plain
自动配置脚本目录：auto/config
创建镜像时安装的软件所在目录：config/package-lists/
自定义镜像内容目录：config/includes.chroot/
创建系统后执行的脚本所在目录：config/hooks/
自定义引导程序目录：config/bootloaders/
```
14. 重新打包ISO：
```plain
xorriso -as mkisofs -r -checksum_algorithm_iso md5,sha1 -V 'Ubuntu-Server 16.04.3 LTS arm64' \
-o ./new-ubuntu-16.04.3-server-arm64.iso -J -joliet-long -cache-inodes -e boot/grub/efi.img \
-no-emul-boot -append_partition 2 0xef ubuntu/boot/grub/efi.img  -partition_cyl_align all ubuntu/
```
15. 1
16. 1
## (15) 常用

1. hostnamectl set-hostname
2. 取消32位支持
```
dpkg --remove-architecture i386
```
3. find / -name 
4. 还原系统后解决开机慢的问题
```
sed -ri 's/.*swap.*/#&/' /etc/fstab
rm /etc/mdadm/mdadm.conf
update-initramfs  -u
```
5. win10下面打开powershell输入tar -czvf .\jdk7.tar.gz ./jdk7-server-release-1708
6. 查看温度，AMD显卡：
```shell
apt-get install lm-sensors sensors-applet && watch sensors
apt install radeontop && radeontop
apt install hddtemp && hddtemp SATA:/dev/sda
```
7. 让nano显示行号：
```
nano ~/.nanorc
set linenumbers
```
8. 让root有颜色
```
PS1="[\u@\h \w]\$ " 
```
9. 让root可以命令行打开图形化：
```plain
cp /home/guest/.Xauthority /root/
```
10. 命令行连WiFi：
```plain
nmcli device wifi connect wifiname password wifipasswd
```
11. 新系统安装：
```
wget https://gitee.com/haolixu/test/raw/master/.vimrc && mv -b .vimrc /root
wget https://gitee.com/haolixu/test/raw/master/motd && mv -b motd /etc
wget https://gitee.com/haolixu/test/raw/master/.bashrc && mv -b .bashrc  /root  
```
12. 谷歌搜索：
```plain
https://go.readmorejoy.com/search/
```
13. 查看系统信息，硬盘速率：
```plain
apt install inxi   
dmesg | grep SATA |head -n 1
```
14. 修改密码安全等级（降低）
```plain
apt-get install libpam-cracklib
nano /etc/pam.d/common-password
apt之后直接就可以设置了，nano可以提供安全等级
```
15. GitHub中国专用：
```plain
github.com.cnpmjs.org
hub.fastgit.org
```
16. 程序后台执行：nohup sh test.sh &
17. 建立文件夹软连接，强制覆盖：
```plain
ln -sfn /var/lib/docker/ lixu666/
```
18. 设置默认启动界面：
```plain
systemctl set-default multi-user.target #字符
systemctl set-default graphical.target #图形
```
19. 查看开机启动的程序
```markdown
systemctl list-unit-files | grep enable
```
20. 1
## (16) wine

https://www.novaspirit.com/2019/04/15/run-x86-arm/

```
2.6更新
apt-get install qemu qemu-user qemu-user-static binfmt-support debootstrap binutils
debootstrap --foreign --arch i386 stable ./chroot-stretch-i386 http://mirrors.163.com/debian
mount -t sysfs sys ./chroot-stretch-i386/sys/
mount -t proc proc ./chroot-stretch-i386/proc/
mount --bind /dev ./chroot-stretch-i386/dev/
mount --bind /dev/pts ./chroot-stretch-i386/dev/pts/
mount --bind /dev/shm ./chroot-stretch-i386/dev/shm/
cp /usr/bin/qemu-i386-static ./chroot-stretch-i386/usr/bin/
chroot ./chroot-stretch-i386/ /debootstrap/debootstrap --second-stage
chroot ./chroot-stretch-i386/ /bin/su -l root
adduser -uid 1000 test
apt install leafpad ca-certificates git dirmngr sudo bzip2 zenity curl
chroot ./chroot-stretch-i386/ /bin/su -l root
tar -jxf PlayOnLinux-wine-3.9-linux-x86.pol --strip-components=1
mv ./3.9/bin/wine-preloader ./3.9/bin/wine-preloader.renamed
mv ./3.9 /opt/wine-3.9/
echo PATH=/opt/wine-3.9/bin/:$PATH >> ~/.bashrc
source ./.bashrc
chroot /home/test/Desktop/chroot-stretch-i386/ /bin/su -l test
echo PATH=/opt/wine-3.9/bin/:$PATH >> ~/.bashrc
source ./.bashrc
winecfg
wget http://security.ubuntu.com/ubuntu/pool/main/libp/libpng/libpng12-0_1.2.46-3ubuntu4.2_i386.deb
dpkg -i libpng12-0_1.2.46-3ubuntu4.2_i386.deb

git clone https://gitee.com/AN_drew/deepin-wine-ubuntu.git
cd deepin-wine-ubuntu && ./install.sh
wine必装：
wget https://mirrors.ustc.edu.cn/deepin/pool/non-free/d/deepin-wine-plugin/deepin-wine-plugin_1.0deepin2_i386.deb
wget https://mirrors.ustc.edu.cn/deepin/pool/non-free/d/deepin-wine-plugin-virtual/deepin-wine-plugin-virtual_1.0deepin3_all.deb
apt install -f
wget https://mirrors.aliyun.com/deepin/pool/non-free/d/deepin-wine-helper/deepin-wine-helper_1.2deepin8_i386.deb
apt install -f
微信：
wget https://mirrors.ustc.edu.cn/deepin/pool/non-free/d/deepin.com.wechat/deepin.com.wechat_2.6.8.65deepin0_i386.deb
2.12更新！
debootstrap --foreign --arch arm64 bionic ./arm64-ubuntu http://mirrors.aliyun.com/ubuntu-ports
deb [by-hash=force] http://packages.deepin.com/deepin lion main contrib non-free
3.13.更新！
wget https://qd.myapp.com/myapp/qqteam/pcqq/PCQQ2020.exe
3.30更新！
export DISPLAY=:0
export DISPLAY=:1
```

## (17)虚拟化（ docker，kvm）

1. uos原生可安装docker，但是无法安装镜像，需更新内核到5.4。
2. docker安装图形化管理：
```
apt install curl gnupg2 ca-certificates apt-transport-https software-properties-common
curl -sSL https://get.docker.com | sh
docker --version
#国内源参考big教程
docker pull portainer/portainer
docker volume create portainer_data
docker run -d -p 9000:9000 --name portainer --restart always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
127.0.0.1:9000
```
3. docker安装ubuntu
```
docker pull ubuntu
docker run -it -p 80:80 ubuntu /bin/bash
docker ps -a
docker start (CONTAINER ID）
docker attach (CONTAINER ID）
```
4. 安装kvm
```markdown
wget https://gitee.com/haolixu/test/raw/master/kvm.sh && bash kvm.sh
apt install virtinst python-libvirt virt-viewer virt-manager bridge-utils uml-utilities ovmf qemu-efi libvirt-daemon-system qemu qemu-system qemu-system-arm qemu-system-common qemu-user qemu-utils qemu-kvm
#yum install kvm virt-manager libvirt libvirt-python python-virtinst libvirt-client qemu-kvm qemu-img
modprobe kvm   #加载kvm内核模块，vmare虚拟机需要开启虚拟化支持
systemctl status libvirtd
virsh list --all #上两步验证是否安装成功
```
5. 1
## (18) 内网穿透（web服务器)

1. http://download.natapp.cn/assets/downloads/clients/2_3_9/natapp_linux_arm64_2_3_9.zip
2. unzip  natapp_linux_arm64_2_3_9.zip
3. 85cf1d014092dc88.natapp.cc:11910 
4. chmod 777 natapp &&./natapp -authtoken=8582fece4a62980c
5. wget [https://gitee.com/haolixu/test/raw/master/](https://gitee.com/haolixu/test/raw/master/fio.sh)natapp.sh &&./natapp -authtoken=8582fece4a62980c
6. 检查有无公网IP：  
```
TRACERT.EXE baidu.com
```
7. web服务器设置：
```
apt install apache2
iptables -I INPUT -p tcp --dport 3389 -j ACCEPT
nano /etc/apache2/ports.conf
service apache2 restart
service apache2 status
apt install php7.3 php-mbstring php-pdo php-curl php-mysql mariadb-server-10.3 php-gd php-zip php-xml*
mysqladmin -u root -p password #这个有效
nano /etc/mysql/mariadb.conf.d/50-server.cnf
#bind-address            = 127.0.0.1
port                   = 3389
mysql -u root -p
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '密码' WITH GRANT OPTION;
FLUSH PRIVILEGES;
#赋予root权限并立即生效
service mysql restart
mysql -u root -p这里设置密码 -h 192.168.20.12 -P 3389
远程连接：
use mysql
update user set host='%' where user ='root';
FLUSH PRIVILEGES;
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '1';
FLUSH PRIVILEGES;
```
8. 1
## (18) BMC

1. 通过BMC命令行
```
ipmcset -t precisealarm -d mock -v 0x0D000009 1 assert
ipmcset -t precisealarm -d mock -v 0x0D000009 1 deassert
ipmcset -t precisealarm -d mock -v 0x0D000003 1 assert
ipmcset -t precisealarm -d mock -v 0x0D000003 1 deassert
```
查看面板led指示灯及登录BMC web首页，查看“告警与事件-当前告警”。
2. 通过ipmitool命令行chassis power status查询单板业务平面的上下电状态，执行单板上电，下电，复位命令：
```
ipmitool -H *.*.*.* -I lanplus -U <用户名> -P <密码> chassis power on
ipmitool -H *.*.*.* -I lanplus -U <用户名> -P <密码> chassis power off
ipmitool -H *.*.*.* -I lanplus -U <用户名> -P <密码> chassis power reset
```
3. 查询系统当前功率及所有传感器的信息
```
ipmitool -I lanplus -H <BMC IP> -U <BMC账号> -P <密码> raw 0x30 0x93 0xdb 0x07 0x00 0x11 0x00
ipmitool -I lanplus -L administrator -H <BMC IP> -U <BMC账号> -P <密码> sensor
```
4. 打开KVM，勾选“本地文件夹”，点击浏览并确定，点击连接。
## (19) 天翼网关，云盘

1. 超级管理员账户 telecomadmin
2. 密码 nE7jA%5m
3. 查看WiFi：
```plain
wpa_cli -iwlan0 status
wpa_cli -iwlan0 scan
wpa_cli -iwlan0 scan_results
ifconfig  wlan0 up
dhclient wlan0
```
4. Linux使用云盘：
```plain
wget https://raw.fastgit.org/haolixu/Temporary-image-backup/master/cloudpan189-go-v0.0.9-linux-arm64.zip
unzip cloudpan189-go-v0.0.9-linux-arm64.zip
cd cloudpan189-go-v0.0.9-linux-arm64/ && ./cloudpan189-go
login #登录
sign #签到
ls && cd 来自家庭共享/
download gcc9.3.tar.gz #下载
upload /root/postgresq-lixu666.py ./
```
5. 1
## (20) C++

1. string必须加头文件<string.h>
2. 值传递形参不改变实参，地址传递会改变。如果不想修改主函数中的数据，用值传递。
3. 用指针可以节约内存空间。
## (21) Git

1. 远程拉项目及上传更新项目：
```plain
mkdir git-lixu666 && cd git-lixu666 && git init 
git config --global user.name "haolixu"
git config --global user.email "1300311464@qq.com"
git remote add origin https://gitee.com/haolixu/test.git
git pull origin master
git commit -m "lixu666"
git push -u origin master
```
2. 只下载最新版本，不下载历史版本：
```plain
git clone git仓库地址 --depth=1
```
3. 1

















