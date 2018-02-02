FROM amazonlinux
LABEL kuryu "kuryu <revision279@gmail.com>"

#RUN yum -y update && yum clean all
RUN yum -y install sudo findutils shadow-utils openssh-server

RUN useradd ec2-user -m -s /bin/bash
RUN echo 'ec2-user ALL=(ALL) NOPASSWD:ALL' > /etc/sudoers.d/ec2-user


USER ec2-user
WORKDIR /home/ec2-user

ADD ./id_rsa.pub /home/ec2-user/.ssh/authorized_keys

USER root
RUN ssh-keygen -A

RUN chown -R ec2-user:ec2-user /home/ec2-user/.ssh
RUN chmod 600 /home/ec2-user/.ssh/*


EXPOSE 22
EXPOSE 3000
CMD /usr/sbin/sshd -D && tail -f /dev/null
