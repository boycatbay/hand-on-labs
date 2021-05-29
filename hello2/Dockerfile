FROM node:14.17-alpine


RUN mkdir -p  /home/node/app

WORKDIR /home/node/app

COPY . .

RUN npm install

EXPOSE 8000

CMD [ "node", "hello-2.js" ]

