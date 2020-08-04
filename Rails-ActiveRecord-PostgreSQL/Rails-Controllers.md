## Controllers

Refer to Rails MD for setting up and starting migrations in Rails.

#### Seeding data

_Note_: Used 'faker' gem for the examples that follow

> Add `gem 'faker`' to gem file and run `bundle`

`seed.rb` will use it as follows to generate fake data.

```rb
10.times do
    Song.create(
        title: Faker::Hipster.sentence(3),
        artist: Faker::Team.name,
        cover: Faker::Placeholdit.image("50x50")
    )
end
```

> rails db:seed

```rb
irb(main):006:0> Song.all
  Song Load (0.7ms)  SELECT "songs".* FROM "songs"
+----+------------------------------------------+------------------------+----------------------------------+
| id | title                                    | artist                 | cover                            |
+----+------------------------------------------+------------------------+----------------------------------+
| 1  | Tote bag cliche mumblecore food truck... | Massachusetts bears    | https://placehold.it/300x300.png |
| 2  | Hashtag farm-to-table schlitz normcor... | Montana dragons        | https://placehold.it/300x300.png |
| 3  | Poutine distillery paleo brooklyn far... | Colorado conspirators  | https://placehold.it/300x300.png |
| 4  | Tacos viral ramps loko.                  | Indiana dragons        | https://placehold.it/300x300.png |
| 5  | Post-ironic goth street before they s... | Minnesota bees         | https://placehold.it/300x300.png |
| 6  | Migas offal viral food truck shabby c... | Pennsylvania elephants | https://placehold.it/300x300.png |
| 7  | Tacos whatever 90's austin pabst chic... | Massachusetts frogs    | https://placehold.it/300x300.png |
| 8  | Art party fixie echo synth kombucha e... | Maryland dogs          | https://placehold.it/300x300.png |
| 9  | Chia hashtag wes anderson neutra smal... | Montana spiders        | https://placehold.it/300x300.png |
| 10 | Banh mi +1 chambray listicle.            | Iowa banshees          | https://placehold.it/300x300.png |
+----+------------------------------------------+------------------------+----------------------------------+
10 rows in set
```

#### Creating routes

In `config` directory, `routes.rb` already exists:

```rb
Rails.application.routes.draw do
  # For details on the DSL available within this file, see https://guides.rubyonrails.org/routing.html
  resources :songs
end
```

**`rails routes`**

```rb
$ rails routes
Prefix Verb   URI Pattern          Controller#Action
 songs GET    /songs(.:format)     songs#index
       POST   /songs(.:format)     songs#create
  song GET    /songs/:id(.:format) songs#show
       PATCH  /songs/:id(.:format) songs#update
       PUT    /songs/:id(.:format) songs#update
       DELETE /songs/:id(.:format) songs#destroy
```

In `app` > `controllers`, create `songs_controller.rb`.
We'll need to add methods:

##### Index route:
```rb
class SongsController < ApplicationController
    def index
        query_string = "SELECT * FROM songs;"
        results = ActiveRecord::Base.connection.execute(query_string)
        render json: results
    end
end
```

Using ActiveRecord instead, we write:
```rb
class SongsController < ApplicationController
    def index
        render json: Song.all
    end
end
```

Now, add a status:
```rb
class SongsController < ApplicationController
    def index
        songs = Song.all
        render json: {status: 200, songs: songs}
    end
end
```

