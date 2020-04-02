ARG RUBY_VERSION=2.5.3
FROM ruby:$RUBY_VERSION
ENV DEBIAN_FRONTEND=noninteractive
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
RUN apt-get update \
  && apt-get install -y \
    git \
    locales \
    make

COPY pages-gem/.git /src/gh/pages-gem/.git
COPY pages-gem/Gemfile* /src/gh/pages-gem/
COPY pages-gem/github-pages.gemspec /src/gh/pages-gem
COPY pages-gem/lib/ /src/gh/pages-gem/lib
COPY pages-gem/bin/ /src/gh/pages-gem/bin

RUN \
  bundle config local.github-pages /src/gh/pages-gem && \
  NOKOGIRI_USE_SYSTEM_LIBRARIES=true bundle install --gemfile=/src/gh/pages-gem/Gemfile

COPY pages-gem/ /src/gh/pages-gem

RUN \
  echo "en_US UTF-8" > /etc/locale.gen && \
  locale-gen en-US.UTF-8

ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8
ENV LC_ALL en_US.UTF-8

# Configure apt and install packages
RUN apt-get update \
    && apt-get -y install --no-install-recommends apt-utils dialog 2>&1 \
    #
    # Verify git, process tools, lsb-release (useful for CLI installs) installed
    && apt-get -y install git iproute2 procps lsb-release \
    #
    # Install C++ tools
    && apt-get -y install build-essential cmake cppcheck valgrind \
    #
    # Create a non-root user to use if preferred - see https://aka.ms/vscode-remote/containers/non-root-user.
    && groupadd --gid $USER_GID $USERNAME \
    && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
    # [Optional] Add sudo support for the non-root user
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME\
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    #
    # Clean up
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

ENV DEBIAN_FRONTEND=dialog
