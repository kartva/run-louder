```
sudo apt install -y clang gcc-multilib
clang -target bpf -O2 -g -o bpf.o -c bpf.c

# replace "wlp3s0" with the network adaptor you want;
# use ip a to look at available network adaptors

# add the special ingress qdisc to mess with incoming packets

sudo tc qdisc add dev wlp3s0 ingress

# add the prio classful qdisc to the network adator

sudo tc qdisc add dev wlp3s0 root handle 1: prio

# add the filter to the ingress qdisc
# da = direct-action

sudo tc filter add dev wlp3s0 ingress bpf da obj bpf.o sec ingress

# add the filter to the outbound prio qdisc
# da = direction-action
# protocol all = affect all packets
# prio 1 = filter has highest priority

sudo tc filter add dev wlp3s0 protocol all parent 1: prio 1 bpf da obj bpf.o sec egress
```