![parsed data](https://i.imgur.com/ym6G6M7.png)
Meanwhile the console will show:
![console all routes](https://i.imgur.com/6piX175.png)

##### Show route:
```rb
    def show
        puts "show me #{params}"
        song = Song.find(params[:id])
        render json: {status: 200, song: song}
    end
```
![](https://i.imgur.com/NiFd94P.png)
![](https://i.imgur.com/SuuaQJ2.png)

##### Create route:

The following can be tested in with CURL in a terminal or using Postman.

```rb
curl -X POST -d "song[title]=Peaks" -d "song[artist]=Dictaphone" -d "song[cover]=None" localhost:3000/songs
```
Will return the console message (in the console where the server is running):
```rb
Started POST "/songs" for ::1 at 2020-08-03 11:58:28 -0700
Processing by SongsController#create as */*
  Parameters: {"song"=>{"title"=>"Peaks", "artist"=>"Dictaphone", "cover"=>"None"}}
create - params {#params}
Completed 204 No Content in 0ms (ActiveRecord: 0.0ms | Allocations: 31)
```

In `application_controler.rb` (app > controllers)

```rb
class ApplicationController < ActionController::API
    ActionController::Parameters.permit_all_parameters = true
end
```
but the above, lessens security as it accepts all params by default. We will want to create a private method within and by the `songs_controller`.

Instead, we'll keep:
```rb
class ApplicationController < ActionController::API
end
```
and add a method to the `song` class:
```rb
def create
    puts "create - params #{params[:song]}"
    Song.create(song_params)
end

private

def song_params
    params.required(:song).permit(:title, :artist, :cover)
end
```

Now, we'll add code to return a success or error message to the front-end:
```rb
def create
    puts "create - params #{params[:song]}"
    song = Song.new(song_params)
    if song.save
        render json: {status: 201, song: song}
    else
        render json: {status: 422, song: song}
    end
end

private # Any methods below here

def song_params
    params.required(:song).permit(:title, :artist, :cover)
end
```

Running the curl command again:
```rb
curl -X POST -d "song[title]=Peaks" -d "song[artist]=Dictaphone" -d "song[cover]=None" localhost:3000/songs
# =>{"status":201,"song":{"id":11,"title":"Peaks","artist":"Dictaphone","cover":"None"}}%
```
![](https://i.imgur.com/D17yEKr.png)
![](https://i.imgur.com/zwpCfKy.png)

**`private`:** Regular, public methods of a controller class are exposed to the web server through Rails routes, but private methods are not.

We can test the same route in Postman:
![Postman - create](https://i.imgur.com/wPIoRE7.png)

##### Adding validation:

In `song.rb` (app > models)
```rb
class Song < ApplicationRecord
    validates :artist, presence: true
end
```

#### Update route:
```rb
def update
    song = Song.find(params[:id])
    song.update(song_params)
    render json: {status: 201, song: song}
end
```

![console-put](https://i.imgur.com/MVnkeY0.png)
![postman-put](https://i.imgur.com/HPIp30z.png)

#### Destroy route:
```rb
def destroy
    song = Song.destroy(params[:id])
    render json: {status: 204}
end
```
![console-del](https://i.imgur.com/Y7UjNcn.png)
![postman-delete](https://i.imgur.com/xSlNZQP.png)

****
Using cURL:
- POST request to create data with cURL:

> curl -X POST -d "song[title]=Peaks" -d "song[artist_name]=Dictaphone" -d song[artwork]="None" localhost:3000/songs

- PUT request to update data

> curl -X PUT -d "song[title]=Dem Bones" localhost:3000/songs/13

- DELETE request to delete data

> curl -X DELETE localhost:3000/songs/13

### One-to-many

##### New tools in Rails
**Scaffholding**
Rails is so developer friendly that it even has a command to help scaffold an app with following:

- Controller
- Model
- A Migration
- Routes
- Views for every standard controller action (index, edit, show, new)

```rb
rails g scaffold model_name lat:decimal lng:decimal name
# => model name followed by keys
```

We now have boilerplate files and code to:

- db/migrate
- app/models
- config/routes.rb
- app/controllers

**Foreign Keys**
> e.j.: If Locations have many Temperatures, and a Temperature belongs to a Location...

The foreign key would belong to the many.

```rb
rails g migration AddForeignKeyToTemperatures
```
In the generated migration file, we'll want to build a relationship by adding the foreign key:
```rb
class AddForeignKeyToTemperatures < ActiveRecord::Migration[6.0]
  def change
    add_column :temperatures, :location_id, :integer
  end
end
```
![schema](https://i.imgur.com/FXbtoYi.png)

Now that we have a schema.rb, add those relations to models:
`Location`:
```rb
class Location < ApplicationRecord
    has_many :temperatures
end
```
`Temperature`:
```rb
class Temperature < ApplicationRecord
    belongs_to :location
end
```
Once data is seeded, starting the server should allow to get locations, and temperatures -- which should include a `location_id`

![locations](https://i.imgur.com/Ddxjtiq.png)
![temperatures](https://i.imgur.com/OeUMavi.png)

With relations built and data seeded, ActiveRecord commands can call data across tables (temparatues for one location or the location associated with one temperature specifically)

![activerec](https://i.imgur.com/ZuSu1WY.png)





