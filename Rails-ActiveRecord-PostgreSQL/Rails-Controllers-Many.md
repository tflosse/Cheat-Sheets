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

Conceptually:
![](https://i.imgur.com/Pf8ZHqO.png)

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

We can now find Ledger, but we wouldn't be able to query across models. For instance `ap Trader.find(1).commodities` because the relations are not yet defined.

#### Models:

`Trader.rb`
```rb
class Trader < ApplicationRecord
    has_many :ledgers
    has_many :commodities, through: :ledgers
end
```
`Commodity.rb`
```rb
class Commodity < ApplicationRecord
    has_many :ledgers
    has_many :traders, through: :ledgers
end
```
**Attention** to syntax with the `:`

Note the Ledger model was scaffolded as:
```rb
class Ledger < ApplicationRecord
  belongs_to :trader
  belongs_to :commodity
end
```
A single ledger *does not have multiple traders or multiple commodities*. A ledger will have ***one trader and one commodity***. Therefore it belongs to a trader and belongs to a commodity.

With the model changes made, the previous command will get:
![](https://i.imgur.com/lb88ISh.png)

We can also try to find all the traders for one commodity:
![](https://i.imgur.com/dDV2jlk.png)

**This isn't many-to-many yet** We've estblished *one-to-many* both ways, but not many-to-many.

Create two more ledgers, that will establish that both Trader 1 and Trader 2 also have Commodity 2.

```rb
ap Ledger.create( trader_id: 1, commodity_id: 2, qty: 1 )
ap Ledger.create( trader_id: 2, commodity_id: 2, qty: 1 )
```

Now we can check with:
![](https://i.imgur.com/r2Wgkej.png)
Or by querying ledgers for each trader or commodity:
![](https://i.imgur.com/JWug6vC.png)

****
So far, we have established many-to-many relations between models with ledgers. Now we can **create a ledger `through` Trader**.

```rb
ap Trader.find(1).ledgers.create( commodity_id: 8, qty: 8 )
# check with 
ap Trader.find(1).commodities
```
![](https://i.imgur.com/yyR1p6k.png)

### Controller Routes

In `traders_controller.rb`, we want to change the GET routes to show the associated commodities along with each trader:
```rb
  # GET /traders
  def index
    @traders = Trader.all

    render json: @traders.to_json(include: :commodities)
  end

  # GET /traders/1
  def show
    render json: @trader.to_json(include: :commodities)
  end
```

And vice-versa 
```rb
  # GET /commodities
  def index
    @commodities = Commodity.all

    render json: @commodities.to_json(include: :traders)
  end

  # GET /commodities/1
  def show
    render json: @commodity.to_json(include: :traders)
  end
```

Lastly, make sure the ledger model will do the same -- in `ledgers_controller.rb` the syntax will be slightly different because of the `belongs_to` relations.
```rb
  # GET /ledgers
  def index
    @ledgers = Ledger.all

    render json: @ledgers.to_json(include: [:trader, :commodity])
  end

  # GET /ledgers/1
  def show
    render json: @ledger.to_json(include: [:trader, :commodity])
  end
  ```

  ![](https://i.imgur.com/jWzgW69.png)
  ![](https://i.imgur.com/nHnrf4F.png)
  ![](https://i.imgur.com/LAXHRiu.png)

  ****
  #### Validating & avoiding duplicates:

If you happened to run the same command twice, and ended up with duplicate ledgers, we can validate in the `Ledger.rb` model: 


```rb
class Ledger < ApplicationRecord
  belongs_to :trader
  belongs_to :commodity

  validates_uniqueness_of :commodity_id, scope: :trader_id
  validates_uniqueness_of :trader_id, scope: :commodity_id
end
```

Adding duplicates will lead to an error, meanwhile delete the Ledgers we don't want with `Ledger.destroy(3)`.


