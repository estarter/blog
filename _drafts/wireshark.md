tshark -D

tcpdump -i any -vvvvv -w test1.txt "(src host localhost and dst host localhost)"
tshark -i lo0 -w test.ping.pcap icmp
tshark -i any -w test.all2.pcap

capinfos test.all
tshark -r test.all.pcap -c 1000 -V icmp  | less
tshark -r test.all.pcap -q -z expert
tshark -r test.all2.pcap -q -z hosts
tshark -r test.all2.pcap -q -z http,tree
tshark -r test.all2.pcap -q -z http_req,tree
tshark -r test.all2.pcap -q -z io,phs
tshark -r test.ya.pcap  -T fields -e ip.src -e ip.dst

SSLKEYLOGFILE - define an output file for the browser, this file can be used to decrypt ssl traffic

https://www.youtube.com/watch?v=F2HAPAUqitc
