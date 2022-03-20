FROM rockylinux:8.5

WORKDIR /
COPY ./repofile/* /etc/yum.repos.d
ENV TZ=America/Los_Angeles

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo $TZ > /etc/timezone \
    && PERFORCE_GPGKEY_SUM=cfd3de702b06cdc904fb17604767d6c484bfc54d7befef23a669a641816d42bd \
    && curl -fsSL https://package.perforce.com/perforce.pubkey | sed '/^Version/d' > /etc/pki/rpm-gpg/RPM-GPG-KEY-PERFORCE \
    && echo "$PERFORCE_GPGKEY_SUM  /etc/pki/rpm-gpg/RPM-GPG-KEY-PERFORCE" | sha256sum -c --strict - \
    && dnf -y install \
	helix-p4d \
	openssh-server \
	passwd \
	sudo \
	&& dnf clean all && rm -rf /var/cache/dnf \
	&& ssh-keygen -t rsa -b 4096 -N '' -f ~/.ssh/id_rsa \
	&& echo docker | passwd root --stdin

CMD ["/usr/sbin/sshd", "-D"] && /usr/bin/bash
