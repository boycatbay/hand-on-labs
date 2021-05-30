FROM node:14.17-alpine

ARG PORT
ARG HOST
ENV port=$PORT
ENV host="$HOST"

RUN mkdir -p  /home/node/app

WORKDIR /home/node/app

COPY . .

RUN npm install


EXPOSE 8000

CMD [ "node", "hello-1.js" ]