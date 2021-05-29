FROM node:14.17-alpine

ENV port 6379
ENV host "redis-master.redis-beta.svc.cluster.local"

RUN mkdir -p  /home/node/app

WORKDIR /home/node/app

COPY . .

RUN npm install


EXPOSE 8000

CMD [ "node", "hello-1.js" ]