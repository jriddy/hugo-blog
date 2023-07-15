# Adapted from https://gist.github.com/exavolt/80cbdf4148fac42ab367eea793b8d4ac
FROM golang:1.19-alpine

# VARIANT can be either 'hugo' for the standard version or 'hugo_extended' for the extended version.
ARG VARIANT=hugo_extended
# VERSION can be either 'latest' or a specific version number
ARG VERSION=latest

ARG MUSLSTACK_VERSION=latest

RUN apk add --update --no-cache ca-certificates openssl git curl && \
    case ${VERSION} in \
    latest) \
    export VERSION=$(curl -s https://api.github.com/repos/gohugoio/hugo/releases/latest | grep "tag_name" | awk '{print substr($2, 3, length($2)-4)}') ;;\
    esac && \
    echo ${VERSION} && \
    wget -O ${VERSION}.tar.gz https://github.com/gohugoio/hugo/releases/download/v${VERSION}/${VARIANT}_${VERSION}_Linux-64bit.tar.gz && \
    tar xf ${VERSION}.tar.gz && \
    mv hugo* /usr/bin/hugo && \
    go install github.com/yaegashi/muslstack@${MUSLSTACK_VERSION} && \
    muslstack -s 0x800000 /usr/bin/hugo

FROM mcr.microsoft.com/vscode/devcontainers/base:alpine-3.14

# Required for hugo_extended
RUN apk add --update --no-cache libc6-compat gcompat && \
    ln -s /lib/libc.so.6 /usr/lib/libresolv.so.2

COPY --from=0 /usr/bin/hugo /usr/bin
EXPOSE 1313
WORKDIR /src
CMD ["/usr/bin/hugo server"]
