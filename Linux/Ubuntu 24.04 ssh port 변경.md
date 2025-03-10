# ssh: Connection refused
방금 전까지만 해도 잘 쓰던 Ubuntu 서버에 ssh 접근을 시도하니 Connection Refused 에러가 발생했다.
처음에는 무엇이 문제인지 몰라서 서버 인스턴스 재부팅(restart)을 수 차례 시도하는 등 여러 삽질을 하였으나, 결국 SSH가 기존 설정과 다르게 기본 포트인 22번 포트를 listen하고 있는 것이 원인인 것을 알게 되었다.

문제가 발생한 인스턴스는 Ubuntu 24.04를 사용하고 있는 서버로, 처음 서버를 설정할 때에도 기존 방식과 같은 `/etc/ssh/sshd_config`를 수정하는 방식이 아니어서 당황하다가, 대충 검색해보고 다른 파일을 수정해야 한다는 것을 확인하고 해당 파일을 수정하였던 기억이 났다. 

물론 기억은 나지 않았기 때문에 다시 검색을 하였고, `/lib/systemd/system/ssh.socket`을 수정하여 SSH 포트를 변경하였던 것을 떠올리게 되었다.^[https://askubuntu.com/questions/1439461/ssh-default-port-not-changing-ubuntu-22-10]
```ini
[Unit]
Description=OpenBSD Secure Shell server socket
Before=sockets.target ssh.service
ConditionPathExists=!/etc/ssh/sshd_not_to_be_run

[Socket]
ListenStream=22
Accept=no
FreeBind=yes

[Install]
WantedBy=sockets.target
RequiredBy=ssh.service
```
이상한 점은, 내가 설정해둔 것과 달리 설정 파일이 SSH 포트로 22번 포트를 지정하고 있다는 것이다.
문제가 발생하기 직전까지도 다른 포트를 사용하도록 설정 파일을 구성하여 SSH 접근을 하고 있었으니, 설정 파일이 수정된 것이 분명했다.

원인을 파악하기 위해 `/lib/systemd/system/ssh.socket`에 관련된 내용을 찾아보려 했으나, 해당 내용을 처음 알게 된 페이지에서 그 원인을 알게 되었다.

> Any edits to `/lib/systemd/system/ssh.socket` will be lost when the package is updated. Use override files to change settings instead. – [muru](https://askubuntu.com/users/158442/muru "200,235 reputation") [Commented Jul 27, 2023 at 15:41](https://askubuntu.com/questions/1479500/how-to-change-the-ssh-port-on-ubuntu-23-04#comment2591376_1479906)

슨배님의 은혜에 감사하며, `/lib/systemd/system/ssh.socket`를 직접 변경하는 것이 아니라, `/lib/systemd/system/ssh.socket.d/listen.conf`를 따로 만들었어야 함을 알게 되었고, 이를 적용하여 문제를 해결하였다.
```
mkdir -p /etc/systemd/system/ssh.socket.d
cat >/etc/systemd/system/ssh.socket.d/listen.conf <<EOF
[Socket]
ListenStream=
ListenStream=2222
EOF
```

# ssh.socket
문제의 가장 근본적인 원인은 서버가 사용하는 sshd가 동작하는 방식에 대해 충분히 숙지하지 않은 채로  SSH 포트를 변경된 것만 확인하고 넘겼였기 때문이었다.
Ubuntu는 22.10부터 socket-based activation을 기본으로 동작하게 되었다.^[https://discourse.ubuntu.com/t/sshd-now-uses-socket-based-activation-ubuntu-22-10-and-later/30189]
이는 연결이 발생하기 전에는 sshd가 실행되지 않기 때문에, 사용되는 메모리를 절약하여 컨테이너, VM환경에서 특히 장점이 된다.
물론 socket 기반 ssh를 사용할 수 없는 환경에서는 아래와 같이 설정하여 기존과 같이 서비스를 사용할 수 있다.
```
systemctl disable --now ssh.socket
rm -f /etc/systemd/system/ssh.service.d/00-socket.conf
rm -f /etc/systemd/system/ssh.socket.d/addresses.conf
systemctl daemon-reload
systemctl enable --now ssh.service
```


# 그런데 업데이트는 누가...?
다만 그 시점부터 연결에 문제가 생겼다는 것은 패키지 업데이트가 발생하면서 설정 파일이 덮어씌워진 시점이 그 즈음이라는 것인데, 누가 패키지를 업데이트한걸까?
확인하고자 로그를 뒤졌고, `/var/log/apt/history.log`에서 아래의 내용을 찾을 수 있었다.
```
Start-Date: 2024-07-02  06:11:33

Commandline: /usr/bin/unattended-upgrade

Upgrade: openssh-client:arm64 (1:9.6p1-3ubuntu13, 1:9.6p1-3ubuntu13.3), openssh-server:arm64 (1:9.6p1-3ubuntu13, 1:9.6p1-3ubuntu13.3), openssh-sftp-server:arm64 (1:9.6p1-3ubuntu13, 1:9.6p1-3ubuntu13.3)

End-Date: 2024-07-02  06:11:38
```

발생한 시간(UTC)으로 보나 항목(openssh)으로 보나, 위 업그레이드가 문제의 원인일 가능성이 아주 높아 보였다.
그럼 결국 업그레이드를 수행한 것은 특정 사용자가 아니라 `/usr/bin/unattended-upgrade`인데, 이게 뭘까?

# unattended-upgrade
It is a script that automatically installs security and other important updates for the system packages.

The unattended-upgrades package is designed to keep the system up-to-date with the latest security patches and bug fixes automatically, without user intervention. It downloads and installs the available updates from the configured repositories at a scheduled time, typically once per day.

The `/usr/bin/unattended-upgrade` command is executed by a systemd timer or cron job, depending on the system configuration. It reads the configuration from `/etc/apt/apt.conf.d/50unattended-upgrades` and `/etc/apt/apt.conf.d/20auto-upgrades files`, which specify the repositories to check for updates, the packages to upgrade, and other options.
#### 50unattended-upgrades
```
Unattended-Upgrade::Allowed-Origins {
        "${distro_id}:${distro_codename}";
        "${distro_id}:${distro_codename}-security";
        "${distro_id}ESMApps:${distro_codename}-apps-security";
        "${distro_id}ESM:${distro_codename}-infra-security";
};
Unattended-Upgrade::Package-Blacklist {
};
Unattended-Upgrade::DevRelease "auto";
```
#### 20auto-upgrades
```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```

By default, unattended-upgrades is configured to install security updates from the security repositories, but it can be configured to also install updates from other repositories or for specific packages. It provides a convenient way to keep the system up-to-date without manual intervention, while still allowing the user to review and control the updates if desired.