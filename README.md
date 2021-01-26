# Тестовое приложение для разворачивания с помощью Ansible

В рамках финального задания для новичков я предлагаю "испачкать руки" и развернуть приложение с большим количеством зависимостей на нашем тестовом стенде.

Нужно создать playbook, который выполнит развертывание приложения на узле `web`. Inventory будет предоставлен. Тестовый стенд на базе CentOS 7.

1. Используем роли rotoro_cloud.mysql_role и rotoro_cloud.nginx-php-fpm-role для настройки окружения.
    - нужно переопределить переменные `mysql_user_pass`

2. Создаем свою роль, которая выполнит нужные команды в размещении, определенном в переменной `app_dir`
    - склонировать https://github.com/rotoro-cloud/Laravel-Real-Estate-Venue-Portal.git в нужный каталог
    - создать .env из .env.example
    - поменять в нем параметры `DB_DATABASE`, `DB_USERNAME`, `DB_PASSWORD` на заданные ранее на уровне playbook
    - с помощью пакетного менеджера OC установить `composer`
    - подтянуть зависимости проекта с помощью 
    
      ```
      sudo composer install
      ```
      
    - выполнить создание ключа приложения 
    
      ```
      sudo php artisan key:generate;
      ```
      
    - наполнить базу тестовой информацией
    
      ```
      sudo php artisan migrate --seed;
      ```
      
    - создать символические ссылки для хранилища 
    
      ```
      sudo rm -rf public/storage; sudo php artisan storage:link;
      ```
      
    - дать доступ нужным директориям
    
      ```
      sudo chown -R nginx.nginx /usr/share/nginx/html/;
      ```
      ```
      sudo chmod -R ug+rwx /usr/share/nginx/html/storage /usr/share/nginx/html/bootstrap/cache;
      ```
      ```
      sudo chmod -R o+rwx /usr/share/nginx/html/storage/logs;)
      ```

Я предполагаю, что ты будешь использовать специализированные модули Ansible, а не только используя модули вроде `command` и `shell`
В курсе будет демонстрация решения, если ты вдруг застрял.
Для проверки используй ссылку в терминале `Estate App`.

Я предлагаю начать с такой конструкции плейбука

```
- name: Deploy the Application
  hosts: web
  become: yes
  vars:
    app_dir: /usr/share/nginx/html/
    git_repo: https://github.com/rotoro-cloud/Laravel-Real-Estate-Venue-Portal.git
    db_name: appDb
    mysql_user_name: username
    mysql_user_password: Password
    service_user: nginx
    service_group: nginx
    php_repo: remi-php74
  roles:
    - role: mysql-role
    - role: nginx-php-fpm-role
    - role: estate-app-role
```

# Laravel 6 Real Estate / Venues Management with Adminpanel

Transformed [Bootstrap theme Homespace](https://colorlib.com/wp/template/homespace/) into a mini-clone of [Hirespace.com](https://hirespace.com) portal, fully manageable with adminpanel generated with [QuickAdminPanel](https://quickadminpanel.com), 
to manage all the venues, locations, event types.

Also added SEO important stuff like Articles Slugs in URLs.

This is a result of 2-hours live-coding series on Youtube: [see playlist](https://www.youtube.com/watch?v=Oq_b1g_JpCM&list=PLdXLsjL7A9k0HPZVGiA7WZDM9Zg0OnulJ)

![Laravel Real estate administration](https://laraveldaily.com/wp-content/uploads/2019/10/Screen-Shot-2019-10-25-at-11.12.16-AM.png)

![Laravel Real estate front](https://laraveldaily.com/wp-content/uploads/2019/10/Screen-Shot-2019-10-25-at-1.53.12-PM.png)

- - - - -

- Front-end part is taken from [Homespace theme](https://colorlib.com/wp/template/homespace/) by [Colorlib](https://colorlib.com) and transformed into Laravel Blade and assets.
- Admin part is fully generated with [QuickAdminPanel](https://2019.quickadminpanel.com).

---

## How to use

- Clone the repository with __git clone__
- Copy __.env.example__ file to __.env__ and edit database credentials there
- Run __composer install__
- Run __php artisan key:generate__
- Run __php artisan migrate --seed__ (it has some seeded data for your testing)
- That's it: launch the main URL. 
- You can login to adminpanel by going go `/login` URL and login with credentials __admin@admin.com__ - __password__

## License

Basically, feel free to use and re-use any way you want.

---

## More from our LaravelDaily Team

- Check out our adminpanel generator [QuickAdminPanel](https://quickadminpanel.com)
- Read our [Blog with Laravel Tutorials](https://laraveldaily.com)
- FREE E-book: [50 Laravel Quick Tips (and counting)](https://laraveldaily.com/free-e-book-40-laravel-quick-tips-and-counting/)
- Subscribe to our [YouTube channel Laravel Business](https://www.youtube.com/channel/UCTuplgOBi6tJIlesIboymGA)
- Enroll in our [Laravel Online Courses](https://laraveldaily.teachable.com/)
