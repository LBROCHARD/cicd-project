# syntax=docker/dockerfile:1

FROM node:19.5.0-alpine as builder
WORKDIR /app
COPY . .
RUN npm install
RUN npm ci
RUN npm run build

FROM nginx:1.21.5-alpine as release
COPY --from=builder /app/build /usr/share/nginx/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
