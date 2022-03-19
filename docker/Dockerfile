FROM rockylinux:8.5

WORKDIR /
COPY ./repofile/* /etc/yum.repos.d
ENV TZ=America/Los_Angeles

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo $TZ > /etc/timezone \
    && PERFORCE_GPGKEY_SUM=E58131C0AEA7B082C6DC4C937123CB760FF18869 \
    && curl -fsSL https://package.perforce.com/perforce.pubkey | sed '/^Version/d' > /etc/pki/rpm-gpg/RPM-GPG-KEY-NVIDIA \
    && echo "$PERFORCE_GPGKEY_SUM  /etc/pki/rpm-gpg/RPM-GPG-KEY-NVIDIA" | sha256sum -c --strict - \
    && dnf -y install \
	helix-p4d \
	openssh-server \
	passwd \
	sudo \
	wget \
	&& dnf clean all && rm -rf /var/cache/dnf \
	&& ssh-keygen

gpg -n --import --import-options import-show perforce.pubkey

/opt/perforce/sbin/configure-helix-p4d.sh -n -p 1666 -r /opt/perforce/helix-core -u rcon -p j_f7WeXjk7fCBpyL.NpX
