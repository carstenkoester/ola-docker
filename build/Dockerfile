FROM debian:11 AS builder

ARG OLA_VERSION
ARG CHECKOUT_COMMIT

RUN apt-get update \
 && apt-get -y install git \
 && useradd -m build

RUN git clone https://github.com/OpenLightingProject/ola.git \
 && cd ola \
 && git checkout ${CHECKOUT_COMMIT}

RUN apt-get -y install libcppunit-dev libcppunit-1.15-0 uuid-dev pkg-config \
      libncurses5-dev libtool autoconf automake g++ libmicrohttpd-dev \
      libmicrohttpd12 protobuf-compiler libprotobuf-lite23 python3-protobuf \
      libprotobuf-dev libprotoc-dev zlib1g-dev bison flex make libftdi-dev \
      libftdi1 libusb-1.0-0-dev liblo-dev libavahi-client-dev python3-numpy \
      bash-completion devscripts libftdi1-dev dh-python

RUN cd ola \
 && autoreconf -i \
 && ./configure \
 && make dist \
 && cp ola-${OLA_VERSION}.tar.gz /tmp/ola_${OLA_VERSION}.orig.tar.gz

USER build

RUN cd /tmp \
 && tar xfzv ola_${OLA_VERSION}.orig.tar.gz \
 && cd ola-${OLA_VERSION} \
 && debuild

FROM debian:11 AS final

COPY --from=builder /tmp/*.deb /tmp

RUN apt-get update \
 && apt-get -y install sudo /tmp/*.deb \
 && mkdir /etc/olad \
 && chown olad.olad /etc/olad

USER olad
