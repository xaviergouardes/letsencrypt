FROM golang:alpine3.6 AS binary

RUN set -eux; \
 apk add --no-cache --virtual .build-deps \
 	 bash \
 	 gcc \
 	 musl-dev \
 	 openssl \
 	 go \
 ;

ADD . /app
WORKDIR /app
RUN go build -o http

FROM alpine:3.6
WORKDIR /app
ENV PORT 8000
EXPOSE 8000
COPY --from=binary /app/http /app
# CMD ["ls /app/http"]
CMD ["/app/http"]
