FROM centos:7 AS base

#add dinstall group
RUN groupadd dinstall && useradd -g dinstall -m -d /home/dmdba -s /bin/bash dmdba

FROM base AS install

#copy install files
ARG installFilesDir=/home/dmdba/install
ARG installConfName=setup.xml

COPY DMInstall.bin  ${installFilesDir}/
COPY ${installConfName} ${installFilesDir}/

#change user.DAMENG recommend installing with noroot
USER dmdba




#prepare install diretory
RUN mkdir /home/dmdba/dmdbms

#execute silence install
RUN sh ${installFilesDir}/DMInstall.bin -q ${installFilesDir}/${installConfName}

FROM base

RUN ping -c 5 www.baidu.com ; exit 0

COPY ./yum_sources/* /etc/yum.repos.d/

RUN yum clean all


# add gosu

COPY ./binary_file/* /usr/local/bin/

RUN mv /usr/local/bin/gosu-amd64 /usr/local/bin/gosu

RUN chmod +x /usr/local/bin/gosu


RUN gosu dmdba mkdir /home/dmdba/dmdbms
COPY --chown=dmdba:dinstall --from=install /home/dmdba/dmdbms /home/dmdba/dmdbms

#RUN yum install -y initscripts

#RUN chown -R dmdba /home/dmdba/dmdbms/  \
#    && chown -R dmdba:dinstall /home/dmdba/ \
#    && chmod -R 775 /home/dmdba/ 




#更改目录权限
#RUN chown -R dmdba /home/dmdba/dmdbms/ 
#RUN chown -R dmdba:dinstall /home/dmdba/
#RUN chmod -R 775 /home/dmdba/


#RUN /home/dmdba/dmdbms/script/root/root_installer.sh
RUN cp "/home/dmdba/dmdbms/bin/dm_svc.conf" /etc/dm_svc.conf
RUN chmod 6755 "/home/dmdba/dmdbms/bin/dminit" \
    && chmod 6755 "/home/dmdba/dmdbms/bin/dmserver" \
    && chown 0:0 "/home/dmdba/dmdbms/bin/dmcss" \
    && chmod 6755 "/home/dmdba/dmdbms/bin/dmcss"


COPY docker-entrypoint.sh /usr/local/bin
RUN chmod +x /usr/local/bin/docker-entrypoint.sh

EXPOSE 5236

WORKDIR /home/dmdba



#写入环境变量
RUN echo "export DM_HOME=/home/dmdba/dmdbms" >> //home/dmdba/.bash_profile && \
    echo "export LD_LIBRARY_PATH=\$LD_LIBRARY_PATH:\$DM_HOME/bin" >> //home/dmdba/.bash_profile && \
    echo "export PATH=\$PATH:\$HOME/.local/bin:\$HOME/bin:\$DM_HOME/bin" >> //home/dmdba/.bash_profile



ENTRYPOINT ["docker-entrypoint.sh"]