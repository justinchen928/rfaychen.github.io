---
title: "透過 SSH key pair 登入遠端伺服器"
date: 2019-12-29T22:10:38+08:00
categories: [tutorials]
tags: [ssh]
draft: false
---

![authentication flow](https://www.ssh.com/hubfs/Imported_Blog_Media/SSH_simplified_protocol_diagram-2.png)
*image from https://www.ssh.com/ssh*

## 前言
這篇文件在介紹如何建立ssh的金鑰組並透過這組金鑰登入遠端的伺服器，來解決使用VS Code直接遠端開發時會需要一直輸入密碼的困擾。

## 1. 建立金鑰
首先要建立一組金鑰，打開terminal輸入
```shell
ssh-keygen -t ed25519
```
`-t`可以指定演算法。
接著會詢問你要儲存的地方和名稱，直接按enter跳過的話，預設會存在使用者目錄下的`.ssh`目錄下。
名稱會是：
- public key `id_ed25519.pub`
- private key `id_ed25519`

同時還會詢問你是否要設定`passphrase`，像是通關密語。有設定的話，使用這把key登入時就要輸入。這邊可以直接按enter跳過。

## 2. 放到遠端伺服器
當建立好public key和private key後，接下來只要透過`ssh-copy-id`將public key安裝到遠端伺服器上就完成了。
```shell
ssh-copy-id -i ~/.ssh/your_key.pub user@host
```
`user@host`改成你的帳號和遠端伺服器的ip或domain。

## 3. 使用private key登入遠端伺服器
當上述步驟都完成後可以直接透過 `-i` 指定private key的位置ssh登入遠端伺服器。
```shell
ssh -i ~/.ssh/your_key user@host
```

## 人生不如意十之八九
當前兩步驟做完後第三步驟卻還是無法登入時可以看看遠端伺服器的sshd可能沒有設定接受public key的驗證方式，檢查伺服器的`/etc/ssh/sshd_config`檔案中有看到下面這行
```shell
PubkeyAuthentication yes
```
如果是 `false` 的話改完重啟`sshd`服務，再試試看。
如果還是不行，在伺服器端可以使用
```shell
sshd -d
```
開啟debug模式，看看是哪邊的問題。詳細內容參考 [sshd](https://www.ssh.com/academy/ssh/sshd)