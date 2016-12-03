План к занятию №3
=================

* [Model](http://rusrails.ru/active-model-basics) (M из MVC) в Rails
* [Команды Postgres](lab3/postgres.md)
* [Конфигурация базы данных приложения](lab3/database.md)
* [Каркас (scaffold)](http://rusrails.ru/a-guide-to-the-rails-command-line)
* [Миграции базы данных](http://rusrails.ru/rails-database-migrations) и `db/schema.rb`
* *Scaffold с Bootstrap'ом

---

Домашнее задание
================

1. Настроить подключение к Postgres'у, находящемуся на нашей вагрантовской виртуалке, из RubyMine на домашнем компьютере.
* Создать scaffold для пользователей. Подумайте, какие поля могут быть нужны конкретно в вашем случае: поле `name` и `email` и т.д.
* В появившиеся view добавить нужные элементы дизайна, который вы делали в прошлом д/з.
* Разобраться со всеми основными методами, автоматически создавшимися в контроллере `users_controller`. Понять, что делает каждый из них.
* `rails c`: научиться создавать, редактировать и удалять записи в базе посредством интерактивной консоли Rails.

---

Что почитать по Ruby?
=====================

* [Вики-книга](https://ru.wikibooks.org/wiki/Ruby)
* [Вики-справочник по классам](https://ru.wikibooks.org/wiki/Ruby/%D0%A1%D0%BF%D1%80%D0%B0%D0%B2%D0%BE%D1%87%D0%BD%D0%B8%D0%BA#.D0.9A.D0.BB.D0.B0.D1.81.D1.81.D1.8B)
* [Официальная документация](https://ruby-doc.org/core-2.3.1/) (снизу описание классов и методов с обилием примеров; например, про [массивы](https://ruby-doc.org/core-2.3.1/Array.html))
* [Ruby Style Guide](https://github.com/bbatsov/ruby-style-guide/blob/master/README.md#the-ruby-style-guide) (у гайда есть [перевод](https://github.com/arbox/ruby-style-guide/blob/master/README-ruRU.md#blue_book-Руби-руководство-по-стилю-оформления), но он не до конца актуальный)
* [Задание про звёздочки](https://github.com/itmoprog/2016-s2-ruby/blob/master/star_pyramid.md#Пирамида-из-звёздочек) — желающие могут поделать :)
