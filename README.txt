# node_project
1.Create a new user in the IAM Section on AWS.

2.Create *.tf file.
			provider "aws" {
			access_key = "access_key_here"
			secret_key ="secret_key_here"
			region ="us-east-2"
			}
			resource "aws_instance" "example"{
			ami="ami-07c1207a9d40bc3bd"
			instance_type="t2.micro"
			key_name = "key-name"
			}
			
3.terraform init in the location where .tf file is present

4.terraform plan to see the things what it can do when we apply it.

5.terraform apply

In Ubuntu(18.04) work using putty by providing the key.
1.apt-grt update
2.apt install docker.io
3.git clone any node_project

4.db.js
const mongoose = require('mongoose');
const {
  MONGO_USERNAME,
  MONGO_PASSWORD,
  MONGO_HOSTNAME,
  MONGO_PORT,
  MONGO_DB
} = process.env;

const options = {
  useNewUrlParser: true,
  reconnectTries: Number.MAX_VALUE,
  reconnectInterval: 500,
  connectTimeoutMS: 10000,
};

const url = `mongodb://${MONGO_USERNAME}:${MONGO_PASSWORD}@${MONGO_HOSTNAME}:${MONGO_PORT}/${MONGO_DB}?authSource=admin`;

mongoose.connect(url, options).then( function() {
  console.log('MongoDB is connected');
})
  .catch( function(err) {
  console.log(err);
});

Dockerfile
FROM node:10-alpine
RUN mkdir -p /home/node/app/node_modules && chown -R node:node /home/node/app
WORKDIR /home/node/app
COPY package*.json ./
USER node
RUN npm install
COPY --chown=node:node . .
EXPOSE 8080
CMD [ "node", "app.js" ]


docker_compose.yml
version: '3'
services:
  nodejs:
    build:
      context: .
      dockerfile: Dockerfile
    image: nodejs
    container_name: nodejs
    restart: unless-stopped
    env_file: .env
    environment:
      - MONGO_USERNAME=$MONGO_USERNAME
      - MONGO_PASSWORD=$MONGO_PASSWORD
      - MONGO_HOSTNAME=db
      - MONGO_PORT=$MONGO_PORT
      - MONGO_DB=$MONGO_DB
    ports:
      - "80:8080"
    volumes:
      - .:/home/node/app
      - node_modules:/home/node/app/node_modules
    networks:
      - app-network
    command: ./wait-for.sh db:27017 -- /home/node/app/node_modules/.bin/nodemon app.js 

  db:
    image: mongo:4.1.8-xenial
    container_name: db
    restart: unless-stopped
    env_file: .env
    environment:
      - MONGO_INITDB_ROOT_USERNAME=$MONGO_USERNAME
      - MONGO_INITDB_ROOT_PASSWORD=$MONGO_PASSWORD
    volumes:     
      - dbdata:/data/db
    networks:
      - app-network  

networks:
  app-network:
    driver: bridge

volumes:
  dbdata:
  node_modules:  
  
  
For Zabix spin up
docker run \
 --name=zabbix-agent-xxl \
 -h $(hostname) \
 -p 10050:10050 \
 -v /:/rootfs \
 -v /var/run:/var/run \
 -e "ZA_Server=<ZABBIX SERVER IP/DNS NAME>" \
 -d monitoringartist/zabbix-agent-xxl-limited:latest
 
