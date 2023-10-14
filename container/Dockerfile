# BUILD: docker build -t thespanglab/urnc-example-course:latest container
# RUN: docker run -it --rm -p 8888:8888 -v "$(pwd):/home/jovyan/urnc-example-course" thespanglab/urnc-example-course:latest /bin/bash
# BASE: https://hub.docker.com/r/jupyter/minimal-notebook/tags
# NOTE: This Dockerfile is built and pushed to `thespanglab/urnc-example-course` automatically whenever a tag is pushed to main. In theory you could use below commands to push the image manually, but this should NOT BE DONE unless you have a good reason to do so.
# docker login -u thespanglab -p $DOCKERHUB_TOKEN docker.io
# docker push thespanglab/urnc-example-course:1.0.0
FROM jupyter/minimal-notebook:lab-4.0.6

# Temporarily switch to root user because all installations should be executed as root. This allows users to mount local folders at location `/home/jovyan` during container startup without losing the installated packages and/or config files.
USER root

# Install Jupyter Extensions:
# - "notebook>=6,<7": Notebook v7 doesn't support rise anymore, so we need to stick with notebook v6
# - jedi-language-server: Language Server for Python
# - jupyter_contrib_nbextensions: Collection of extensions that add functionality to the Jupyter notebook
# - jupyter_nbextensions_configurator: Adds an additional tab to the classic notebok interface for configuring extensions
# - jupyter-archive: Adds the options to download the files/folders as zip
# - "jupyterlab>=3,<4": jupyter lab v4 doesn't include the classic notebook view anymore, so we need to stick with v3
# - jupyterlab-lsp: Language Server Protocol integration for Jupyter(lab)
# - rise: Adds slideshow mode to jupyter notebook v6 (doesnt support jupyter lab, doesnt support jupyter notebook v7)
# - urnc: Uni Regensburg Notebook Converter. Used for cloning/pulling repo on container startup.
RUN pip install -U \
    "notebook>=6,<7" \
    jedi-language-server \
    jupyter_contrib_nbextensions \
    jupyter_nbextensions_configurator \
    jupyter-archive \
    "jupyterlab>=3,<4" \
    jupyterlab-lsp \
    rise \
    urnc

# Enable Jupyter Extensions: this part can be improved and the effect of every line should be documented.
RUN jupyter labextension disable "@jupyterlab/apputils-extension:announcements" && \
    jupyter contrib nbextension install --system && \
    jupyter nbextensions_configurator enable --system && \
    jupyter nbextension install --system --py jupyter_nbextensions_configurator --overwrite && \
    jupyter nbextension enable --system --py jupyter_nbextensions_configurator && \
    jupyter serverextension enable --system --py jupyter_nbextensions_configurator

# Enable scrolling and chalkboard for rise and set transition theme to 'none' by copying the local config file `jupyter/serverconfig/notebook.json` into the correct location for system config files in the container (`/opt/conda/etc/jupyter/serverconfig`). To list all config paths used by jupyter you can use command `jupyter --paths` from inside the container.
RUN mkdir -p /opt/conda/etc/jupyter/serverconfig
COPY jupyter/serverconfig/notebook.json /opt/conda/etc/jupyter/serverconfig

# Switch back to normal user jovyan, who has user id 1000.
USER 1000

# Disable jupyter warning about frozen modules. For details see: https://stackoverflow.com/questions/75114841/debugger-warning-from-ipython-frozen-modules
ENV PYDEVD_DISABLE_FILE_VALIDATION=1
