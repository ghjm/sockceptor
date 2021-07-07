FROM centos:8

ARG GOLANG_VERSION=1.15.7
ARG PYTHON_VERSION=3.8.7

RUN set -x \
    && echo 'fastestmirror=True' >> /etc/dnf/dnf.conf \
    && dnf -y update \
    && dnf -y install \
        # Receptor build tools
        git wget make iproute openssl findutils virtualenv \
        # Python build requirements
        gcc zlib-devel bzip2 bzip2-devel readline-devel sqlite \
        sqlite-devel openssl-devel tk-devel libffi-devel xz-devel \
    && dnf clean all

# Install specific python version
RUN set -x \
    && cd /tmp \
    && wget -q https://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tgz \
    && tar -zxf Python-${PYTHON_VERSION}.tgz \
    && cd Python-${PYTHON_VERSION} \
    && ./configure \
    && make altinstall \
    && cd / \
    && rm -rf /tmp/Python-${PYTHON_VERSION}

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
    && virtualenv -p python$(echo ${PYTHON_VERSION} | rev | cut -d'.' -f2- | rev) /opt/venv \
    && source /opt/venv/bin/activate \
    && pip3 install -r requirements.txt \
    && pip3 install -r test-requirements.txt \
    && pip3 install --upgrade -r build-requirements.txt

ADD ./tests/container/base/build-artifacts.sh /

RUN chmod +x /build-artifacts.sh

WORKDIR /
