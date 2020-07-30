## Postgres

[Postgres.app page](https://postgresapp.com/)
![download instructions](https://i.imgur.com/U9YlqC1.png)
To configure your PATH after installing:
```
sudo mkdir -p /etc/paths.d &&
echo /Applications/Postgres.app/Contents/Versions/latest/bin | sudo tee /etc/paths.d/postgresapp
```

`psql` in terminal to connect to the database associated with user's database.
or `-# psql -d username`
`user=#`

The App will show a visual representation of databases:

![postgr](https://i.imgur.com/5mLBB20.png)

To install the pg gem, make sure you have set up your $PATH correctly, then execute the following command:
```
sudo ARCHFLAGS="-arch x86_64" gem install pg
```

###### with Rails:
In config/database.yml, use the following settings:
```
development:
    adapter: postgresql
    database: [YOUR_DATABASE_NAME]
    host: localhost
```

### SQL vs. MongoDB

#### PostgresSQL
PostgreSQL is a powerful, open source ***object-relational database*** system that uses and extends the SQL language combined with many features that safely store and scale the most complicated data.

Terminology:
![terms](https://i.imgur.com/PCHRt4r.png)



