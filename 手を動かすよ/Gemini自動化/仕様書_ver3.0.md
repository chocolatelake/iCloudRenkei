URL: https://roy-tough-logan-gifts.trycloudflare.com

パスワード: 792c551888436d84bf63f652




Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

新機能と改善のために最新の PowerShell をインストールしてください!https://aka.ms/PSWindows

PS C:\Users\ほっくん> wsl
Linux 用 Windows サブシステムにインストールされているディストリビューションはありません。
この問題を解決するには、以下の手順に従ってディストリビューションをインストールしてください:

'wsl.exe --list --online' を使用して利用可能な配布を一覧表示する
および 'wsl.exe --install <Distro>' を使用してインストールしてください。
PS C:\Users\ほっくん> wsl --install -d Ubuntu
ダウンロードしています: Ubuntu
インストールしています: Ubuntu
ディストリビューションが正常にインストールされました。'wsl.exe -d Ubuntu' を使用して起動できます
Ubuntu を起動しています...
Provisioning the new WSL instance Ubuntu
This might take a while...
Create a default Unix user account: hokuntu
New password:
Retype new password:
passwd: password updated successfully
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

hokuntu@DESKTOP-KLMS8CM:/mnt/c/Users/ほっくん$ curl -fsSL https://code-server.dev/install.sh | sh
Ubuntu 24.04.3 LTS
Installing v4.107.0 of the amd64 deb package from GitHub.

+ mkdir -p ~/.cache/code-server
+ curl -#fL -o ~/.cache/code-server/code-server_4.107.0_amd64.deb.incomplete -C - https://github.com/coder/code-server/releases/download/v4.107.0/code-server_4.107.0_amd64.deb
######################################################################## 100.0%
+ mv ~/.cache/code-server/code-server_4.107.0_amd64.deb.incomplete ~/.cache/code-server/code-server_4.107.0_amd64.deb
+ sudo dpkg -i ~/.cache/code-server/code-server_4.107.0_amd64.deb
[sudo] password for hokuntu:
Selecting previously unselected package code-server.
(Reading database ... 40754 files and directories currently installed.)
Preparing to unpack .../code-server_4.107.0_amd64.deb ...
Unpacking code-server (4.107.0) ...
Setting up code-server (4.107.0) ...

deb package has been installed.

To have systemd start code-server now and restart on boot:
  sudo systemctl enable --now code-server@$USER
Or, if you don't want/need a background service you can run:
  code-server

Deploy code-server for your team with Coder: https://github.com/coder/coder
hokuntu@DESKTOP-KLMS8CM:/mnt/c/Users/ほっくん$ curl -L --output cloudflared.deb https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb && sudo dpkg -i cloudflared.deb
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 19.2M  100 19.2M    0     0  12.9M      0  0:00:01  0:00:01 --:--:-- 26.4M
Selecting previously unselected package cloudflared.
(Reading database ... 48708 files and directories currently installed.)
Preparing to unpack cloudflared.deb ...
Unpacking cloudflared (2025.11.1) ...
Setting up cloudflared (2025.11.1) ...
Processing triggers for man-db (2.12.0-4build2) ...
hokuntu@DESKTOP-KLMS8CM:/mnt/c/Users/ほっくん$ code-server
[2026-01-09T15:37:58.434Z] info  Wrote default config file to /home/hokuntu/.config/code-server/config.yaml
[2026-01-09T15:37:58.627Z] info  code-server 4.107.0 ac7322ce566a5dc99c60d92180375329f0bbd759
[2026-01-09T15:37:58.628Z] info  Using user-data-dir /home/hokuntu/.local/share/code-server
[2026-01-09T15:37:58.644Z] info  Using config file /home/hokuntu/.config/code-server/config.yaml
[2026-01-09T15:37:58.644Z] info  HTTP server listening on http://127.0.0.1:8080/
[2026-01-09T15:37:58.644Z] info    - Authentication is enabled
[2026-01-09T15:37:58.644Z] info      - Using password from /home/hokuntu/.config/code-server/config.yaml
[2026-01-09T15:37:58.644Z] info    - Not serving HTTPS
[2026-01-09T15:37:58.645Z] info  Session server listening on /home/hokuntu/.local/share/code-server/code-server-ipc.sock
^Chokuntu@DESKTOP-KLMS8CM:/mnt/c/Users/ほっくん$ cat ~/.config/code-server/config.yaml
bind-addr: 127.0.0.1:8080
auth: password
password: 792c551888436d84bf63f652
cert: false
hokuntu@DESKTOP-KLMS8CM:/mnt/c/Users/ほっくん$


