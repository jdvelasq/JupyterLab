#
# docker build --tag=jdvelasq/jupyterlab .
# docker push jdvelasq/jupyterlab
# docker run --rm -it -v "$PWD":/datalake --name colab -p 8888:8888 -p 5000:5000 jdvelasq/jupyterlab
# docker run --rm -it -v datalake:/datalake -name colab -p 8888:8888 jdvelasq/jupyterlab
# docker exec -it colab bash
#
# docker run --rm -it  --name colab -p 8888:8888 -p 5000:5000 jdvelasq/jupyterlab
#
FROM ubuntu:18.04

ENV LANG C.UTF-8 

WORKDIR /app
COPY . /app

ENV DEBIAN_FRONTEND noninteractive

#
#  Base
#
RUN apt-get update \
    && apt-get install -yq --no-install-recommends \
    apt-utils \
    bsdmainutils \
    curl \
    git \
    nano \
    wget \
    make \
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

#
#  ssh 
#
RUN apt-get update \
    && apt-get install -yq --no-install-recommends \
    pdsh \
    rsync \
    ssh \
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/* \
    && mv ssh/ssh_config /etc/ssh/ssh_config \
    && rm -f /etc/ssh/ssh_host_dsa_key /etc/ssh/ssh_host_rsa_key /root/.ssh/id_rsa \
    && ssh-keygen  -t rsa -P '' -f /root/.ssh/id_rsa \
    && cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys \
    && chmod 0600 /root/.ssh/authorized_keys  

#
# nodejs
#
RUN apt-get update \
    && apt-get install -y ca-certificates xz-utils \
    && curl -O https://nodejs.org/dist/v13.12.0/node-v13.12.0-linux-x64.tar.xz \
    && tar -xf node-v13.12.0-linux-x64.tar.xz \
    && cp -r node-v13.12.0-linux-x64/* /usr/ \
    && ln -s "$(which node)" /usr/bin/nodejs \
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

#
# Python 3.7 + Jupyter Lab
#
RUN apt-get update \
    && apt-get install -yq --no-install-recommends \
    build-essential \
    python-dev \
    python3.6 \
    python3-pip \
    python3-dev \       
    python3-venv \ 
    ipython \
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/* \
    && pip3 install --upgrade pip \
    && pip install --trusted-host pypi.python.org  --default-timeout=100 \
    setuptools \
    wheel \
    && pip install --trusted-host pypi.python.org --default-timeout=100 \
    #'jupyterlab>=2,<2.1.0a0' \
    #'jupyterlab<2.0.0' \
    "jupyterLab>=2.2.0,<3.0.0a0" \
    # jupyterlab \
    && mkdir /root/.jupyter \
    && mv jupyter_notebook_config.py /root/.jupyter/ 

RUN pip3 install --trusted-host pypi.python.org --default-timeout=100  --user -r requeriments.txt

#
# Jupyter Lab extensions
#
RUN jupyter labextension install @jupyter-widgets/jupyterlab-manager 
RUN jupyter labextension install @lckr/jupyterlab_variableinspector
RUN jupyter labextension install jupyter-matplotlib \
    && pip install --trusted-host pypi.python.org --default-timeout=100 ipympl
RUN jupyter labextension install @jupyterlab/toc 
RUN jupyter labextension install \
    @ryantam626/jupyterlab_code_formatter \
    && pip install --trusted-host pypi.python.org --default-timeout=100 \
    jupyterlab_code_formatter \
    black \
    isort \
    && jupyter serverextension enable --system --py jupyterlab_code_formatter

RUN jupyter labextension install nbdime-jupyterlab@2.0.0 \
    && pip install --trusted-host pypi.python.org --upgrade --default-timeout=100 jupyterlab-git \
    && jupyter lab build 

RUN jupyter labextension install jupyterlab-execute-time


#---------------------------------
# jupyterlab < 2.0.0
# RUN jupyter labextension install @kaggle/jupyterlab
#---------------------------------

RUN pip install --trusted-host pypi.python.org --upgrade --default-timeout=100 ipywidgets

# RUN jupyter labextension install @jupyterlab/github

#---------------------------------------------------
# unicode warning
# requiere 'jupyterLab >=2.2.0,<3.0.0a0'
#
RUN pip install --trusted-host pypi.python.org --default-timeout=100 jupyter-lsp \
    && jupyter labextension install @krassowski/jupyterlab-lsp \
    && pip install --trusted-host pypi.python.org --default-timeout=100 python-language-server[all]
#---------------------------------------------------

RUN pip install --trusted-host pypi.python.org  xeus-python ptvsd \
    && jupyter labextension install  @jupyterlab/debugger

#---------------------------------------------------
# The extension "jupyterlab_voyager" does not yet support the current version of JupyterLab.
# >=1.0.3 <2.0.0 @jupyterlab/application
# RUN jupyter labextension install jupyterlab_voyager
#---------------------------------------------------

#---------------------------------------------------
# # requiere 'jupyterlab<2.0.0'
# RUN jupyter labextension install \
#         @jupyterlab/dataregistry-extension
#---------------------------------------------------

#
# Python Data Science & ML
#
# RUN pip install --trusted-host pypi.python.org  --default-timeout=100 \
#     blaze \
#     nltk \
#     numba \
#     numpy \
#     pandas \
#     scipy \
#     sklearn \
#     statsmodels \
#     rope \
#     cdlib \
#     git+https://github.com/tqdm/tqdm.git@master#egg=tqdm

# RUN echo "import nltk; nltk.download('stopwords'); nltk.download('wordnet'); nltk.download('averaged_perceptron_tagger')" > n.py \
#     && python3 n.py

# RUN pip install --trusted-host pypi.python.org  --default-timeout=100 \
#     graphviz \
#     h5py \
#     ipython-sql \
#     pydot \
#     pygments \
#     pymysql \    
#     pyPDF2 \
#     pyyaml \
#     tables \
#     bs4

# RUN pip install --trusted-host pypi.python.org  --default-timeout=100 \
#     flask \
#     WTForms \
#     Flask-WTF

# RUN pip install --trusted-host pypi.python.org  \
#     altair \
#     bokeh \
#     dash \
#     imageio \
#     matplotlib \        
#     networkx \
#     plotly \
#     scikit-image \
#     seaborn \
#     vega_datasets \
#     wordcloud \
#     squarify \
#     pywaffle \
#     calmap \
#     python-igraph \
#     leidenalg 

# RUN pip install --trusted-host pypi.python.org --default-timeout=100 \
#     bandit \
#     docutils \
#     flake8 \
#     jinja2 \
#     m2r \
#     mypy \
#     nose \
#     paver \
#     pylint \
#     pytest  \
#     pytest-mpl \
#     rednose \
#     twine


#
# TensorFlow 2.0
#

# RUN pip install --trusted-host pypi.python.org --upgrade --default-timeout=100 \
#     tensorflow==2.0.1 

# RUN pip install --trusted-host pypi.python.org --default-timeout=100 \
#     tensorflow-transform \
#     tensorflow_hub \
#     tensorflow-text 

# RUN pip install --trusted-host pypi.python.org --default-timeout=100 \    
#     git+https://github.com/tensorflow/docs \
#     git+https://github.com/tensorflow/examples.git \
#     tensorflow_datasets 

#
# Sphinx
#
RUN apt-get update \
    && apt-get -yq --no-install-recommends install \
    pandoc \  
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*  \
    && pip install --trusted-host pypi.python.org --default-timeout=100 \
    sphinx \
    sphinx_rtd_theme \
    nbsphinx \
    pydata-sphinx-theme

#
# Cartopy & GeoPandas
#
# RUN apt-get update \
#     && apt-get install -yq --no-install-recommends \
#     libproj-dev \
#     proj-data \
#     proj-bin \
#     libgeos-dev \
#     libgeos-c1v5 \
#     && apt-get autoremove -y \
#     && apt-get clean -y \
#     && rm -rf /var/lib/apt/lists/* \
#     && pip install --trusted-host pypi.python.org --default-timeout=100 \
#     Cython \
#     && pip install --trusted-host pypi.python.org --default-timeout=100 \ 
#     six \
#     pyshp \
#     shapely \
#     cartopy \
#     geopandas \
#     geoplot

#
# R Language
#
# RUN apt-get update \
#     && apt-get install -yq --no-install-recommends \
#     r-base \
#     r-base-dev \
#     r-recommended \
#     libffi-dev \
#     libldap2-dev \
#     libsasl2-dev \
#     libxml2-dev \
#     libzmq3-dev \
#     libcurl4-openssl-dev \
#     libssh2-1-dev \
#     libssl-dev \
#     && apt-get autoremove -y \
#     && apt-get clean -y \
#     && rm -rf /var/lib/apt/lists/* \
#     && pip install --trusted-host pypi.python.org --default-timeout=100 rpy2==2.9.0 simplegeneric \
#     && Rscript -e "install.packages(c('formatR', 'styler'), repos = 'http://cran.us.r-project.org')" \
#     && Rscript -e "install.packages(c('devtools'), repos = 'http://cran.us.r-project.org')"  \
#     && Rscript -e "install.packages(c('tidyverse'), repos = 'http://cran.us.r-project.org')"  \
#     && Rscript -e "install.packages(c('tidytext'), repos = 'http://cran.us.r-project.org')" 

#     && Rscript -e "install.packages(c('ggraph'), repos = 'http://cran.us.r-project.org')"  \
#     && Rscript -e "install.packages(c('igraph'), repos = 'http://cran.us.r-project.org')"  \
#     && Rscript -e "install.packages(c('widyr'), repos = 'http://cran.us.r-project.org')"  \
#     && Rscript -e "install.packages(c('bibliometrix'), repos = 'http://cran.us.r-project.org')" 

ENV DEBIAN_FRONTEND=dialog

#
# Parte generica
#
EXPOSE   8888  5000  8000  6006 
RUN rm -rf /app/*
WORKDIR /datalake
ENTRYPOINT /etc/init.d/ssh start && jupyter lab

# MySQL
# RUN apt-get update \
#     && apt-get install -yq --no-install-recommends  \ 
#         build-essential \
#         python3-dev \
#         python-dev \
#         libssl-dev \
#         libffi-dev \
#     && pip3 install --trusted-host pypi.python.org  \
#         cryptography \
#     && apt-get install -yq --no-install-recommends \
#         mysql-server \
#         libmysqlclient-dev \
#     && rm -rf /var/lib/apt/lists/* \
#     && pip3 install --trusted-host pypi.python.org  \ 
#         ipython-sql \
#         pymysql \
#         mysqlclient

# Apache Superset
# ENV LANG=C.UTF-8 \
#     LC_ALL=C.UTF-8 \
#     FLASK_APP=superset
# RUN apt-get update -y \
#     && apt-get install -yq --no-install-recommends \
#         apt-transport-https \
#         build-essential \
#         libssl-dev \
#         libffi-dev \
#         python3.6-dev \
#         python-dev \
#         python-pip \
#         libsasl2-dev \
#         libldap2-dev \
#         libxi-dev \
#     && rm -rf /var/lib/apt/lists/* \
#     && pip3 install --trusted-host pypi.python.org apache-superset \
#     && superset db upgrade \
#     && superset init

## flask fab create-admin \
# EXPOSE 8080