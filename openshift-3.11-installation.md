# OpenShift 3.11安装

    yum update -y

    cat /etc/redhat-release
    CentOS Linux release 7.7.1908 (Core)
    uname -a
    Linux ansible-master 3.10.0-1062.18.1.el7.x86_64 #1 SMP Tue Mar 17 23:49:17 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux

    vi /etc/hosts
    192.168.1.73 ansible-master ansible-master.twingao.com
    192.168.1.74 ansible-node1
    192.168.1.75 ansible-node2
    
    ssh-keygen
    ssh-copy-id ansible-master
    ssh-copy-id ansible-node1
    ssh-copy-id ansible-node2
    
    vi /etc/selinux/config
    SELINUX=enforcing
    SELINUXTYPE=targeted
    
    yum install docker-1.13.1 -y
    
    
    gunzip -c docker.io_openshift_origin-console.v3.11.0.tar.gz | docker load
    gunzip -c docker.io_openshift_origin-control-plane.v3.11.0.tar.gz | docker load
    gunzip -c docker.io_openshift_origin-deployer.v3.11.0.tar.gz | docker load
    gunzip -c docker.io_openshift_origin-docker-registry.v3.11.0.tar.gz | docker load
    gunzip -c docker.io_openshift_origin-haproxy-router.v3.11.0.tar.gz | docker load
    gunzip -c docker.io_openshift_origin-node.v3.11.0.tar.gz | docker load
    gunzip -c docker.io_openshift_origin-pod.v3.11.0.tar.gz | docker load
    gunzip -c docker.io_openshift_origin-service-catalog.v3.11.0.tar.gz | docker load
    gunzip -c docker.io_openshift_origin-template-service-broker.v3.11.0.tar.gz | docker load
    gunzip -c docker.io_openshift_origin-web-console.v3.11.0.tar.gz | docker load
    
    gunzip -c prometheus-operator.v0.23.2.tar.gz | docker load
    gunzip -c grafana.5.2.1.tar.gz | docker load
    gunzip -c etcd.v3.2.22.tar.gz | docker load
    gunzip -c oauth-proxy.v1.1.0.tar.gz | docker load
    
    
    
    docker pull docker.io/openshift/origin-node:v3.11.0
    docker pull docker.io/openshift/origin-control-plane:v3.11.0 
    docker pull docker.io/openshift/origin-haproxy-router:v3.11.0
    docker pull docker.io/openshift/origin-deployer:v3.11.0
    docker pull docker.io/openshift/origin-template-service-broker:v3.11.0
    docker pull docker.io/openshift/origin-pod:v3.11.0
    docker pull docker.io/openshift/origin-docker-registry:v3.11.0
    docker pull docker.io/openshift/origin-console:v3.11.0
    docker pull docker.io/openshift/origin-service-catalog:v3.11.0
    docker pull docker.io/openshift/origin-web-console:v3.11.0
    docker pull docker.io/tripleorocky/coreos-prometheus-operator:v0.23.2
    docker pull quay.io/coreos/prometheus-operator:v0.23.2
    docker pull docker.io/grafana/grafana:5.2.1
    docker pull quay.io/coreos/etcd:v3.2.22
    docker pull docker.io/openshift/oauth-proxy:v1.1.0
    
    
    
    docker images
    REPOSITORY                                 TAG                 IMAGE ID            CREATED             SIZE
    openshift/origin-node                      v3.11.0             4270b03eb4c4        8 weeks ago         1.19 GB
    openshift/origin-control-plane             v3.11.0             fe78a3c5b0fd        8 weeks ago         835 MB
    openshift/origin-haproxy-router            v3.11.0             fd1d1f2b5ca1        8 weeks ago         415 MB
    openshift/origin-deployer                  v3.11.0             e8115f12e4e7        8 weeks ago         388 MB
    openshift/origin-pod                       v3.11.0             b874f1d7a829        8 weeks ago         265 MB
    openshift/origin-template-service-broker   v3.11.0             e792bf3f82a3        8 weeks ago         339 MB
    openshift/origin-docker-registry           v3.11.0             9dffb2abf1dd        14 months ago       310 MB
    openshift/origin-console                   v3.11.0             ab1db955ef9e        14 months ago       264 MB
    openshift/origin-service-catalog           v3.11.0             eda829aae0a0        15 months ago       330 MB
    openshift/origin-web-console               v3.11.0             be30b6cce5fa        18 months ago       339 MB
    quay.io/coreos/prometheus-operator         v0.23.2             835a7e260b35        20 months ago       47 MB
    grafana/grafana                            5.2.1               1bfead9ff707        21 months ago       245 MB
    quay.io/coreos/etcd                        v3.2.22             ff5dd2137a4f        22 months ago       37.3 MB
    openshift/oauth-proxy                      v1.1.0              90c45954eb03        2 years ago         235 MB
    
    
    yum install -y ansible-2.7.17-1.el7.ans.noarch.rpm
    
    
    
    
    
    
    
    
    
    
    vi /root/openshift-ansible-release-3.11/roles/openshift_repos/templates/CentOS-OpenShift-Origin311.repo.j2
    
    [centos-openshift-origin311]
    name=CentOS OpenShift Origin
    baseurl=http://mirrors.aliyun.com/centos/7/paas/x86_64/openshift-origin311/
    enabled=1
    gpgcheck=1
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-PaaS
    
    [centos-openshift-origin311-testing]
    name=CentOS OpenShift Origin Testing
    baseurl=http://buildlogs.centos.org/centos/7/paas/x86_64/openshift-origin311/
    enabled={{ 1 if openshift_repos_enable_testing else 0 }}
    gpgcheck=0
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-PaaS
    
    [centos-openshift-origin311-debuginfo]
    name=CentOS OpenShift Origin DebugInfo
    baseurl=http://debuginfo.centos.org/centos/7/paas/x86_64/
    enabled=0
    gpgcheck=1
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-PaaS
    
    [centos-openshift-origin311-source]
    name=CentOS OpenShift Origin Source
    baseurl=http://vault.centos.org/centos/7/paas/Source/openshift-origin311/
    enabled=0
    gpgcheck=1
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-PaaS
    
    
    vi /etc/ansible/hosts
    [OSEv3:children]
    masters
    nodes
    etcd
    
    [OSEv3:vars]
    ansible_ssh_user=root
    openshift_deployment_type=origin
    
    openshift_disable_check=disk_availability,docker_storage,memory_availability,docker_image_availability
    openshift_master_identity_providers=[{'name':'htpasswd_auth','login':'true','challenge':'true','kind':'HTPasswdPasswordIdentityProvider',}]
    openshift_master_cluster_hostname=ansible-master.twingao.com
    
    [masters]
    ansible-master
    
    [etcd]
    ansible-master
    
    [nodes]
    ansible-master openshift_node_group_name='node-config-master'
    ansible-node1 openshift_node_group_name='node-config-compute'
    ansible-node2 openshift_node_group_name='node-config-compute'
    
    
    ansible-playbook playbooks/prerequisites.yml
    
    ansible-playbook playbooks/deploy_cluster.yml
    
    
    



