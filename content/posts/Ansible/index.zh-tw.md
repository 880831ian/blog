---
weight: 4
title: "Ansible 介紹與實作 (Inventory、Playbooks、Module)"
date: 2022-05-16T11:26:40+08:00
lastmod: 2022-05-16T11:26:40+08:00
draft: false
author: "PinYi"
authorLink: "https://pin-yi.me"
description: "此文章是接續前面 Jenkins 及 Ansible IT 自動化 CI/CD 介紹、使用 Jenkins 設定 GitHub 觸發程序並通知 Telegram Bot，歡迎大家先去觀看前面兩篇文章 😋"
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["Ansible", "CI/CD", "介紹", "實作"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

本篇文章是接續前面兩篇 [Jenkins 及 Ansible IT 自動化 CI/CD 介紹](https://pin-yi.me/jenkins-ansible/) 跟 [使用 Jenkins 設定 GitHub 觸發程序並通知 Telegram Bot](https://pin-yi.me/jenkins/) 文章，歡迎大家先去觀看前面兩篇文章 🤪

<br>

## Ansible 是如何運作的？

在 Ansible 世界裡，我們會透過 `inventory 檔案` 來定義有哪些的 `Managed Node`，並藉由 `SSH` 與 `Python` 來進行溝通。那我們先來看一張圖：

{{< image src="/images/Ansible/run.png"  width="600" caption="Ansible 運作原理  (圖片來源：[七分鐘掌握 Ansible 核心觀念](https://school.soft-arch.net/courses/28546/lectures/655359))" src_s="/images/Ansible/run.png" src_l="/images/Ansible/run.png" >}}

<br>

誒 😱 突然多了很多新名詞，沒關係我來一一解釋，首先我們先從 `Managed Node` 是什麼，以及圖片中的 `Control machine` 開始說起吧！


### 什麼是控制主機及被控節點？

在 Ansible 裡，我們會把所有機器的角色做以下的區分：

* 控制主機 (Control Machine)：顧名思義，這類主機可以透過運行 Ansible 的劇本 (Playbooks) 對被控節點進行部署。
* 被控節點 (Managed Node)：也稱為遙控節點 (Remote Node)。相對於控制主機，這類節點就是我們透過 Ansible 進行部署的對象。

所以代表我們在操作這邊就是 Control Machine，要部署的機器就是 Managed Node，透過 SSH 來做連線。但什麽是 `inventory` 跟 `Playbooks` 呢？

<br>

### 什麼是 Ansible inventory

`inventory` 這個單子本身有**詳細目錄**、**清單**和**列表**的意思。在這裡我們可以把它理解成一份主機列表，可以透過它來定義每個 Managed Node 的代號、IP 位址、連線設定和群組。

```sh
$ vim hosts
# ansible_ssh_host：遠端 SSH 主機位址
# ansible_ssh_port：遠端 SSH Port
# ansible_ssh_user：遠端 SSH 使用者名稱
# ansible_ssh_private_key_file：本機 SSH 私鑰檔案路徑
# ansible_ssh_pass：遠端 SSH 密碼 (建議使用私鑰)

[local]
server1 ansible_ssh_host=127.0.0.1  ansible_ssh_port=55000 ansible_ssh_pass=docker
```
所以我們可以在這邊輸入很多個主機來做管理，可以把它想成一個設定檔。

<br>


### 什麼是 Ansible Playbooks

再談 Ansible Playbooks 之前，先說明我們要怎麼去操作 Ansible？一般來說，我們可以使用 Ad-Hoc commands 和 Playbooks 兩種方式來操作 Ansible。

<br>

#### Ad-Hoc commands 是什麼？

Ad hoc 可以翻譯成**簡短地指令**，也就是我們常用的指令模式，最常見的 `ping`和`echo` 為例。

* `ping`

```sh
$ ansible all -m ping

server1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

<br>

* `echo`

```sh
$ ansible all -m command -a "echo Hello World"

server1 | CHANGED | rc=0 >>
Hello World
```
從上面的例子中可以看到 Ad-Hoc commands 一次只能處理一件事情，這是它與 Playbooks 最大的差異。

<br>

### Playbooks 是什麼？

Playbooks 就是字面上的意思為**劇本**，我們可以先透過寫好的**劇本 (Playbooks)** 來讓各個 Managed Node 進行指定的**動作 (Plays)** 和**任務 (Tasks)**。

簡而言之，Playbooks 就是 Ansible 的腳本 (Script)，而且比傳統 Shell Script 還要強大好幾百倍的腳本！此外它是使用 **YAML** 格式，寫 Code 就如同寫文件一樣，簡單易讀。

有關詳細的**動作 (Plays)** 和**任務 (Tasks)**，等我們實際安裝好再來說明 😆

<br>

## Ansible 安裝與實作

安裝之前先讓大家看一下版本吧！大家要記得檢查自己的版本與教學是否相同，如果不同，記得要先查看官網是否有修改內容。

### 版本

* macOS：11.6
* Docker：Docker version 20.10.14, build a224086
* Aansible：ansible [core 2.12.5]

<br>

### 如何安裝 Ansible 在控制主機

由於 Ansible 是一套開源的軟體，所以在目前大部分主流作業系統上都可以透過對應的套件管理 (package manager) 進行安裝。

本人使用 macOS ，所以這邊僅列出 masOS 安裝方式，其他的可以參考[官方的安裝指南](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-ansible-on-specific-operating-systems)。

<br>

macOS 安裝可以使用兩種方式，官方較推薦使用 `pip` 來做安裝：

* [Pip Install Packages (pip 官方較推薦)](https://pip.pypa.io/en/stable/#)

```sh
$ sudo pip install ansible
```

<br>

* [Homebrew (brew)](https://formulae.brew.sh/formula/ansible#default)

```sh
$ sudo brew install ansible
```

<br> 

安裝完後，可以使用 `--version` 指令來檢查是否安裝完成：

```
$ ansible --version

ansible [core 2.12.5]
  config file = None
  configured module search path = ['/Users/ian_zhuang/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/Cellar/ansible/5.7.1/libexec/lib/python3.10/site-packages/ansible
  ansible collection location = /Users/ian_zhuang/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.10.4 (main, Apr 26 2022, 19:43:24) [Clang 13.0.0 (clang-1300.0.29.30)]
  jinja version = 3.1.2
  libyaml = True
```

<br>

### 如何安裝 Ansible 在被控節點

不需要！！！ 透過 Ansible 進行管理的被控節點完全不需要安裝 Ansible。我們只需要確保這個節點可以透過 SSH 與控制主機做溝通，並安裝 Python 2.6 以上版本就可以透過控制主機來進行部署及管理了。

<br>

那我們為了要模擬，所以我們使用 Docker 來模擬 Managed Node，首先老樣子，一樣先寫一個 Dockerfile 來建立我們的映像檔，此映像檔是微調 [chusiang/ansible-managed-node.dockerfile](https://github.com/chusiang/ansible-managed-node.dockerfile/blob/master/ubuntu-14.04/Dockerfile) 的內容，修改 ubuntu 版本以及內容作調整，我會把程式碼放在 [GitHub 連結](https://github.com/880831ian/Ansible)，以及 [DockerHub 連結](https://hub.docker.com/r/880831ian/ansible-ubuntu-server)，歡迎大家前去下載使用。

<br>

```dockerfile
FROM ubuntu:22.10

LABEL maintainer="880831ian@gmail.com"

# Update the index of available packages.
RUN apt-get update

# Install the requires package.
RUN apt-get install -y openssh-server sudo curl wget bash-completion openssl && apt-get clean

# Setting the sshd.
RUN mkdir /var/run/sshd
RUN echo 'root:root' | chpasswd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

# Create a new user.
#
# - username: docker
# - password: docker
RUN useradd --create-home --shell /bin/bash \
      --password $(openssl passwd -1 docker) docker

# Add sudo permission.
RUN echo 'docker ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers

# Setting ssh public key.
RUN wget https://raw.githubusercontent.com/chusiang/ansible-jupyter.dockerfile/master/files/ssh/id_rsa.pub \
      -O /tmp/authorized_keys && \
      mkdir /home/docker/.ssh && \
      mv /tmp/authorized_keys /home/docker/.ssh/ && \
      chown -R docker:docker /home/docker/.ssh/ && \
      chmod 644 /home/docker/.ssh/authorized_keys && \
      chmod 700 /home/docker/.ssh

EXPOSE 22

# Run ssh server daemon.
CMD ["/usr/sbin/sshd", "-D"]
```

<br>

接下來將它包成 Image 並啟動他：

```sh
$ docker build -t ansible-ubuntu-server . && docker run --name server1 -d -p 8888:22 ansible-ubuntu-server

64c51235e34a7ba42c0c45e690201dd80248c9aac76c3b855c99cf63f7f0af7c
```

<br>

可以用 `exec` 進入容器：

```sh
docker exec -it server1 /bin/bash
```

<br>

### 如何讓 Ansible 操控 Docker 容器？

我們在工作目錄下，新增一個 `ansible.cfg`：

```cfg
[defaults]

inventory = hosts
remote_user = docker
host_key_checking = False
```

<br>

設定 inventory hosts：

```hosts
[local]
server1 ansible_ssh_host=127.0.0.1  ansible_ssh_port=8888 ansible_ssh_pass=docker
```
其中 8888 是我們在啟動時所開放的 Port，也可以自行更改。
* `ansible_ssh_host`：設為本機的 IP。
* `ansible_ssh_port`：設為 `docker ps `取得的 SSH Port 也就是我們的 8888。
* `ansible_ssh_pass`：因為我們沒有連線用的金鑰，所以直接使用密碼方式做連結。(建議只用於練習環境使用) 

<br>

### Hello World On Managed Node

當我們都設置完成後，就可以使用 Terminal 用 Docker 建立好的 Ansible 來練習了！

```sh
$ ansible all -m command -a 'echo Hello World on Docker.'

server1 | CHANGED | rc=0 >>
Hello World on Docker.	
```


<br>

{{< admonition question "ansible 安裝時常見問題">}}
Q1. server1 | FAILED | rc=-1 >> to use the 'ssh' connection type with passwords or pkcs11_provider, you must install the sshpass program

<br>

Q2. ~paramiko/transport.py:236: CryptographyDeprecationWarning: Blowfish has been deprecated
{{< /admonition >}}

{{< admonition success "ansible 安裝時常見問題">}}
Ans1. 會遇到這個問題是因為需要多安裝 sshpass，一般系統安裝 sshpass 很簡單，但在 macOS 上稍微麻煩，詳細可以參考[這篇文章](https://stackoverflow.com/questions/32255660/how-to-install-sshpass-on-mac)。

<br>

Ans2. 在我安裝過程中，發現上前幾天才出現這個 Bug 詳細情形可以參考 [GitHub issues](https://github.com/paramiko/paramiko/issues/2038)，目前解決辦法有降板或是先將錯誤訊息給註解掉，之後再等新的版本出來再更新，大家可以自行選擇，我這邊是直接把出現問題的 `transport.py` 內容註解掉，大概位於236行，可以看下方圖片。

<br>

{{< image src="/images/Ansible/blowfish.png"  width="600" caption="CryptographyDeprecationWarning 錯誤訊息修正" src_s="/images/Ansible/blowfish.png" src_l="/images/Ansible/blowfish.png" >}}
{{< /admonition >}}

<br>

## 第一個 Playbook

在我們都安裝好後，要來說說我們剛剛有偷偷提到的 Playbooks 的動作 (Plays) 和任務 (Tasks)。在一份 Playbooks 裡面，可以有多個 Play、多個 Task 和多個 Module：

* Play：通常為某個特定的目的，例如：
	* `Setup a official website with Drupal` 藉由 Drupal 建置官網
	* `Restart the API Service` 重開 API 服務
* Task：要實行 Play 這個目的所需做的每個步驟，例如：
	* `Install the Nginx` 安裝 Nginx
	* `Kill the djnago process` 強制停止 django 的行程
* Module：Ansible 所提供的各種操作方式，例如：
	* `apt: name=vim state=present` 使用 apt 套件安裝 vim
	* `command: /sbin/shutdown -r now` 使用 shutdown 的指令關機

<br>

有點聽不懂吧！我來舉個例子，我們最熟悉的 Hello World，先建立一個 `helloworld.yaml` 的檔案：


```yaml
---

- name: say 'hello world'
  hosts: all
  tasks:

    - name: echo 'hello world'
      command: echo 'hello world'
      register: result

    - name: print stdout
      debug:
        msg: "{{ result.stdout }}"
```

可以看到這整個就是 Play，我們想要達到 say 'hello world' 的目的，其中有兩個 name 分別代表兩個 Task，也就是達成 Play 目的所需得步驟。最後 command 與 debug 就是我們的 Module 要怎麼達成這兩個步驟的操作方式。

<br>

{{< image src="/images/Ansible/playbooks.gif"  width="800" caption="Playbooks 組成結構" src_s="/images/Ansible/playbooks.gif" src_l="/images/Ansible/playbooks.gif" >}}

<br>

我們使用 `ansible-playbook` 執行 Playbook，在這個範例中，我們執行了１個 Play、3 個 Tasks 和 2 個 Modules：

<br>

```sh
$ ansible-playbook helloworld.yaml
```

<br>

{{< image src="/images/Ansible/helloworld.png"  width="1000" caption="執行 Playbooks" src_s="/images/Ansible/helloworld.png" src_l="/images/Ansible/helloworld.png" >}}

<br>

{{< admonition question "我們剛剛明明只寫兩個 tasks，為什麼執行就變成 3 個 tasks？">}}
這是因為 Ansible 預設會使用 `Setup` task 來取得 Managed node 的 facts。關於 facts 的詳細說明，請滑到後面 "" 觀看😬
{{< /admonition >}}

<br>

那如果沒有 Ansible 時，我們是怎麼操作的？我會附上 Shell Script 的做法，我們來比較看看吧！

<br>

* **Shell Script** 建立 `helloworld.sh` 檔案

```sh
#! /bin/bash
echo "Hello World"
```

* 執行 `helloworld.sh`

```sh
./ helloworld.sh
Hello World
```

<br>

看起來 Shell Script 已經夠用了，為什麼還要寫 Playbook 呢？這邊整理幾個理由給大家參考：
1. 用 Ansible 的 Module 可以把很多複雜的指令給標準化，例如不同的 Linux 發行版本在安裝套件時需代各種不同的參數。
2. 在現有的雲原生 (cloud native) 的架構下，傳統的 Shell Script 已經不敷使用，一般而言 Shell Script 只能對一台機器 (instance) 進行操作。

<br>

## 常用的 Ansible Module 有哪些？

接下來簡單介紹一下比較常用到的 8 個 Module：

### `ansible.builtin.apt`
[apt module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html#ansible-collections-ansible-builtin-apt-module) 是給 Debian, Ubuntu 等作業系統使用的套件模組 (Packing Modules)，我們可以透過它管理 apt 套件。類似的有 `apt-get`、`dpkg`等。

<br>

1. 更新套件索引(快取)，等同於 `apt-get update` 指令。

```yaml
- name: Update repositories cache
  ansible.builtin.apt:
    update_cache: yes
```

2. 安裝 vim 套件。

```yaml
- name: Install the package "vim"
  ansible.builtin.apt:
    name: vim
    state: present
```
3. 移除 nano 套件。

```yaml
 - name: Remove "nano" package
   ansible.builtin.apt:
     name: nano
     state: absent
```

<br>

### `ansible.builtin.command`
[command module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/command_module.html#ansible-collections-ansible-builtin-command-module) 是可以在遠端上執行指令的指令模組，但它不支援變數 (variables) 和 `<`、`>`、`|`、`;`、`&`，若有這類需求要改用 `shell` module。

<br>

1. 重新開機

```yaml
- name: Reboot at now
  ansible.builtin.command: /sbin/shutdown -r now
```

2. 當某個檔案不存在時才執行指令

```yaml
- name: create .ssh directory
  ansible.builtin.command: mkdir .ssh creates=.ssh/
```

3. 先切換目錄再執行指令。

```yaml
- name: cat /etc/passwd
  ansible.builtin.command: cat passwd
  args:
    chdir: /etc
```

<br>

### `ansible.builtin.copy`

[copy moudule](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/copy_module.html#ansible-collections-ansible-builtin-copy-module) 是從本地複製檔案到遠端的檔案模組，若有使用變數需求，可以改用 `template`。它類似 Linux 指令的 `scp`。

<br>

1. 複製 ssh public key 到遠端 (chmod 644 /target/file)

```yaml
- name: copy ssh public key to remote node
  ansible.builtin.copy:
    src: files/id_rsa.pub
    dest: /home/docker/.ssh/authorized_keys
    owner: docker
    group: docker
    mode: 0644
```

2. 複製 ssh public key 到遠端 (chmod u=rw,g=r,o=r /target/file)

```yaml
- name: copy ssh public key to remote node
  ansible.builtin.copy:
    src: files/id_rsa.pub
    dest: /home/docker/.ssh/authorized_keys
    owner: docker
    group: docker
    mode: "u=rw,g=r,o=r"
```

3. 複製 nginx vhost 設定檔到遠端，並備份原有的檔案

```yaml
- name: copy nginx vhost and backup the original
  ansible.builtin.copy:
    src: files/ironman.conf
    dest: /etc/nginx/sites-available/default
    owner: root
    group: root
    mode: 0644
    backup: yes
```

<br>

### `ansible.builtin.file`

[file module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/file_module.html#ansible-collections-ansible-builtin-file-module) 是在遠端建立和刪除檔案 (file)、目錄 (directory) 和軟連結 (symlinks) 的檔案模組。它類似的 Linux 指令為 `chown`、`mkdir` 和 `touch`。

1. 建立檔案 (touch)，並設定權限為 644

```yaml
- name: touch a file, and set the permissions
  ansible.builtin.file:
    path: /etc/motd
    state: touch
    mode: "u=rw,g=r,o=r"
```

2. 建立目錄 (mkdir)，並設定檔案擁有者為 docker。

```yaml
- name: create a directory, and set the permissions
  ansible.builtin.file:
    path: /home/docker/.ssh/
    state: directory
    owner: docker
    mode: "700"
```

3. 建立軟連結 (ln)。

```yaml
- name: create a symlink file
  ansible.builtin.file:
    src: /tmp
    dest: /home/docker/tmp
    state: link
```

<br>

### `ansible.builtin.lineinfile`

[lineinfile](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/lineinfile_module.html) module 是個可用正規表示法對檔案進行插入或取代文字的檔案模組。它類似的 Linux 指令是 `sed`。

1. 移除 docker 使用者的 sudo 權限

```yaml
- name: remove sudo permission of docker
  ansible.builtin.lineinfile:
    dest: /etc/sudoers
    state: absent
    regexp: '^docker'
```

2. 在 /etc/hosts 檔案裡用 127.0.0.1 localhost 取代開頭為 127.0.0.1 的一行

```yaml
- name: set localhost as 127.0.0.1
  ansible.builtin.lineinfile:
    dest: /etc/hosts
    regexp: '^127\.0\.0\.1'
    line: '127.0.0.1 localhost'
    owner: root
    group: root
    mode: 0644
```

<br>

### `ansible.builtin.service`

[service module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html#ansible-collections-ansible-builtin-service-module) 是用來管理遠端系統服務的系統模組。它類似的 Linux 指令為 `service`。

1. 啟動 Nginx

```yaml
- name: start nginx service
  ansible.builtin.service:
    name: nginx
    state: started
```

2. 停止 Nginx

```yaml
- name: stop nginx service
  ansible.builtin.service:
    name: nginx
    state: stopped
```

3. 重開網路服務

```yaml
- name: restart network service
  ansible.builtin.service:
    name: network
    state: restarted
    args: eth0
```

<br>

### `ansible.builtin.shell`

[shell module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/shell_module.html#ansible-collections-ansible-builtin-shell-module) 是可以在遠端用 `/bin/sh` 執行指令的指令模組，支援變數 (variables) 和 `<`、`>`、`|`、`;` 和 `&` 等運算。

1. 藉由 `ls` 和 `wc` 檢查檔案數量

```yaml
- name: check files number
  ansible.builtin.shell: ls /home/docker/ | wc -l
```

2. 把所有的 Python 行程給砍掉

```yaml
- name: kill all python process
  ansible.builtin.shell: kill -9 $(ps aux | grep python | awk '{ print $2 }')
```

<br>

### `ansible.builtin.stat`

[stat module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/stat_module.html#ansible-collections-ansible-builtin-stat-module) 是用來檢查檔案狀態的檔案模組。其類似的 Linux 指令為 `stat`。

1. 檢查檔案是否存在，若不存在則建立他。

```yaml
- name: check the 'vimrc' target exists
  ansible.builtin.stat:
    path: /home/docker/.vimrc
  register: stat_vimrc

- name: touch vimrc
  file:
    path: /home/docker/.vimrc
    ansible.builtin.state: touch
          mode: "u=rw,g=r,o=r"
  when: stat_vimrc.stat.exists == false
```

2. 取的某檔案的 md5sum

```yaml
- name: Use md5sum to calculate checksum
  ansible.builtin.stat:
    path: /path/to/something
    checksum_algorithm: md5sum
```

<br>

### 其他

其他還有很多可以使用的 Module ，詳情可以查看 [Ansible.Builtin](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/index.html)。

<br>

## Ansible 發送通知到 Telegram Bot

剛剛看了很多內建的模組，當然 Ansible 還有很多好玩的模組可以使用，我們就跟 [使用 Jenkins 設定 GitHub 觸發程序並通知 Telegram Bot 文章](https://pin-yi.me/jenkins/) 一樣，將我們取得的內容傳送到 Telegram Bot 吧！那首先我們要先創造一個 Telegram Bot，在 Telegram 找到一個機器人叫 `BotFather` 的官方機器人帳號。並使用指令 `/newbot`，會看到一下畫面：

<br>

{{< image src="/images/Ansible/telegram_1.png"  width="600" caption="Telegram 創建機器人" src_s="/images/Ansible/telegram_1.png" src_l="/images/Ansible/telegram_1.png" >}}

<br>

他詢問你要幫機器人取叫什麼名稱，可以直接在輸入欄位輸入想要取的名稱，當然不能是別人已經取過的：

<br>

{{< image src="/images/Ansible/telegram_2.png"  width="600" caption="Telegram 創建機器人" src_s="/images/Ansible/telegram_2.png" src_l="/images/Ansible/telegram_2.png" >}}

看到它回覆你 `Done!` 代表成功了，接下來你會拿到一組 API Token，像我的是 `5335968936:AAEDO_Tudhy0t577jtbF9TpgrzqOsL99h9c` (已更換，大家放心 😂 )，接下來開啟瀏覽器輸入以下網址 `https://api.telegram.org/bot{API Token}/getupdates`，其中的 `{API Token}` 請帶入自己的 Token，直到出現 `{"ok":true,"result":[]}` 代表完成。

<br>

接下來開啟你自己的 Bot ，打上 `/start` 指令，重新整理剛剛的網頁就可以看到以下這樣的文字：

```
{"ok":true,"result":[{"update_id":606594112,"message":{"message_id":1,"from":{"id":493995679,"is_bot":false,"first_name":"\u54c1\u6bc5","last_name":"Ian","username":"pinyichuchu","language_code":"zh-hans"},"chat":{"id":493995679,"first_name":"\u54c1\u6bc5","last_name":"Ian","username":"pinyichuchu","type":"private"},"date":1652695148,"text":"/start","entities":[{"offset":0,"length":6,"type":"bot_command"}]}}
```

這是你傳訊息給 Bot 它所收到的 API，資料很多沒關係，我們找到 `id`，像我的是 `493995679`，這個就是我跟機器人的聊天室，我們就先回到 Ansible 這邊吧！

<br>

開啟一個新的檔案叫 `send_notify_tg.yaml`，打以下內容：

```yaml
---
- name: Send notify
  hosts: all
  tasks:
    - name: Send notify to Telegram
      community.general.telegram:
        token: "9999999:XXXXXXXXXXXXXXXXXXXXXXX"
        api_args:
          chat_id: 000000
          parse_mode: "markdown"
          text: "Your precious application has been deployed: https://example.com"
          disable_web_page_preview: True
          disable_notification: True
```
可以看到我們使用的模組不是 Ansible 內建的，而是社群別人寫的，詳細可以參考 [community.general.telegram module – module for sending notifications via telegram](https://docs.ansible.com/ansible/latest/collections/community/general/telegram_module.html#ansible-collections-community-general-telegram-module)：

<br>

其中 token 就是我們剛剛在 `BotFather` 那邊所拿到的 Token，chat_id 就是我們剛剛在網頁上看到的 id，把資料都輸入進去後，我們可以修改 text 內容，改成 "Send notify to Telegram 測試傳送通知"，接著執行 `ansible-ploybook send_notify_tg.yaml` ，看看能不能正常收到通知！ 

<br>

{{< image src="/images/Ansible/telegram_3.png"  width="600" caption="發送通知至 Telegram Bot" src_s="/images/Ansible/telegram_3.png" src_l="/images/Ansible/telegram_3.png" >}}
這樣子就成功透過 Ansible Module 傳送通知給 Telegram 囉！

<br>

我們可能需要將機器人加入群組內，這時候需要更換一下 chat_id，先將機器人加入群組，再次到剛剛瀏覽器的網頁刷新，查看 chat 後面的 id 帶有 `-`，像是 `-540226836` 這樣，這個就是該群組的 ID，將 send_notify_tg.yaml 的 chat_id 修改成 `-540226836` 在測試看看，他就會在群組中發送通知囉！

```
{"update_id":606594124,"message":{"message_id":14,"from":{"id":493995679,"is_bot":false,"first_name":"\u54c1\u6bc5","last_name":"Ian","username":"pinyichuchu","language_code":"zh-hans"},"chat":{"id":-540226836,"title":"\u54c1\u6bc5 & AnsibleSendMessageBot","type":"group","all_members_are_administrators":true},"date":1652696181,"group_chat_created":true}}
```

<br>

{{< image src="/images/Ansible/telegram_4.png"  width="600" caption="發送通知至 Telegram 群組 Bot" src_s="/images/Ansible/telegram_4.png" src_l="/images/Ansible/telegram_4.png" >}}

<br>

## 參考資料

[現代 IT 人一定要知道的 Ansible 自動化組態技巧](https://chusiang.gitbooks.io/automate-with-ansible/content/)

[Ansible 安裝](https://tso-liang-wu.gitbook.io/learn-ansible-and-jenkins-in-30-days/ansible/ansible/ansible-installation)

[怎麼用 Docker 練習 Ansible？](https://chusiang.gitbooks.io/automate-with-ansible/content/05.how-to-practive-the-ansible-with-docker.html)

[community.general.telegram module – module for sending notifications via telegram](https://docs.ansible.com/ansible/latest/collections/community/general/telegram_module.html#ansible-collections-community-general-telegram-module)