The frontend of jupyterlab is composed of many packages/extensions, that bundle functionalities and provide the option to
add or replace features. Some of the most important packages are described here:

## @jupyterlab

### notebook
This extension implements most of the core functionality of a jupyter notebook. These implementations are meant to be building
blocks for a jupyter application and cannot be used by itself.

### notebook-extension
This extension adds the jupyter notebook as a usable notebook-panel or standalone notebook application. It relies heavily
on the implementations from @jupyterlab/notebook. It consists mainly of glue code and configuration files.

### apputils

## @lumino

### widgets