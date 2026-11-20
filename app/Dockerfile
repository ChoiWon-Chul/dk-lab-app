# 실습용 Nginx 공식 이미지: 출판 전 멀티아키텍처 digest 고정 및 실제 빌드 검증 필요
FROM nginx:1.30.6-alpine
ARG SITE_FILE=index.html
COPY ${SITE_FILE} /usr/share/nginx/html/index.html
EXPOSE 80
