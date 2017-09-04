FROM ubuntu:rolling
MAINTAINER Diego Ferigo <diego.ferigo@iit.it>

# Useful tools
RUN apt-get update &&\
    apt-get install -y \
        tree \
        sudo \
        git \
        &&\
    rm -rf /var/lib/apt/lists/*

# Dependencies
RUN apt-get update &&\
    apt-get install -y \
        # Jekyll
        ruby \
        ruby-dev \
        make \
        gcc \
        # github-pages dependencies
        nodejs \
        # nokogiri dependencies
        libxml2-dev \
        libxslt-dev \
        pkg-config \
        &&\
    rm -rf /var/lib/apt/lists/*

# Nokogiri doesn't build with its own libraries. Force using system's libraries as workaround
# http://www.nokogiri.org/tutorials/installing_nokogiri.html#using_your_system_libraries
ENV NOKOGIRI_USE_SYSTEM_LIBRARIES=1

RUN gem install \
        jekyll \
        bundler \
        &&\
    gem clean &&\
    jekyll -v

# Fix ruby and unset locale
# http://jaredmarkell.com/docker-and-locales/
RUN apt-get update &&\
    apt-get install -y \
        locales \
        &&\
    rm -rf /var/lib/apt/lists/* &&\
    locale-gen en_US.UTF-8
ENV LANG="en_US.UTF-8" LANGUAGE="en_US:en" LC_ALL="en_US.UTF-8"

# Jupyter Notebooks support
RUN apt-get update &&\
    apt-get install -y \
        jupyter-core \
        jupyter-nbconvert \
        &&\
    rm -rf /var/lib/apt/lists/*

EXPOSE 4000

# Setup an entrypoint script
COPY entrypoint.sh /entrypoint.sh
COPY entrypoint-user.sh /entrypoint-user.sh
RUN chmod 755 /entrypoint.sh
RUN chmod 755 /entrypoint-user.sh
ENTRYPOINT ["/entrypoint.sh"]

ENV JEKYLL_DIR="/srv/jekyll"
ENV WWW_DIR="/srv/jekyll/www"
WORKDIR $JEKYLL_DIR
ENV GEM_HOME /var/lib/gems

CMD ["jekyll", "--help"]
