FROM buildpack-deps:jammy-curl

ARG INSTALL_ZSH="true"
ARG UPGRADE_PACKAGES="false"
ARG ENABLE_NONROOT_DOCKER="true"
ARG USE_MOBY="true"
ARG DOCKER_VERSION="latest"

ENV DOCKER_BUILDKIT=1

# Install needed packages and setup non-root user. Use a separate RUN statement to add your
# own dependencies. A user of "automatic" attempts to reuse an user ID if one already exists.
ARG USERNAME=automatic
ARG USER_UID=1000
ARG USER_GID=$USER_UID
COPY library-scripts/*.sh /tmp/library-scripts/
RUN apt-get update \
    && /bin/bash /tmp/library-scripts/common-debian.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" "true" "true" \
    && /bin/bash /tmp/library-scripts/docker-in-docker-debian.sh "${ENABLE_NONROOT_DOCKER}" "${USERNAME}" "${USE_MOBY}" "${DOCKER_VERSION}" \
    && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/* /tmp/library-scripts/

VOLUME [ "/var/lib/docker" ]

ENTRYPOINT [ "/usr/local/share/docker-init.sh" ]
CMD [ "sleep", "infinity" ]

RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
    wget software-properties-common libpq-dev

RUN echo "if test -f /workspace/.devcontainer/.bashrc ; then source /workspace/.devcontainer/.bashrc ; fi" >> /home/vscode/.bashrc \
    && wget -O /usr/local/bin/git-id https://raw.githubusercontent.com/holygeek/git-number/master/git-id && sudo chmod +x /usr/local/bin/git-id \
    && wget -O /usr/local/bin/git-list https://raw.githubusercontent.com/holygeek/git-number/master/git-list && sudo chmod +x /usr/local/bin/git-list \
    && wget -O /usr/local/bin/git-number https://raw.githubusercontent.com/holygeek/git-number/master/git-number && sudo chmod +x /usr/local/bin/git-number

ARG PYTHON_VERSION=3.11
RUN echo "Add Python" \
    && add-apt-repository -y ppa:deadsnakes/ppa \
    && apt update -y \
    && apt install -y python${PYTHON_VERSION} python${PYTHON_VERSION}-distutils \
    && update-alternatives --install /usr/bin/python3 python3 /usr/bin/python${PYTHON_VERSION} 1 \
    && curl -sS https://bootstrap.pypa.io/get-pip.py | python${PYTHON_VERSION}