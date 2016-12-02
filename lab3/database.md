Настройка базы данных
=====================

Файл: `config/database.yml`

```yaml
default: &default
  adapter: postgresql
  encoding: unicode
  host: localhost
  username: experteese_admin
  password: password
  pool: 5

development:
  <<: *default
  database: experteese_development

production:
  <<: *default
  database: experteese_production

test:
  <<: *default
  database: experteese_test
```
