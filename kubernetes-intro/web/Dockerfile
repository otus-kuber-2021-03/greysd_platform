FROM nginx:latest
WORKDIR /app
COPY default.conf /etc/nginx/conf.d/
COPY index.html /app
RUN chown -R 1001 /app /etc/nginx /var/cache/nginx /var/log/nginx /var/run/
USER 1001
CMD ["nginx", "-g", "daemon off;"]
EXPOSE 8000
