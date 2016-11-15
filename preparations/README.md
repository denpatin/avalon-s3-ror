Важное предисловие
==================

На основной машине, где Windows, **НЕ** должно быть кириллических символов в пути к домашней директории юзера. Т.е. `C:\Users\студент15` — недопустимо для Vagrant. Желательно, конечно, чтобы вообще не было символов, отличных от букв, цифр и знака подчёркивания или дефиса, то есть Vagrant может ругаться на наличие пробела в имени пользователя — и тогда беспрепятственно установить всё дома будет не так и просто. Если подобного нет, то можно идти дальше :) Если же такое есть, быть может, есть смысл создать нового пользователя с допустимым логином.

Поехали?
========

Для более гладкой работы с [Ruby](https://ru.wikipedia.org/wiki/Ruby), [Ruby on Rails](https://ru.wikipedia.org/wiki/Ruby_on_Rails), базами данных (в нашем случае — [PostgreSQL](https://ru.wikipedia.org/wiki/PostgreSQL)), а также просто закрепления материала по [Linux](https://ru.wikipedia.org/wiki/Linux)'у (в нашем случае — [Ubuntu](https://ru.wikipedia.org/wiki/Ubuntu)) мы воспользуемся [виртуальной машиной](https://ru.wikipedia.org/wiki/%D0%92%D0%B8%D1%80%D1%82%D1%83%D0%B0%D0%BB%D1%8C%D0%BD%D0%B0%D1%8F_%D0%BC%D0%B0%D1%88%D0%B8%D0%BD%D0%B0), но будем ставить её не вручную, а упростим и убыстрим задачу — дадим всё сделать [Vagrant](https://www.vagrantup.com/docs/why-vagrant)'у.

В итоге работа распределится следующим образом: на вашей основной машине (Windows или MacOS) вы редактируете код и смотрите в браузере результат, а на виртуальной машине код будет исполняться.

Установка необходимого ПО
=========================

1. Необходимо будет скачать и установить следующие программы или проверить, что установлены их последние версии:

  * [VirtualBox](https://www.virtualbox.org/wiki/Downloads) — система виртуализации ОС
    > Владельцы MacOS: если у вас Parallels, VirtualBox вам не нужен.
  * [Vagrant](https://www.vagrantup.com/downloads.html) — система конфигурирования виртуальных машин
  * [Git](https://git-scm.com/downloads) — система контроля версий

2. По завершению установки нужно установить плагин:

  ```bash
  vagrant plugin install vagrant-vbguest
  ```

  > Для владельцев MacOS, у которых Parallels, будет плагин
  > ```bash
  > vagrant plugin install vagrant-parallels
  > ```

3. Нужно будет создать файл `Vagrantfile` в корне своего проекта. Поскольку проекта у вас сейчас ещё нет, создайте директорию на локальном компьютере и перейдите в неё в терминале. В этой директории и должен лежать файл Vagrant'а. Создастся же он автоматически по следующей команде (если мы хотим 64-битную Ubuntu 16.04):

  ```bash
  vagrant init ubuntu/xenial64
  ```
  
  > Опять же, для Parallels на MacOS:
  > ```bash
  > vagrant init parallels/ubuntu-16.04
  > ```

  Тем не менее, для наших занятий я заранее заготовил настройки будущей виртуальной машины, поэтому вы можете заменить содержимое создавшегося файла на следующее:

  ```ruby
  Vagrant.configure('2') do |config|
    # No automatic updates
    config.vbguest.auto_update = false
    config.vm.box_check_update = false

    config.vm.define 'experteese', primary: true do |experteese|
      # We're going to use Ubuntu 16.04 x64
      experteese.vm.box = 'ubuntu/xenial64'

      # Disabling anytime updates
      experteese.vm.box_check_update = false

      # Forwarding all necessary ports
      experteese.vm.network 'forwarded_port', # Default Rails app port
                            guest: 3000,
                            host: 3000
      experteese.vm.network 'forwarded_port', # Default Postgres port
                            guest: 5432,
                            host: 5432

      # Virtual machine's hostname
      experteese.vm.hostname = 'experteese'

      # Initial configuration script
      experteese.vm.provision 'shell', path: 'setup.sh'

      # We are going to use the following virtual environment:
      # - VirtualBox as a hypervisor
      # - Maximum 2GB RAM
      # - One CPU core
      experteese.vm.provider 'virtualbox' do |vm|
        vm.memory = 2048
        vm.name = 'experteese'
        vm.cpus = 1
      end
    end
  end
  ```

4. Помимо `Vagrantfile` в той же директории должен находиться и файл `setup.sh`, отвечающий за установку дополнительного ПО. Создайте его:

  ```bash
  #!/bin/bash

  # Updating the system
  sudo apt-get -y update

  # Installing necessary software
  sudo apt-get -y install curl nodejs git

  # Installing RVM and Ruby
  # Importing the GPG key
  command curl -sSL https://rvm.io/mpapis.asc | gpg --import -
  # Downloading and installing RVM
  \curl -sSL https://get.rvm.io | bash -s stable
  # Adding the current user to the rvm group
  sudo usermod -a -G rvm ubuntu
  # Making RVM executable
  source /etc/profile.d/rvm.sh
  # Switching to the non-interactive mode
  bash --login
  # Installing the latest Ruby
  rvm install 2.3.1
  # Making Ruby v2.3.1 default
  rvm use --default 2.3.1
  # Installing necessary gems
  gem install bundler --no-ri --no-rdoc
  gem install rails

  # Installing Postgres
  sudo apt-get -y install postgresql postgresql-contrib postgresql-server-dev-9.5
  # Creating the role with privileges
  # -s makes a superuser, -r allows role creation, -d allows DB creation
  sudo -u postgres createuser -srd experteese_admin
  # Creating the DBs
  sudo -u postgres createdb experteese_development
  sudo -u postgres createdb experteese_test
  sudo -u postgres createdb experteese_production
  # Allowing Postgres to listen all ports
  echo "listen_addresses = '*'" | sudo tee -a /etc/postgresql/9.5/main/postgresql.conf
  echo "host all all all trust" | sudo tee -a /etc/postgresql/9.5/main/pg_hba.conf
  sudo -u postgres psql -c "ALTER ROLE experteese_admin WITH PASSWORD 'password';"
  sudo service postgresql restart
  ```

5. Файлы созданы. Теперь можно установить и базово настроить ОС, а также установить и настроить ПО. Вот здесь-то и пригождается Vagrant — всё это делается одной лишь командой:

  ```bash
  vagrant up
  ```

Подключение к гостевой ОС
=========================

Самый простой способ получить доступ к консоли гостевой машины — это ssh. Vagrant предоставляет эту возможность в самом простом для нас виде (нужно ввести эту команду из директории, где расположен `Vagrantfile`):

```bash
vagrant ssh
```

Не нужно вводить никаких паролей — Vagrant позаботился об этом за нас.

> Единственный совет: очень не рекомендую использовать `cmd` для работы с Vagrant'ом. Как минимум просто потому, что вы не сможете корректно использовать ssh. В папке (на Windows), куда установился Git, — например, `C:\Program Files\Git\` — найдите бинарник `git-bash.exe` и через него работайте с `vagrant ssh`.
>
> Нужно будет перейти в директорию, где находится на локальном компьютере `Vagrantfile`, но простые команды типа `cd C:\` тут не работают. Вместо этого у вас будет смонтированная подсистема, которая проецируется на привычную вам следующим образом: какое-нибудь `C:\Users\denpatin\projects\experteese` станет `/c/Users/denpatin/projects/experteese` — и именно в такой вариант надо будет делать команду `cd`.

Настройка связи RubyMine <> виртуальная машина
==============================================

Воспользуемся gemset'ами. [Gemset](https://rvm.io/gemsets/basics) — это в своём роде слепок/контейрен конкретной версии Ruby с набором версий конкретных гемов. Gemsets нередко используются, когда программист имеет дело в одном окружении с большим количеством проектов (чтобы версии гемов не смешивались — в одном gemset каждый гем может иметь только одну версию).

В нашем случае gemsets нам пригодятся для того, чтобы локально установленный RubyMine «видел» наличие нужных гемов. Ведь на локальной машине у нас не установлен ни Ruby, ни, соответственно, гемы, но они есть на виртуальной машине, но там их для конкретной версии Ruby может быть установлено (из-за разных проектов) бесчисленное множество, в т.ч. каждый гем может иметь по многу версий — и локальному RubyMine пришлось бы их все импортировать в себя, что и времени бы много заняло, и производительность IDE сильно упала. Именно для этого мы и создадим свой набор гемов и только его «скормим» RubyMine.

1. В консоли виртуальной машины (т.е. уже сделан `vagrant ssh`) из директории с вашим проектом выполните следующую команду, чтобы создать gemset:
```bash
rvm use 2.3.1@experteese --create
```
> Наш gemset назовём `experteese`. Если название проекта будет иным, просто замените это слово.

2. Чтобы впоследствии устанавливать гемы для проекта (в т.ч. и через `bundle install`), нужно указать, что это должно происходить в контексте нужного gemset'а:
```bash
rvm 2.3.1@experteese
```
3. Установите bundler (`gem install bundler`) и сделайте `bundle install`.
> Наш gemset будет расположен в `/usr/local/rvm/gems/ruby-2.3.1@experteese`.
* Далее откройте в RubyMine свой проект.
* `File -> Settings -> Languages & Frameworks -> Ruby SDK and Gems`.
* Нажмите зелёную инконку `+` и выберите `New remote...`.
* Переключите checkbox на `Vagrant` и в поле `Vagrant Instance Folter` выберите свою папку с проектом, где лежит `Vagrantfile`.
* Нам нужно указать RubyMine, где именно на виртуальной машине лежат руби и используемые в проекте гемы — вставить нужно тот самый путь к gemset'у, который мы получили: `/usr/local/rvm/gems/ruby-2.3.1@experteese`.
* RubyMine импортирует нужные гемы и сможет автодополнять код по контексту. Проверить, что гемы успешно импортировались, можно, посмотрев в Gemfile: строчки, в которых указаны использующиеся гемы, не должны подсвечиваться светло-жёлтым цветом.
