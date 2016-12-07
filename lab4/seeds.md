Пример файла `db/seeds.rb`
==========================

```ruby
Image.delete_all
Image.reset_pk_sequence
Image.create(
  [
    {name: 'Первый', file: '1.jpg', theme_id: 2},
    {name: 'Второй', file: '2.jpg', theme_id: 2}
  ]
)

Theme.delete_all
Theme.reset_pk_sequence
Theme.create(
  [
    {name: '-----'},
    {name: 'Тема'}
  ]
)
```
