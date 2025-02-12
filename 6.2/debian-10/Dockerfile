FROM registry.access.redhat.com/ubi8/ubi-minimal:latest as builder

MAINTAINER "Vathsala . <vaths367@in.ibm.com>"

#This docker file is generated as basis of "https://github.com/bitnami/bitnami-docker-redis/blob/master/6.2/debian-10/Dockerfile"

RUN microdnf install -y glibc-devel glibc-headers gcc-c++ golang git make cmake findutils

RUN cd /root && git clone https://github.com/bitnami/wait-for-port && cd wait-for-port/ \
    && git checkout v1.0.1 && go build .

RUN cd /root && git clone https://github.com/redis/redis && cd redis/ \
  && git checkout 6.2.6 && make -j && mkdir -p redis/bin && cp `find -name redis-* -executable` ./redis/bin



FROM registry.access.redhat.com/ubi8/ubi-minimal:latest
LABEL maintainer "Bitnami <containers@bitnami.com>"

ENV HOME="/" \
    OS_ARCH="ppc64le" \
    OS_FLAVOUR="rhel8" \
    OS_NAME="linux"

COPY prebuildfs /

RUN microdnf install -y git wget acl ca-certificates curl gzip glibc openssl procps tar glibc-devel

RUN chmod g+rwX /opt/bitnami
RUN ln -s /opt/bitnami/scripts/redis/entrypoint.sh /entrypoint.sh
RUN ln -s /opt/bitnami/scripts/redis/run.sh /run.sh

COPY rootfs /

RUN mkdir -p /opt/bitnami/common/bin
COPY --from=builder /root/wait-for-port/wait-for-port /opt/bitnami/common/bin
COPY --from=builder /root/redis/redis /opt/bitnami/redis
RUN wget https://github.com/tianon/gosu/releases/download/1.14/gosu-ppc64el && mv gosu-ppc64el /opt/bitnami/common/bin/gosu && chmod +x /opt/bitnami/common/bin/gosu
RUN ls /opt/bitnami/common/bin && ls /opt/bitnami/redis/bin/
RUN /opt/bitnami/scripts/redis/postunpack.sh
ENV BITNAMI_APP_NAME="redis" \
    BITNAMI_IMAGE_VERSION="6.2.6-ubi8" \
    PATH="/opt/bitnami/common/bin:/opt/bitnami/redis/bin:$PATH"

EXPOSE 6379

USER 1001

ENV REDIS_PASSWORD default-password

ENTRYPOINT [ "/opt/bitnami/scripts/redis/entrypoint.sh" ]
CMD [ "/opt/bitnami/scripts/redis/run.sh" ]
