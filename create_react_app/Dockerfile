FROM node:alpine

RUN npx create-react-app my-app

WORKDIR my-app

ADD src src

EXPOSE 3000

CMD npm start