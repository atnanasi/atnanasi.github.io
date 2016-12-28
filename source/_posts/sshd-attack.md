---
title: SSHに対するアタックへの効果的な対処方法
date: 2016-12-23 20:35:43
tags:
    - Security
    - Linux
    - SSH
    - Server
---

# SSHに対するアタック
皆さんこんにちは。

サーバーを運用するとなると殆どの場合はSSHによるコンソールへのアクセスを用意しますね。
しかしSSHというのは設定をきちんとしないと簡単に不正ログインを許してしまう場合があります。
また、アドレスを晒していないから大丈夫などと思ってる人も居るかもしれませんが、そんな事はありません。
攻撃者はありとあらゆる方法でIPアドレスをリストアップして攻撃してきます。

今回は私が実践しているSSHサーバーのセキュリティを書いておきます。

# 1.rootでのログインを禁止
これは基本中の基本です。

/etc/ssh/sshd_config
```
 PermitRootLogin no
```

# 2.パスワードによるログインを禁止
これも基本中の基本です。

/etc/ssh/sshd_config
```
PermitEmptyPasswords no
```

# 3.公開鍵認証で使える鍵の制限
これが私がしているなかでかなり効果的です。

基本的には皆さんはrsa鍵を使用していると思います。
しかし、最近の攻撃では何とrsa鍵を使ってアタックしてくる人が居るのです。

じゃあどうするか、簡単です。
**使わない鍵を許可しなければよいのです。**

最初は全て使えるようになっているので、使う鍵だけコメントアウトを外します。

```
#HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_dsa_key
#HostKey /etc/ssh/ssh_host_ecdsa_key
#HostKey /etc/ssh/ssh_host_ed25519_key
```

私はこのようにしています。

```
#HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_dsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key
```

ecdsaとed25519はあまり使われていない鍵で、短い鍵のビット長で強い強度を持てるように設計されています。

# 最後に
どうでしたか。

簡単な設定でセキュリティ強度は飛躍的に向上するのが分かって貰えたかと思います。
サーバーに不正ログインされてしまうと自分だけではなく、踏み台などで他のサーバーにも迷惑をかけてしまいます。
公開してないから大丈夫などと思わず、必ずセキュリティ設定をするようにしてください。

# 蛇足
fail2banとかsshguardとかを使うのが最強
