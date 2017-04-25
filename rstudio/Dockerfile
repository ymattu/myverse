FROM rocker/tidyverse:latest
MAINTAINER "ymattu"

RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    ghostscript \
    imagemagick \
    ## system dependency of hadley/pkgdown
    libmagick++-dev \
    ## system dependency of hunspell (devtools)
    libhunspell-dev \
    ## R CMD Check wants qpdf to check pdf sizes, or iy throws a Warning
    qpdf \
    ## for git via ssh key
    ssh \
    ## for building pdfs via pandoc/LaTeX
    lmodern \
    texlive-fonts-recommended \
    texlive-humanities \
    texlive-latex-extra \
    texinfo \
    ## just because
    less \
    vim \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/ \
  ## R manuals use inconsolata font, but texlive-fonts-extra is huge, so:
  && cd /usr/share/texlive/texmf-dist \
  && wget http://mirrors.ctan.org/install/fonts/inconsolata.tds.zip \
  && unzip inconsolata.tds.zip \
  && rm inconsolata.tds.zip \
  && echo "Map zi4.map" >> /usr/share/texlive/texmf-dist/web2c/updmap.cfg \
  && mktexlsr \
  && updmap-sys \
  ## And some nice R packages for publishing-related stuff
  && . /etc/environment \
  && install2.r --error --repos $MRAN --deps TRUE \
    bookdown rticles rmdshower

## For Japanse LaTeX environment
RUN apt-get update && apt-get upgrade -y \
    && apt-get install -y wget bsdtar perl ca-certificates \
    && wget --progress=dot:giga \
            http://mirror.ctan.org/systems/texlive/Images/texlive.iso \
    && mkdir /install \
    && bsdtar -xpC /install -f /texlive.iso \
    && rm /texlive.iso \
    && wget --directory-prefix=/install \
            https://raw.githubusercontent.com/kenjimyzk/rstudio-latex/master/installation.profile \
    && ./install/install-tl --profile /install/installation.profile \
    && rm -R /install && apt-get clean    
RUN tlmgr update --self --all
RUN apt-get install -y equivs
RUN wget http://www.tug.org/texlive/files/debian-equivs-2016-ex.txt
RUN equivs-build debian-equivs-2016-ex.txt
RUN apt-get remove -y texlive-base
RUN apt-get install -y freeglut3 fonts-ipaexfont fonts-ipafont
RUN dpkg -i texlive-local_2016-2_all.deb \
    && apt-get clean


## Mecab
RUN wget -O mecab-0.996.tar.gz "https://drive.google.com/uc?export=download&id=0B4y35FiV1wh7cENtOXlicTFaRUE" ;\
    tar -xzf mecab-0.996.tar.gz ;\
    cd mecab-0.996; ./configure --enable-utf8-only; make; make install; ldconfig

## Ipadic
RUN wget -O mecab-ipadic-2.7.0-20070801.tar.gz "https://drive.google.com/uc?export=download&id=0B4y35FiV1wh7MWVlSDBCSXZMTXM" ;\
    tar -xzf mecab-ipadic-2.7.0-20070801.tar.gz ;\
    cd mecab-ipadic-2.7.0-20070801; ./configure --with-charset=utf8; make; make install ;\
    echo "dicdir = /usr/local/lib/mecab/dic/ipadic" > /usr/local/etc/mecabrc

## Clean up
RUN apt remove -y build-essential ;\
    rm -rf rm -rf /var/cache/apt/archives/* /var/lib/apt/lists/* ;\
    rm -rf mecab-0.996.tar.gz* ;\
    rm -rf mecab-ipadic-2.7.0-20070801*

## Change environment to Japanese(Character and DateTime)
ENV LANG ja_JP.UTF-8
ENV LC_ALL ja_JP.UTF-8
RUN sed -i '$d' /etc/locale.gen \
  && echo "ja_JP.UTF-8 UTF-8" >> /etc/locale.gen \
  && locale-gen ja_JP.UTF-8 \
  && /usr/sbin/update-locale LANG=ja_JP.UTF-8 LANGUAGE="ja_JP:ja"
RUN /bin/bash -c "source /etc/default/locale"
RUN ln -sf  /usr/share/zoneinfo/Asia/Tokyo /etc/localtime

## Install additional R packages
RUN Rscript -e "install.packages(c('githubinstall','tm','slam','tidytext'))"
RUN Rscript -e "install.packages('RMeCab',repos='http://rmecab.jp/R')"

CMD ["/init"]
