FROM harbor.dankal.cn/common/php-nginx

ENV VIRTUAL_HOST hx.dankal.cn
ENV LETSENCRYPT_HOST hx.dankal.cn
ENV LETSENCRYPT_EMAIL bingo@dankal.cn

COPY . /app
COPY nginx.conf /etc/nginx/conf.d/default.conf

RUN chmod 777 /app/runtime