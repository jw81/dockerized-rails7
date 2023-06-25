# Description

This is a bare-bones example of how to use Docker Compose to Dockerize a Rails 7 app with a Postgres DB.  Below is a "script" for how to build a project exactly like this.

(These steps assume that you are using and are familiar with RVM and you're on a silicon chip MacOS.)
## Steps to Reproduce
- In terminal, navigate to where you're going to create the app.
- run :: `rvm use ruby-3.1.0`
- run :: `rvm gemset create dockerized-rails-7.0.4.2`
- run :: `rvm gemset use dockerized-rails-7.0.4.2`
- run :: `gem install bundler`
- run :: `gem install rails -v 7.0.4.2`
- run :: `rails new dockerized-rails7 --database=postgresql`
- run :: `cd dockerized-rails7`
- run :: `touch Dockerfile`
  - Copy/Paste and Save this into the new `Dockerfile`
  ```
  FROM ruby:3.1.0

  RUN apt-get update -qq && apt-get install -y nodejs postgresql-client
  WORKDIR /app
  COPY . /app/
  
  ENV BUNDLE_PATH /gems
  RUN bundle install

  ENTRYPOINT ["bin/rails"]
  CMD ["s", "-b", "0.0.0.0"]

  EXPOSE 3000
- run :: `touch docker-compose.yml`
  - Copy/Paste and Save this into the new `docker-compose.yml` file
  ```
  services:
    db:
      image: postgres:15.2
      environment:
        - POSTGRES_PASSWORD=password
      ports:
        - "5432:5432"
      volumes:
        - "dbdata:/var/lib/postgresql/data"
  
    web:
      build: .
      ports:
        - "3000:3000"
      depends_on:
        - db
      environment:
        - DATABASE_URL=postgres://postgres:password@db:5432/postgres
      volumes:
        - .:/app

  volumes:
    dbdata:
- run :: `docker compose up`
- Give it a bit to build and when the console output shows that everything is ready, navigate to `localhost:3000`
- You should now see the Rails splash screen in your browser
- hit `ctrl + c` to stop the Dockerized app
- run :: `docker compose down` to remove the app containers
