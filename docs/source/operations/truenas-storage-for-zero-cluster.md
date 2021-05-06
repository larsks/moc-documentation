# TrueNAS NFS Storage for Zero OpenShift Cluster

## Overview

We repurposed an recently retired Fujitsu server, `10.0.17.8`, to
use as temporary storage to relieve storage capacity issues on the
[Zero cluster][].

[zero cluster]: https://console-openshift-console.apps.zero.massopen.cloud/

### Connecting to the system

You can reach the system by first logging into
kzn-rtr.infra.massopen.cloud (or any of the OpenShift nodes).

You can access the web UI at <http://10.253.0.10/>, or by setting up
port forwarding from your local machine, e.g:

```
ssh -L 8080:10.253.0.10:80 kzn-rtr.infra.massopen.cloud
```

And then connecting to <http://localhost:8080>.

You can log into the system using `ssh` if your ssh key has been added
to `root`'s `authorized_keys` file, or you can connect as the `csi`
user using the private key embedded in the OpenShift cluster:

```
oc -n democratic-csi get secret moc-nfs-democratic-csi-driver-config \
  -o yaml |
  yq -r '.data."driver-config-file.yaml"' |
  base64 -d |
  yq -r .sshConnection.privateKey
```

## Operating System

The server runs [TrueNAS][] (neè [FreeNAS][]) 12, which is based on FreeBSD.

[TrueNAS]: https://www.truenas.com/
[FreeNAS]: https://www.freenas.org/

## Networking

One of the system's two 10Gb interfaces is attached to VLAN 210:

```
# ifconfig ix0
ix0: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> metric 0 mtu 1500
        description: ix0
        options=a538b9<RXCSUM,VLAN_MTU,VLAN_HWTAGGING,JUMBO_MTU,VLAN_HWCSUM,WOL_UCAST,WOL_MCAST,WOL_MAGIC,VLAN_HWFILTER,VLAN_HWTSO,RXCSUM_IPV6>
        ether 90:1b:0e:0f:5d:8a
        inet 10.253.0.10 netmask 0xfffffe00 broadcast 10.253.1.255
        media: Ethernet autoselect (10Gbase-Twinax <full-duplex,rxpause,txpause>)
        status: active
        nd6 options=9<PERFORMNUD,IFDISABLED>
```

## Available drives

The system has 39 838GB drives.

