.. _installation:

############
Installation
############

Manual-mode
===========

.. |br| raw:: html

   <br />

You'll need `wfmash <https://github.com/ekg/wfmash>`_, `seqwish <https://github.com/ekg/seqwish>`_, `smoothxg <https://github.com/pangenome/smoothxg>`_, 
`odgi <https://github.com/pangenome/odgi>`_, `gfaffix <https://github.com/marschall-lab/GFAffix>`_, `bcftools <https://github.com/samtools/bcftools>`_ and `vg <https://github.com/vgteam/vg>`_ 
in your shell's ``PATH``. They can be build from source, or installed via Bioconda.
Then, add the ``pggb`` bash script to your ``PATH`` to complete the installation. 
`How to add a binary to my path? <https://zwbetz.com/how-to-add-a-binary-to-your-path-on-macos-linux-windows/>`_ |br|
Optionally, install `MultiQC <https://multiqc.info/>`_ for reporting or `pigz <https://zlib.net/pigz/>`_ to compress the output files of the pipeline.

Bioconda
========

A ``pggb`` recipe for ``Bioconda`` is available at https://anaconda.org/bioconda/pggb.
To install the latest version using ``Conda`` execute:

.. code-block:: bash

    conda install -c bioconda pggb


GUIX
====

.. code-block:: bash

    git clone https://github.com/ekg/guix-genomics
    cd guix-genomics
    GUIX_PACKAGE_PATH=. guix package -i pggb


Docker
======

To simplify installation and versioning, we have an automated GitHub action that pushes the current docker build to the GitHub registry.
To use it, first pull the actual image:

.. code-block:: bash

    docker pull ghcr.io/pangenome/pggb:latest


Or if you want to pull a specific snapshot from `https://github.com/orgs/pangenome/packages/container/package/pggb <https://github.com/orgs/pangenome/packages/container/package/pggb>`_:

.. code-block:: bash

    docker pull ghcr.io/pangenome/pggb:TAG


Going in the ``pggb`` directory

.. code-block:: bash

    git clone --recursive https://github.com/pangenome/pggb.git
    cd pggb

You can run the container using the human leukocyte antigen (HLA) data provided in this repo:

.. code-block:: bash

    docker run -it -v ${PWD}/data/:/data ghcr.io/pangenome/pggb:latest "pggb -i /data/HLA/DRB1-3123.fa.gz -p 70 -s 3000 -G 2000 -n 10 -t 16 -v -V 'gi|568815561:#' -o /data/out -M -C cons,100,1000,10000 -m"


The ``-v`` argument of ``docker run`` always expects a full path.
**If you intended to pass a host directory, use absolute path.**
This is taken care of by using ``${PWD}``.

If you want to experiment around, you can build a docker image locally using the ``Dockerfile``:

.. code-block:: bash

    docker build --target binary -t ${USER}/pggb:latest .


Staying in the ``pggb`` directory, we can run ``pggb`` with the locally build image:

.. code-block:: bash

    docker run -it -v ${PWD}/data/:/data ${USER}/pggb "pggb -i /data/HLA/DRB1-3123.fa.gz -p 70 -s 3000 -G 2000 -n 10 -t 16 -v -V 'gi|568815561:#' -o /data/out -M -C cons,100,1000,10000 -m"

--------------------------
Docker and AVX
--------------------------

``abPOA`` of ``pggb`` uses SIMD instructions which require AVX.
The currently built docker image has ``-march=haswell`` set.
This means the docker image can be run by processors that support AVX256 or later.
If you have a processor that supports AVX512, it is recommended to rebuild the docker image locally, removing the line

.. code-block:: bash

    sed -i 's/-march=native/-march=haswell/g' deps/abPOA/CMakeLists.txt


from the ``Dockerfile``. This can lead to better performance in the ``abPOA`` step on machines which have AVX512 support.

Nextflow
========

A Nextflow DSL2 port of ``pggb`` is actively developed by the `nf-core <https://nf-co.re/>`_ community.
See `nf-core/pangenome <https://github.com/nf-core/pangenome>`_ for more details. The aim is to implement a cluster-scalable version of ``pggb``. 
The Nextflow version can run the precise base-level alignment step of ``wfmash`` in parallel across the nodes of a cluster. 
This makes it already faster than this `bash` implementation.
