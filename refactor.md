refactor

1. スクリプト概要の表示
2. b/env判定(root,linux,arch,ram) - function
3. f/オプション選択 - function
    0. compose.yamlがあるか引数でオプション指定されていればそれを拾う #値が不正な場合、エラーを出す(値を直すか、compose.yamlを削除するように言う)
    1. method選択(dockerhub,docker_build,systemd)
    2. source入力
        i. docker_hub
            1. dockerhubリポジトリ入力
        ii. docker_build
            1. gitリポジトリ入力
        iii. systemd
            1. gitリポジトリ入力
    3. 実行ユーザー名入力
    4. ホスト名入力
    5. misskeyのポート入力
    6. nginx入れるか確認
        i. 入れない
            0. nginx, cloudflare, certbotをfalseにする
        ii. 入れる
            1. ポート開けるか(ufw/iptables/no)、開ける場合はsshポートも聞く
            2. certbot入れるか
                i. 入れない
                ii. 入れる
                    1. 認証方法の選択(dns-cloudflare,http)
                        i. dns-cloudflare
                            1. cloudflareのメールアドレスとapikeyの入力 #ここで入力したメールアドレスが証明書取得にも使用されることに注意
                            2. cloudflare.iniに書き込む
                        ii. http
                            1. メールアドレス入力 #証明書取得に使用
    7. postgresql入れるか確認
        i. 入れない #すでに構築済みの場合(構築していない場合は予め構築するように言う)
            1. hostとportを入力
        ii. 入れる
            1. hostはmisskeyと同じ、portは5432
    8. postgresqlのユーザー名とパスワード、db名を入力
    9. redis入れるか確認
        i. 入れない #すでに構築済みの場合(構築していない場合は予め構築するように言う)
            1. hostとportを入力
        ii. 入れる
            1. hostはmisskeyと同じ、portは6379
    10. redisのパスワードを入力
    11. swap確認
        i. 十分なメモリがある場合
        ii. 十分なメモリがない場合
            1. swapを作成するか確認
                i. 作成する
                ii. 作成しない
                    1. メモリ不足でインストールが失敗する可能性がある旨を表示
    12. 設定内容の確認 #compose.yamlがあるか引数でオプション指定されていた場合、確認を出すかどうか確認する必要がある #installed = trueの場合、失敗する&データが失われる可能性がある旨の警告を出す
    13. 設定内容の保存 #compose.yamlに保存する(上書き)
4. b/インストール - function
    1. compose.yamlでinstalled = trueにする #再実行された場合に警告するため
    #methodによらず共通設定
    2. ユーザー作成
    3. apt update && apt install(methodによって入れるパッケージが一部異なる)
    4. すでにmisskeyディレクトリがある場合は削除
    5. misskey用のconfig.yamlを作成
    6. nginx入れる場合、ポートを開けて、gpgとリポジトリを追加
    7. リポジトリ追加
        i. systemdの場合
            1. nodejsのセットアップをする(インストールの前段階)
        ii. docker_hub,docker_buildの場合
            1. dockerのgpgとリポジトリを追加
    8. redis入れる場合、gpgとリポジトリを追加
    9. apt update && apt install(nginx, nodejs, docker, postgresql, いずれもif)
    10. postgresql入れる場合、セットアップスクリプト動かす
    10. systemdの場合、corepackを有効化する
    11. インストール確認(バージョン表示)
        i. systemdの場合
            1. node, corepack
            2. redis, nginx, postgresql (if)
        ii. docker_hub,docker_buildの場合
            1. docker
            2. redis, nginx, postgresql (if)
    11. postgresql入れる場合、DBとユーザーを作成
    12. redis入れる場合
        1. redis-serverのservice有効化
        2. redis.confを設定
    13. nginx入れる場合
        1. configファイルを作成 #certbotでcloudflare認証使わない場合に、web認証のために:80でアクセス受け付けないといけないため
        2. certbot入れる場合、証明書取得してnginx.confファイルを設定
        3. misskeyをnginx.confに設定
        4. nginx.confの構文チェック
        5. nginxのserviceを有効化
    14. docker_hub, docker_buildの場合
        1. misskeyユーザーで実行するように設定
        2. postgresql使う場合、pg_hba.confとpostgresql.confを設定
    15. misskeyのセットアップ
        i. systemdの場合
            1. セットアップ
            2. service作成
            3. .misskey.envの作成
            4. fin
        ii. docker_hub, docker_buildの場合
            1. docker_buildの場合、ビルド
            2. dockerコンテナの立ち上げ
            3. .misskey-docker.envの作成
            4. dockerのログ表示
fin