hokuntu@DESKTOP-KLMS8CM:/mnt/c/Users/ほっくん$ code-server --bind-addr 127.0.0.1:8080 & cloudflared tunnel --url http://127.0.0.1:8080
[1] 807
2026-01-09T15:39:47Z INF Thank you for trying Cloudflare Tunnel. Doing so, without a Cloudflare account, is a quick way to experiment and try it out. However, be aware that these account-less Tunnels have no uptime guarantee, are subject to the Cloudflare Online Services Terms of Use (https://www.cloudflare.com/website-terms/), and Cloudflare reserves the right to investigate your use of Tunnels for violations of such terms. If you intend to use Tunnels in production you should use a pre-created named tunnel by following: https://developers.cloudflare.com/cloudflare-one/connections/connect-apps
2026-01-09T15:39:47Z INF Requesting new quick Tunnel on trycloudflare.com...
[2026-01-09T15:39:47.783Z] info  code-server 4.107.0 ac7322ce566a5dc99c60d92180375329f0bbd759
[2026-01-09T15:39:47.784Z] info  Using user-data-dir /home/hokuntu/.local/share/code-server
[2026-01-09T15:39:47.798Z] info  Using config file /home/hokuntu/.config/code-server/config.yaml
[2026-01-09T15:39:47.798Z] info  HTTP server listening on http://127.0.0.1:8080/
[2026-01-09T15:39:47.798Z] info    - Authentication is enabled
[2026-01-09T15:39:47.798Z] info      - Using password from /home/hokuntu/.config/code-server/config.yaml
[2026-01-09T15:39:47.798Z] info    - Not serving HTTPS
[2026-01-09T15:39:47.798Z] info  Session server listening on /home/hokuntu/.local/share/code-server/code-server-ipc.sock
2026-01-09T15:39:51Z INF +--------------------------------------------------------------------------------------------+
2026-01-09T15:39:51Z INF |  Your quick Tunnel has been created! Visit it at (it may take some time to be reachable):  |
2026-01-09T15:39:51Z INF |  https://roy-tough-logan-gifts.trycloudflare.com                                           |
2026-01-09T15:39:51Z INF +--------------------------------------------------------------------------------------------+
2026-01-09T15:39:51Z INF Cannot determine default configuration path. No file [config.yml config.yaml] in [~/.cloudflared ~/.cloudflare-warp ~/cloudflare-warp /etc/cloudflared /usr/local/etc/cloudflared]
2026-01-09T15:39:51Z INF Version 2025.11.1 (Checksum 83ea55259e419549817460d0c097f23ad1327364d0a63fab2c5463b9283251cb)
2026-01-09T15:39:51Z INF GOOS: linux, GOVersion: go1.24.9, GoArch: amd64
2026-01-09T15:39:51Z INF Settings: map[ha-connections:1 protocol:quic url:http://127.0.0.1:8080]
2026-01-09T15:39:51Z INF cloudflared will not automatically update if installed by a package manager.
2026-01-09T15:39:51Z INF Generated Connector ID: 5713c6c4-338a-4548-b358-dd9586b7cf79
2026-01-09T15:39:51Z INF Initial protocol quic
2026-01-09T15:39:51Z INF ICMP proxy will use 172.18.49.9 as source for IPv4
2026-01-09T15:39:51Z INF ICMP proxy will use fe80::215:5dff:febe:af84 in zone eth0 as source for IPv6
2026-01-09T15:39:51Z WRN The user running cloudflared process has a GID (group ID) that is not within ping_group_range. You might need to add that user to a group within that range, or instead update the range to encompass a group the user is already in by modifying /proc/sys/net/ipv4/ping_group_range. Otherwise cloudflared will not be able to ping this network error="Group ID 1000 is not between ping group 1 to 0"
2026-01-09T15:39:51Z WRN ICMP proxy feature is disabled error="cannot create ICMPv4 proxy: Group ID 1000 is not between ping group 1 to 0 nor ICMPv6 proxy: socket: permission denied"
2026-01-09T15:39:51Z ERR Cannot determine default origin certificate path. No file cert.pem in [~/.cloudflared ~/.cloudflare-warp ~/cloudflare-warp /etc/cloudflared /usr/local/etc/cloudflared]. You need to specify the origin certificate path by specifying the origincert option in the configuration file, or set TUNNEL_ORIGIN_CERT environment variable originCertPath=
2026-01-09T15:39:51Z INF ICMP proxy will use 172.18.49.9 as source for IPv4
2026-01-09T15:39:51Z INF ICMP proxy will use fe80::215:5dff:febe:af84 in zone eth0 as source for IPv6
2026-01-09T15:39:51Z INF Starting metrics server on 127.0.0.1:20241/metrics
2026-01-09T15:39:51Z INF Tunnel connection curve preferences: [X25519MLKEM768 CurveP256] connIndex=0 event=0 ip=198.41.192.7
2026/01/10 00:39:51 failed to sufficiently increase receive buffer size (was: 208 kiB, wanted: 7168 kiB, got: 416 kiB). See https://github.com/quic-go/quic-go/wiki/UDP-Buffer-Sizes for details.
2026-01-09T15:39:51Z INF Registered tunnel connection connIndex=0 connection=522af740-be96-42c2-a574-db7e67017d2c event=0 ip=198.41.192.7 location=nrt10 protocol=quic
