開発用

* amazonlinux の dockerイメージに
* `ansible` で `rbenv` を入れて
* `sinatra` を動かす

まで。

## docker 

### コンテナ内のsshdにログインする鍵を作成

```sh
ssh-keygen -f container/ec2_sample/id_rsa  -N ""
```

とかやってパスなしの鍵を作っておく。

#### 注意

image作り直すとホストの `~/.ssh/known_hosts` の登録済みホストのせいで

> Host key verification failed.

とかなるので、消すかなんかする。

https://qiita.com/grgrjnjn/items/8ca33b64ea0406e12938

### docker image のビルド

```sh
docker build -t kuryu/ec2-sample container/ec2_sample/
```

`docker-compose.yml` の image で指定するので適当にタグ名をつけておく。

### docker コンテナ起動

```sh
cd container/
docker-compose up
```

#### docker exec でコンテナ内に入る場合

```sh
docker exec -it ec2-sample bash
```

### sshで入れるか確認

```sh
ssh ec-user@localhost -p 2222 -i container/ec2_sample/id_rsa
```

もしくは

```sh
cd ansible
ssh -F ssh_config  ec2-sample
```

## ansible

### 実行

```sh
cd ansible
ansible-playbook -v main.yml
```

## 確認


### コンテナ側

sshでコンテナに入って、ansibleで指定したバージョンの `ruby` が入っていればOK。

```
[ec2-user@e733b3a090c0 ~]$ ruby -v
ruby 2.5.0p0 (2017-12-25 revision 61468) [x86_64-linux]
[ec2-user@e733b3a090c0 ~]$ rbenv  versions
* 2.5.0 (set by /home/ec2-user/.rbenv/version)
```

で、テスト用 `sinatra` アプリを動かす。

```sh
cd ~/app
gem install bundler
bundle install

ruby app.rb -p 3000 -o 0.0.0.0
```

bind address `-h 0.0.0.0` つけておかないと外からアクセス出来ないので注意。

### ホスト側

```
curl localhost:3000
```

で `Hello World!` が返ってくればOK。