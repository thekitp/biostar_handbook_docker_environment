FROM debian:buster-slim

# Make bash the default shell
SHELL [ "/bin/bash", "--login", "-c" ]

# Environments
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /opt/conda/bin:$PATH
ENV ENV_PREFIX bioinfo


# Essential deps
RUN apt-get update --fix-missing && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y wget bzip2 ca-certificates libglib2.0-0 libxext6 libsm6 libxrender1 git curl && \
    apt-get clean

# Install miniconda
RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda && \
    rm ~/miniconda.sh && \
    /opt/conda/bin/conda clean -tipsy && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc && \
    find /opt/conda/ -follow -type f -name '*.a' -delete && \
    find /opt/conda/ -follow -type f -name '*.js.map' -delete && \
    /opt/conda/bin/conda clean -afy

# Add bioconda deps channel
RUN conda config --add channels bioconda  && \
    conda config --add channels conda-forge 

# Create a bioinformatics enviroment called bioinfo.
RUN conda create -y --name bioinfo python=3.6

# Activate conda environment and install the requirement of biostar handbook + jupyterlab
RUN conda init bash && conda activate bioinfo && \
    curl http://data.biostarhandbook.com/install/conda.txt | xargs conda install -y && \
    conda install -c conda-forge jupyterlab

# Set workspace
WORKDIR /workspace

# Ensure Conda environment is activated at runtime
COPY entrypoint.sh /usr/local/bin/entrypoint.sh
RUN chmod +x /usr/local/bin/entrypoint.sh
ENTRYPOINT [ "/usr/local/bin/entrypoint.sh" ]

# Default command will launch JupyterLab server for development
CMD [ "jupyter", "lab", "--allow-root", "--no-browser", "--ip", "0.0.0.0"]