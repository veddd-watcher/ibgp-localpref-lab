# Day 9: iBGP and LOCAL_PREF (Containerlab + FRR 10.2.1)

## Topology
- r1: AS 65001, lo 1.1.1.1/32
- r2: AS 65001, lo 2.2.2.2/32
- r3: AS 65002, lo 3.3.3.3/32
- r1-r2 10.0.12.0/30 (iBGP), r1-r3 10.0.13.0/30 (eBGP), r2-r3 10.0.23.0/30 (eBGP)

## Config
- r1: route-map PREFER-R3 (permit 10, set local-preference 200) inbound on neighbor 10.0.13.2
- r1: neighbor 10.0.12.2 next-hop-self
- r2, r3: no policy

## Results
- 01-r2-before-localpref.txt: r2 best path for 3.3.3.3/32 is via 10.0.23.2 (eBGP from r3)
- 02-r2-after-localpref.txt: r2 best path is via r1 over iBGP, LocPrf 200, next hop 10.0.13.2
- 03-r1-advertised-to-r2.txt: r1 sends 3.3.3.3/32 to r2 with LocPrf 200, next hop unchanged
- 04-r2-after-nexthopself.txt: r2 best path for 3.3.3.3/32 is via 10.0.12.1, LocPrf 200
- 05-r1-advertised-after-nexthopself.txt: r1 advertised routes to r2 after next-hop-self
- 06-r3-no-localpref.txt: r3 shows no LocPrf on routes learned from AS 65001

## Run
sudo containerlab deploy -t triangle.clab.yml
sudo containerlab destroy -t triangle.clab.yml
