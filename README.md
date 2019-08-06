# On-RAMP's API Documentation

Base and fork from [Slate](https://github.com/lord/slate)

## Prerequisites 

This projects builds and runs with Ruby 2.3.1, but it is not mandatory for you to have Ruby installed because it can also be built and run with Docker.

## Building and Running

### Docker

- First build docker images of this project

`docker build -f deploy/docker/Dockerfile -t coinweb/onramp-api-doc:latest .`

- Build solution

`docker run --rm -ti -p 4567:4567 -v $PWD/source:/usr/src/app/source -w /usr/src/app/source -v $PWD/build:/usr/src/app/build coinweb/onramp-api-doc:latest bundle exec middleman build`

- Run solution

`docker run --rm -ti -p 4567:4567 -v $PWD/source:/usr/src/app/source -w /usr/src/app/source -v $PWD/build:/usr/src/app/build coinweb/onramp-api-doc:latest bundle exec middleman`


### Ruby 

- Building 

`bundle install`
`bundle exec middleman build`

- Running

`bundle install`
`bundle exec middleman`

### Browsing 

If you want to browse in your local machine go to `http://localhost:4567`

## Publishing

### Docker

- After building 

`git checkout gh-pages`

- Copy all under `/build` directory into root directory

- Commit and push changes to `gh-pages` branch


### Ruby

`./deploy.sh`

