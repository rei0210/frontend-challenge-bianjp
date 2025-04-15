#  install the dependencies and pack the app
FROM node:18-alpine as build

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build

# use nginx to start the app
FROM nginx:alpine

# clear the default html
RUN rm -rf /usr/share/nginx/html/*

# copy it to the directory
COPY --from=build /app/dist /usr/share/nginx/html

# port:80
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

