One SQL via where not:
We have:
- product has many items
- item belong to product

```
class Product < ApplicationRecord
   has_many :items
end
```

```
class Item < ApplicationRecord
   belongs_to :product
end
```

Imagine we have a list of item and a product.
```
  except_items = Item.where('id > 10')
  product = Product.first
```
we want to get all items belongs to product but exclude `except_items`
SO we can: 

```
# Not good
product.items.where.not(id: except_items.ids)
# it will class two query:
# 1 get items_ids via "except_items.ids"
# 2 get items 'product.items.where.not(id: except_items.ids)'
# SQL
# SELECT `items`.`id` FROM `items` WHERE `items`.`id` > 10
# SELECT COUNT(*)
#   FROM `items`
#     WHERE `items`.`product_id` = 1
#     AND (`items`.`id` NOT IN [1,2,3,4,5,6,7,8,9])

# Good
product.items.where.not(id: except_items)
# it will have only one quest and result

#SQL
# SELECT COUNT(*)
#   FROM `items`
#     WHERE `items`.`product_id` = 1
#     AND (`items`.`id` NOT IN (SELECT `items`.`id` FROM `items` WHERE `items`.`id` > 10))

# The final result is the same
```

Product.items.not(id: )