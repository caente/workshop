# Use an official Python runtime as a parent image
FROM workhorse

USER root

##################### PREREQUISITES ########################

RUN apt-get -y update
RUN apt-get -y install m4 wget unzip aspcud libshp-dev libplplot-dev gfortran
RUN apt-get -y install pkg-config git camlp4-extra curl build-essential libcap-dev

#RUN curl -sLO https://github.com/projectatomic/bubblewrap/archive/v0.3.1.tar.gz

#RUN tar xzvf v0.3.1.tar.gz 

#RUN cd bubblewrap-0.3.1 && sh autogen.sh && make && make install

RUN curl -sLO https://github.com/ocaml/opam/releases/download/2.0.2/opam-2.0.2-x86_64-linux 

RUN mv opam-2.0.2-x86_64-linux /usr/local/bin/opam

RUN chmod +x /usr/local/bin/opam

USER caente

RUN opam init -y --disable-sandboxing 
RUN opam update 
RUN opam switch create 4.06.0 
RUN eval $(opam env)
RUN opam install -y oasis dune ocaml-compiler-libs ctypes utop plplot base stdio configurator alcotest sexplib

#################### SET UP ENV VARS #######################

ENV PATH /home/caente/.opam/4.06.0/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:$PATH
ENV CAML_LD_LIBRARY_PATH /home/caente/.opam/4.06.0/lib/stublibs
ENV LD_LIBRARY_PATH /usr/lib/:/usr/local/lib:/home/caente/.opam/4.06.0/lib/:/home/caente/.opam/4.06.0/lib/stublibs/:/usr/lib/x86_64-linux-gnu/:/opt/OpenBLAS/lib


#################### INSTALL OPENBLAS ######################

ENV OPENBLASPATH /home/caente/OpenBLAS
RUN cd /home/caente && git clone https://github.com/xianyi/OpenBLAS.git
WORKDIR $OPENBLASPATH 
RUN make 
USER root
RUN make install 
USER caente
RUN make clean
WORKDIR /home/caente

################# INSTALL EIGEN LIBRARY ####################

ENV EIGENPATH /home/caente/eigen
RUN cd /home/caente/ && git clone https://github.com/owlbarn/eigen.git

RUN sed -i -- 's/-Wno-extern-c-compat -Wno-c++11-long-long -Wno-invalid-partial-specialization/-Wno-ignored-attributes/g' $EIGENPATH/lib/Makefile \
    && sed -i -- 's/-flto/ /g' $EIGENPATH/lib/Makefile \
    && sed -i -- 's/-flto/ /g' $EIGENPATH/_oasis 

WORKDIR $EIGENPATH 
RUN make oasis && make 
USER root
RUN make install 
RUN cp $EIGENPATH/_build/include/libeigen.a /usr/local/lib/
USER caente
WORKDIR /home/caente

####################   INSTALL OWL  #######################

ENV OWLPATH /home/caente/owl
RUN cd /home/caente && git clone https://github.com/owlbarn/owl.git
RUN sed -i -- 's/\"-llapacke\" :: ls/ls/g' $OWLPATH/src/owl/config/configure.ml  # FIXME: hacking
WORKDIR $OWLPATH 
RUN make 
USER root
RUN make install
USER caente
WORKDIR /home/caente

############## SET UP DEFAULT CONTAINER VARS ##############

RUN echo "#require \"owl-top\";; open Owl;;" >> /home/caente/.ocamlinit \
    && bash -c 'echo -e "export LD_LIBRARY_PATH=$LD_LIBRARY_PATH" >> /home/caente/.profile' \
    && opam config env >> /home/caente/.bashrc \
    && bash -c "source /home/caente/.bashrc" 


