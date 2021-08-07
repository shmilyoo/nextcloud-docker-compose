## 外部存储

使用 external storage， 在 docker 中把物理目录挂载到 nextcloud app
在管理中，管理外部存储，注意：外部存储目录下面不能有 软链，会出错

## 文件自动发现

NextCloud 安装之后并不认得文件夹内已经存在的内容，需要使用 occ 命令扫描。

在 Docker 里，需要使用下面的命令行，记录一下：

su www-data -s /bin/bash -c "php /var/www/html/occ files:scan --all"
使用方式：

docker exec nextcloud su www-data -s /bin/bash -c "php /var/www/html/occ files:scan --all"
记得把 exec 后面的换成你的容器名即可。

每次都执行命令扫描显然不是长久之计，可以在 config.php 中添加 'filesystem_check_changes' => 1, 选项实现文件夹的自动扫描，实测效果很理想。

命令参考 https://www.orgleaf.com/2400.html

## 支持视频预览

支持 视频预览
nextcloud 默认不支持视频类型预览，为了预防一些风险 https://docs.nextcloud.com/server/12/admin_manual/configuration_server/config_sample_php_parameters.html#previews

config.php 中没有预览的配置，是按照默认来的，可以手动修改 加进去

```
'enable_previews' => true,
'enabledPreviewProviders' =>
array (
'OC\Preview\Movie',
'OC\Preview\PNG',
'OC\Preview\JPEG',
'OC\Preview\GIF',
'OC\Preview\BMP',
'OC\Preview\XBitmap',
'OC\Preview\MP3',
'OC\Preview\MP4',
'OC\Preview\TXT',
'OC\Preview\MarkDown',
),
```

另外，需要额外安装 ffmpeg，直接把 image 做一个 Dockerfile 就可以了

```
From nextcloud:fpm

COPY ./sources.list /etc/apt/

RUN apt update && apt install ffmpeg -y
```
