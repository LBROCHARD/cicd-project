
FROM node:slim
ENV NODE_ENV development
WORKDIR /express-docker
COPY . .
RUN npm install

CMD [ "node", "server.js" ]
EXPOSE 3002