```
Geom name: da0
Providers:
1. Name: da0
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e2
   descr: LSI MR SAS 6G 1GB
   lunid: 6003005701e387502117a99e129b7f83
   ident: 00837f9b129ea917215087e301570003
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da1
Providers:
1. Name: da1
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR SAS 6G 1GB
   lunid: 6003005701e5b5901be6df1c18118972
   ident: 00728911181cdfe61b90b5e501570003
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da2
Providers:
1. Name: da2
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR SAS 6G 1GB
   lunid: 6003005701e38750211637773a310fae
   ident: 00ae0f313a773716215087e301570003
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da3
Providers:
1. Name: da3
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR SAS 6G 1GB
   lunid: 6003005701e38750211637773a31a95a
   ident: 005aa9313a773716215087e301570003
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da4
Providers:
1. Name: da4
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR SAS 6G 1GB
   lunid: 6003005701e38750211637773a31c9e8
   ident: 00e8c9313a773716215087e301570003
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da5
Providers:
1. Name: da5
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR SAS 6G 1GB
   lunid: 6003005701e38750211637773a31eac8
   ident: 00c8ea313a773716215087e301570003
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da6
Providers:
1. Name: da6
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR SAS 6G 1GB
   lunid: 6003005701e38750211637773a3208dd
   ident: 00dd08323a773716215087e301570003
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da7
Providers:
1. Name: da7
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR SAS 6G 1GB
   lunid: 6003005701e38750211637773a32aaae
   ident: 00aeaa323a773716215087e301570003
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da8
Providers:
1. Name: da8
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR SAS 6G 1GB
   lunid: 6003005701e38750211637773a32ca4c
   ident: 004cca323a773716215087e301570003
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da9
Providers:
1. Name: da9
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR SAS 6G 1GB
   lunid: 6003005701e38750211637773a32eb30
   ident: 0030eb323a773716215087e301570003
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da10
Providers:
1. Name: da10
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR SAS 6G 1GB
   lunid: 6003005701e38750211637773a338139
   ident: 003981333a773716215087e301570003
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da11
Providers:
1. Name: da11
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR SAS 6G 1GB
   lunid: 6003005701e38750211637773a33a2f3
   ident: 00f3a2333a773716215087e301570003
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da12
Providers:
1. Name: da12
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR SAS 6G 1GB
   lunid: 6003005701e38750211637773a33c34e
   ident: 004ec3333a773716215087e301570003
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da13
Providers:
1. Name: da13
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR SAS 6G 1GB
   lunid: 6003005701e38750211637773a346593
   ident: 009365343a773716215087e301570003
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da14
Providers:
1. Name: da14
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR SAS 6G 1GB
   lunid: 6003005701e5b5902115f9c46b308d0e
   ident: 000e8d306bc4f9152190b5e501570003
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da15
Providers:
1. Name: da15
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df3e1a8e82e6
   ident: 00e6828e1a3edfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da16
Providers:
1. Name: da16
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df401aaeab1d
   ident: 001dabae1a40dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da17
Providers:
1. Name: da17
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df421acf030c
   ident: 000c03cf1a42dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da18
Providers:
1. Name: da18
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df441af00446
   ident: 004604f01a44dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da19
Providers:
1. Name: da19
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df471b112ea3
   ident: 00a32e111b47dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da20
Providers:
1. Name: da20
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df491b33025b
   ident: 005b02331b49dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da21
Providers:
1. Name: da21
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df4b1b54fe62
   ident: 0062fe541b4bdfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da22
Providers:
1. Name: da22
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df4d1b779d64
   ident: 00649d771b4ddfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da23
Providers:
1. Name: da23
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r1w1e3
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df501b9a646b
   ident: 006b649a1b50dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da24
Providers:
1. Name: da24
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r2w2e5
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df521bbdd221
   ident: 0021d2bd1b52dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da25
Providers:
1. Name: da25
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r2w2e5
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df541be1644d
   ident: 004d64e11b54dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da26
Providers:
1. Name: da26
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r2w2e5
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df571c059105
   ident: 000591051c57dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da27
Providers:
1. Name: da27
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r2w2e5
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df591c29f229
   ident: 0029f2291c59dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da28
Providers:
1. Name: da28
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r2w2e5
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df5b1c4ef831
   ident: 0031f84e1c5bdfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da29
Providers:
1. Name: da29
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r2w2e5
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df5e1c7af946
   ident: 0046f97a1c5edfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da30
Providers:
1. Name: da30
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r2w2e5
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df611ca0e7c4
   ident: 00c4e7a01c61dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da31
Providers:
1. Name: da31
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r2w2e5
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df631cc6f043
   ident: 0043f0c61c63dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da32
Providers:
1. Name: da32
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r2w2e5
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df661ced8d32
   ident: 00328ded1c66dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da33
Providers:
1. Name: da33
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r2w2e5
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df681d144568
   ident: 006845141d68dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da34
Providers:
1. Name: da34
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r2w2e5
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df6b1d3b9a53
   ident: 00539a3b1d6bdfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da35
Providers:
1. Name: da35
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r2w2e5
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df6e1d631e60
   ident: 00601e631d6edfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da36
Providers:
1. Name: da36
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r2w2e5
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df711d925523
   ident: 002355921d71dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da37
Providers:
1. Name: da37
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r2w2e5
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df731dbac0b0
   ident: 00b0c0ba1d73dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

Geom name: da38
Providers:
1. Name: da38
   Mediasize: 899627876352 (838G)
   Sectorsize: 512
   Mode: r2w2e5
   descr: LSI MR9286CV-8e
   lunid: 600605b009275cc01be6df761de3bf6c
   ident: 006cbfe31d76dfe61bc05c2709b00506
   rotationrate: unknown
   fwsectors: 63
   fwheads: 255

```

## Zpool configuration

The drives are configured as a set of three RAIDZ2 vdevs with two hot
spares.

```
  pool: pool0
 state: ONLINE
config:

        NAME                                            STATE     READ WRITE CKSUM
        pool0                                           ONLINE       0     0     0
          raidz2-0                                      ONLINE       0     0     0
            gptid/f463ba10-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f4e63ba8-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f5b71bd9-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f5a8c04f-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f6761b6c-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f6962c80-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f703659f-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f741c71e-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f83c5c41-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
          raidz2-1                                      ONLINE       0     0     0
            gptid/f78c5ea8-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f7cdae80-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f8828b60-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f9028771-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f9c16102-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f9f71362-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fa36b199-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fab63c1a-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fb2fa199-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
          raidz2-2                                      ONLINE       0     0     0
            gptid/fbac1ab9-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fc134b95-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fc85d895-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fcf9e00b-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fd5f5654-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fdad80f7-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fdee72e7-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fe2ab932-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fe594a0e-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
          raidz2-3                                      ONLINE       0     0     0
            gptid/fea1d2f2-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/ff15ad27-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/ff1ece3f-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/ff4a32f6-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/ff6b07ae-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/ff9e36b0-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/ffb8521a-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/ffcb2138-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/000b03bb-adb8-11eb-b057-901b0e42a41b  ONLINE       0     0     0
        spares
          gptid/001d8210-adb8-11eb-b057-901b0e42a41b    AVAIL
          gptid/0024cda9-adb8-11eb-b057-901b0e42a41b    AVAIL
```

## dnsmasq configuration

The NFS server runs `dnsmasq` in order to provide address to clients
on VLAN 210. TrueNAS doesn't provide DHCP services out of the box, so
it was necessary to manually configured the service.

We largely followed the model described in "[DNSMasq in a FreeNAS Jail
-- A better approach][dnsmasq-freenas]".

[dnsmasq-freenas]: https://blog.udance.com.au/2020/03/25/dnsmasq-in-a-freenas-jail-a-better-approach/

`dnsmasq` runs in a jail at 10.253.0.11, with a configuration file
located outside the jail at `/mnt/pool0/moc/config/dnsmasq.conf`. The
configuration can be found in
<https://github.com/larsks/truenas-dnsmasq-config>.
