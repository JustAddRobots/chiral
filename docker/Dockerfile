FROM rockylinux:8.5

WORKDIR /
COPY ./repofile/* /etc/yum.repos.d/
ENV TZ=America/Los_Angeles
ENV P4PORT=ssl:1666
ENV P4ROOT=/opt/perforce/perforce-data
ENV P4USER=perforce
ENV P4PASSWD=j_f7WeXjk7fCBpyL.NpX
ENV P4SSLDIR=/opt/perforce/tls

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo $TZ > /etc/timezone \
#     && PERFORCE_GPGKEY_SUM=cfd3de702b06cdc904fb17604767d6c484bfc54d7befef23a669a641816d42bd \
    && curl -fsSL https://package.perforce.com/perforce.pubkey | sed '/^Version/d' > /etc/pki/rpm-gpg/RPM-GPG-KEY-PERFORCE \
#    && echo "$PERFORCE_GPGKEY_SUM  /etc/pki/rpm-gpg/RPM-GPG-KEY-PERFORCE" | sha256sum -c --strict - \
    && dnf -y install \
	helix-p4d \
	mlocate \
	nginx \
	openssh-clients \
	openssh-server \
	passwd \
	procps \
	sudo \
	tree \
	vim \
	&& dnf clean all && rm -rf /var/cache/dnf \
	&& ssh-keygen -t rsa -b 4096 -N '' -f ~/.ssh/id_rsa && ssh-keygen -A \
	&& echo docker | passwd root --stdin \
	&& useradd rcon && echo password | passwd rcon --stdin \
	&& usermod -a -G wheel rcon \
	&& mkdir /home/rcon/.ssh \
	&& ssh-keygen -t rsa -b 4096 -N '' -f /home/rcon/.ssh/id_rsa \
	&& rm -f /var/run/nologin 

COPY ./tls/privatekey.txt /etc/pki/tls/private/
COPY ./tls/certificate.txt /etc/pki/tls/certs/
COPY ./tls/privatekey.txt /opt/perforce/tls/
COPY ./tls/certificate.txt /opt/perforce/tls/
COPY ./tls/authorized_keys /root/.ssh/authorized_keys
COPY ./tls/authorized_keys /home/rcon/.ssh/authorized_keys
COPY ./conf/nginx.conf /etc/nginx/nginx.conf
COPY ./conf/setenv.sh /etc/profile.d/
COPY ./conf/typemap_ue4.txt /usr/local/tmp/

RUN echo "P4PORT: $P4PORT" \
	&& echo "P4ROOT: $P4ROOT" \
	&& echo "P4USER: $P4USER" \
	&& echo "P4SSLDIR: $P4SSLDIR" \
	&& chown -R perforce:perforce $P4SSLDIR \
	&& chmod 700 $P4SSLDIR \
	&& chmod 600 $P4SSLDIR/* \
	&& chmod 600 /root/.ssh/authorized_keys \
	&& chmod 600 /home/rcon/.ssh/authorized_keys \
	&& chown -R rcon:rcon /home/rcon/.ssh \
	&& /opt/perforce/sbin/configure-helix-p4d.sh main -n -p $P4PORT -r $P4ROOT -u $P4USER -P $P4PASSWD --unicode --case 0 \
	&& sudo -u perforce P4SSLDIR=$P4SSLDIR p4d -Gf > /opt/perforce/tls/signature.txt

CMD /usr/sbin/sshd ; /usr/sbin/nginx ; sudo -u perforce p4dctl start main ; /usr/bin/bash
