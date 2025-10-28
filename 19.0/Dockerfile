FROM python:3.12-slim-bookworm
ARG ODOO_VERSION=19.0
ARG DOCKER_ODOO_UID=9999  
ARG DOCKER_ODOO_GID=9999
ENV PIP_NO_CACHE_DIR=1
ARG POSTGRES_VERSION=16
ARG TARGETARCH
SHELL ["/bin/bash", "-xo", "pipefail", "-c"]

# Generate locale C.UTF-8 for postgres and general locale data
ENV LANG C.UTF-8

# Install dependencies as describe in Odoo documentation
# https://www.odoo.com/documentation/14.0/administration/install/install.html#setup-install-source-linux-git
# We also install  virtualenv package which will be created lated to create virtual environment

RUN apt-get update -qq && DEBIAN_FRONTEND=noninteractive apt-get install -qq --no-install-recommends curl python3-pip libldap2-dev libpq-dev libsasl2-dev \
    python3-dev python3-virtualenv build-essential gnupg npm git \
    # OCA extra deps 
    # for lxml
    libxml2-dev \
    libxslt1-dev \
    libz-dev \
    libxmlsec1-dev \
    # for pycups
    libcups2-dev \
    # some other build tools
    swig \
    libffi-dev \
    pkg-config \
    # For running tours
    chromium

# Install wkhtmltopdf
RUN if [ -z "${TARGETARCH}" ]; then \
    TARGETARCH="$(dpkg --print-architecture)"; \
    fi; \
    curl -sSL https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6.1-3/wkhtmltox_0.12.6.1-3.bookworm_${TARGETARCH}.deb -o /tmp/wkhtml.deb \
    && apt-get update -qq \
    && dpkg --force-depends -i /tmp/wkhtml.deb \
    && DEBIAN_FRONTEND=noninteractive apt-get install -qq -f --no-install-recommends \
    && rm /tmp/wkhtml.deb

# Install latest version of the PG client
RUN curl -sSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - \
    && echo "deb http://apt.postgresql.org/pub/repos/apt/ bookworm-pgdg main" > /etc/apt/sources.list.d/pgclient.list \
    && apt-get update -qq \
    && DEBIAN_FRONTEND=noninteractive apt-get install -qq postgresql-client-${POSTGRES_VERSION}

# Install nodejs dependencies
RUN npm install -g rtlcss

# Enable VENV
ENV VIRTUAL_ENV=/home/odoo/venv
RUN python3 -m venv $VIRTUAL_ENV
ENV PATH="$VIRTUAL_ENV/bin:$PATH"

# Install debugger
RUN pip install setuptools wheel debugpy pre-commit

# Install Odoo python dependencies
RUN curl https://raw.githubusercontent.com/odoo/odoo/${ODOO_VERSION}/requirements.txt > /tmp/requirements.txt && \
    pip install -r /tmp/requirements.txt


# Add "odoo" user. We use same guid uid as in the official Odoo image
RUN groupadd --gid ${DOCKER_ODOO_GID} odoo && useradd odoo -u ${DOCKER_ODOO_UID} -g ${DOCKER_ODOO_GID} -m -s /bin/bash

# Set permissions
RUN mkdir -p /home/odoo/odoo \
    && chown odoo /home/odoo

# Expose Odoo services
EXPOSE 5678 8069 8071 8072


# Set default user when running the container
USER odoo
