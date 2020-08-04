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

#### User stories and app-specific needs

We might want to make changes or require specific routes base don what the users can or cannot do.

> In this example, Limit location routes only to index and show, since:

- User should not be able to add or edit locations.
- User should be able to view a list of locations and information for each location.

In `routes.db` (< Config):
```rb
Rails.application.routes.draw do
  resources :temperatures
  resources :locations, only: [:index, :show]
  # For details on the DSL available within this file, see https://guides.rubyonrails.org/routing.html
end
```
***This limits routes to `index` and `show`***

In the controllers (locations), we can remove or comment out the code we don't need, make changes to the `before_action` (explained below)

![controller](https://i.imgur.com/O57wcZG.png)

```rb
class LocationsController < ApplicationController
  before_action :set_location, only: [:show]

  # GET /locations
  def index
    @locations = Location.all

    render json: @locations
  end

  # GET /locations/1
  def show
    render json: @location
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_location
      @location = Location.find(params[:id])
    end

end
```

> We might also want to Limit temperature routes only to index and create, if we had such user stories:

- User should be able to view temperature records associated with a location.
- User should be able to add temperature data for a given location.

Still in `routes.db`
```rb
Rails.application.routes.draw do
  resources :temperatures, only: [:index, :create]
  resources :locations, only: [:index, :show]
  # For details on the DSL available within this file, see https://guides.rubyonrails.org/routing.html
end
```
***This limits routes to `index` and `create`***

![routes](https://i.imgur.com/lD2g19C.png)

While we can request to show one location, showing one temparature alone will not work.
![loc show](https://i.imgur.com/EqiRsxK.png)
![temp show](https://i.imgur.com/bgfOUKP.png)

Again, depending on the needs of the App, we might use other filters or callbacks, like `before_action` called above. 

`before_action` is a callback that is performed before an action is executed and can be used to halt the request cycle. Common use cases for using `before_action` might be:
- confirm user is logged in
- checking for permissions to resource

**The problem here is that we are not getting temparatures for one location...**

The location routes (`show`) needs to be changed to allow that:
```rb
 # GET /locations/1
  def show
    location_temps = @location.temperatures
    render json: {
      location: @location, 
      temperatures: location_temps
    }
  end
  ```
![updated location](https://i.imgur.com/vwUWr2K.png)

The format might cause issues parsing data and using it. `to_json` will allow us to keep a more managable format:
```rb
def show
    location_temps = @location.temperatures
    render json: @location.to_json(include: :temperatures)
end
```
![json format](https://i.imgur.com/eAoKAbd.png)

We'll change the `index` route to have the same format:
```rb
def index
    @locations = Location.all
    render json: @locations.to_json(include: :temperatures)
end
```
![nested temp](https://i.imgur.com/PkpEhAp.png)

**Do the same in `temperatures_controller` but with *nested* routes**

In `routes.db`
```rb
Rails.application.routes.draw do
  resources :temperatures, only: [:index]
  resources :locations, only: [:index, :show] do
    resources :temperatures, only: [:create]
  end
  # For details on the DSL available within this file, see https://guides.rubyonrails.org/routing.html
end
```

| Prefix Verb | URI Pattern |             Controller#Action |
| :---: | :---: | :----:|
| temperatures GET | /temperatures(.:format) | temperatures#index |
| location_temperatures POST | /locations/:location_id/temperatures(.:format) | temperatures#create |
| locations GET | /locations(.:format)|locations#index |
| location GET | /locations/:id(.:format) | locations#show |

Change the `create` route to:
```rb
# POST /temperatures
def create
    @temperature = Temperature.new(temperature_params)
    @temperature.location_id = params[:location_id]

    if @temperature.save
        render json: @temperature, status: :created
    else
        render json: @temperature.errors, status: :unprocessable_entity
    end
end
```






