FROM python:3.10-bullseye
ARG ODOO_VERSION=16.0
ARG DOCKER_ODOO_UID=9999  
ARG DOCKER_ODOO_GID=9999

SHELL ["/bin/bash", "-xo", "pipefail", "-c"]

# Generate locale C.UTF-8 for postgres and general locale data
ENV LANG C.UTF-8

# Install dependencies as describe in Odoo documentation
# https://www.odoo.com/documentation/16.0/administration/install/install.html#setup-install-source-linux-git
# We also install  virtualenv package which will be created lated to create virtual environment

RUN apt-get update && apt-get install -y python3-pip libldap2-dev libpq-dev libsasl2-dev \
    python3-virtualenv virtualenv wkhtmltopdf chromium

# Install latest version of the PG client
RUN sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt bullseye-pgdg main" > /etc/apt/sources.list.d/pgdg.list' \
    && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - \
    && apt update && apt install -y postgresql-client

# This is needed for RtL languages only
# Comment this code in case you don't need this feature
RUN apt-get -y install node-less npm
RUN npm install -g rtlcss


# Enable VENV
ENV VIRTUAL_ENV=/home/odoo/venv
RUN python3 -m venv $VIRTUAL_ENV
ENV PATH="$VIRTUAL_ENV/bin:$PATH"

# Install debugger
# Websocket client is required for TOTP tests
RUN python3 -m pip install --upgrade pip && \
    python3 -m pip install debugpy pre-commit setuptools wheel websocket-client

# Install Odoo python dependencies
RUN curl https://raw.githubusercontent.com/odoo/odoo/${ODOO_VERSION}/requirements.txt > /tmp/requirements.txt && \
    pip3 install -r /tmp/requirements.txt


# Add "odoo" user. We use same guid uid as in the official Odoo image
RUN groupadd --gid ${DOCKER_ODOO_GID} odoo && useradd odoo -u ${DOCKER_ODOO_UID} -g ${DOCKER_ODOO_GID} -m -s /bin/bash

# Set permissions
RUN mkdir -p /home/odoo/odoo \
    && chown -R odoo /home/odoo

# Expose Odoo services
EXPOSE 5678 8069 8071 8072


# Set default user when running the container
USER odoo
