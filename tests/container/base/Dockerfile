FROM fedora:34

ARG GOLANG_VERSION=1.15.7

RUN set -x \
    && echo 'fastestmirror=True' >> /etc/dnf/dnf.conf \
    && dnf -y update \
    && dnf -y install git wget python3 python3-pip python3-pyyaml make iproute openssl findutils \
    && dnf clean all \
    && pip3 install pre-commit

# Install specific golang version
ENV PATH=${PATH}:/usr/local/go/bin
RUN set -x \
    && cd /tmp/ \
    && wget -q https://golang.org/dl/go${GOLANG_VERSION}.linux-amd64.tar.gz \
    && rm -rf /usr/local/go \
    && tar -C /usr/local -xzf go${GOLANG_VERSION}.linux-amd64.tar.gz \
    && rm -f go${GOLANG_VERSION}.linux-amd64.tar.gz \
    && echo 'export PATH=${PATH}:/usr/local/go/bin' >> /etc/bashrc

# Caching dependencies
WORKDIR /dependencies
ADD --chown=${UID}:${UID} \
    ./go.mod \
    ./go.sum \
    ./receptorctl/requirements.txt \
    ./receptorctl/test-requirements.txt \
    ./receptorctl/build-requirements.txt .
RUN set -x \
    # Go
    && go get -u golang.org/x/lint/golint \
    && go get -d -v ./... \
    # Python
    && pip3 install -r requirements.txt \
    && pip3 install -r test-requirements.txt \
    && pip3 install --upgrade -r build-requirements.txt

ADD ./tests/container/base/build-artifacts.sh /

RUN chmod +x /build-artifacts.sh

WORKDIR /

# Use Python 3.8.x

# Build Receptor
# ADD . .

# RUN set -x \
#     && make receptor \
#     && cd ./receptorctl \
#     && python3 -m build
