FROM quay.io/jupyter/scipy-notebook:python-3.11.6

# Configure environment variables
ENV DEBIAN_FRONTEND=noninteractive
ENV DEBCONF_NOWARNINGS=yes

USER root

### Install system packages
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        lmodern \
        file \
    && rm -rf /var/lib/apt/lists/*

### Install Quarto
ENV QUARTO_VERSION=1.6.40
RUN curl --silent -L --fail https://github.com/quarto-dev/quarto-cli/releases/download/v${QUARTO_VERSION}/quarto-${QUARTO_VERSION}-linux-amd64.deb > /tmp/quarto.deb && \
    apt-get update && \
    apt-get install -y --no-install-recommends /tmp/quarto.deb && \
    rm -rf /var/lib/apt/lists/*

### Ensure proper user and group settings
RUN groupadd jovyan && \
    usermod -g jovyan -a -G users jovyan && \
    usermod -aG sudo jovyan && \
    echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

USER ${NB_USER}

USER root
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        texlive-xetex \
        texlive-fonts-recommended \
        texlive-plain-generic && \
    rm -rf /var/lib/apt/lists/*
USER ${NB_USER}

### Install Python packages needed for the final project
RUN pip install --no-cache-dir \
    jupyterlab-quarto==0.2.8 \
    csvkit \
    otter-grader \
    scikit-learn \
    statsmodels

### Print Jupyter server token when opening a terminal
RUN echo "echo \"Jupyter server token: \$(jupyter server list 2>&1 | grep -oP '(?<=token=)[[:alnum:]]*')\"" > ${HOME}/.get-jupyter-url.sh && \
    echo "sh \${HOME}/.get-jupyter-url.sh" >> ${HOME}/.bashrc

# Expose port 8888 for JupyterLab (the base image handles the CMD)
EXPOSE 8888
