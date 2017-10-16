Description
-----------

Cut an updated PostgreSQL release for rpmbuild

Updating sources
----------------

    #!/bin/sh -xe

    cd ~/git/postgresql
    git ls-remote --heads
    git checkout REL_10_STABLE

    package=postgresql-10
    rpmbuild=~/git/$package-rpmbuild

    git archive --prefix=$package/ REL_10_STABLE | bzip2 > $rpmbuild/SOURCES/$package.tar.bz2
    sha256 $rpmbuild/SOURCES/$package.tar.bz2 > $rpmbuild/SOURCES/$package.tar.bz2.sha256

Salt Initialization
-------------------

    # pgbuild.sls
    packages:
      pkg.installed:
        - pkgs:
          - bison
          - docbook-dtds
          - docbook-style-dsssl
          - docbook-style-xsl
          - docbook-utils
          - e2fsprogs-devel
          - elinks
          - flex
          - fop
          - gcc
          - git
          - help2man
          - krb5-devel
          - libselinux-devel
          - libuuid-devel
          - libxslt
          - libxslt-devel
          - make
          - multilib-rpm-config
          - openjade
          - openldap-devel
          - opensp
          - openssl-devel
          - pam-devel
          - perl-ExtUtils-Embed
          - perl-ExtUtils-MakeMaker
          - python-devel
          - readline-devel
          - readline-devel
          - rpm-build
          - systemd-devel
          - systemtap-sdt-devel
          - tcl-devel
          - uuid-devel
          - vim-enhanced
    eradman:
      user.present:
        - fullname: Eric Radman
        - home: /home/eradman
        - shell: /bin/bash
        - groups:
            - wheel

    srv_salt_latest:
      git.latest:
        - name: https://github.com/eradman/postgresql-10-rpmbuild.git
        - target: /home/eradman/rpmbuild
        - force_reset: True
        - force_fetch: True
        - user: eradman

    eradman_shared_key:
      ssh_auth.present:
        - user: eradman
        - source: salt://id_ed25519.pub

History
-------

The spec script and patches were pulled using

    cd SPECS
    ftp https://download.postgresql.org/pub/repos/yum/srpms/10/redhat/rhel-7Server-x86_64/postgresql10-10.0-1PGDG.rhel7.src.rpm
    rpm2cpio postgresql10-10.0-1PGDG.rhel7.src.rpm | cpio -idmv
    rm *.bz2 *.tpm
    mv postgresql-10.spec ../SPECS

The only modification was to ensure docs were built in `

    make -C doc/src/sgml all

