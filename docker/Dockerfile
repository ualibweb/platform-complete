FROM node:lts-alpine3.14 as stripes_build

ARG OKAPI_URL=http://localhost:9130
ARG TENANT_ID=diku
ARG CXXFLAGS="-std=c++17"

RUN mkdir -p /etc/folio/stripes
WORKDIR /etc/folio/stripes

COPY . /etc/folio/stripes/

RUN apk add --no-cache alpine-sdk python3
RUN yarn config set python /usr/bin/python3

RUN yarn config set @folio:registry https://repository.folio.org/repository/npm-folio/
RUN yarn install
RUN yarn build-module-descriptors
RUN yarn build output --okapi $OKAPI_URL --tenant $TENANT_ID

# nginx stage
FROM nginx:stable-alpine
EXPOSE 80

COPY --from=stripes_build /etc/folio/stripes/output /usr/share/nginx/html
COPY --from=stripes_build /etc/folio/stripes/yarn.lock /usr/share/nginx/html/yarn.lock
COPY docker/nginx.conf /etc/nginx/conf.d/default.conf
COPY docker/entrypoint.sh /usr/bin/entrypoint.sh

ENTRYPOINT ["/usr/bin/entrypoint.sh"]
