FROM nginx:1.15.5-alpine as builder
ARG HTML_DEST=/usr/html
ARG DEFAULT_CFG=default.conf
WORKDIR /app
COPY . /app
RUN rm -rf /usr/html/index.html && \
    apk add --update --no-cache chromium \
                                git \
                                g++ \
                                make \
                                openssh-client \
                                python \
                                npm && \
    npm install npm@latest -g && \
    npm install && \
    npm install @angular/cli@latest --save-dev -g && \
    export CHROME_BIN=/usr/bin/chromium-browser && \
    npm run test-prod

FROM nginx:1.15.5-alpine
ARG HTML_DEST=/usr/html
ARG DEFAULT_CFG=default.conf
WORKDIR /app
COPY . /app
RUN rm -rf /usr/html/index.html && \
    apk add --update --no-cache git \
                                g++ \
                                make \
                                openssh-client \
                                python \
                                npm && \
    npm install npm@latest -g && \
    npm install && \
    npm install @angular/cli@latest --save-dev -g && \
    echo "executing `npm run build-prod`" && \
    npm run build-prod && \
    ln -sf /dev/stdout /var/log/nginx/access.log && \
    ln -sf /dev/stderr /var/log/nginx/error.log && \
    npm run build-prod && \
    mkdir -p -m 777 ${HTML_DEST} && \
    cp -r ./dist/ ${HTML_DEST} && \
    cp ./${DEFAULT_CFG} /etc/nginx/conf.d/default.conf && \
    cp ./run /etc/sysctl.d/

EXPOSE 80