## Rails Controllers, Cont'd

### Many-to-many Setup

```rb
rails new trading_app_api --api -d postgresql --skip-git --skip-active-storage
cd trading_app_api

# I added -- gem 'awesome_print' -- to Gemfile for ease of read later on, you don't need to run bundle if you didn't use it.
bundle

rails db:create
rails g scaffold trader name money:decimal{8.2}
rails g scaffold commodity name price:decimal{8.2}
# That is, a precision of 8 which means 8 digits max
# and a scale of 2 which means 2 digits after the decimal point.
```

`Migration` files should look like this:

```rb
class CreateTraders < ActiveRecord::Migration[6.0]
  def change
    create_table :traders do |t|
      t.string :name
      t.decimal :money, precision: 8, scale: 2

      t.timestamps
    end
  end
end
```
```rb
class CreateCommodities < ActiveRecord::Migration[6.0]
  def change
    create_table :commodities do |t|
      t.string :name
      t.decimal :price, precision: 8, scale: 2

      t.timestamps
    end
  end
end
```

Run the migration with `rails db:migrate`.
`Schema.rb` will look like this:
```rb
ActiveRecord::Schema.define(version: 2020_08_05_152606) do

  # These are extensions that must be enabled in order to support this database
  enable_extension "plpgsql"

  create_table "commodities", force: :cascade do |t|
    t.string "name"
    t.decimal "price", precision: 8, scale: 2
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end

  create_table "traders", force: :cascade do |t|
    t.string "name"
    t.decimal "money", precision: 8, scale: 2
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end

end
```

Seed the database (using faker, other, or writing it out), and after running `raild db:seed` and `rails s` - check in the browser:

![](https://i.imgur.com/xcfwgVf.png)
![](https://i.imgur.com/0xXKJw4.png)
![](https://i.imgur.com/gITLoCC.png)

### Building the relations and controllers

We previously used `Foreign Keys` but we now have to establish that many traders can have many commodities, and that commodities can belong to many traders.

A better way to go about it, would be to create a table that keeps track of those relations. We can make a join table a couple of ways.

- `has_many_and_belongs_to`: makes an anonymous table that can't be expanded. For that it would need to have its own model, so it is recommended to use a more complicated relationship in case you want to expand the table in the future.

> a bit like this:
![](https://i.imgur.com/ZQiPMoM.png)

- `has_many :through`: makes a new model for the join table, where we can add columns and validation to the table if we need to; it is known as a **join model**.

`Ledger` will be its own model, and it will map the relationship between Traders and Commodities, except here we can add extra columns to the table. For instance, a qty column to document how many of a given commodity a trader owns -- it is information about the relation.

![](https://i.imgur.com/qKIqoDu.png)

```rb
rails g scaffold ledger trader:references commodity:references qty:integer
```
Migration will be created as:
```rb
class CreateLedgers < ActiveRecord::Migration[6.0]
  def change
    create_table :ledgers do |t|
      t.references :trader, null: false, foreign_key: true
      t.references :commodity, null: false, foreign_key: true
      t.integer :qty

      t.timestamps
    end
  end
end
```
Run `rails db:migrate`

`Schema.rb` now looks like this:
```rb
ActiveRecord::Schema.define(version: 2020_08_05_160344) do

  # These are extensions that must be enabled in order to support this database
  enable_extension "plpgsql"

  create_table "commodities", force: :cascade do |t|
    t.string "name"
    t.decimal "price", precision: 8, scale: 2
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end

  create_table "ledgers", force: :cascade do |t|
    t.bigint "trader_id", null: false
    t.bigint "commodity_id", null: false
    t.integer "qty"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
    t.index ["commodity_id"], name: "index_ledgers_on_commodity_id"
    t.index ["trader_id"], name: "index_ledgers_on_trader_id"
  end

  create_table "traders", force: :cascade do |t|
    t.string "name"
    t.decimal "money", precision: 8, scale: 2
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end

  add_foreign_key "ledgers", "commodities"
  add_foreign_key "ledgers", "traders"
end
```

(This is where awesome_print might come in handy...)
In the rails console -- `rails c` test the Ledger model:

![](https://i.imgur.com/Nc3QP8C.png)
![](https://i.imgur.com/0ZRESFS.png)

'references' type created columns called `trader_id` and `commodities_id`. With three models, **Trader, Commodity, Ledger** interacting with each other, there are a few variations of ActiveRecord commands that can be used.

We can create an ***association*** between a trader and a commodity by creating a ledger:

```rb
# ap is used to call on awesome_print, you might not be using it -- omit tha 'ap'
# Below creates a Ledger for Trader 1
ap Ledger.create(trader_id: 1, commodity_id: 1, qty: 1)
# We'll also create a second Ledger for Trader 2
ap Ledger.create(trader_id: 2, commodity_id: 1, qty: 1)
```
The first ledger creation should have looked like this:
![](https://i.imgur.com/aUwg65o.png)
No we can query for all ledger to check they were created:
![](https://i.imgur.com/v5mYPBs.png)

