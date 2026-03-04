FROM alpine:latest
RUN apk add --no-cache hugo
WORKDIR /app
COPY . .
EXPOSE 1313
CMD ["hugo", "server", "--bind=0.0.0.0", "--port=1313", "--appendPort=false"]