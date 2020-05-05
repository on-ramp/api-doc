# On-RAMP's API Documentation

Base and fork from [Slate](https://github.com/lord/slate)

## Modify Documentation

In order to modify documentation everything is under `source` folder. In particular the main file to be modify is `index.html.md` which is in Markdown file. 

Please check [Slate Documentation](https://github.com/lord/slate/wiki) to know about:

- Markdown support
- Includes files
- Change images and logos


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

## Publishing Github Pages

`./deploy.sh`


