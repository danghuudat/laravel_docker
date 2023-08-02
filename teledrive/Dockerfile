FROM node:18.16.0 as build
ARG REACT_APP_TG_API_ID
ARG REACT_APP_TG_API_HASH

COPY --chown=www-data:www-data . ${APP_CODE_PATH_CONTAINER}
WORKDIR ${APP_CODE_PATH_CONTAINER}

RUN yarn cache clean
RUN yarn install --network-timeout 1000000
