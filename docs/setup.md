🚀 Laravel Docker環境構築 まとめ
1. 起動
docker compose up -d --build

・nginx → 80番 (http://localhost)
・php → Laravel実行環境
・mysql → laravel_db (ユーザ: laravel_user / laravel_pass)
・phpMyAdmin → http://localhost:8080

2. Laravelプロジェクト準備
docker compose exec -u 1000:1000 php bash -lc "composer create-project laravel/laravel ."
docker compose exec -u 1000:1000 php bash -lc "cp .env.example .env && php artisan key:generate"


.env のDB設定：

DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=laravel_db
DB_USERNAME=laravel_user
DB_PASSWORD=laravel_pass

3. 権限設定
docker compose exec php bash -lc "chown -R www-data:www-data storage bootstrap/cache && chmod -R ug+rwX storage bootstrap/cache"

4. DBマイグレーション
docker compose exec php php artisan migrate

5. タイムゾーン統一

・php.ini
date.timezone = Asia/Tokyo


・docker-compose.yml
environment:
  HOME: /var/www
  TZ: Asia/Tokyo


・MySQL (my.cnf)
[mysqld]
default-time-zone = '+09:00'

6. よく使う確認コマンド
# phpコンテナ内の時刻
docker compose exec php date

# PHPのタイムゾーン
docker compose exec php php -r "echo date_default_timezone_get(), PHP_EOL;"

# Laravel視点
docker compose exec php php artisan tinker
>>> now()

# MySQL
docker compose exec mysql mysql -uroot -proot -e "SHOW VARIABLES LIKE 'time_zone';"
